# ROADMAP DE SEGURIDAD INTEGRADO · AGENCIA IA
## Protección máxima + Velocidad de negocio

**Filosofía**: No es "seguridad LUEGO implementación". Es "seguridad DENTRO de cada fase".
- **Fase 1**: Stack local seguro (0 clientes en producción)
- **Fase 2**: Aislamiento multi-tenant (1–3 clientes piloto)
- **Fase 3**: Compliance + auditoría (3–8 clientes pagando)
- **Fase 4**: Escala segura (15+ clientes)
- **Fase 5**: Mercado internacional (acceso USD)

---

## FASE 1 (Semanas 1–3) · Stack Propio Seguro
**Goal**: Demo local con secretos protegidos. SIN acceso a clientes reales aún.
**Timeline**: Paralelo con cambio de modelo + stack OK.

### 🔒 Tarea 1.1: HashiCorp Vault en tu laptop
**Por qué**: API keys de Gemini, Brave, Telegram viviendo en `~/.bashrc` o `openclaw.json` = 1 sudo y pierdes TODO.

**Implementación (WSL Ubuntu)**: 40h → 8h con esta guía
```bash
# 1. Instalar Vault (WSL Ubuntu)
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt update && sudo apt install vault

# 2. Iniciar Vault en dev mode (laptop solo)
vault server -dev

# En otra terminal
export VAULT_ADDR='http://127.0.0.1:8200'
vault login -method=token token=<token_from_startup>

# 3. Crear secrets engine
vault secrets enable -path=agencia kv-v2

# 4. Guardar API keys (NUNCA en plaintext)
vault kv put agencia/gemini api_key="sk-..." model="gemini-2.5-flash-lite-preview"
vault kv put agencia/brave api_key="<brave-key>"
vault kv put agencia/telegram bot_token="<telegram-token>"
vault kv put agencia/gogcli client_id="..." client_secret="..." refresh_token="..."

# 5. Verificar
vault kv list agencia
vault kv get agencia/gemini

# 6. (LUEGO) Automatizar: Script que lee desde Vault en startup
# Esto va en openclaw.json como referencia, no el valor real
```

**CheckList Tarea 1.1**:
- [ ] Vault instalado y corriendo en `http://127.0.0.1:8200`
- [ ] Todos los secretos (Gemini, Brave, Telegram, GOG) guardados en Vault
- [ ] `openclaw.json` contiene referencias a Vault, NO valores reales
- [ ] Documentado: "¿Cómo iniciar Vault?" en README

**Entregable**: `~/.vault_startup.sh` + README de credenciales

---

### 🔐 Tarea 1.2: GOG CLI + Google Calendar seguro
**Por qué**: OAuth tokens no deben vivir en keyring accesible al mundo. Y menos aún en un sistema multi-usuario.

**Implementación**: 30h → 6h
```bash
# Estado actual: GOG_KEYRING_BACKEND=file
# Problema: Sigue siendo un archivo readable en el filesystem

# Solución: Vault + GOG script
# 1. GOG autentica → genera refresh_token
# 2. Guardar refresh_token en Vault (nunca en disk)
# 3. Cada vez que OpenClaw necesita acceso: obtener token de Vault via API

# Script: ~/.openclaw/vault_gog_bridge.sh
#!/bin/bash
VAULT_ADDR='http://127.0.0.1:8200'
TOKEN=$(curl -s $VAULT_ADDR/v1/agencia/data/gogcli | jq -r '.data.data.refresh_token')
export GOG_REFRESH_TOKEN=$TOKEN
export GOG_KEYRING_BACKEND=memory # En memoria, no en disk
gog calendar list
```

**CheckList Tarea 1.2**:
- [ ] GOG se autentica una sola vez → refresh_token guardado en Vault
- [ ] Script bridge automatiza obtención de token
- [ ] `/home/daaavid/.config/gogcli/keyring/` está VACÍO (no guarda nada)
- [ ] Test: `gog calendar list` funciona sin interacción manual

**Entregable**: `vault_gog_bridge.sh` + GOG keyring limpio

---

### 🛡️ Tarea 1.3: TLS local (self-signed → Let's Encrypt después)
**Por qué**: Aunque sea local, no transmitas credenciales en HTTP.

