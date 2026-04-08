# TECHNICAL SYNC TEMPLATE · Agencia IA (2 ingenieros)
## Lunes 8am + Viércoles 9am + Viernes 4pm

---

## FORMATO STANDUP (Lunes 8am + Viernes 4pm · 15-20 min)

**Hora**: Lunes 8:00 AM, Viernes 4:00 PM (Colombia)  
**Medium**: Llamada de 20 min (no chat asincrónico)  
**Output**: Notas guardadas en Notion (referencia)

### AGENDA FIJA (15 min max)

**David (Backend/n8n Lead)**: 5 min
```
COMPLETÉ (esta semana):
- [ ] n8n workflow X (estado: %, bloqueadores)
- [ ] SQL query Y (estado: testing, merged)
- [ ] RAG feature Z (estado: in progress, esperando K8s deployment)

NECESITO:
- [ ] K8s deployment de mi container (para testear workflow)
- [ ] Supabase RLS policy review (¿está correcta?)
- [ ] Que deploys la nueva version de n8n image

BLOQUEADORES:
- [ ] Esperando K8s setup para testear end-to-end
- [ ] Pregunta: ¿cómo roto credenciales en n8n?

PRÓXIMOS 3 DÍAS:
- [ ] Terminar workflow Telegram → n8n → Supabase
- [ ] Test coverage del RAG
```

**Tu amigo (Infrastructure/K8s Lead)**: 5 min
```
COMPLETÉ (esta semana):
- [ ] Vault setup en VPS (estado: 90%, falta documentar)
- [ ] NetworkPolicy entre namespaces (estado: working, testeado)
- [ ] ELK logging (estado: deployed, David revisa dashboards)

NECESITO:
- [ ] Review de mi Dockerfile para OpenClaw
- [ ] Que me digas qué env vars necesita tu container
- [ ] Code review: ¿está bien mi K8s manifest?

BLOQUEADORES:
- [ ] Vault keys generation (necesito 5, tengo 3)
- [ ] Falco rules: ¿qué debería alertar?

PRÓXIMOS 3 DÍAS:
- [ ] Completar Vault setup en VPS
- [ ] Deploy ELK a staging
- [ ] Testear failover
```

**Ambos**: 5 min (decisiones rápidas)
```
DECISIONES:
- [ ] ¿Usamos OpenAI embeddings o local LLM para RAG? (costo vs latencia)
  → Decidimos: OpenAI (faster, mejor para MVP)
  → Documentado: ADR-001-embeddings.md

- [ ] ¿Escalamos a 2 VPS ya o esperamos 5 clientes?
  → Decidimos: Esperamos 5 (cost-effective)

- [ ] ¿Rotamos Telegram token este mes o esperar?
  → Decidimos: Este mes (security first)

ASIGNACIONES PRÓXIMA SEMANA:
- David: Finish RAG + 10 test cases
- Amigo: Multi-VPS setup + failover testing
```

---

## MIÉRCOLES DEEP DIVE (1-1.5h · 9:00 AM)

**Hora**: Miércoles 9:00 AM  
**Tema**: Code reviews, architecture decisions, debugging complex issues  
**Dinámica**: Ambos revisan trabajo del otro + planning próxima semana

### ESTRUCTURA (60-90 min)

**Bloque 1: David presenta (15-20 min)**
```
CÓDIGO QUE ESCRIBÍ:
- [ ] n8n workflow (show en pantalla)
- [ ] SQL schema change (explain tradeoffs)
- [ ] Test cases (coverage%, edge cases)

PREGUNTAS PARA AMIGO:
- "¿Escala esto a 1000 clientes?"
- "¿Hay vuln de seguridad en esta query?"
- "¿Cómo deployamos esto a producción?"

CÓDIGO/TESTS QUE NECESITO REVISAR:
```

