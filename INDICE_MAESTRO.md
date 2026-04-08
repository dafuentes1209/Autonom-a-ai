# ÍNDICE MAESTRO · Agencia IA
## Lo que hicimos hoy (sesión 2026-04-09)

---

## 📚 DOCUMENTOS CREADOS (copiar a tu proyecto)

### 1. **roadmap_seguridad_integrado_v1.md**
**Qué es**: Roadmap completo de seguridad (5 fases, 680h, $15.5K)
**Para quién**: Ambos (David referencia técnica, Tu amigo entiende bloqueadores)
**Cuándo leer**: 
- David: Antes de empezar Fase 1 (entiende qué va a hacer las próximas 18 semanas)
- Tu amigo: Semana 1 (entender qué es Vault, K8s, por qué importa)
**Estructura**:
- Fase 1 (Sem 1-3): Vault + GOG + TLS + SECURITY.md policy
- Fase 2 (Sem 4-6): Kubernetes aislamiento multi-tenant
- Fase 3 (Sem 7-9): Logging centralizado (ELK) + Falco
- Fase 4 (Sem 10-18): Vault producción + credential rotation + GDPR + SOC2
- Fase 5 (Oct 2026-Mar 2027): Multi-región US
**Key insight**: 680h + $15.5K pero PARALELO a negocio = cero retraso clientes

---

### 2. **amigo_learning_path.md**
**Qué es**: Camino de aprendizaje de 7 semanas para tu amigo (no-tech → operativo)
**Para quién**: Tu amigo SOLAMENTE (David no necesita leer)
**Cuándo leer**: Lunes de la semana 1 (leer toda la semana 1 antes de empezar)
**Estructura**:
- Semana 1: Docker + Kubernetes conceptos (2h) + instalación
- Semana 2: Minikube hands-on (3h) — crear/destruir clientes test
- Semana 3: Testing + troubleshooting (2h) — cómo reportar bugs útilmente
- Semanas 4+: Operaciones (day-to-day de cliente)
**Key insight**: No es para hacerte dev. Es para que entiendas qué está roto y sepas reportarlo.

---

### 3. **standup_sync_template.md**
**Qué es**: Formato de syncs + matriz de decisión + bug report template
**Para quién**: Ambos (esto es tu "manual de coordinación")
**Cuándo leer**: Lunes antes del standup (entienden estructura)
**Estructura**:
- Formato standup 15min: lunes 8am + viernes 4pm
- Deep dive 1h: miércoles 9am
- Formato de bug report (cómo reportar issues útilmente)
- Matriz de decisión (quién decide qué)
- Template semanal + quarterly
**Key insight**: Si siguen esto, gastan 4h/semana en meetings (no 10h+)

---

### 4. **prompt_actualizar_memoria.md**
**Qué es**: Prompts ready-to-copy para actualizar mi memoria cada viernes
**Para quién**: David (tú lo vas a copiar/pegar después del standup)
**Cuándo usar**:
- Viernes 4pm after standup (actualizar estado semanal)
- End of Fase completada (actualizar milestone)
- Cuando hay cambios significativos
**Estructura**:
- Versión completa (cambios grandes)
- Versión corta (actualización semanal)
- Versión fin de semana (metrics + blockers)
**Key insight**: 20 segundos de copy/paste = memoria actualizada para próxima sesión

---

### 5. **prompt_sesion_inicial.md**
**Qué es**: Prompts para empezar cada sesión con Claude con contexto completo
**Para quién**: David (tú lo vas a copiar al inicio de cada sesión)
**Cuándo usar**:
- Primera sesión nueva
- Cuando hay múltiples cambios
- Cuando alguien más necesita trabajar contigo (le pasas versión full)
**Estructura**:
- Versión full (contexto completo, 18 semanas, roles, financials)
- Versión corta (si memoria actualizada)
- Versión debugging (si hay un problema específico)
- Versión decisión (si hay que decidir algo)
**Key insight**: Copy/pegate, no reescribas. Los detalles importan.

---

## 📊 VISUALES CREADOS (en esta sesión, en canvas)

### 6. **Security Breach Matrix** (SVG interactive)
15 riesgos identificados, priorizados por severidad + likelihood
- Críticos: API keys exposed, multi-tenant data leak, Docker priv esc, OAuth tokens
- Costo mitigation: 680h + $15.5K
- Costo no mitigar: $1-3M demandas + cierre

### 7. **Team Structure & Roles** (HTML widget)
- David: DevOps Lead, 410h, 23h/week
- Tu amigo: Product Lead, 270h, 15h/week
- Syncs: lunes+viernes 15min, miércoles 1h
- Timeline semanal sugerido (quién qué cada día)

### 8. **Client Security Readiness Checklist** (Interactive widget)
- 15 checkboxes (Fase 1-4)
- Barra de progreso
- Decision: "Listo para cliente piloto?" (cuando 80%+)
- Export a TXT

---

## 📋 ARCHIVOS ORIGINALES (que ya tenías)

### 9. **roadmap-agencia-ia-v2.html**
Tu roadmap original (5 fases de negocio). Este sigue siendo válido, ahora solo tiene gates de seguridad.

### 10. **modelo_financiero_v2.xlsx**
Tu modelo financiero (precios, costos, P&L 12 meses, meta 30 clientes). Actualizado sin cambios respecto a seguridad (0 retraso).

