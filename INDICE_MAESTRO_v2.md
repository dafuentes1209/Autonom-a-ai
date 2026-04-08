# ÍNDICE MAESTRO V2 · Agencia IA
## Roles revisados: David (Backend/n8n) + Tu amigo (Infrastructure/K8s) · Abril 2026

---

## 📊 RESUMEN EJECUTIVO

**Cambio estructural**: Ambos ingenieros, especialización complementaria.
- **David (tú)**: n8n workflows, SQL, RAG, testing, APIs
- **Tu amigo**: Vault, Kubernetes, Linux, networking, monitoring

**Documentos**: 12 totales
- **7 al proyecto** (ambos acceso)
- **3 solo David** (tú usas)
- **2 solo Tu amigo** (él usa)

**Syncs**: Lunes 8am, miércoles 9am, viernes 4pm = 4h/week overhead

---

## 📁 ARCHIVOS ENTREGADOS · VERSIÓN v2

### 🟢 PARA EL PROYECTO (Ambos tienen acceso)

**1. roadmap_seguridad_integrado_v1_REVISED.md**
- Qué: Security roadmap con distribución David (testing) + Amigo (infra)
- Por qué: Plan claro de quién hace qué cada semana
- Cuándo leer: Lunes (entender misión semanal)
- Cómo usarlo: Referencia de tareas distribuidas + gates de validación
- **Destino**: `/proyecto/roadmap_seguridad_integrado_v1_REVISED.md`

**2. roadmap-agencia-ia-v2.html**
- Qué: Roadmap de negocio original (sin cambios)
- Por qué: Milestones de clientes/ingresos
- Cuándo leer: Monthly (tracking progress)
- **Destino**: `/proyecto/roadmap-agencia-ia-v2.html`

**3. modelo_financiero_v2.xlsx**
- Qué: Financial model (precios, costos, P&L 12 meses)
- Por qué: Números clave (viability check)
- Cuándo leer: End of month (financial review)
- **Destino**: `/proyecto/modelo_financiero_v2.xlsx`

**4. standup_sync_template.md** → **TECHNICAL_SYNC_TEMPLATE.md**
- Qué: Cómo se hacen los syncs (standup format, bug reporting, code review)
- Por qué: Reduce meetings, aumenta productivity
- Cuándo leer: Antes de primer standup
- Cómo usarlo: Copy la agenda cada lunes, rellena secciones
- **Destino**: `/proyecto/TECHNICAL_SYNC_TEMPLATE.md`

**5. ESTRUCTURA_EQUIPOS_REVISADA_v2.md**
- Qué: Distribución de roles + tabla de tareas por fase
- Por qué: Clarity sobre quién es responsable de qué
- Cuándo leer: Primera semana (entender estructura)
- **Destino**: `/proyecto/ESTRUCTURA_EQUIPOS_REVISADA_v2.md`

**6. INDICE_MAESTRO_v2.md** ← Este documento
- Qué: Guía de todos los archivos + cómo usarlos
- Por qué: No perderse en 12 archivos
- Cuándo leer: Ahora (orientación)
- **Destino**: `/proyecto/INDICE_MAESTRO_v2.md`

**7. (Opcional) DECISION_LOG.md**
- Qué: Registro de decisiones arquitectónicas (arquitectura decisions, por qué)
- Por qué: Future context (por qué hicimos esto)
- Cuándo leer: Cuando tengas duda "¿por qué Vault?"
- Cómo usarlo: Ambos agregan decisión semanal
- **Destino**: `/proyecto/DECISION_LOG.md` (crear en primer standup)

---

### 🔵 SOLO PARA TI, DAVID (Backend Lead)