**Tu amigo revisa + feedback (10 min)**
```
OBSERVACIONES:
- [ ] Está bien porque: X, Y, Z
- [ ] Preocupación: X (solución: Y)
- [ ] Mejora: considerar Z para performance

PREGUNTAS A DAVID:
- "¿Cómo testa esto en Minikube?"
- "¿Qué pasa si Supabase está down?"
```

**Bloque 2: Tu amigo presenta (15-20 min)**
```
INFRAESTRUCTURA QUE CONFIGURÉ:
- [ ] K8s manifest (show YAML)
- [ ] Vault policy (explain security model)
- [ ] Monitoring alerts (thresholds, escalation)

PREGUNTAS PARA DAVID:
- "¿Necesitas variables en el container?"
- "¿Cómo quieres los logs estructurados?"
- "¿Latencia máxima que tolerarás?"

CÓDIGO/CONFIG QUE NECESITO REVISAR:
```

**David revisa + feedback (10 min)**
```
OBSERVACIONES:
- [ ] Está bien porque: X, Y, Z
- [ ] Preocupación: X (solución: Y)
- [ ] Mejora: considerar Z para redundancia

PREGUNTAS A AMIGO:
- "¿Qué pasa si Vault se cae?"
- "¿Cómo escalamos a 2 VPS?"
```

**Bloque 3: Juntos (15-20 min)**
```
DECISIONES ARQUITECTÓNICAS:
- API contract: David especifica → Amigo valida implementabilidad
- Data flow: Supabase → Chroma → OpenClaw → n8n (review)
- Failure modes: X falla → qué pasa?

BLOCKING ISSUES:
- "Can't deploy because..."
- "Can't test because..."

PLANNING PRÓXIMA SEMANA:
- David: priorities
- Amigo: priorities
- Qué depende de qué (critical path)
```

### Herramientas de review
- VS Code (code review live)
- Postman (API testing)
- kubectl (K8s debugging)
- Supabase console (schema changes)
- n8n UI (workflow visualization)

---

## FORMATO REPORTE DE ISSUE (cuando algo se rompe)

**Por favor NUNCA reporte así**:
```
❌ "n8n no funciona"
❌ "Hay problema con la base de datos"
❌ "Kubernetes está roto"
```

**Así es correcto** (ejemplo David reportando a Amigo):
```
✅ PROBLEMA: n8n workflow no envía mensaje a Telegram

✅ SÍNTOMA: Workflow toma 30s (timeout en 10s)

✅ CUÁNDO: 2026-04-09 14:30 UTC (después de deploy de Vault)

✅ REPRODUCCIÓN PASO A PASO:
   1. Trigger: Manual en n8n UI
   2. Node 1: HTTP GET a Vault (obtiene token)
   3. Node 2: HTTP POST a Telegram API
   4. Expected: Mensaje enviado en <1s
   5. Actual: Timeout after 10s

✅ LOGS/ERRORS:
   - n8n logs: [copias completas aquí]
   - Vault logs: kubectl logs vault-0 -n vault
   - Verificaste: ¿Vault está up? (vault status = Sealed: false)

✅ CUÁNDO FUNCIONABA:
   Ayer funcionaba. Hoy falló después de deploy de Vault.

✅ CAMBIOS DESDE ENTONCES:
   - Amigo deployó Vault en K8s
   - David no cambió n8n config
   - Postgr

es está OK
   - Network policies: OK

✅ INTENTOS:
   - Restarteé container (no funcionó)
   - Checkeé logs de Vault (see above)
   - Verificé curl manual a Telegram (funciona)
```

**Amigo responde en <30 min** con:
```
ROOT CAUSE: Vault policy no tiene permiso para Telegram secret

FIX: Actualizar policy client-001:
  path "agencia/data/clients/client-001/*" {
    capabilities = ["read"]
  }

COMANDO:
  vault policy write client-001 -c policy.hcl

¿AFECTA OTROS CLIENTES? No (solo client-001)

TIEMPO PARA ARREGLAR: 5 minutos
```

---

## MÉTRICAS SEMANALES (viernes 4pm, 10 min)