---

## 🎯 CÓMO USARLOS (GUÍA RÁPIDA)

### DAVID (semana 1):
1. Lee: roadmap_seguridad_integrado_v1.md (Fase 1 completa, ~15 min)
2. Lee: prompt_sesion_inicial.md (entiende estructura de prompts)
3. Haz: Tarea 1.1-1.4 (Vault + GOG + TLS + docs)
4. Viernes 4pm: Copia prompt_actualizar_memoria.md y pégalo aquí
5. Next monday: Copia prompt_sesion_inicial.md (short version) para sesión siguiente

### TU AMIGO (semana 1):
1. Lee: amigo_learning_path.md semana 1 (2h)
2. Mira: Videos YouTube sugeridos (Docker, Kubernetes) (1h)
3. Haz: Docker install + Minikube install + test client
4. Lee: standup_sync_template.md (entender formato standup)
5. Viernes 4pm: Attend standup con David, prepare metrics

### AMBOS (ahora mismo):
1. Lean esto (índice maestro) — 5 min
2. Discutan: ¿Hacemos esto así o hay cambios? — 10 min
3. Calendaricer: Lunes 8am, miércoles 9am, viernes 4pm — 2 min
4. David: `vault status` en terminal (test que funciona) — 2 min
5. Tu amigo: `docker run hello-world` (test que funciona) — 2 min

---

## 🚀 PRIMER MILESTONE: VIERNES 11 ABRIL

**Si todo ✅:**
- Vault corriendo, GOG tokenless, TLS working
- Docker + Minikube instalados, test client en K8s
- SECURITY.md documentado
- Comprenden ambos por qué todo esto importa

**Resultado**: Avanzar a Fase 2 (RAG + n8n + Kubernetes real)

**Si algo ❌:**
- Debug viernes
- Retry lunes
- No desesperen (es normal año 1)

---

## 📞 CÓMO PEDIR AYUDA A CLAUDE

**Opción 1: Debugging específico**
- Copy `prompt_sesion_inicial.md` (debugging version)
- Paste en chat
- Describe error + context + logs

**Opción 2: Pregunta técnica**
- Copy `prompt_sesion_inicial.md` (short version)
- "Claude, he actualizado memoria. Mi pregunta es: ..."

**Opción 3: Decisión estratégica**
- Copy `prompt_sesion_inicial.md` (decision version)
- Describe opciones A/B
- "Qué recomiendas?"

**Opción 4: Fin de semana (actualizar memoria)**
- Copy `prompt_actualizar_memoria.md` (weekly version)
- Llena datos
- "Actualizar memoria con esto"

---

## 🔗 RELACIÓN ENTRE DOCUMENTOS

```
prompt_sesion_inicial.md
    ↓ (dá contexto a Claude cada sesión)
    
standup_sync_template.md (defines HOW to coordinate)
    ↓
prompt_actualizar_memoria.md (captures state cada Friday)

roadmap_seguridad_integrado_v1.md (WHAT David does)
    ↓
amigo_learning_path.md (HOW tu amigo aprende a entender)
```

---

## 📌 NÚMEROS IMPORTANTES (para recordar)

- **680h**: Total de trabajo de seguridad (David 410h + distributed 270h)
- **$15.5K**: Costo estimado (auditor SOC2)
- **4h/week**: Overhead de syncs (lunes+viernes+miércoles)
- **18 semanas**: Timeline completo (7 abril 2026 - 25 junio 2026 Fase 2, Sept soft launch, Dec Fase 4)
- **0 delay**: A clientes/ingresos (paralelo total)
- **50% → <5%**: Probabilidad breach (sin seguridad → con seguridad)

---

## ✅ CHECKLIST: "¿Leímos todo?"

- [ ] David leyó roadmap_seguridad_integrado_v1.md (Fase 1)
- [ ] Tu amigo leyó amigo_learning_path.md (Semana 1)
- [ ] Ambos leyeron standup_sync_template.md
- [ ] Ambos leyeron este índice maestro
- [ ] Calendaricer: lunes 8am, miércoles 9am, viernes 4pm
- [ ] David instaló Vault (test: `vault status`)
- [ ] Tu amigo instaló Docker (test: `docker run hello-world`)

Si todo ✅ → Ready para semana 1

---

## 🎓 FILOSOFÍA (LEE ESTO CUANDO DUDES)

> **Somos 2 personas con nivel bajo-intermedio construyendo algo real.**
> 
> No somos Google. No necesitamos architecture perfecta.
> 
> Lo que sí necesitamos:
> - Roles claros (David ≠ Tu amigo)
> - Documentación viva (si uno falta, el otro continúa)
> - Especialización (no todos hacen todo)
> - Seguridad de verdad (GDPR, backups, audits)
> - Velocity paralela (seguridad + negocio simultáneo)
> 
> Si algo no funciona, lo arreglamos. Si algo confunde, lo documentamos.
> 
> El éxito de esto es que **ambos entienden su rol y el del otro**.

---

**Last updated**: April 9, 2026 (después de sesión de 4h estructurando equipo + seguridad + coordinación)

**Next review**: Friday April 11, 2026 (después del first standup)

Good luck. You've got this.
