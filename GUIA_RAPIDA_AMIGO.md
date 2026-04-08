# TU ROL EN LA AGENCIA · Resumen 5 minutos
## Para: Tu amigo (co-founder, Product/Biz Lead)

---

## LO BÁSICO

**Eres**: Product + Biz + Operations Lead  
**No eres**: Developer (eso es David)  
**Horas/semana**: ~15h (2 días full-time)  
**Duración**: 18 semanas (abril 2026 - marzo 2027)  

---

## TUS RESPONSABILIDADES (LAS ÚNICAS QUE IMPORTAN)

### 1. Cerrar clientes
- Contactar potenciales (20 por semana, empezando semana 7)
- Demostración en vivo (David prepara, tú explicas)
- Contrato + pago
- **Meta**: Semana 8 primer cliente, semana 9 clientes #2-3

### 2. Testear el producto
- Crear clientes test en Kubernetes
- Decir "el cliente X no responde" de forma útil (con logs)
- Documentar cómo se usa
- **No necesitas entender cómo funciona**, solo reportar si está roto

### 3. Documentación + Onboarding
- Escribir guías para nuevos clientes
- Documentar qué hace el asistente
- Responder preguntas de clientes (técnicas → escalas a David)
- **Meta**: Cliente nuevo tiene respuestas en 24h

### 4. Métricas + Operaciones
- Viernes 4pm: Contar clientes activos, pods corriendo, issues abiertos
- Tracking ingresos semanales
- Alertar a David si algo cae (cliente sin respuesta >30min)

### 5. Aprendizaje
- Semanas 1-3: Aprender Docker + Kubernetes basics (7h total)
- **No es para ser dev**, es para entender qué está roto
- Documento: amigo_learning_path.md (7 semanas estructura)

---

## TU HORARIO (FLEXIBLE PERO CONSISTENTE)

| Día | Hora | Tarea |
|-----|------|-------|
| **Lun** | 8:00-8:15 AM | Standup con David (15 min) |
| | 8:30-12:30 | Estudiar/Documentación |
| | Resto | Contactar clientes, documentación |
| **Mar** | 9:00-13:00 | Negocio + documentación |
| **Mié** | 9:00-10:00 AM | Deep dive con David (1h) — él explica, tú aprendes |
| | 14:00-18:00 | Ventas/Outreach |
| **Jue** | 9:00-13:00 | Contactar clientes (outreach) |
| **Vie** | 8:00-8:15 AM | Standup con David |
| | 8:30-12:30 | Compilar métricas semanales |

---

## CÓMO REPORTAR UN PROBLEMA A DAVID

**NUNCA DIGAS**: "El cliente no funciona"  
**SIEMPRE DI**:
```
Cliente-001 no responde a Telegram desde 14:30.
Intenté: /start
Error: (sin respuesta, sin timeout)
Logs: [copias output de: kubectl logs -n client-001 -l app=openclaw]
Última vez OK: 10:00 AM
```

David responde en <1h con causa + arreglo.

---

## SINCS SEMANALES

**Lunes 8:00 AM (15 min)**
- David: "Hice X, me falta Y, necesito que hagas Z"
- Tú: "Contacté 10 clientes, cerré llamadas, necesito que expliques ABC"
- Decisiones rápidas (sí/no questions)

**Miércoles 9:00 AM (1h)**
- David enseña cómo funciona lo que hizo esta semana
- Tú testeas en vivo
- Preguntas que tengas

**Viernes 4:00 PM (15 min)**
- Tú reportas: clientes activos, ingresos, issues
- David reporta: qué completó, si hay blockers para próxima semana
- Decisión: ¿avanzamos o hay que arreglar algo?

---

## LOS 5 DOCUMENTOS QUE NECESITAS LEER

1. **amigo_learning_path.md** (OBLIGATORIO, semana 1)
   - 7 semanas estructura
   - Semana 1: 2 horas
   - Videos YouTube + labs Minikube

