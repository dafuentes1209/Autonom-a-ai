# ROADMAP DE SEGURIDAD INTEGRADO · AGENCIA IA
## Protección máxima + Velocidad de negocio | Roles: David (Backend/Testing) + Amigo (Infra/Implementation)

**Filosofía**: No es "seguridad LUEGO implementación". Es "seguridad DENTRO de cada fase".
- **Fase 1**: Stack local seguro (0 clientes en producción) — 60h
- **Fase 2**: Aislamiento multi-tenant (1–3 clientes piloto) — 120h
- **Fase 3**: Compliance + auditoría (3–8 clientes pagando) — 100h
- **Fase 4**: Escala segura (15+ clientes) — 250h
- **Fase 5**: Mercado internacional (acceso USD) — 150h

**Total: 680h + $15.5K, ZERO delay a clientes**

---

## FASE 1 (Semanas 1–3) · Stack Propio Seguro
**Goal**: Demo local con secretos protegidos. SIN acceso a clientes reales aún.
**Timeline**: Paralelo con cambio de modelo + stack OK.

### 🔒 Tarea 1.1: HashiCorp Vault en tu laptop
**Por qué**: API keys de Gemini, Brave, Telegram viviendo en `~/.bashrc` o `openclaw.json` = 1 sudo y pierdes TODO.

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Instalar Vault en WSL, inicializar, crear secrets engine, documentar startup | 6h |
| **David** | Testing: verificar que todos los secretos están guardados, que openclaw.json NO tiene hardcoded values | 2h |

**Implementación (Amigo)**:
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
vault kv put agencia/gemini api_key="sk-..." model="gemini-3.1-flash-lite-preview"
vault kv put agencia/brave api_key="<brave-key>"
vault kv put agencia/telegram bot_token="<telegram-token>"
vault kv put agencia/gogcli client_id="..." client_secret="..." refresh_token="..."

# 5. Verificar
vault kv list agencia
vault kv get agencia/gemini
```

**CheckList Tarea 1.1**:
- [ ] Amigo: Vault instalado y corriendo en `http://127.0.0.1:8200`
- [ ] Amigo: Todos los secretos (Gemini, Brave, Telegram, GOG) guardados en Vault
- [ ] Amigo: `openclaw.json` contiene referencias a Vault, NO valores reales
- [ ] Amigo: Documentado: "¿Cómo iniciar Vault?" en README
- [ ] David: `vault kv list agencia` retorna 4+ items ✅
- [ ] David: `env | grep -i gemini` no muestra nada ✅

**Entregable**: `~/.vault_startup.sh` + README de credenciales

---

### 🔐 Tarea 1.2: GOG CLI + Google Calendar seguro
**Por qué**: OAuth tokens no deben vivir en keyring accesible al mundo.

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Crear GOG bridge script, configurar backend=file → memory, autenticar | 4h |
| **David** | Testing: verificar que ~/.config/gogcli/keyring/ está vacío, gog calendar list funciona | 1h |

**Implementación (Amigo)**:
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
- [ ] Amigo: GOG se autentica una sola vez → refresh_token guardado en Vault
- [ ] Amigo: Script bridge automatiza obtención de token
- [ ] Amigo: `/home/daaavid/.config/gogcli/keyring/` está VACÍO (no guarda nada)
- [ ] David: `gog calendar list` funciona sin interacción manual ✅
- [ ] David: Script es idempotent (puedes correrlo 2x) ✅

**Entregable**: `vault_gog_bridge.sh` + GOG keyring limpio

---

### 🛡️ Tarea 1.3: TLS local (self-signed → Let's Encrypt después)
**Por qué**: Aunque sea local, no transmitas credenciales en HTTP.

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Generar cert self-signed, configurar OpenClaw para HTTPS | 1h |
| **David** | Testing: `curl -k https://localhost:8443/health` retorna 200 | 30min |

**Implementación (Amigo)**:
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
- [ ] Amigo: Cert self-signed generado
- [ ] Amigo: openclaw.json configura TLS
- [ ] Amigo: OpenClaw inicia en HTTPS (no HTTP)
- [ ] David: `curl -k https://localhost:8443/health` = 200 ✅

**Entregable**: Certs en `~/.openclaw/certs/`

---

### 📋 Tarea 1.4: Documentar "Credential Policy"
**Por qué**: Si no lo escribes, alguien va a poner un API key en GitHub.

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Draft de SECURITY.md policy | 45min |
| **David** | Review + pre-commit hook anti-leak | 15min |

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
- ✅ VAULT_ADDR + auth credentials en máquina asegurada
- ✅ Rotación automática c/30 días (después)
- ✅ Auditoría de acceso a secretos (logs)