**Implementación**: 10h → 2h
```bash
# En WSL: generar cert self-signed para testing
mkdir -p ~/.openclaw/certs
openssl req -x509 -newkey rsa:4096 -keyout ~/.openclaw/certs/key.pem \
  -out ~/.openclaw/certs/cert.pem -days 365 -nodes \
  -subj "/CN=localhost.openclaw.local"

# En openclaw.json:
# "tls": {
#   "cert_path": "/home/daaavid/.openclaw/certs/cert.pem",
#   "key_path": "/home/daaavid/.openclaw/certs/key.pem"
# }

# Test:
curl -k https://localhost:8443/health
```

**CheckList Tarea 1.3**:
- [ ] Cert self-signed generado
- [ ] openclaw.json configura TLS
- [ ] OpenClaw inicia en HTTPS (no HTTP)
- [ ] Curl test pasa (ignora insecure con `-k`)

**Entregable**: Certs en `~/.openclaw/certs/`

---

### 📋 Tarea 1.4: Documentar "Credential Policy"
**Por qué**: Si no lo escribes, alguien va a poner un API key en GitHub.

**Documento**: `SECURITY.md` en repo
```markdown
# CREDENTIAL MANAGEMENT POLICY

## Prohibido (NUNCA):
- ❌ API keys en .env, .bashrc, openclaw.json
- ❌ Tokens en comentarios de código
- ❌ Secrets en GitHub (ni en commits históricos)
- ❌ Valores en Slack/email
- ❌ Acceso a Vault sin autenticación

## Obligatorio:
- ✅ Todos los secretos en HashiCorp Vault
- ✅ Vault_ADDR + auth credentials en máquina asegurada
- ✅ Rotación automática c/30 días (después)
- ✅ Auditoría de acceso a secretos (logs)

## Si filtraste un secret:
1. INMEDIATAMENTE: revoke en source (Gemini console, Telegram BotFather, etc)
2. Generar nueva credencial
3. Actualizar Vault
4. Auditar: ¿Quién accedió? ¿Cuándo? ¿Desde dónde?
```

**CheckList Tarea 1.4**:
- [ ] `SECURITY.md` creado y committed
- [ ] Tu amigo lee y entiende la policy
- [ ] Pre-commit hook que bloquea pushes con `sk-` o `sk_` patterns

---

### ✅ Validación Fase 1
**Prueba integral antes de Semana 3**:
```bash
# 1. Vault corriendo
vault status

# 2. Todos los secretos guardados
vault kv list agencia | wc -l  # Debe ser ≥5

# 3. OpenClaw funciona SIN env vars expuestos
env | grep -i "gemini\|telegram\|brave" # Nada

# 4. GOG CLI funciona vía bridge
source ~/.openclaw/vault_gog_bridge.sh && gog calendar list

# 5. TLS funciona
curl -k https://localhost:8443/health

# 6. Sin archivos de credenciales en disco
find ~ -name "*.json" -o -name "*.env" | xargs grep -l "sk-\|sk_" # Nada encontrado
```

**Bloqueador para avanzar a Fase 2**: Todas estas pruebas PASS.

---

## FASE 2 (Semanas 4–6) · Aislamiento Multi-tenant
**Goal**: Contrainer-level isolation para cliente piloto. 1–3 clientes en contenedor aislado.
**Arquitectura**: Kubernetes en miniatura (Minikube local) → Escalado a VPS después.

### 🎯 Tarea 2.1: Kubernetes local (Minikube)
**Por qué**: Docker Compose NO ofrece aislamiento de red ni políticas de seguridad. Kubernetes sí.

**Implementación**: 60h → 12h (usando Minikube preconfigurado)
```bash
# 1. Instalar Minikube en WSL
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# 2. Iniciar cluster
minikube start --cpus=4 --memory=8192 --driver=docker

# 3. Verificar
kubectl get nodes

# 4. Crear namespace por cliente (AISLAMIENTO CRÍTICO)
kubectl create namespace client-001
kubectl create namespace client-002
kubectl create namespace client-003

# 5. Crear NetworkPolicy (cliente A ↔ A SOLO)
cat << 'EOF' | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: client-isolation-001
  namespace: client-001
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: client-001
  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: client-001
  - to:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          k8s-app: kube-dns
    ports:
    - protocol: UDP
      port: 53
EOF

# 6. Label los namespaces
kubectl label namespace client-001 name=client-001
kubectl label namespace client-002 name=client-002
kubectl label namespace client-003 name=client-003
```