**8. BACKEND_DEVELOPER_PATH.md** (Renombrado de `amigo_learning_path.md`)
- Qué: Tu learning path 7 semanas (n8n, SQL, RAG, testing)
- Por qué: Estructura clara de qué aprender cada semana
- Cuándo leer: Lunes 7 abril (semana 1)
- Cómo usarlo: Sigue exactamente, 10h/semana
- Semanas: SQL (2), RAG (3), Testing (4), APIs (5), Integration (6-7)
- **Destino**: SOLO TÚ lo tienes (PERO puedes compartir con tu amigo si necesita context)
- **Checkpoints**: Viernes cada semana (¿completaste los deliverables?)

**9. prompt_sesion_inicial.md**
- Qué: Template que copias/pegas al inicio de cada sesión con Claude
- Por qué: Contexto rápido (no repetir historia cada vez)
- Cuándo usarlo: Lunes nueva semana, cuando necesites debug, decisiones
- Cómo: Copy la versión "short" (ya tienes memoria de viernes)
- **Destino**: SOLO TÚ lo usas (pero la estructura es tuya para entender)

**10. prompt_actualizar_memoria.md**
- Qué: Template que copias viernes 4pm para actualizar mi memoria
- Por qué: Yo recuerdo estado semanal, no pierdo contexto
- Cuándo usarlo: Viernes después de standup
- Cómo: Copy la versión "weekly", llena datos, paste en chat
- **Destino**: SOLO TÚ lo usas

---

### 🟠 SOLO PARA TU AMIGO (Infrastructure Lead)

**11. AMIGO_INFRASTRUCTURE_PATH.md** (NUEVO)
- Qué: Su learning path 7 semanas (Linux, Docker, K8s, Vault, Networking, ELK, Integration)
- Por qué: Estructura clara paralela a la tuya
- Cuándo leer: Lunes 7 abril (semana 1)
- Cómo usarlo: Sigue exactamente, 12h/semana
- Semanas: Linux (1), Docker (2), K8s 101 (3), Vault (4), Networking (5), ELK (6), Integration (7)
- **Destino**: SOLO ÉL lo tiene (pero puedes revisar estructura)
- **Checkpoints**: Viernes cada semana (¿completó deliverables?)

**12. (Opcional) ROADMAP_SEGURIDAD_AMIGO.md** (NUEVO)
- Qué: Extract de roadmap_seguridad solo SUS tareas (Vault, K8s, etc.)
- Por qué: No ve tareas de testing de David
- **Destino**: SOLO ÉL lo tiene

---

## 🎯 DISTRIBUCIÓN CLARA

```
┌─────────────────────────────────────────────────────┐
│ PROYECTO (7 archivos - ambos acceso)                │
├─────────────────────────────────────────────────────┤
│ 1. roadmap_seguridad_integrado_v1_REVISED.md        │
│ 2. roadmap-agencia-ia-v2.html                       │
│ 3. modelo_financiero_v2.xlsx                        │
│ 4. TECHNICAL_SYNC_TEMPLATE.md                       │
│ 5. ESTRUCTURA_EQUIPOS_REVISADA_v2.md                │
│ 6. INDICE_MAESTRO_v2.md                             │
│ 7. DECISION_LOG.md (crear viernes)                  │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│ DAVID PERSONAL (3 archivos - solo tú)               │
├─────────────────────────────────────────────────────┤
│ 8. BACKEND_DEVELOPER_PATH.md                        │
│ 9. prompt_sesion_inicial.md                         │
│ 10. prompt_actualizar_memoria.md                    │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│ AMIGO PERSONAL (2 archivos - solo él)               │
├─────────────────────────────────────────────────────┤
│ 11. AMIGO_INFRASTRUCTURE_PATH.md                    │
│ 12. ROADMAP_SEGURIDAD_AMIGO.md (opcional)           │
└─────────────────────────────────────────────────────┘
```

---

## 📅 CÓMO USAR ESTO (WORKFLOW SEMANAL)

### **LUNES 8:00 AM (Standup + Week Planning)**