**Documento compartido** (Google Sheets):
```
SEMANA | David (h) | Amigo (h) | Blockers | Status
-------|-----------|-----------|----------|--------
Sem 1  | 18/23h    | 20/23h    | Vault    | On track
Sem 2  | 19/23h    | 21/23h    | None     | On track
Sem 3  | 22/23h    | 19/23h    | K8s DNS  | Caught up
```

**Métricas técnicas**:
```
N8N WORKFLOWS:
- Total: 15 | Working: 14 | Failing: 1

SUPABASE:
- Tables: 5 | Rows: 10K+ | Query latency: <100ms

K8S:
- Pods: 12 | Ready: 12 | CPU avg: 40% | Memory: 60%

MONITORING:
- Alert rate: <5/day | False positives: 0% | Uptime: 99.8%
```

---

## MATRIZ DE DECISIÓN: ¿Quién decide QUÉ?

| Decisión | David | Amigo | Ambos |
|----------|-------|-------|-------|
| n8n workflow design | David decides | Amigo: "¿escala?" | Mutual buy-in |
| Database schema | David proposes | Amigo validates (perf) | Mutual review |
| K8s architecture | Amigo decides | David: "¿latency?" | Mutual buy-in |
| TLS/encryption approach | Amigo proposes | David: "¿seguro?" | Mutual review |
| API contract | David leads | Amigo validates (impl) | Mutual agreement |
| Deployment strategy | Amigo leads | David: "¿downtime?" | Mutual agreement |
| When to refactor | Ambos notan need | Ambos deciden timing | Necessary both |
| When to scale infra | Metrics show | Amigo proposes | David validates |

---

## COMUNICACIÓN ENTRE SYNCS

**Slack/Discord discipline**:
- ✅ "David, mi query en Supabase toma 5s. Hay 100k rows. ¿Indexing?" (actionable)
- ✅ "Amigo, estoy dockerizando n8n. Puerto 5678, env vars: X, Y, Z" (specific)
- ✅ "Bloqueado: esperando que deploys mi código para testear" (blocking status)
- ✅ "Code review ready: PR #123" (with link)
- ❌ "n8n está roto" (vague)
- ❌ "No entiendo K8s networking" (use deep dive, not Slack)
- ❌ "Necesitamos hablar de estrategia" (OK, pero agendar sync)

**Timeout para responder**:
- ✅ Issue crítico (cliente down): 30 min
- ✅ Pregunta técnica (blocker): 2 horas
- ✅ Code review: same day (dentro de 4h)
- ✅ Comentario no-urgent: next standup (OK no responder mismo día)

---

## DOCUMENTACIÓN VIVA (actualizar cada viernes)

**David mantiene**:
- `N8N_WORKFLOWS.md` — workflow specs, integrations, logic
- `API_SPEC.md` — endpoints, inputs/outputs, error handling
- `TESTING.md` — test suite, how to run tests, coverage goals
- `FEATURE_SPECS.md` — what features exist, how they work for customers
- `CHROMA_RAG.md` — vector database setup, prompt engineering notes

**Tu amigo mantiene**:
- `ARCHITECTURE.md` — Kubernetes design, networking, security policies
- `DEPLOYMENT.md` — VPS setup, Vault init, TLS certs, backup procedures
- `SECURITY.md` — Credential policy, incident response, compliance checklist
- `INFRA_RUNBOOK.md` — "If X breaks, do Y"
- `DATABASE.md` — Supabase schema (auto-generated + notes)

**Ambos mantienen**:
- `DECISION_LOG.md` — Why we chose X over Y, trade-offs
- `PERFORMANCE_BENCHMARKS.md` — Latency, throughput, resource usage
- `CHANGELOG.md` — What changed, when, impact on clients
- `TROUBLESHOOTING.md` — Common issues + fixes

---

## ESCALADA: Cuándo NO esperar al standup

**LLAMADA INMEDIATA** si:
```
1. Cliente está sin respuesta hace >30 min
2. Perdimos datos de un cliente
3. Un cliente amenaza con irse
4. David cerró un cliente >$4M/año
5. Amigo descubrió vulnerabilidad de seguridad (breach)
```