2. **standup_sync_template.md** (antes de primera reunión)
   - Cómo funciona el standup
   - Cómo reportar bugs
   - Matriz de decisión (quién decide qué)

3. **INDICE_MAESTRO.md** (5 min, ahora)
   - Guía rápida de todos los docs
   - Cómo pedir ayuda
   - Checklist "estamos listos?"

4. **roadmap_seguridad_integrado_v1.md** (opcional, para entender bloqueadores)
   - Por qué David hace Fase 1, 2, 3, 4, 5
   - Gates: antes de cliente #2 necesita K8s, etc.

5. **prompt_sesion_inicial.md** (solo si trabajas directo con Claude)
   - Cómo darle contexto cuando pides ayuda

---

## PUNTOS CRÍTICOS (MEMORIZA ESTO)

✅ **Tu trabajo es importante**: Sin clientes, sin ingresos, sin agencia.  
✅ **Tú NO eres dev**: Si no entiendes un error técnico, pregunta. Normal.  
❌ **NO digas "funciona/no funciona"**: Siempre reporta QUÉ, CUÁNDO, LOGS.  
❌ **NO cambies decisiones técnicas**: David decide arquitectura, tú propones reqs.  
✅ **Documenta TODO**: Si se enferma David, tú continúas.  
✅ **Habla con David 3x/semana**: Lunes, miércoles, viernes.  
❌ **NO trabajen en paralelo sin saber qué hace el otro**: Syncs son obligatorios.

---

## META SEMANA 1 (7-11 ABRIL)

- [ ] Leíste amigo_learning_path.md semana 1
- [ ] Viste videos Docker + Kubernetes (1.5h YouTube)
- [ ] Instalaste Docker Desktop + Minikube
- [ ] Creaste cliente test en Minikube
- [ ] Entiendes qué es un "namespace"
- [ ] Lunes 8am + Viernes 4pm: Attendiste syncs

Si todo ✅ → Fase 1 completa, avanzan a Fase 2.

---

## SI TE SIENTES PERDIDO

1. **Relee amigo_learning_path.md semana 1** (solo esa semana, 2h)
2. **Pregunta a David**: "¿Qué es X?" (en el miércoles deep dive)
3. **Googlea**: "Docker tutorial" si quieres entender más
4. **No entres en pánico**: Es normal no entender todo en semana 1

---

## NÚMEROS QUE IMPORTAN

- **$2.5M**: Meta tuya + David año 1 (COP/persona)
- **$10K USD**: Meta año 3 (USD/persona)
- **30 clientes**: Meta año 2
- **680h**: Horas de trabajo total (tu +David) para seguridad
- **$15.5K**: Costo auditoría SOC2 (año 1)
- **4h/semana**: Tiempo en meetings (si haces bien los syncs)
- **0 delay**: Seguridad NO retrasa clientes (paralelo total)

---

## UNA COSA IMPORTANTE

> La agencia funciona si tú entiendes qué está haciendo David,  
> y David entiende qué estás haciendo tú.  
> 
> Si alguien no entiende qué hace el otro,  
> la comunicación se rompe,  
> y todo cae.  
> 
> Los syncs existen para eso.

---

## ESTE VIERNES (ABRIL 11)

Después del standup, vas a responder estas 3 preguntas:

1. **¿Instalé Docker + Minikube sin errores?**  
   Sí/No (si No → debug el viernes)

2. **¿Entiendo qué es un namespace (cliente aislado)?**  
   Sí/No (si No → David explica en deep dive)

3. **¿Sé cómo reportar un bug (PROBLEMA + LOGS)?**  
   Sí/No (si No → David demuestra en deep dive)

Si todo Sí → Fase 1 completada ✅

---

**¿Preguntas?** Pregunta ya, no el viernes.

**¿Listo?** Lunes 8am, David te espera.

Good luck. You've got this.
