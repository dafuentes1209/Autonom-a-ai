# STANDUP & SYNC TEMPLATE · Agencia IA (2 personas)
## Rápido, claro, sin bullshit

---

## FORMATO STANDUP (Lunes + Viernes, 15-30 min)

**Hora**: Lunes 8:00 AM, Viernes 4:00 PM (elige TU timezone)
**Medium**: Llamada de 20 min (no chat asincrónico)
**Output**: Notas guardadas en Slack/Notion (para referencia)

### AGENDA FIJA (15 min max)

**David (Tech Lead)**: 5 min
```
COMPLETÉ (esta semana):
- [ ] Task A (horas gastadas: X)
- [ ] Task B (estado: 90%, bloqueado por X)

BLOCKERS (hoy/mañana):
- [ ] Necesito Y de ti para continuar
- [ ] Tengo pregunta sobre Z

PRIORIDAD PRÓXIMOS 3 DÍAS:
- [ ] Task C
```

**Tu amigo (Biz Lead)**: 5 min
```
COMPLETÉ (esta semana):
- [ ] Contacté 10 clientes potenciales
- [ ] Escribí documentación de landing
- [ ] Testeé cliente-001 (logs en /attachments)

BLOCKERS:
- [ ] Necesito que expliques X a cliente Y
- [ ] Documentación de cómo funciona Z

PRIORIDAD PRÓXIMOS 3 DÍAS:
- [ ] Calls con 5 clientes
```

**Ambos**: 5 min (Q&A + decisions)
```
¿Alguien tiene decisión que tomar?
- [ ] ¿Aceptamos cliente B (requerimientos raros)?
- [ ] ¿Bajamos precio del paquete básico?
- [ ] ¿Compartimos code en GitHub público?

Decisión: [opción elegida + por qué]
Documented: [enlace a documento]
```

---

## MIÉRCOLES DEEP DIVE (1h, high-context)

**Hora**: Miércoles 9:00 AM
**Tema**: Lo que se rompió, lo que se aprendió, lo que viene
**Dinámica**: Tu amigo testea mientras David explica código

### ESTRUCTURA (60 min)

**15 min: Recap de la semana**
```
David: "Implementé Vault + GOG bridge. Aquí está funcionando."
[David comparte screen]

Tu amigo: "Testeé, tomé notas:"
- [ ] Esto es claro
- [ ] Esto no entiendo
- [ ] Esto necesita documentación
```

**30 min: Troubleshooting + Learning**
```
David corre problema del cliente:
$ kubectl logs -n client-001 -l app=openclaw

Tu amigo observa + pregunta:
"¿Por qué está ese error?"
"¿Cómo detectamos si un cliente se rompió?"
"¿Cómo lo arreglamos sin hacerlo manual?"

David documenta solución en compartido.
```

**15 min: Planning próximos 7 días**
```
David: "Necesito 40 horas para Task X. Tú tienes que:"
- [ ] Mantenerse al tanto (20 min diarios de lectura)
- [ ] Testear cuando termine
- [ ] Reportar issues de manera útil

Tu amigo: "Yo voy a vender mientras tanto. Si me bloqueo, aviso."
```

---

## FORMATO REPORTE DE BUG (cuando algo se rompe)

**Por favor NUNCA reporte así**:
```
❌ "No funciona Telegram"
❌ "El cliente no responde"
❌ "Algo está mal"
```

**Así es correcto**:
```bash
✅ PROBLEMA: Cliente-001 no responde a mensajes de Telegram

✅ CUÁNDO: 2026-04-09 14:30 UTC

✅ REPRODUCCIÓN: 
   1. Abrí chat de cliente-001 en Telegram
   2. Envié "/start"
   3. Bot no respondió (sin timeout, sin error, nada)

✅ LOGS (runea esto):
   kubectl logs -n client-001 -l app=openclaw --tail=100
   [copias el output completo aquí, no resumido]

✅ ÚLTIMA VEZ QUE FUNCIONÓ: 2026-04-09 10:00 UTC

✅ ¿QUÉ CAMBIÓ DESDE ENTONCES?
   - David hizo deployment de security patch?
   - Cliente cambió sus credenciales?
   - Telegram API tuvo downtime?
```