**CheckList Tarea 2.1**:
- [ ] Minikube corriendo con 3 namespaces
- [ ] NetworkPolicy deny-all-ingress entre namespaces
- [ ] Test: Pod en client-001 NO puede acceder a pod en client-002
  ```bash
  # Terminal 1: Pod en client-001 escucha
  kubectl -n client-001 run netcat --image=alpine:latest -- nc -l -p 8888
  # Terminal 2: Intenta conectar desde client-002
  kubectl -n client-002 run test --image=alpine:latest -- nc -zv client-001-service 8888
  # Result: timeout (bueno)
  ```

**Entregable**: Minikube config + NetworkPolicy YAML

---

### 🔑 Tarea 2.2: Secrets en Kubernetes (no env vars)
**Por qué**: Kubernetes Secrets (aunque base64, no encriptados por defecto) son mejor que env vars en dockerfile.

**Implementación**: 20h → 4h
```bash
# 1. Crear Secret por cliente (con datos de Vault)
kubectl -n client-001 create secret generic openclaw-credentials \
  --from-literal=gemini_api_key=$(vault kv get -field=api_key agencia/gemini) \
  --from-literal=brave_api_key=$(vault kv get -field=api_key agencia/brave) \
  --from-literal=telegram_bot_token=$(vault kv get -field=bot_token agencia/telegram)

# 2. Reference en Pod (NO hardcoded)
cat << 'EOF' | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: openclaw-client-001
  namespace: client-001
spec:
  containers:
  - name: openclaw
    image: openclaw:latest
    env:
    - name: GEMINI_API_KEY
      valueFrom:
        secretKeyRef:
          name: openclaw-credentials
          key: gemini_api_key
    volumeMounts:
    - name: config
      mountPath: /etc/openclaw
      readOnly: true
  volumes:
  - name: config
    secret:
      secretName: openclaw-credentials
EOF

# 3. (LUEGO) Encrypted at rest
# Esto requiere etcd encryption en Kubernetes — después
```

**CheckList Tarea 2.2**:
- [ ] Secrets creados en Kubernetes (NO en Vault aún, es overhead)
- [ ] Pods leen credenciales desde volumeMounts
- [ ] Verificar: `kubectl -n client-001 describe pod openclaw-client-001` no muestra valores
- [ ] Audit: `kubectl -n client-001 logs openclaw-client-001` no loguea credenciales

**Entregable**: Pod manifests con Secret references

---

### 💾 Tarea 2.3: Encrypted volumes per client
**Por qué**: Si el disco de Hostinger se roba, no quiero que lea datos de cliente.

**Implementación**: 40h → 8h (usando Kubernetes)
```bash
# Esto se hace en VPS después, pero estructura local ahora:

# 1. En Minikube, usar PersistentVolume con local encryption
# 2. Cada cliente obtiene PVC cifrado
# Estructura YAML:
cat << 'EOF' | kubectl apply -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: client-001-data
  namespace: client-001
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: encrypted
---
# StorageClass con encryption (simulated, real en VPS)
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: encrypted
provisioner: hostpath.local
parameters:
  encryption: true  # Flag para implementación real
EOF

# Mount en pod:
volumeMounts:
- name: client-data
  mountPath: /data
volumes:
- name: client-data
  persistentVolumeClaim:
    claimName: client-001-data
```

**CheckList Tarea 2.3**:
- [ ] PVC creado por namespace
- [ ] Pod monta `/data` desde PVC
- [ ] Documentado: "En VPS real, LUCS encryptment en LVM"

**Entregable**: StorageClass + PVC YAML

---

### 🧹 Tarea 2.4: Pod Security Policy (read-only root FS)
**Por qué**: Si contenedor es comprometido, no puede escribir en `/usr/bin` o cambiar binarios.