1. **David**:
   - Abre `BACKEND_DEVELOPER_PATH.md` → semana actual (ej: Semana 2: SQL + Supabase)
   - Revisa `roadmap_seguridad_integrado_v1_REVISED.md` → qué testea David esta semana
   - Copia template semanal de `TECHNICAL_SYNC_TEMPLATE.md`
   - **Standup**: "Semana pasada completé X, esta semana haré Y"

2. **Tu amigo**:
   - Abre `AMIGO_INFRASTRUCTURE_PATH.md` → semana actual
   - Revisa su parte de `roadmap_seguridad_integrado_v1_REVISED.md`
   - Copia template semanal
   - **Standup**: "Semana pasada completé X, esta semana haré Y"

3. **Ambos**:
   - Lee `TECHNICAL_SYNC_TEMPLATE.md` (matrix de decisiones)
   - Acuerdan: qué depende de qué esta semana
   - Asignan: qué hace David primero, qué hace Amigo primero

### **MIÉRCOLES 9:00 AM (Deep Dive Code Review)**

1. **David presenta** (15-20 min):
   - "Estos son mis n8n workflows, SQL queries, tests que escribí"
   - Muestra code, explica tradeoffs
   - Pide feedback: "¿Escala a 1000 clientes?" "¿Hay vuln de seguridad?"

2. **Tu amigo revisa** (10 min):
   - Pregunta sobre performance, security, deployment
   - Dice "esto está bien porque..." o "preocupación: ..."

3. **Tu amigo presenta** (15-20 min):
   - "Estos son mis K8s manifests, Vault policies, networking configs"
   - Muestra YAML, explica decisiones
   - Pide feedback: "¿Necesitas latencia menor?" "¿Cómo deployamos?"

4. **David revisa** (10 min):
   - Pregunta sobre reliability, scalability, observability
   - Dice "esto está bien porque..." o "preocupación: ..."

5. **Juntos** (15-20 min):
   - Deciden: "Esta semana David hace X, Amigo hace Y"
   - Aclaran: "Mi workflow necesita K8s deployment" o "Mi K8s setup necesita n8n container"

### **VIERNES 4:00 PM (Standup + Memory Update)**

1. **David**:
   - Checklist de semana (¿completé `BACKEND_DEVELOPER_PATH.md` objectives?)
   - Standup: "Esta semana completé X, Q problemas: Y"
   - Copia `prompt_actualizar_memoria.md` (weekly version)
   - Paste aquí con datos (qué completaste, qué bloqueadores, qué viene)

2. **Tu amigo**:
   - Checklist de semana (¿completé `AMIGO_INFRASTRUCTURE_PATH.md` objectives?)
   - Standup: "Esta semana completé X, Q problemas: Y"
   - Reporte de métricas (clientes, pods, uptime, etc.)

3. **Ambos**:
   - Agregan fila a spreadsheet (David h/23, Amigo h/23, issues, revenue)
   - Actualizar `DECISION_LOG.md` si hubo decisión grande

---

## 🔄 CÓMO PEDIR AYUDA A CLAUDE (Para ti, David)

### **Opción 1: Debugging técnico**
```
Copia prompt_sesion_inicial.md (debugging version)
Paste en chat
Describe error + what you tried + logs
"Claude, debugging blocker"
```

### **Opción 2: Pregunta arquitectónica**
```
Copia prompt_sesion_inicial.md (decision version)
Paste en chat
"David, I need to decide between A and B"
```

### **Opción 3: Fin de semana (memory update)**
```
Copia prompt_actualizar_memoria.md (weekly version)
Llena tus datos
"Update memory with this week's state"
```

---

## ✅ CHECKLIST: "¿Estamos listos?"

**Antes del lunes 7 april:**
- [ ] Ambos leen este índice (5 min)
- [ ] David entiende roles finales (backend/n8n)
- [ ] Tu amigo entiende roles finales (infra/K8s)
- [ ] Ambos comprenden: syncs 3x/semana, 4h/week overhead
- [ ] Ambos comprenden: learning path 7 semanas en paralelo

