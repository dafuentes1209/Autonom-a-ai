# PROMPT PARA EMPEZAR SESIÓN · Agencia IA

**Uso**: Copiar/pegar esto al INICIO de cada sesión con Claude para darle contexto completo.

---

## VERSIÓN FULL (primera sesión, o cuando hay muchos cambios)

```
I'm Ruben (David), building an AI assistant agency with my co-founder. We have a specific structure and this is the current state. Please keep this context for our entire session.

**PROJECT BASICS**
- **What**: Building an agency of isolated AI assistants (Telegram + Google Calendar + Web Search) using OpenClaw, n8n, Kubernetes, deployed on Hostinger VPS with max security
- **When**: April 7, 2026 — March 2027 (18 weeks, 5 phases)
- **Who**: 2 people (me: Tech/DevOps, my friend: Biz/Sales/Ops, both level bajo-intermedio)
- **Where**: WSL Ubuntu-24.04 on Windows 11 (my laptop as local dev), will move to Hostinger KVM2 VPS for clients
- **Why**: $2.5M COP/person year 1 ($10K USD/person year 3), 30 clients goal by year 2

**MY ROLE (David/Tech Lead)**
- 410 hours across 18 weeks (~23h/week)
- Focus: Backend, infrastructure, security, APIs, integrations
- Current expertise: OpenClaw, Telegram bots, WSL/Linux, GOG CLI, REST APIs
- Must learn: HashiCorp Vault, Kubernetes, Docker, security best practices
- Responsible for: Vault setup, K8s aislamiento, ELK logging, Falco monitoring, credential rotation, GDPR compliance, SOC2 audit path
- NOT responsible for: Sales, client meetings, marketing (that's my co-founder)

**CO-FOUNDER'S ROLE (Tu amigo/Biz Lead)**
- 270 hours across 18 weeks (~15h/week)
- Focus: Clients, sales, documentation, testing, operations
- No tech background but learning Docker/Kubernetes basics (7-week structured learning path provided)
- Responsible for: Product testing, customer documentation, client onboarding, sales outreach, metrics tracking
- NOT responsible for: Code, infrastructure, security implementation (that's me)

**SYNCHRONIZATION (CRITICAL FOR 2-PERSON TEAM)**
- **Lunes 8am + Viernes 4pm**: 15-min standup (what's done, blockers, next 3 days)
- **Miércoles 9am**: 1-hour deep dive (code reviews, testing, troubleshooting, next week planning)
- **Quarterly**: 2-hour retrospective
- Total overhead: ~4 hours/week
- Documents: standup_sync_template.md (has templates, bug report format, decision matrix)

**SECURITY ROADMAP (INTEGRATED WITH BUSINESS, NOT SEQUENTIAL)**
- **Fase 1 (Sem 1-3)**: Vault local + GOG bridge + TLS + SECURITY.md policy → 60h
  - Blocker: Must pass before demoing to clients
- **Fase 2 (Sem 4-6)**: Kubernetes namespaces + NetworkPolicy (deny-all between clients) + PodSecurityPolicy → 120h
  - Blocker: Must pass before cliente #2 (critical for GDPR/data isolation)
- **Fase 3 (Sem 7-9)**: ELK Stack (logging per client) + Falco (runtime anomaly detection) → 100h
  - Blocker: Falco alerts working before 5+ clientes
- **Fase 4 (Sem 10-18)**: Vault producción + automatic credential rotation + GDPR data deletion API + SOC2 audit → 250h
  - Blocker: SOC2 audit initiated before >10 clientes
- **Fase 5 (Oct 2026-Mar 2027)**: Multi-región (Medellín + Miami) + CCPA/GDPR full compliance → 150h
- **Total**: 680 hours + $15.5K, ZERO delay to client pilots or revenue (all parallel to business)
- **Philosophy**: Security is not a phase, it's integrated in each phase. No "we'll fix it later."

**CURRENT TECHNICAL STATE**
- **Model**: google/gemini-3.1-flash-lite-preview (cheapest, replaced Groq due to ~42k token limit)
- **OpenClaw**: v2026.4.1 at /home/daaavid/.npm-global/bin/openclaw
- **Config**: ~/.openclaw/openclaw.json (references Vault secrets, NOT hardcoded)
- **Integrations**: 
  - Telegram ✅ (bot configured, my Telegram ID: 2059061143)
  - Google Calendar ✅ (GOG CLI, OAuth tokens in Vault)
  - Brave Search ✅ (API key in Vault)
- **Infrastructure**: My laptop (Ubuntu) running as local server → will scale to Hostinger VPS
- **Database**: Supabase planned (not yet)

**IMMEDIATE GOALS (This week: Apr 7-11)**
**David's tasks:**
- [ ] Tarea 1.1: Install/validate HashiCorp Vault (4-6h) — vault status must show "Sealed: false"
- [ ] Tarea 1.2: GOG CLI bridge to Vault (1-2h) — no tokens on disk, only in memory
- [ ] Tarea 1.3: TLS self-signed cert (30min-1h) — curl -k https://localhost:8443 must return 200
- [ ] Tarea 1.4: Write SECURITY.md policy (1h) — document credential management rules
- By Friday: Fase 1 validation must pass (all 4 tasks working)

**Tu amigo's tasks:**
- [ ] Install Docker Desktop + test hello-world (1h)
- [ ] Install Minikube + kubectl get nodes returns "Ready" (1.5h)
- [ ] Week 1 of amigo_learning_path.md: Docker concepts + Kubernetes basics (2h videos + reading)
- [ ] Create test client in Minikube: kubectl create namespace client-test (30min)
- By Friday: Understand namespaces, can create/delete test clients, pass Week 1 validation

**DOCUMENTS CREATED THIS SESSION (all in /mnt/user-data/outputs/)**
1. **roadmap_seguridad_integrado_v1.md** — Complete security roadmap (5 phases, task-by-task, gates, timeline)
2. **amigo_learning_path.md** — 7-week structured learning (Docker → K8s → testing) for non-tech founder
3. **standup_sync_template.md** — Standup format, bug report template, decision matrix, role clarity
4. Visuals: Security breach matrix (15 risks), team structure (roles/hours/timeline)

**FILES I'VE ALREADY PROVIDED**
- roadmap-agencia-ia-v2.html (original 5-phase business roadmap)
- modelo_financiero_v2.xlsx (financial model: pricing, costs, 30-client scale, 12-month P&L)

**FINANCIAL MODEL (KEY NUMBERS)**
- **Paquetes**: Básico $2.5M setup + $700K/mes | Estándar $4M + $1.2M/mes | Premium $7M + $2M/mes
- **US market**: $1,500 setup + $500/mes USD (4.7M latino businesses in US)
- **Year 1 goal**: 8 clientes, $2.5M COP/person (reached by month 6)
- **Year 2 goal**: 30 clientes (5 per VPS × 6 VPS), $500K/mes per cliente = $15M/mes recurrencia
- **Infrastructure cost**: 6 VPS at $6.99 USD each = $42/month = ~$176K COP/month (1.2% of revenue)

**KEY CONSTRAINTS**
1. **Nivel bajo-intermedio**: We're not experts. This is learning while building. That's OK.
2. **2 people**: No room for duplication. Roles must be clear (see decision matrix in standup template).
3. **Zero delay to revenue**: Security doesn't slow client acquisition (Phases 1-4 happen in parallel).
4. **No security shortcuts**: We respect GDPR, do encrypted backups, rotate credentials, audit everything.
5. **Cost-conscious**: Gemini 3.1 Flash Lite (cheapest), open-source tools (Vault, K8s, ELK, Falco), Hostinger cheap VPS.

**IF SOMETHING CHANGES MID-SESSION, TELL ME IMMEDIATELY**
- David gets sick or busy → tell me, we adjust timeline
- Co-founder wants pivot → tell me, we update roadmap
- Client asks for early feature → tell me, we assess priority vs Fase gates
- Budget changes → tell me, we cut non-critical tasks

**PHILOSOPHY (READ THIS)**
- We're building a real business with real security, not a startup hack.
- Isolation between clients is non-negotiable (GDPR = €20K+ fines per breach).
- Documentation is sacred (if either of us is unavailable, the other must continue).
- Specialization over generalization (David: deep tech, Tu amigo: broad ops).
- Velocity matters but not at the cost of losing customer data or compliance.

**NOW, HOW CAN I HELP YOU TODAY?**
[Ask your question, describe your blocker, request specific task breakdown, etc.]
```