**Implementación**: 15h → 3h
```bash
# 1. Crear PodSecurityPolicy restrictivo
cat << 'EOF' | kubectl apply -f -
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted-client
spec:
  privileged: false
  allowPrivilegeEscalation: false
  requiredDropCapabilities:
  - ALL
  volumes:
  - 'configMap'
  - 'emptyDir'
  - 'projected'
  - 'secret'
  - 'downwardAPI'
  - 'persistentVolumeClaim'
  hostNetwork: false
  hostIPC: false
  hostPID: false
  runAsUser:
    rule: 'MustRunAsNonRoot'
  runAsGroup:
    rule: 'MustRunAs'
    ranges:
    - min: 999
      max: 65535
  fsGroup:
    rule: 'MustRunAs'
    ranges:
    - min: 999
      max: 65535
  readOnlyRootFilesystem: true
  seLinux:
    rule: 'MustRunAs'
    seLinuxOptions:
      type: spc_t
EOF

# 2. Bind a namespace
kubectl create rolebinding restricted-client \
  --clusterrole=restricted-client \
  --group=system:serviceaccounts:client-001 \
  -n client-001

# 3. Dockerfile de OpenClaw debe soportar read-only root:
# - App code en /app (readOnly=false)
# - Binarios en /usr/bin (readOnly=true)
# - Logs en /var/log (emptyDir)
```

**CheckList Tarea 2.4**:
- [ ] PSP restrictivo aplicado a cada namespace
- [ ] Pod NO puede escribir en /usr/bin, /etc
- [ ] Pod SÍ puede escribir en /var/log, /tmp (emptyDir)
- [ ] Dockerfile base refactorizado (leído en Fase 1, implementado aquí)

**Entregable**: PodSecurityPolicy + dockerfile updates

---

### ✅ Validación Fase 2
```bash
# 1. Minikube corriendo con 3 namespaces
kubectl get namespaces

# 2. NetworkPolicy activo
kubectl -n client-001 get networkpolicies

# 3. Pods aislados (NO pueden comunicarse)
kubectl -n client-001 run test1 --image=nginx --restart=Never
kubectl -n client-002 run test2 --image=nginx --restart=Never
# Intentar curl: TIMEOUT (bueno)
kubectl exec -it test2 -n client-002 -- curl http://test1.client-001:80
# Timeout ✓

# 4. Secrets no expuestos
kubectl -n client-001 get secrets openclaw-credentials -o jsonpath='{.data}' | base64 -d # Encrypted ✓

# 5. Volumes encriptados (estructura)
kubectl get pvc -A

# 6. PSP restrictivo
kubectl auth can-i create pods --as=system:serviceaccount:client-001:default -n client-001 --with-namespace=restricted-client
```

**Bloqueador**: Validación 2.3 pass (aislamiento de red funciona).

---

## FASE 3 (Semanas 7–9) · Validación + Logging
**Goal**: Auditoría completa. Detectar intentos de breakout.

### 📊 Tarea 3.1: Logging centralizado (ELK Stack local)
**Por qué**: Si un contenedor es hackeado, necesitas saber QUÉ hizo antes de que lo borre.

**Implementación**: 80h → 16h
```bash
# 1. Instalar Elasticsearch + Logstash + Kibana en Minikube
helm repo add elastic https://helm.elastic.co
helm install elasticsearch elastic/elasticsearch -n logging --create-namespace
helm install kibana elastic/kibana -n logging
helm install logstash elastic/logstash -n logging

# 2. Configurar logstash para recolectar logs de K8s
cat << 'EOF' | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: logstash-config
  namespace: logging
data:
  logstash.conf: |
    input {
      kubernetes {
        namespace_watch => true
      }
    }
    filter {
      if [kubernetes][namespace_name] =~ /^client-/ {
        mutate {
          add_field => { "client_id" => "%{[kubernetes][namespace_name]}" }
        }
      }
    }
    output {
      elasticsearch {
        hosts => ["elasticsearch:9200"]
        index => "k8s-logs-%{+YYYY.MM.dd}"
      }
    }
EOF

# 3. Verificar logs en Kibana
# localhost:5601 → Discover → buscar "client-001"
```