**Lunes 7 april**:
- [ ] David abre `BACKEND_DEVELOPER_PATH.md` semana 1
- [ ] Tu amigo abre `AMIGO_INFRASTRUCTURE_PATH.md` semana 1
- [ ] Ambos leen `TECHNICAL_SYNC_TEMPLATE.md`
- [ ] Primer standup: "OK, entendí qué hacer"

**Viernes 11 april**:
- [ ] David: Completó Semana 1 objectives (n8n hello-world, Supabase setup, etc.)
- [ ] Tu amigo: Completó Semana 1 objectives (Linux basics, Docker, etc.)
- [ ] David: Enviaste weekly memory update (prompt_actualizar_memoria.md)
- [ ] Ambos: Documentaron al menos 1 decisión en DECISION_LOG.md

Si todo ✅ → **Fase 1 validada**, avanzar a Semana 2

---

## 📞 SOPORTE RÁPIDO

**¿Dónde está X?**
- Mi learning path → `BACKEND_DEVELOPER_PATH.md`
- Learning path de Amigo → `AMIGO_INFRASTRUCTURE_PATH.md`
- Cómo hacemos syncs → `TECHNICAL_SYNC_TEMPLATE.md`
- Quién decide qué → matriz en `TECHNICAL_SYNC_TEMPLATE.md`
- Roles finales → `ESTRUCTURA_EQUIPOS_REVISADA_v2.md`
- Seguridad tareas → `roadmap_seguridad_integrado_v1_REVISED.md`

**¿Qué hago cada semana?**
1. Lunes: Leer tu path para la semana + standup
2. Miércoles: Code review (both sides)
3. Viernes: Standup + memory update + metrics

**¿Qué pasa si me atasco?**
1. Revisa la semana en tu learning path (hay soluciones)
2. Pregunta a tu amigo en Slack (max 2h timeout)
3. Sync emergencia si es blocker
4. Pide ayuda a Claude (usa los prompts de tu carpeta personal)

---

## 🎓 FILOSOFÍA FINAL

> **Somos 2 ingenieros con backgrounds diferentes.**
> 
> **Tú eres sistemas. Él es química pero sabe código.**
> 
> **Eso es una fortaleza, no una debilidad.**
> 
> **Lo que hace esto funcionar:**
> - Especialización clara (no conflicto)
> - Syncs 3x/semana (no desalineamiento)
> - Documentación viva (si uno falta, el otro continúa)
> - Learning paths en paralelo (no bloqueadores)
> - Code reviews mutuos (calidad)
> 
> **Lo que puede romper esto:**
> - Asumir que el otro entiende sin preguntar
> - No documentar decisiones
> - Syncs sueltos (15min se convierte en 2h)
> - No revisar código del otro
> 
> **Éxito mide así:**
> - Semana 1: Ambos entienden sus roles ✅
> - Semana 4: Ambos pueden trabajar en paralelo sin blockers ✅
> - Semana 18: Sistema funcionando en producción, ambos pueden operarlo ✅

---

## 📌 NÚMEROS CLAVE (Memorizar)

- **18 semanas**: Timeline total (7 abril - 25 junio)
- **4h/week**: Overhead en meetings (manageable)
- **680h**: Total trabajo seguridad (ambos)
- **7 archivos**: Al proyecto (acceso ambos)
- **3 archivos**: Solo David (personal)
- **2 archivos**: Solo Amigo (personal)
- **$15.5K**: Costo de seguridad (SOC2 audit)
- **0 delay**: A clientes (paralelo = no retraso)

---

**Última actualización**: 9 abril 2026  
**Next review**: Viernes 11 abril 2026 (después de Semana 1 completa)  
**Versión**: v2 (roles revisados, ambos técnicos)

Good luck. Ahora sí están listos.

---

**¿Preguntas sobre este índice?** Pregunta ahora, no el lunes.