## Si filtraste un secret:
1. INMEDIATAMENTE: revoke en source (Gemini console, Telegram BotFather, etc)
2. Generar nueva credencial
3. Actualizar Vault
4. Auditar: ¿Quién accedió? ¿Cuándo? ¿Desde dónde?
```

**CheckList Tarea 1.4**:
- [ ] Amigo: `SECURITY.md` creado y committed
- [ ] David: Pre-commit hook que bloquea pushes con `sk-` o `sk_` patterns ✅
- [ ] Ambos: Leyeron y entienden la policy

**Entregable**: SECURITY.md + pre-commit hook

---

### ✅ Validación Fase 1 (Viernes 11 abril)
```bash
# Amigo corre:
vault status
vault kv list agencia | wc -l  # Debe ser ≥4
ls -la ~/.openclaw/certs

# David corre:
env | grep -i "gemini\|telegram\|brave"  # Nada
curl -k https://localhost:8443/health
source ~/.openclaw/vault_gog_bridge.sh && gog calendar list
find ~ -name "*.json" | xargs grep -l "sk-\|sk_"  # Nada encontrado
```

**Gate**: Todos los tests PASS ✅ → Avanzan a Fase 2

---

## FASE 2 (Semanas 4–6) · Aislamiento Multi-tenant
**Goal**: Kubernetes con aislamiento real. Cliente A ≠ Cliente B (GDPR critical).

### 🎯 Tarea 2.1: Kubernetes setup (Minikube → VPS después)

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Instalar Minikube, crear namespaces, setup NetworkPolicy | 18h |
| **David** | Testing: crear 2 clientes test, verificar aislamiento, escalar | 4h |

**Implementación (Amigo)**:
```bash
# 1. Instalar Minikube
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube start --cpus=4 --memory=8192 --driver=docker

# 2. Crear namespaces
kubectl create namespace client-001
kubectl create namespace client-002
kubectl create namespace client-003

# 3. NetworkPolicy: cliente A solo habla con A
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

# 4. Label namespaces
for ns in client-001 client-002 client-003; do
  kubectl label namespace $ns name=$ns
done
```

**CheckList Tarea 2.1**:
- [ ] Amigo: Minikube corriendo con 3+ namespaces
- [ ] Amigo: NetworkPolicy deny-all entre namespaces
- [ ] David: Pod en client-001 NO puede acceder a client-002 ✅ (timeout)
- [ ] David: Pod en client-002 NO puede acceder a client-001 ✅ (timeout)

**Entregable**: Minikube config + NetworkPolicy YAML

---

### 🔑 Tarea 2.2: Secrets en Kubernetes (no env vars)

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Crear Secrets por namespace, configurar Pod volumeMounts | 4h |
| **David** | Testing: verificar que env vars NO exponen credenciales en describe | 1h |

**Implementación (Amigo)**:
```bash
# 1. Crear Secret por cliente (con datos de Vault)
kubectl -n client-001 create secret generic openclaw-credentials \
  --from-literal=gemini_api_key=$(vault kv get -field=api_key agencia/gemini) \
  --from-literal=brave_api_key=$(vault kv get -field=api_key agencia/brave) \
  --from-literal=telegram_bot_token=$(vault kv get -field=bot_token agencia/telegram)

# 2. Pod references (NO hardcoded)
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
```

**CheckList Tarea 2.2**:
- [ ] Amigo: Secrets creados en Kubernetes por namespace
- [ ] Amigo: Pods leen credenciales desde volumeMounts
- [ ] David: `kubectl describe pod openclaw-client-001` NO muestra valores ✅
- [ ] David: `kubectl logs openclaw-client-001` NO loguea credenciales ✅

**Entregable**: Pod manifests con Secret references

---

### 💾 Tarea 2.3: Encrypted volumes per client

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | PVC + StorageClass setup (local encryption structure) | 4h |
| **David** | Testing: verificar mount, acceso a /data | 1h |

**Implementación (Amigo)**:
```bash
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
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: encrypted
provisioner: hostpath.local
parameters:
  encryption: true
EOF
```

**CheckList Tarea 2.3**:
- [ ] Amigo: PVC creado por namespace
- [ ] Amigo: Pod monta `/data` desde PVC
- [ ] David: Datos persisten en `/data` ✅

**Entregable**: StorageClass + PVC YAML

---

### 🧹 Tarea 2.4: Pod Security Policy (read-only root FS)

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Crear PodSecurityPolicy restrictivo | 2h |
| **David** | Testing: intentar write a /usr/bin (debe fallar) | 30min |

**CheckList Tarea 2.4**:
- [ ] Amigo: PSP restrictivo aplicado a cada namespace
- [ ] Amigo: Pod NO puede escribir en /usr/bin, /etc
- [ ] David: Pod SÍ puede escribir en /var/log, /tmp ✅

**Entregable**: PodSecurityPolicy YAML

---

### ✅ Validación Fase 2 (Viernes 25 abril)
```bash
# Amigo corre:
kubectl get namespaces
kubectl get networkpolicies -A

