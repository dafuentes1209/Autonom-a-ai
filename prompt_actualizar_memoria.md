# PROMPT PARA ACTUALIZAR MEMORIA · Agencia IA

**Uso**: Copiar/pegar esto al final de cada sesión importante, o cuando hay cambios significativos en el proyecto.

---

## VERSIÓN COMPLETA (cuando hay muchos cambios)

```
Please update my memory with the current state of the Agencia IA project:

**PROJECT STATUS (as of [DATE])**

**STRUCTURE & ROLES (CRITICAL)**
- David (Ruben, aka "daaavid" in WSL): DevOps/Tech Lead. 410 total hours across 18 weeks (Phases 1-5). ~23h/week focus on: Vault, Kubernetes, security, infrastructure, APIs, integrations. Current expertise: OpenClaw, Telegram bots, WSL/Linux, GOG CLI, REST APIs.
- Tu amigo (unnamed): Product/Biz Lead. 270 total hours across 18 weeks. ~15h/week focus on: clients, sales, documentation, testing, operations. No tech background but learning Docker/Kubernetes basics (amigo_learning_path.md, 7 weeks structured).

**SYNCHRONIZATION STRUCTURE**
- Lunes + viernes: 15-min standup (8am lunes, 4pm viernes) — what's done, blockers, next 3 days
- Miércoles: 1-hour deep dive (9am) — code reviews, testing, troubleshooting, planning
- Quarterly: 2-hour retrospective — what worked, what didn't, next quarter goals
- Total overhead: ~4 hours/week (viable for 2 people)

**SECURITY ROADMAP (INTEGRATED, NOT SEQUENTIAL)**
- Fase 1 (Sem 1-3): Vault local + GOG bridge + TLS self-signed + SECURITY.md = 60h
- Fase 2 (Sem 4-6): Kubernetes namespaces + NetworkPolicy + PodSecurityPolicy = 120h
- Fase 3 (Sem 7-9): ELK Stack + Falco runtime security = 100h
- Fase 4 (Sem 10-18): Vault producción + credential rotation + GDPR API + SOC2 audit = 250h
- Fase 5 (Oct 2026-Mar 2027): Multi-región US + CCPA compliance = 150h
Total: 680h + $15.5K, ZERO delay to client pilots/revenue (parallel execution)

**CURRENT STATE (this week)**
- Modelo: google/gemini-3.1-flash-lite-preview (cheapest per million tokens, replaced Groq due to token limits ~42k)
- OpenClaw: v2026.4.1 at /home/daaavid/.npm-global/bin/openclaw
- Config: ~/.openclaw/openclaw.json (references Vault, NOT hardcoded secrets)
- Integraciones: Telegram ✅, GOG CLI + Google Calendar ✅, Brave Search ✅
- INFRA: Laptop propio con Ubuntu as local server (will move to Hostinger KVM2 VPS for production)

**IMMEDIATE GOALS (Week of Apr 7-11)**
- David: Install/validate Vault (Tarea 1.1), GOG bridge (1.2), TLS (1.3), SECURITY.md docs (1.4)
- Tu amigo: Docker + Minikube install, Week 1 of amigo_learning_path.md (understand namespaces, create test clients)
- By Friday 11 Apr: Fase 1 validation pass (Vault running, TLS working, GOG tokenless, 1 test client in Minikube)

**DOCUMENTS CREATED THIS SESSION**
1. roadmap_seguridad_integrado_v1.md — 5-phase security plan (680h breakdown, gates before scaling)
2. amigo_learning_path.md — 7-week learning path (Docker → K8s → testing) for non-tech co-founder
3. standup_sync_template.md — Standup format, bug report template, metrics weekly, decision matrix
4. Security breach matrix visual — 15 identified risks prioritized by severity/likelihood
5. Team structure widget — Roles, tasks, timeline, expertise, weekly schedule

**PHILOSOPHY**
- 2 people, level bajo-intermedio: divide roles clearly (no context switching)
- Seguridad integrada en cada fase, no sequential (0 delay to revenue)
- Aislamiento multi-tenant es blocker before cliente #2 (critical for GDPR/compliance)
- David: specialist in tech (deep), Tu amigo: specialist in operations (broad)
- Bias toward documentation (if either is sick, other can continue)

**KEY FILES (in /mnt/user-data/outputs/ and project)**
- roadmap_seguridad_integrado_v1.md
- amigo_learning_path.md
- standup_sync_template.md
- modelo_financiero_v2.xlsx (financial model with pricing, cost structure, 30-client goal)
- roadmap-agencia-ia-v2.html (original 5-phase business roadmap)
```