**CheckList Tarea 3.1**:
- [ ] ELK Stack corriendo en namespace `logging`
- [ ] Logstash ingesta logs de todos los pods
- [ ] Kibana searchable por cliente (namespace)
- [ ] Alertas automáticas por patrones sospechosos (intrusion detection después)

**Entregable**: Logstash configs + Kibana dashboards

---

### 🔍 Tarea 3.2: Falco (Runtime Security)
**Por qué**: Detectar en tiempo real si un container intenta syscalls sospechosas.

**Implementación**: 40h → 8h
```bash
# 1. Instalar Falco
kubectl create namespace falco
helm repo add falcosecurity https://falcosecurity.github.io/charts
helm install falco falcosecurity/falco -n falco \
  --set falco.grpc.enabled=true \
  --set falco.grpcOutput.enabled=true

# 2. Regla custom: detectar si container intenta escribir en /usr/bin
cat << 'EOF' | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: falco-custom-rules
  namespace: falco
data:
  custom_rules.yaml: |
    - rule: Write to system directories
      desc: Detect writes to protected system directories
      condition: >
        open_write and 
        container and 
        (fd.name glob("/usr/bin/*") or fd.name glob("/etc/*"))
      output: >
        ALERT: Suspicious write detected
        (user=%user.name command=%proc.name file=%fd.name)
      priority: WARNING
      source: syscall
EOF

# 3. Output a Elasticsearch (para alertas)
```

**CheckList Tarea 3.2**:
- [ ] Falco corriendo en todos los nodos
- [ ] Reglas custom para detección de anomalías
- [ ] Alertas fluyen a Elasticsearch
- [ ] Dashboard en Kibana mostrando "Falco alerts"

**Entregable**: Falco rules + dashboards

---

### ✅ Validación Fase 3
```bash
# 1. ELK está activo
kubectl get pods -n logging

# 2. Falco está activo
kubectl get pods -n falco

# 3. Simular ataque: intentar escribir en /usr/bin
kubectl -n client-001 exec -it openclaw-client-001 -- sh -c "echo bad > /usr/bin/test"
# Falco debe alertar en Kibana

# 4. Logs centralizados
curl -s http://localhost:9200/_search?q=client-001 | jq '.hits.total'
# Debe retornar logs
```

**Bloqueador**: Falco alertando correctamente.

---

## FASE 4 (Semanas 10–18) · Compliance + Secrets Manager
**Goal**: GDPR-ready. Rotación automática de credenciales. Auditoría profesional.

### 🔐 Tarea 4.1: HashiCorp Vault en VPS (multi-tenant)
**Por qué**: Ya no es solo tu laptop. Es VPS con datos de clientes reales.

**Implementación**: 120h → 24h (usando Terraform)
```bash
# 1. Instalar Vault en VPS Hostinger
ssh user@vps_ip
sudo apt update && sudo apt install vault

# 2. Inicializar Vault (producción)
vault operator init \
  -key-shares=5 \
  -key-threshold=3
# Guardar keys en 5 lugares DIFERENTES (no en VPS)

# 3. Unseal (requiere 3 de 5 keys)
vault operator unseal <key1>
vault operator unseal <key2>
vault operator unseal <key3>

# 4. Enable auth methods
vault auth enable kubernetes
vault auth enable approle

# 5. Por cliente: crear role aislado en Vault
for client in client-001 client-002 client-003; do
  vault policy write $client - << EOF
path "agencia/data/clients/$client/*" {
  capabilities = ["read", "list"]
}
EOF
done

# 6. Kubernetes auth (automático)
kubectl -n client-001 create serviceaccount openclaw
kubectl -n client-001 create rolebinding openclaw \
  --clusterrole=system:auth-delegator \
  --serviceaccount=client-001:openclaw
```

**CheckList Tarea 4.1**:
- [ ] Vault corriendo en VPS (no en laptop ya)
- [ ] Initialized con 5 keys guardadas OFFLINE
- [ ] Auth method Kubernetes habilitado
- [ ] Cada cliente puede leer SOLO sus propios secrets

**Entregable**: Vault terraform configs + key recovery procedures

---