---

## VERSIÓN CORTA (sesiones subsiguientes, si memoria está actualizada)

```
Context: Building AI assistant agency with my co-founder. 2 people (me = Tech/DevOps 410h, him = Biz/Sales 270h), 18-week timeline, 5 integrated security phases (680h total), zero delay to revenue. We're at [CURRENT WEEK], working on [CURRENT PHASE].

Current state:
- Tech: [what David is doing]
- Biz: [what co-founder is doing]
- Blockers: [if any]

Documents available: roadmap_seguridad_integrado_v1.md, amigo_learning_path.md, standup_sync_template.md, modelo_financiero_v2.xlsx, roadmap-agencia-ia-v2.html.

Question: [your question]
```

---

## VERSIÓN PARA DEBUGGING/TROUBLESHOOTING

```
Context: Agencia IA, 2-person team, currently on [PHASE/WEEK].

**PROBLEM**: [Describe clearly]
- **Error message**: [exact error or symptom]
- **What I did**: [exact steps, command if applicable]
- **Expected**: [what should have happened]
- **Actual**: [what actually happened]
- **When**: [timestamp if relevant]
- **Logs**: [relevant logs or output, full output not summary]

**WHAT I'VE TRIED**:
- [ ] [Attempt 1]
- [ ] [Attempt 2]

**CONTEXT**: 
- This is blocking [what]: [David's task / Tu amigo's task]
- Priority: [blocker / nice-to-have]
- Time spent: [X hours already]

**NEXT STEPS I EXPECT**:
1. [Expected help type]
2. [Expected help type]
```