**David me responde en <1h** con:
```
ROOT CAUSE: [explicación técnica]
FIX: [qué hizo o va a hacer]
TIEMPO PARA ARREGLAR: [X horas]
¿AFECTA OTROS CLIENTES? [Sí/No]
```

---

## MÉTRICAS SEMANALES (viernes 4pm, 10 min)

Tu amigo corre esto cada viernes:
```bash
# 1. Clientes activos
kubectl get namespaces | grep client | wc -l
# Output: "5"

# 2. Alguno sin pods corriendo?
kubectl get pods -A | grep -v "Running\|Completed"
# Output: [lista o "none"]

# 3. Algún pod con muchos restarts? (señal de problema)
kubectl get pods -A --sort-by=.status.containerStatuses[0].restartCount
# Output: [lista de pods con muchos restarts]

# 4. Espacio de disco total usado
kubectl top nodes
# Output: [CPU/memoria por nodo]
```

**Documento compartido** (Google Sheets):
```
Semana | Clientes | Pods OK | Issues | Ingresos | Notas
-------|----------|---------|--------|----------|--------
Sem 1  | 1 test   | 1       | 0      | $0       | Pilot OK
Sem 2  | 3        | 3       | 0      | $8M      | Client A asking questions
Sem 3  | 5        | 4       | 1      | $12M     | Client B DNS issue (resolved)
```

---

## MATRIZ DE DECISIÓN: ¿Quién decide QUÉ?

| Decisión | David | Tu amigo | Ambos |
|----------|-------|----------|-------|
| Qué tecnología usar | David decide | Propone reqs | Ambos acuerdan |
| Precio cliente | Tu amigo propone | David estima horas | Ambos acuerdan |
| Aceptar cliente raro | Tu amigo quiere | David dice si es viable | Ambos acuerdan |
| Cómo monitorear | David decide | Tu amigo usa | Mutual buy-in |
| Marketing messaging | Tu amigo lidera | David da spec técnicas | Ambos revisan |
| Cuándo pivotar | Ambos notan señales | Ambos deciden | Necesarios ambos |

---

## COMUNICACIÓN ENTRE SYNCS

**Slack/Telegram rules**:
- ✅ "Task X está 50%, esperando Y de ti" (5 min context)
- ✅ "Cliente reportó bug Z, logs están en attachment" (actionable)
- ✅ "¿Puedo hacer deploy ahora o espero?" (yes/no decision)
- ❌ "Che, esto me molesta" (no emoticons, sé específico)
- ❌ "¿Leíste lo que escribí hace 3 horas?" (tal vez estaba ocupado)
- ❌ "Necesitamos hablar sobre la estrategia" (OK, pero agendar sync)

**Timeout para responder:**
- ✅ Bug crítico (cliente down): 30 min
- ✅ Pregunta técnica: 2 horas
- ✅ Comentario no-urgent: next standup (OK no responder same-day)

---

## DOCUMENTACIÓN VIVA (actualizar cada viernes)

**Carpeta compartida** (Google Drive / Notion):
```
├── README.md (QUÉ hacemos, CÓMO empezar)
├── ROADMAP.md (fechas + milestones)
├── TECH-STACK.md (qué es cada cosa)
├── TESTING.md (cómo testear un cliente nuevo)
├── RUNBOOK.md (qué hacer si X se rompe)
├── CLIENTS.md (lista de clientes, issues conocidos)
├── METRICS.md (hoja de cálculo de semana)
└── DECISIONS.md (decisiones importantes + por qué)
```