**Chat en <2h** si:
```
1. Algo está roto pero no es crítico
2. Necesitas decisión técnica rápida
3. Tienes pregunta que bloquea tu trabajo
```

**Puede esperar al standup** si:
```
1. Es documentación / learning
2. Es mejora de proceso (no blocker)
3. Es pregunta general (no urgente)
```

---

## TEMPLATE SEMANAL (copia/pega cada lunes)

```markdown
# SEMANA DE [FECHA]

## DAVID (Backend/n8n)
**Meta de la semana**: [1-2 features principales]

**Tareas**:
- [ ] n8n workflow X (estimado 20h)
- [ ] SQL feature Y (estimado 12h)
- [ ] RAG improvements (estimado 8h)

**Riscos**:
- [ ] Risk X (mitigación: Y)

**Blockers**:
- [ ] Necesito K8s deployment para testear

---

## TU AMIGO (Infrastructure/K8s)
**Meta de la semana**: [1-2 components principales]

**Tareas**:
- [ ] Vault multi-VPS setup (estimado 25h)
- [ ] ELK Stack optimization (estimado 15h)
- [ ] Network monitoring (estimado 10h)

**Riscos**:
- [ ] Risk X (mitigación: Y)

**Blockers**:
- [ ] Necesito API spec de David para networking

---

## MÉTRICAS VIERNES
- Horas completadas: David [X/23], Amigo [Y/23]
- Issues abiertos: [lista]
- Ingresos semana: $[X]
- Clientes activos: [N]

## DECISIONES
- [ ] [Decisión A] → elegimos [opción] porque [razón]
```

---

## QUARTERLY SYNC (cada 3 meses, 2h)

**Objetivo**: Retro + planning

**Agenda**:
1. **¿Qué salió bien?** (30 min)
   - David: Qué features escalaron bien, qué test coverage helped
   - Amigo: Qué infraestructura fue decisión acertada, qué saved time

2. **¿Qué no funcionó?** (30 min)
   - David: Qué workflows fueron bad idea, qué fue hard to test
   - Amigo: Qué K8s setup fue overkill, qué causó downtime

3. **¿Qué aprendemos?** (30 min)
   - Documentar lecciones
   - Cambiar procesos si necesario

4. **¿Qué viene?** (30 min)
   - Próximo quarter goals
   - Cambios de roles / hiring / outsourcing

**Output**: Documento de retrospectiva guardado en DECISION_LOG.md

---

## SI VAMOS A CONTRATAR ALGUIEN MÁS

**Qué documentar ANTES**:
```
✅ DAVID'S DOMAIN:
   - n8n workflow patterns (library de ejemplos)
   - SQL queries (common patterns, optimization)
   - Testing framework (how we test)
   - API contracts (what David needs from Amigo)

✅ AMIGO'S DOMAIN:
   - K8s setup (cluster creation, maintenance)
   - Vault operations (secret management)
   - Networking (service mesh, ingress)
   - Monitoring (ELK, Prometheus, alerts)

✅ SHARED:
   - Architecture decisions (why this way)
   - Communication patterns (syncs, reviews)
   - Deployment procedures (how to release)
```

**Onboarding de nuevo hire**:
1. Leyó toda la documentación (4h)
2. Pair programming: 1 week with David (40h)
3. Pair operations: 1 week with Amigo (40h)
4. Independent with supervision (40h)
5. Independent (after)

---

## RECAP: LOS 3 NÚMEROS MÁGICOS

**15 min**: Standup (lunes + viernes)  
**1 hour**: Deep dive (miércoles)  
**2 hours**: Quarterly retro (cada 3 meses)

**Total overhead**: ~4 horas/semana = viable para 2 ingenieros

Todo lo demás es asincrónico (Slack, docs, GitHub PRs)

---

Buena suerte. El sync funciona porque es *corto*, *técnico*, y *sin bullshit*.
