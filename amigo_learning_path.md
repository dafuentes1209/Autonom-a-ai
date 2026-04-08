# PLAN DE APRENDIZAJE · TU AMIGO (No-Tech to Operating Partner)
## Objetivo: Entender lo que David está haciendo, poder testear, y ser independiente en negocio + operaciones

**Duración total**: 7 semanas de aprendizaje estructurado (2-3h/semana)
**Meta**: Pasar de "no entiendo nada" a "puedo explicar a un cliente cómo funciona"

---

## SEMANA 1: Conceptos fundamentales (2h aprendizaje)

**Objetivo**: Saber qué es Docker, Kubernetes, y por qué David los usa.

### Vocabulario que NECESITAS entender
- **Container** = Caja aislada donde vive una aplicación
- **Imagen** = Plano de la caja (qué va adentro)
- **Pod** = Unidad mínima en Kubernetes (= 1 contenedor, casi siempre)
- **Namespace** = Separación entre clientes (cliente A, cliente B, etc.)
- **API key / Token** = Contraseña que abre puertas (NO la compartas)

### Qué ver (45 min)
1. **Docker en 5 min** (YouTube)
   - Link: Search "Docker in 5 minutes" - explica qué es un container
   - Anota: "Un container es como una máquina virtual mini"

2. **Kubernetes basics** (YouTube)
   - Link: Search "Kubernetes for beginners" 
   - Anota: "Kubernetes = orquestador de containers (maneja múltiples contenedores)"

### Qué hacer (1h 15 min)
1. **Instala Docker** (si no lo tienes)
   ```
   # Windows: descarga Docker Desktop
   # Luego: abre terminal
   docker run hello-world
   ```
   ✓ Si ves "Hello from Docker!" → Entendiste qué es un container

2. **Habla con David**:
   - "¿Qué va dentro de MI contenedor del cliente?"
   - "¿Qué datos manejo?"
   - "¿Puedo acceder al contenedor de otro cliente?"
   - Anota: Respuestas (para poder explicar a clientes después)

### Checklist Semana 1
- [ ] Docker instalado y "hello-world" funciona
- [ ] Viste videos de Docker + Kubernetes (1h 15min total)
- [ ] Entiendes qué es un "namespace" (aislamiento entre clientes)
- [ ] Anotaste 3 preguntas para David (y él respondió)

---

## SEMANA 2: Hands-on con Minikube (3h práctica)

**Objetivo**: Poder levantar un cliente test sin que David lo haga.

### Setup inicial (30 min)
```bash
# David te hace esto UNA SOLA VEZ
# Instala Minikube (es Docker mini en tu laptop)

# En terminal:
minikube start

# Verificar:
kubectl get nodes
# Debe salir algo como "minikube   Ready"
```

✓ Si ves "Ready" → Tu Kubernetes está vivo

### Laboratorio A: Ver un cliente corriendo (30 min)
```bash
# David corre esto PRIMERO
kubectl create namespace client-test
kubectl run test-app --image=nginx -n client-test
kubectl get pods -n client-test

# Luego TÚ ves qué salió:
kubectl describe pod test-app -n client-test
```

**¿Qué estás viendo?**
- `client-test` = Ese es TU cliente, separado de otros
- `test-app` = Su aplicación corriendo
- `nginx` = Servidor web (no importa cuál sea, es solo un test)

**Pregunta para David**: "¿Dónde vive el código real del cliente?"
(Debería decir algo como "en Supabase" o "en una carpeta en el VPS")

### Laboratorio B: Destruir un cliente (15 min)
```bash
# Imitar: cliente cancela suscripción
kubectl delete namespace client-test

# Verificar que se fue:
kubectl get namespaces
# "client-test" no debe estar en la lista

# Pregunta para David: "¿Qué pasa con los datos del cliente antes de borrarlo?"
# (Debería decir algo como "lo respaldamos en el backup")
```