# David corre:
kubectl -n client-001 run test1 --image=nginx --restart=Never
kubectl -n client-002 run test2 --image=nginx --restart=Never
kubectl exec -it test2 -n client-002 -- curl http://test1.client-001:80  # TIMEOUT = OK
```

**Gate**: Aislamiento de red funciona ✅ → Avanzan a Fase 3

---

## FASE 3 (Semanas 7–9) · Logging y Auditoría
**Goal**: ELK Stack + Falco. Saber qué hace cada cliente.

### 📊 Tarea 3.1: ELK Stack en Kubernetes

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Helm install Elasticsearch + Kibana + Logstash en K8s | 8h |
| **David** | Testing + dashboards: crear búsquedas por cliente en Kibana | 2h |

**CheckList Tarea 3.1**:
- [ ] Amigo: ELK corriendo en namespace `logging`
- [ ] Amigo: Logstash ingesta logs de todos los pods
- [ ] David: Kibana searchable por cliente (namespace) ✅

---

### 🔍 Tarea 3.2: Falco (Runtime Security)

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Instalar Falco, crear reglas custom para detección de anomalías | 4h |
| **David** | Testing: intentar syscall sospechosa, verificar alerta en Kibana | 1h |

**CheckList Tarea 3.2**:
- [ ] Amigo: Falco corriendo en todos los nodos
- [ ] Amigo: Reglas custom para detección de anomalías
- [ ] David: Falco alerta correctamente en Kibana ✅

---

### ✅ Validación Fase 3 (Viernes 6 junio)

**Gate**: Falco alertando correctamente ✅ → Avanzan a Fase 4

---

## FASE 4 (Semanas 10–18) · Compliance + Secrets Manager (VPS)
**Goal**: Vault en producción, rotación automática, GDPR API, SOC2 iniciado.

### 🔐 Tarea 4.1: Vault en VPS Hostinger

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Instalar Vault en VPS, inicializar con 5 keys, setup Kubernetes auth | 12h |
| **David** | Testing: verificar que credentials rotate, que RLS de Supabase está configurado | 2h |

---

### 🔄 Tarea 4.2: Credential Rotation Automática

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Scripts cron para rotación (Telegram, Brave, GOG tokens) | 8h |
| **David** | Testing: correr rotation manual, verificar que no hay downtime | 2h |

---

### 📋 Tarea 4.3: GDPR Data Deletion API

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Setup: infrastructure para delete (K8s namespace cleanup, backups) | 6h |
| **David** | Implementar: API endpoint DELETE + SQL logic para Supabase, test deletion | 6h |

---

### 🎯 Tarea 4.4: SOC2 Type II Audit

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Preparar evidencia de infraestructura + documentación | 15h |
| **David** | Preparar evidencia de testing + code quality + compliance checks | 10h |
| **Ambos** | Contatar auditor, coordinar interviews | 5h |

**Gate**: SOC2 audit iniciado (no necesariamente completado) ✅

---

## FASE 5 (Oct 2026 - Mar 2027) · Mercado US
**Goal**: Multi-región, CCPA compliance, clientes USD.

### 🌎 Tarea 5.1: Multi-región Vault

| Rol | Tarea | Horas |
|-----|-------|-------|
| **Amigo** | Segundo Vault en región US (Miami), disaster recovery setup | 15h |
| **David** | Testing: failover, data residency per region | 3h |

---

## RESUMEN HORAS POR FASE

| Fase | David | Amigo | Total | Bloqueador |
|------|-------|-------|-------|-----------|
| 1 | 2h + testing | 6h + docs | 8-10h | Vault funciona |
| 2 | 4h + testing | 18h + K8s | 22-26h | Aislamiento OK |
| 3 | 3h + dashboards | 12h + ELK/Falco | 15-18h | Falco alertando |
| 4 | 18h + APIs | 26h + infra | 44-50h | SOC2 iniciado |
| 5 | 5h + testing | 15h + setup | 20-25h | Multi-región OK |
| **TOTAL** | **32h** | **77h** | **109h** | **0 delay** |

**⚠️ Nota**: Estos son SOLO las horas de seguridad. Paralelo a esto, David hace n8n/testing (120h aprox) y Amigo maneja cliente onboarding/ops.

---

## REGLAS DE ORO

✅ **Especialización clara**: David = testing + validación, Amigo = implementación + infra  
✅ **Documentación al ir**: Amigo documenta cada paso de implementación  
✅ **Testing primero**: David no acepta nada sin tests que pasen  
✅ **Syncs regulares**: Miércoles deep dives para revisar código/arquitectura  
✅ **Zero delay**: Paralelo, no secuencial — no esperes a que uno termine para empezar

---

**¿Listos? Lunes 7 abril, 8am standup.**