**Tu amigo actualiza cada viernes**:
- CLIENTS.md (clientes nuevos, churn, etc.)
- METRICS.md (números de la semana)
- DECISIONS.md (qué decidimos + contexto)

**David actualiza cuando hay cambio técnico**:
- TECH-STACK.md (nueva herramienta)
- RUNBOOK.md (nuevo procedimiento)
- README.md (arquitectura actualizada)

---

## ESCALADA: Cuándo NO esperar al standup

**LLAMADA INMEDIATA** si:
```
1. Cliente está sin respuesta hace >30 min
2. Perdimos datos de un cliente
3. Un cliente amenaza con irse
4. Tu amigo cerró un cliente >$4M/año
5. David descubrió vulnerabilidad de seguridad
```

**Chat en <2h** si:
```
1. Algo está roto pero no es crítico
2. Necesitas decisión sobre cliente raro
3. Tienes pregunta sobre cómo se hace algo
```

**Puede esperar al standup** si:
```
1. Es documentación / learning
2. Es mejora de proceso
3. Es pregunta general
```

---

## TEMPLATE SEMANAL (copia/pega cada lunes)

```markdown
# SEMANA DE [FECHA]

## DAVID (Tech)
**Meta de la semana**: [1-2 tareas principales]

**Tareas**:
- [ ] Task A (40h estimadas)
- [ ] Task B (20h estimadas)

**Riscos**:
- [ ] Risk X (mitigation: Y)

**Blockers**:
- [ ] Necesito Z de tu amigo

---

## TU AMIGO (Biz)
**Meta de la semana**: [1-2 tareas principales]

**Tareas**:
- [ ] Contactar N clientes
- [ ] Documentación Y

**Riscos**:
- [ ] Risk X (mitigation: Y)

**Blockers**:
- [ ] Necesito explicación técnica de Z

---

## MÉTRICAS VIERNES
- Clientes activos: [N]
- Issues abiertos: [lista]
- Ingresos semana: $[X]

## DECISIONES
- [ ] [Decisión A] → elegimos [opción] porque [razón]
```

---

## QUARTERLY SYNC (cada 3 meses, 2h)

**Objetivo**: Retro + planning

**Agenda**:
1. **¿Qué salió bien?** (30 min)
   - David: Qué features escalaron bien
   - Tu amigo: Qué estrategia de ventas funcionó

2. **¿Qué no funcionó?** (30 min)
   - David: Qué arquitectura fue mala idea
   - Tu amigo: Qué clientes fueron problema

3. **¿Qué aprendemos?** (30 min)
   - Documentar lecciones
   - Cambiar procesos si necesario

4. **¿Qué viene?** (30 min)
   - Próximo quarter goals
   - Cambios de equipo / hiring / outsourcing

**Output**: Documento de retrospectiva guardado.

---

## SI VAMOS A CONTRATAR ALGUIEN MÁS

**Qué documentar ANTES de contratar**:
```
✅ Qué hace David (y qué puede delegar)
✅ Qué hace Tu amigo (y qué puede delegar)
✅ Procesos y checklists (TESTING.md, RUNBOOK.md, etc.)
✅ Decisiones pasadas y contexto (DECISIONS.md)
✅ Clientes y SLAs (CLIENTS.md)
✅ Arquitectura (TECH-STACK.md)
```

**Onboarding de nuevo hire**:
1. Leyó toda la documentación (2h)
2. Pair programming con David (8h)
3. Pair operations con tu amigo (4h)
4. Solo, con supervisión (16h)
5. Independent (after)

---

## RECAP: LOS 3 NÚMEROS MÁGICOS

**15 min**: Standup (lunes + viernes)
**1 hour**: Deep dive (miércoles)
**2 hours**: Quarterly retro (cada 3 meses)

**Total overhead**: ~4 horas/semana = viable para 2 personas

Todo lo demás es asincrónico (Slack, docs compartidos, etc.)

---

Buena suerte. El standup funciona porque es *corto*, *repetible*, y *sin sorpresas*.