---

## VERSIÓN CORTA (actualizaciones menores, cambios de estado)

```
Update memory with current Agencia IA state:

**THIS WEEK'S CHANGES**
- [Change A] — impact [why matters]
- [Change B] — impact [why matters]

**CURRENT BLOCKERS**
- [Blocker 1]
- [Blocker 2]

**COMPLETED TASKS**
- [Task] ✅

**NEXT PRIORITIES**
- [Priority 1]
- [Priority 2]

**NEW DOCUMENTS**
- [Document name] — [purpose]
```

---

## VERSIÓN PARA FIN DE SEMANA (viernes 4pm standup)

```
Weekly memory update - Agencia IA (Week of [DATE]):

**COMPLETED THIS WEEK**
David:
- [ ] [Task A] — [hours spent]
- [ ] [Task B] — [status: X% done, blocked by Y]

Tu amigo:
- [ ] [Task C] — [clients contacted, docs written]

**BLOCKERS**
- David: [tech blocker or dependency]
- Tu amigo: [business blocker]

**FASE 1 PROGRESS**
- Vault: [status]
- GOG: [status]
- TLS: [status]
- SECURITY.md: [status]
✅ If all pass → Advance to Fase 2
❌ If any fail → Debug, retry next week

**METRICS**
- Clientes activos: [N]
- Pods running: [N]/[N]
- Issues: [list or "none"]
- Ingresos: $[X]

**NEXT WEEK GOALS**
- David: [tasks]
- Tu amigo: [tasks]

**DECISIONS MADE**
- [Decision] → [chosen] because [reason]
```

---

## USAGE NOTES

**When to update:**
1. Every Friday after standup (weekly state)
2. After major milestone completion (phase gates)
3. If roles/structure changes
4. If new blockers arise
5. End of month (financial/metrics review)

**What NOT to include:**
- Sensitive API keys (even in memory, use Vault references only)
- Client names or private data
- Complaint/vent messages (stick to facts)
- Decisions not yet finalized

**Format tips:**
- Use checkboxes ✅❌ for clarity
- Always include "why" for decisions
- Link to documents, don't copy content
- Keep dates (so Claude knows timeline)

---

**Example weekly update:**

```
Update memory for Agencia IA (Week of Apr 7-11, 2026):

**COMPLETED**
David:
- Installed HashiCorp Vault in WSL ✅ (4h)
- GOG CLI bridge to Vault (working, no tokens on disk) ✅ (1h)
- TLS self-signed cert ✅ (30min)
- SECURITY.md policy drafted (90%, needs final review) ⏳

Tu amigo:
- Docker Desktop installed + hello-world test ✅
- Minikube running, kubectl get nodes shows Ready ✅
- Week 1 learning_path completed (Docker, Kubernetes concepts) ✅
- Created test client-001 namespace in Minikube ✅

**BLOCKERS**
- None critical; all on track

**FASE 1 VALIDATION (Friday 4pm)**
✅ Vault corriendo: vault status = "Sealed: false"
✅ Secrets guardados: vault kv list agencia = 5 items
✅ GOG sin tokens en disk: ~/.config/gogcli/keyring/ empty
✅ TLS funciona: curl -k https://localhost:8443/health = 200
✅ Test client: kubectl get pods -n client-001 = 1 Running

**METRICS**
- Clientes activos: 1 (test only)
- Pods OK: 1/1
- Issues: 0
- Ingresos: $0 (phase 1)

**NEXT WEEK**
David: Semana 2 tareas (RAG + n8n + Kubernetes aislamiento)
Tu amigo: Semana 2 learning (aislamiento multi-tenant, NetworkPolicy testing)

**DECISIONS**
- Proceeding to Fase 2 ✅ (all validations pass, team ready)
- David continues Vault in VPS next week (parallel to client work)
```

---

**Final note**: Actualizar memoria es como actualizar un logbook. Si David se enferma 2 semanas, tu amigo puede seguir. Si tu amigo está cerrado un cliente grande, David sabe el contexto. Es protección para ambos.