### Laboratorio C: Crear DOS clientes (30 min)
```bash
# Crear cliente A
kubectl create namespace client-a
kubectl run app-a --image=nginx -n client-a

# Crear cliente B
kubectl create namespace client-b
kubectl run app-b --image=nginx -n client-b

# Listar ambos:
kubectl get pods -A

# IMPORTANTE: Intentar acceder de A → B (no debe funcionar)
# David te enseña cómo intentarlo (no lo logres = bueno)
```

**Lo que entiendes ahora:**
- Cada cliente es una "burbuja" (namespace)
- Los datos de uno NO ven los datos del otro
- Puedo crear/destruir clientes sin afectar a otros

### Checklist Semana 2
- [ ] Minikube corriendo en tu laptop
- [ ] Creaste y viste cliente-test corriendo
- [ ] Borraste cliente-test exitosamente
- [ ] Creaste cliente-a y cliente-b (aislados)
- [ ] Documentaste: "¿Cómo creo un cliente nuevo?" (para staff después)

---

## SEMANA 3: Testeo y Troubleshooting (2h práctica)

**Objetivo**: Poder decir "algo no funciona" Y ayudar a diagnóstico.

### Checklist de testeo (copy/paste para usar siempre)
```bash
# Run this cada vez que testees:

# 1. ¿Los namespaces existen?
kubectl get namespaces
# Debe haber client-001, client-002, etc.

# 2. ¿Los pods están corriendo?
kubectl get pods -A
# STATUS debe ser "Running"

# 3. ¿Puedo acceder a logs?
kubectl logs -n client-001 -l app=openclaw
# Debe haber líneas de log (no errors)

# 4. ¿Está el secreto (API keys) guardado?
kubectl get secrets -n client-001
# Debe listar "openclaw-credentials"

# 5. ¿El cliente puede hablar con internet?
kubectl exec -it pod-name -n client-001 -- curl google.com
# Debe retornar HTML de Google
```

### Scenario 1: "El cliente A no responde"
**Tú reportas**: Escribes exactamente:
```
PROBLEMA: Cliente A no responde a Telegram
TIMESTAMP: 2026-04-09 14:30
LOGS (output de kubectl logs):
[copias y pegas aquí]
```

**David debugguea** usando la info que diste.
✓ Aprendiste: Logs son amigos.

### Scenario 2: "¿Dónde están los datos del cliente B?"
```bash
# David te muestra:
kubectl get pvc -n client-b
# PVC = disco del cliente

# Dentro del disco:
kubectl exec -it pod -n client-b -- ls /data
# Ahí están sus archivos, logs, etc.
```

✓ Aprendiste: Cada cliente tiene su propio disco.

### Documentación que TUVS que escribir
**Archivo**: `TESTING.md` (tu amigo lo escribe)
```markdown
# Cómo testear una instalación nueva

1. Cliente creado en Kubernetes
   kubectl get pods -n client-XXX | grep Running

2. API keys están guardadas
   kubectl get secrets -n client-XXX

3. Cliente puede conectarse a Google Calendar
   [David te enseña a testear esto]

4. Telegram bot responde
   [Abres Telegram y envías /start]

5. Brave Search funciona
   [Testeas escribiendo "busca restaurants"]

Si alguno falla:
- Reporta: QUÉ falló + CUÁNDO + LOGS (kubectl logs -n client-XXX)
- David lo arregla
```

### Checklist Semana 3
- [ ] Documentaste cómo testear una instalación
- [ ] Creaste scenario "cliente no responde" y conseguiste logs
- [ ] Entiendes dónde viven los datos de cada cliente
- [ ] Puedes reportar errores de forma útil a David

---

## SEMANA 4+: Operaciones (continuo)

**Objetivo**: Ser independiente en day-to-day operations.

### Tareas que tú haces (sin David)
1. **Onboarding cliente**:
   - David crea namespace + secrets
   - Tú testeas (checklist del Testing.md)
   - Tú envías email: "Tu asistente está listo"

2. **Soporte cliente**:
   - Cliente dice: "El asistente no responde"
   - Tú corres: `kubectl logs -n client-XXX -l app=openclaw`
   - Si hay error → reporta a David con logs
   - Si no hay logs → cliente tiene problema de internet (no nuestro)