---

## SPECIAL CASES

**If bringing up a client or financial discussion:**
```
Discussing client #[N] or scaling decision.

Current metrics:
- Active clients: [N]
- Revenue this month: $[X]
- Issues/churn: [status]

Question: [about pricing, scope, timeline, hiring, etc.]
```

**If changing roadmap or priorities:**
```
Need to adjust [Fase X / task Y] because:
- [Reason 1]
- [Reason 2]

Impact on timeline:
- [What shifts]
- [What stays same]

Question: Should we [option A] or [option B]?
```

**If learning something new (David leveling up):**
```
I'm learning [technology/concept] this week.

Context: 
- For [task/phase]
- Using [resource: course, docs, tutorial]
- Spending [X hours]

Question: [What I'm stuck on / What I'm not understanding]
```

---

## TEMPLATE TO COPY/PASTE (PICK ONE BASED ON YOUR SESSION TYPE)

**For normal work session:**
```
[Paste short context version]

This week focus: [what we're doing]
Status: [where we are]
Blocker: [if any]

Question: [what you need]
```

**For decision-making session:**
```
[Paste full context version]

We need to decide: [what decision]

Option A: [pros/cons]
Option B: [pros/cons]

My gut: [what you're leaning toward]

Help me think through: [specific aspect you're unsure about]
```

**For debugging urgent issue:**
```
[Paste debugging template]
```

**For end-of-week standup:**
```
[Paste full context version, but add:]

**WEEKLY SUMMARY (Week of [DATE])**
David completed: [tasks]
Tu amigo completed: [tasks]
Blockers: [if any]
Next week priorities: [tasks]

For memory update: [what should Claude remember for next week]
```

---

## FINAL TIPS

1. **Copy/paste entire template** — don't manually paraphrase, lose detail
2. **Update date/names/numbers** — make it current
3. **If it's been >1 week since last session**: Use full context version, not short
4. **If you've updated memory after last session**: Short version is fine (memory has details)
5. **If multiple people are reading this**: Share the full version (so everyone has same context)

---

Good luck. The prompts work because they're specific, detailed, and assume Claude needs to re-orient to your world each time.