### 🔄 Tarea 4.2: Automatic credential rotation
**Por qué**: Un API key no debe vivir más de 30 días.

**Implementación**: 80h → 16h
```bash
# 1. Vault dynamic secrets (Gemini, Brave, Telegram)
# Para cada API provider que soporte (si no, manual)

# 2. Script cron para rotar Telegram bot token
cat << 'EOF' > /usr/local/bin/rotate-telegram-token.sh
#!/bin/bash
# 1. Obtener token actual de Vault
OLD_TOKEN=$(vault kv get -field=bot_token agencia/telegram)

# 2. Crear nuevo bot (vía BotFather en Telegram)
# 3. Guardar nuevo en Vault
vault kv put agencia/telegram bot_token="<new-token>"

# 4. Notificar clientes (via email): "Token rotado, sin acción necesaria"

# 5. Revocar token viejo (via BotFather)
EOF

chmod +x /usr/local/bin/rotate-telegram-token.sh

# 3. Scheduler (cron cada 30 días)
echo "0 0 1 * * /usr/local/bin/rotate-telegram-token.sh" | sudo tee /etc/cron.d/vault-rotation
```

**CheckList Tarea 4.2**:
- [ ] Script de rotación para cada credencial
- [ ] Cron schedulado (c/30 días)
- [ ] Audit log: "Token rotado el 2026-05-01"
- [ ] Test: rotación manual funciona sin downtime

**Entregable**: Rotation scripts + cron configs

---

### 📋 Tarea 4.3: GDPR + Data deletion
**Por qué**: Cliente dice "quiero mis datos borrados". Debes cumplir en 30 días.

**Implementación**: 100h → 20h
```bash
# 1. API de data deletion
# Endpoint: DELETE /api/v1/clients/{client_id}/data
# Requiere: admin auth + audit log

# 2. Script que borra COMPLETAMENTE
cat << 'EOF' > /usr/local/bin/delete-client-data.sh
#!/bin/bash
CLIENT_ID=$1

# 1. Namespace de K8s
kubectl delete namespace client-$CLIENT_ID

# 2. Database rows
psql -U admin -d agencia -c "DELETE FROM conversations WHERE client_id='$CLIENT_ID';"
psql -U admin -d agencia -c "DELETE FROM clients WHERE id='$CLIENT_ID';"

# 3. Backups (muy importante)
# Borrar TODOS los backups asociados
# rm -rf /backups/client-$CLIENT_ID

# 4. Vault secrets
vault kv metadata delete agencia/clients/client-$CLIENT_ID

# 5. Audit log
echo "$(date) - Client $CLIENT_ID data deleted by $(whoami)" >> /var/log/agencia/deletion.log
EOF

chmod +x /usr/local/bin/delete-client-data.sh

# 3. Test: deletion funciona
./delete-client-data.sh client-test
# Verificar: namespace gone, DB empty, Vault empty
```

**CheckList Tarea 4.3**:
- [ ] API DELETE implementado y autenticado
- [ ] Script de deletion probado (test client)
- [ ] Audit log de deletions
- [ ] Respuesta a GDPR request: "Completado en X días"

**Entregable**: Deletion API + scripts + audit logs

---

### 🔎 Tarea 4.4: SOC 2 Type II audit (tercero)
**Por qué**: Clientes Enterprise demandan certificación. "¿Pasaste auditoría?"

**Implementación**: 150h + $10K USD
```bash
# 1. Contratar auditor (Vanta, Drata, o local)
# ~$10K USD para startup
# Timeline: 6 semanas (octubre-noviembre)

# 2. Entregar evidencia (lo que has hecho en Fases 1-4):
# - Credentials management (Vault)
# - Access controls (RBAC, NetworkPolicy)
# - Encryption (TLS, data at rest)
# - Logging & monitoring (ELK, Falco)
# - Incident response plan (documentado)
# - Disaster recovery (backups testeados)

# 3. Documentación que vas a necesitar:
# SECURITY.md, INCIDENT-RESPONSE.md, BACKUP-POLICY.md, DPA.md

# 4. Resultado: SOC 2 Type II cert (válido 2 años)
# Esto es VENDIBLE: clientes grandes lo piden
```