3. **Métricas diarias**:
   ```bash
   # Cuántos clientes activos tengo hoy?
   kubectl get namespaces | grep client | wc -l
   
   # Alguno sin pods corriendo?
   kubectl get pods -A | grep -v Running
   ```

4. **Backups**:
   - ¿Cuándo fue el último backup? (David tiene script)
   - ¿Está encriptado? (Sí = bueno, No = avisa)

### Tareas que David hace (solo él)
- Kubernetes upgrades
- Vault secrets management
- Security patches
- Arquitectura cambios

### Tareas en paralelo (ambos)
- Onboarding de cliente (he coordina, él técnico)
- Demostración a cliente (tú pides, él enseña)
- Debugging crítico (tú reportas, él arregla)

---

## REFERENCIA RÁPIDA: Comandos que usarás (copy/paste)

```bash
# Lista todos los clientes
kubectl get namespaces | grep client

# Ver si un cliente específico está "sano"
kubectl get all -n client-001

# Ver últimos logs de un cliente
kubectl logs -n client-001 -l app=openclaw --tail=50

# Entrar al pod de un cliente (debugging)
kubectl exec -it pod-name -n client-001 -- /bin/sh

# Borrar un cliente (después de GDPR request)
kubectl delete namespace client-001

# Ver cuánto espacio de disco usa cada cliente
kubectl exec -it pod-name -n client-001 -- du -sh /data
```

---

## EVALUACIÓN: ¿Estoy listo?

**Checklist de competencia**:
- [ ] Puedo explicar qué es un "namespace" (sin pausas)
- [ ] Puedo crear un cliente test desde cero (sin ayuda de David)
- [ ] Puedo reportar un bug con logs útiles
- [ ] Sé dónde viven los datos de cada cliente
- [ ] Puedo testear si un cliente nuevo está listo para usar
- [ ] Sé cuándo avisar a David (vs cuando es problema del cliente)

**Si todos checkean ✓** → Estás listo para operar.

---

## TEMAS AVANZADOS (OPCIONAL, si tienes tiempo)

Estos NO son obligatorios, pero helpful:

### A. Entender Vault (guarda contraseñas)
- Qué es: Caja fuerte digital
- Por qué: API keys no deben estar en archivos texto
- Tu rol: Sé que existe, avisa si sospechas leak

### B. Entender ELK (logs centralizados)
- Qué es: Montaña de logs con buscador
- Por qué: Cuando algo falla, necesitamos ver qué pasó
- Tu rol: David configura, tú sabes dónde buscar (Kibana)

### C. Entender Falco (seguridad)
- Qué es: Sistema que detecta comportamiento raro
- Por qué: Si un container es hackeado, Falco lo ve
- Tu rol: David avisa si hay alerta, tú informas al cliente

---

## MATERIAL DE ESTUDIO

**YouTube videos** (en English):
- "Docker in 100 seconds" (Fireship) — 1.5 min, explicación visual
- "Kubernetes explained in 100 seconds" (Fireship) — 1.5 min
- "Kubernetes basics" (TechWorld with Nana) — 1h, pero podés skip a partes

**Documentación** (leer cuando tengas tiempo):
- Kubernetes docs: https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/
- Docker docs: https://docs.docker.com/get-started/overview/

**Práctico** (hands-on):
- Minikube: https://minikube.sigs.k8s.io/docs/start/
- Interactive tutorial: https://kubernetes.io/docs/tutorials/

---

## Si te atascas:

1. **Googlea el error exacto** (sin cambiar palabras)
2. **Pregunta a David** con:
   - QUÉ intentabas hacer
   - QUÉ error viste
   - COMANDO exacto que corriste
3. **No adivinines** — si no lo entiendes, pregunta

---

**Semana 1 start date**: Lunes 7 de abril
**Target completion**: Viernes 25 de abril
**Success metric**: Puedas levantar, testear, y destruir un cliente sin David.

Good luck!