**CheckList Tarea 4.4**:
- [ ] Auditor contratado por septiembre
- [ ] Evidence folder preparado
- [ ] Interviews con tu amigo + equipo
- [ ] Remediación de findings (expected 5-10)
- [ ] Cert obtenido antes de mercado US (febrero 2027)

**Entregable**: SOC 2 Type II certificate + management letter

---

### ✅ Validación Fase 4
```bash
# 1. Vault en VPS
vault status | grep "Sealed"

# 2. Cada cliente aislado en Vault
vault auth list | grep kubernetes

# 3. Credenciales rotadas
grep "rotated" /var/log/agencia/rotation.log | tail -1

# 4. Data deletion testeada
# Test: crear cliente fake → eliminarlo → verificar gone

# 5. SOC 2 status
ls -la /docs/SOC2*
```

**Bloqueador**: SOC 2 audit iniciado (no necesariamente completado).

---

## FASE 5 (Oct 2026 - Mar 2027) · Mercado US (Paralelo)
**Goal**: Expandir con máxima seguridad. Aprender de Fases 1-4.

### 🌎 Infraestructura para US
```bash
# 1. Segundo Vault en región US (disaster recovery)
# Vault cluster: Medellín + Miami (quorum)

# 2. Data residency: clientes US → servidor US
# Clientes CO → servidor CO

# 3. Cumplimiento adicional:
# - CCPA (California)
# - SOC 2 already done
# - GDPR (si venden a EU después)
```

---

## RESUMEN TIMELINE + COSTO

| Fase | Semanas | Tareas Seguridad | H/Dev | Costo USD | Bloqueador |
|------|---------|-----------------|-------|-----------|-----------|
| **1** | 1-3 | Vault, GOG, TLS, Policy | 60h | $0 | Vault funciona |
| **2** | 4-6 | K8s, aislamiento, PSP | 120h | $0 | Aislamiento red OK |
| **3** | 7-9 | Logging, Falco, auditoría | 100h | $500 (Falco license) | Falco alertando |
| **4** | 10-18 | Credential rotation, GDPR, SOC2 | 250h | $10K (auditor) | SOC 2 iniciado |
| **5** | 19-24 | Multi-región, US compliance | 150h | $5K (Vault HA) | Cert obtenido |
| **TOTAL** | | | **680h** | **$15.5K** | |

**Vs. sin seguridad**:
- Inversión: 680h + $15.5K
- Beneficio: Protege $2.5M año 1, $15M año 2
- ROI: (680h / 2000h/año) = 0.34 FTE = muy viable

---

## RECURSOS ADICIONALES

### Documentos a crear (OBLIGATORIOS):
1. **SECURITY.md** — Credential policy + incident response
2. **PRIVACY-POLICY.md** — GDPR compliance
3. **ARCHITECTURE.md** — Vault, K8s, ELK diagrams
4. **RUNBOOK.md** — "Si X pasa, haz Y"
5. **DPA.md** — Data Processing Agreement (para clientes)

### Herramientas recomendadas:
- **HashiCorp Vault** — Secrets management (open source)
- **Kubernetes** — Orchestration + isolation (open source)
- **ELK Stack** — Logging (open source)
- **Falco** — Runtime security (open source)
- **Drata/Vanta** — SOC 2 automation ($10K)

### Lecturas clave:
- NIST Cybersecurity Framework
- OWASP Top 10 para SaaS
- Kubernetes Security Hardening Guide
- GDPR handbook (EDPB)

---

## SI TIENES PRESUPUESTO PARA ACELERAR:

1. **Hire security contractor** (2 meses, $15K)
   - Implementa Phases 1-2 en 4 semanas
   - Capacita tu amigo
   - Deja playbooks

2. **Use managed Vault** (HashiCorp Cloud)
   - $400/mes pero zero-ops
   - Mejor para escala
   - Puede escalarse después

3. **Use Kubernetes managed service** (Linode, DigitalOcean)
   - $50/mes en lugar de self-hosted
   - Menos headache
   - Escala automática

---

**Próximo paso**: Comienza con Tarea 1.1 (Vault) esta semana.
El resto se desbloquea secuencialmente.
