# BACKEND DEVELOPER PATH · David (Ing. Sistemas → n8n + SQL + RAG)
## Objetivo: Pasar de "entiendo la arquitectura" a "construyo workflows, queries, y tests"

**Duración total**: 7 semanas de aprendizaje estructurado (8-10h/semana)
**Meta**: Poder diseñar + implementar features complejas sin depender de nadie

---

## SEMANA 1: n8n Fundamentals (10h)

**Objetivo**: Entender n8n como orquestador de integraciones. Flows básicos funcionando.

### Conceptos clave
- **Node** = componente individual (HTTP, database, condition, etc.)
- **Connection** = credenciales para APIs (Telegram, Google Calendar, Supabase)
- **Workflow** = DAG de nodos conectados
- **Trigger** = qué inicia el workflow (webhook, schedule, manual)
- **Expression** = lógica dentro de nodos (JavaScript)

### Qué aprender (5h estudio)
1. **n8n official course** (YouTube: n8n channel)
   - "Getting started with n8n" (30 min)
   - "Nodes explained" (20 min)
   - "Workflows: how to build" (30 min)
   - "Integrations: Google, Telegram, HTTP" (1h)

2. **Leer documentación** (2h)
   - https://docs.n8n.io/ — architecture overview
   - HTTP node deep dive (cómo hacer requests)
   - Conditional logic (when/else)

### Qué hacer (5h hands-on)
1. **Setup n8n local** (1h)
   ```bash
   # Option 1: Docker
   docker run -it --rm -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n
   
   # Option 2: npm
   npm install -g n8n
   n8n start
   ```
   ✓ Acceder a http://localhost:5678

2. **Workflow 1: Hello World** (1h)
   - Trigger: Manual
   - Node 1: HTTP (GET https://api.github.com/users/github)
   - Node 2: Set (extraer `login`, `followers`)
   - Output: Visualizar JSON
   - Test: Corre el workflow, verifica output

3. **Workflow 2: Telegram + OpenClaw** (2h)
   - Trigger: Webhook (simular Telegram mensaje)
   - Node 1: Telegram Read Message (parse JSON de webhook)
   - Node 2: HTTP POST a OpenClaw endpoint
   - Node 3: Telegram Send Message (respuesta)
   - Test: Usar curl para simular webhook
   ```bash
   curl -X POST http://localhost:5678/webhook/telegram \
     -H "Content-Type: application/json" \
     -d '{"chat_id": 123, "text": "Hola"}'
   ```

4. **Workflow 3: Schedule + Database** (1h)
   - Trigger: Schedule (cada 1 minuto)
   - Node 1: Get current time
   - Node 2: Log to console
   - Output: Ver logs en n8n UI
   - **Próxima semana profundizamos SQL**

### Checklist Semana 1
- [ ] n8n instalado y corriendo en localhost:5678
- [ ] Viste 2h de videos (Fireship n8n beginner, oficial docs)
- [ ] Completaste 3 workflows (Hello, Telegram, Schedule)
- [ ] Entiendes: nodes, connections, triggers, expressions
- [ ] Documentaste: "Mi primer workflow en n8n" (screenshot + explicación)

---

## SEMANA 2: SQL + Supabase (10h)

**Objetivo**: Escribir queries + schema design. Bases de datos como data backbone.

### Conceptos clave
- **Schema** = estructura de tablas (CREATE TABLE)
- **Query** = SELECT, INSERT, UPDATE, DELETE
- **Join** = conectar datos de múltiples tablas
- **Index** = optimización para búsquedas rápidas
- **RLS** (Row Level Security) = solo ve datos que le corresponden (GDPR critical)

### Qué aprender (5h estudio)
1. **SQL basics** (2h)
   - "SQL in 100 seconds" (Fireship, 1.5 min)
   - https://sqlzoo.net/ — interactive SQL lessons (cap 1-5)
   - SELECT, WHERE, JOIN, GROUP BY

2. **Supabase specifics** (2h)
   - https://supabase.com/docs/guides/auth — authentication model
   - https://supabase.com/docs/guides/database/postgres — Postgres basics
   - RLS policies (critical para multi-tenant)

3. **n8n + SQL connection** (1h)
   - n8n PostgreSQL node (how to query)
   - Passing variables from workflows to SQL

### Qué hacer (5h hands-on)
1. **Setup Supabase** (30min)
   - Crear cuenta en supabase.com
   - New project (free tier)
   - Anotar connection string

2. **Schema design para Agencia** (1.5h)
   ```sql
   -- Tabla de clientes
   CREATE TABLE clients (
     id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
     name TEXT NOT NULL,
     email TEXT UNIQUE NOT NULL,
     telegram_chat_id TEXT UNIQUE,
     created_at TIMESTAMP DEFAULT now(),
     subscription_tier TEXT -- 'basic', 'standard', 'premium'
   );

   -- Tabla de conversaciones
   CREATE TABLE conversations (
     id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
     client_id UUID REFERENCES clients(id) ON DELETE CASCADE,
     message_text TEXT NOT NULL,
     from_platform TEXT -- 'telegram', 'email', etc.
     created_at TIMESTAMP DEFAULT now()
   );

   -- Tabla de RAG embeddings (para búsquedas de documentos)
   CREATE TABLE documents (
     id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
     client_id UUID REFERENCES clients(id) ON DELETE CASCADE,
     content TEXT NOT NULL,
     embedding VECTOR(1536), -- OpenAI embeddings
     created_at TIMESTAMP DEFAULT now()
   );

   -- Index para búsquedas rápidas
   CREATE INDEX idx_conversations_client_id ON conversations(client_id);
   CREATE INDEX idx_documents_client_id ON documents(client_id);
   ```
   ✓ Copia esto en Supabase SQL editor, ejecuta

3. **RLS Policies (multi-tenant isolation)** (1.5h)
   ```sql
   -- Cada cliente SOLO ve sus propios datos
   ALTER TABLE clients ENABLE ROW LEVEL SECURITY;
   ALTER TABLE conversations ENABLE ROW LEVEL SECURITY;

   -- Policy: cliente solo ve su data
   CREATE POLICY "clients can view own data" ON clients
     FOR SELECT USING (auth.uid()::text = client_id);

   CREATE POLICY "users can view own conversations" ON conversations
     FOR SELECT USING (
       client_id IN (
         SELECT id FROM clients WHERE auth.uid()::text = client_id
       )
     );
   ```
   ✓ Esto es GDPR magic — automático multi-tenant

4. **Queries prácticas** (1.5h)
   ```sql
   -- Contar mensajes por cliente
   SELECT client_id, COUNT(*) as message_count
   FROM conversations
   GROUP BY client_id;

   -- Últimos 10 mensajes de cliente X
   SELECT * FROM conversations
   WHERE client_id = '...' 
   ORDER BY created_at DESC
   LIMIT 10;

   -- Clientes sin conversaciones (inactive)
   SELECT * FROM clients c
   WHERE NOT EXISTS (
     SELECT 1 FROM conversations WHERE client_id = c.id
   );
   ```
   ✓ Copia en Supabase, experimenta

5. **n8n + Supabase workflow** (1h)
   - Crear workflow que:
     - Recibe mensaje de Telegram
     - Inserta en tabla `conversations`
     - Consulta últimos 5 mensajes del cliente
     - Responde en Telegram
   - Test: Envía mensaje simulado, verifica que se guarda

### Checklist Semana 2
- [ ] Supabase proyecto creado y conectado
- [ ] Schema de tablas (clients, conversations, documents) creado
- [ ] RLS policies configuradas (multi-tenant working)
- [ ] Escribiste 5+ queries SELECT/INSERT/UPDATE prácticas
- [ ] Creaste workflow n8n que inserta datos en Supabase
- [ ] Documentaste: "Mi schema de Supabase y por qué" (diagrama ERD)

---

## SEMANA 3: RAG + Vector Embeddings (12h)

**Objetivo**: Implementar busca de documentos con IA. El "conocimiento" del asistente.

### Conceptos clave
- **Embedding** = número que representa significado (vector 1536D para OpenAI)
- **Vector similarity** = qué tan parecidos son dos documentos
- **Chroma** = vector DB local (alternativa a Pinecone)
- **RAG** = Retrieval Augmented Generation (documentos + LLM = mejor respuestas)

### Qué aprender (5h estudio)
1. **Embeddings 101** (2h)
   - "Embeddings explained" (Fireship, si existe, sino: 3Blue1Brown math)
   - https://platform.openai.com/docs/guides/embeddings — OpenAI API
   - "Vector databases" intro (1h reading)

2. **Chroma setup** (2h)
   - https://docs.trychroma.com/ — full docs
   - Local vs cloud
   - How to query

3. **n8n + Chroma integration** (1h)
   - HTTP calls to Chroma API
   - Semantic search workflow

### Qué hacer (7h hands-on)
1. **Install Chroma local** (30min)
   ```bash
   pip install chromadb --break-system-packages
   python3
   
   import chromadb
   client = chromadb.Client()
   collection = client.create_collection(name="agencia-docs")
   ```
   ✓ Chroma server running on localhost:8000

2. **Create embeddings + store** (2h)
   ```python
   import chromadb
   from openai import OpenAI

   client_chroma = chromadb.Client()
   collection = client_chroma.get_collection(name="agencia-docs")

   # Sample documents (simulating FAQ, manual, etc.)
   docs = [
       "Nuestro asistente está disponible 24/7 via Telegram",
       "Los precios van desde $2.5M COP básico hasta $7M premium",
       "Soportamos Google Calendar, Brave Search, y más integraciones",
   ]

   # Store with auto-embeddings
   collection.add(
       ids=[f"doc_{i}" for i in range(len(docs))],
       documents=docs,
       metadatas=[{"type": "faq"} for _ in docs]
   )
   ```
   ✓ Documents stored in vector DB

3. **Query semantic search** (1.5h)
   ```python
   # Cliente pregunta: "¿Cuánto cuesta?"
   results = collection.query(
       query_texts=["cuál es el precio"],
       n_results=3
   )
   print(results['documents'])  # Retorna FAQ price doc
   ```
   ✓ Semantic search working

4. **n8n workflow: ask document** (2h)
   - Trigger: Telegram message
   - Node 1: HTTP POST to Chroma (semantic search)
   - Node 2: HTTP POST to OpenClaw (augmented prompt)
   - Node 3: Telegram response
   - **Ahora el asistente "sabe" sobre la empresa**

5. **Ingesta de documentos cliente** (1h)
   - Cliente sube PDF/Word de su manual
   - Workflow que:
     - Extrae texto (PDF parsing)
     - Genera embeddings
     - Guarda en Chroma
   - Testing: sube documento, pregunta sobre él

### Checklist Semana 3
- [ ] Chroma instalado y corriendo
- [ ] Creaste colección con 10+ documentos
- [ ] Semantic search queries funcionan
- [ ] Workflow n8n que busca en Chroma funciona
- [ ] Documentaste: "Cómo agregar documentos nuevos" (runbook)

---

## SEMANA 4: Automated Testing (12h)

**Objetivo**: Escribir tests para workflows, queries, RAG responses.

### Conceptos clave
- **Unit test** = test de 1 función/nodo
- **Integration test** = test de múltiples componentes
- **Mocking** = simular APIs sin llamarlas de verdad
- **Coverage** = % del código que tiene tests

### Qué aprender (3h estudio)
1. **pytest basics** (1.5h)
   - https://docs.pytest.org/en/latest/ — getting started
   - assertions, fixtures, parametrize

2. **Testing strategies for workflows** (1h)
   - How to test n8n workflows
   - Mocking HTTP calls

3. **GitHub Actions for CI/CD** (30min)
   - Running tests on every commit

### Qué hacer (9h hands-on)
1. **Setup pytest** (30min)
   ```bash
   pip install pytest pytest-asyncio --break-system-packages
   mkdir tests/
   touch tests/test_*.py
   ```

2. **Test Supabase queries** (2h)
   ```python
   # tests/test_queries.py
   import pytest
   from supabase import create_client

   @pytest.fixture
   def client():
       return create_client("https://...", "anon_key")

   def test_insert_conversation(client):
       """Test that we can insert a conversation"""
       response = client.table("conversations").insert({
           "client_id": "test-uuid",
           "message_text": "Hello",
           "from_platform": "telegram"
       }).execute()
       assert response.status_code == 201
       assert len(response.data) == 1

   def test_rls_isolation(client):
       """Test that RLS prevents cross-client data access"""
       # Client A inserts data
       client.table("conversations").insert({...}).execute()
       # Client B tries to read it (should fail)
       # ...
   ```

3. **Test n8n workflows** (2h)
   - Export workflow as JSON
   - Create test that simulates execution
   - Verify output matches expected

4. **Test RAG responses** (2h)
   ```python
   # tests/test_rag.py
   def test_semantic_search():
       """Verify that semantic search finds relevant docs"""
       results = chroma_collection.query(
           query_texts=["cuál es el precio"],
           n_results=3
       )
       assert len(results['documents']) > 0
       # Verify first result is about pricing
       assert "precio" in results['documents'][0][0].lower()

   def test_rag_augmentation():
       """Test that RAG improves responses"""
       # Query: "cuánto cuesta"
       # RAG finds: "Los precios van desde..."
       # LLM + RAG: generates coherent response
       # Assert response is not generic
   ```

5. **Setup CI/CD** (2.5h)
   ```yaml
   # .github/workflows/test.yml
   name: Tests
   on: [push, pull_request]
   jobs:
     test:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v2
         - uses: actions/setup-python@v2
         - run: pip install -r requirements.txt
         - run: pytest tests/ --cov=src
         - run: coverage report
   ```

### Checklist Semana 4
- [ ] pytest instalado y corriendo
- [ ] 10+ unit tests para queries (INSERT, SELECT, RLS)
- [ ] 5+ integration tests (workflow + DB)
- [ ] 3+ tests para RAG (semantic search, augmentation)
- [ ] CI/CD pipeline en GitHub Actions (tests run on push)
- [ ] Coverage >= 70% documentado

---

## SEMANA 5: API Design + n8n Advanced (10h)

**Objetivo**: Diseñar y documentar APIs que tu amigo/infra puede consumir.

### Conceptos clave
- **API contract** = qué inputs/outputs esperan
- **Status codes** = 200 OK, 400 bad request, 500 error
- **Versioning** = /v1/, /v2/ para cambios breaking
- **Rate limiting** = proteger de DDoS
- **OpenAPI spec** = documentación automática (Swagger)

### Qué aprender (3h estudio)
1. **REST API design** (1h)
   - https://restfulapi.net/ — principles
   - Status codes deep dive

2. **OpenAPI/Swagger** (1h)
   - https://swagger.io/ — documentation standard

3. **n8n advanced patterns** (1h)
   - Error handling (try/catch equivalent)
   - Retries and timeouts
   - Logging best practices

### Qué hacer (7h hands-on)
1. **Design API spec for OpenClaw** (2h)
   ```yaml
   # openapi.yaml
   openapi: 3.0.0
   info:
     title: OpenClaw API
     version: 1.0.0
   paths:
     /chat/send:
       post:
         requestBody:
           content:
             application/json:
               schema:
                 type: object
                 properties:
                   client_id:
                     type: string
                   message:
                     type: string
         responses:
           200:
             description: Message sent
             content:
               application/json:
                 schema:
                   type: object
                   properties:
                     response:
                       type: string
           400:
             description: Invalid input
   ```

2. **Implement error handling in n8n** (2h)
   - Workflow: Telegram → OpenClaw → Response
   - Add: Try/catch, retry logic, timeout
   - Log errors to Supabase (`error_logs` table)

3. **Create Swagger docs** (1.5h)
   - Auto-generate from spec
   - Deploy to `/docs` endpoint
   - Test using Swagger UI

4. **Rate limiting + monitoring** (1.5h)
   - n8n: add rate limiter node
   - Track API usage in Supabase
   - Alert if quota exceeded

### Checklist Semana 5
- [ ] OpenAPI spec escrito (10+ endpoints documented)
- [ ] Error handling en workflows (try/catch, logging)
- [ ] Swagger docs autogenerados y funcionando
- [ ] Rate limiting implementado
- [ ] API contract documentado (qué espera Amigo)

---

## SEMANA 6-7: Integration + Optimization (14h)

**Objetivo**: Poner todo junto. Performance tuning. Ready para producción.

### Tarea 6a: End-to-end integration (7h)
1. **Telegram → n8n → Supabase → RAG → OpenClaw → Response** (5h)
   - Full workflow testing
   - Load testing (1000 messages/day)
   - Latency optimization

2. **Performance profiling** (2h)
   - Identify bottlenecks (n8n slow? DB query slow? RAG slow?)
   - Optimize top 3 slowest operations

### Tarea 6b: Documentation (3.5h)
1. **Write API docs** (1h)
2. **Write deployment guide** (1h)
3. **Write troubleshooting guide** (1.5h)

### Tarea 7: Production readiness (3.5h)
1. **Security checklist** (1h)
   - No API keys in code ✓
   - HTTPS only ✓
   - Input validation ✓

2. **Monitoring setup** (1.5h)
   - Error rate tracking
   - Response time SLA
   - Alert thresholds

3. **Disaster recovery test** (1h)
   - Kill a service, verify recovery
   - Backup restore test

### Checklist Semana 6-7
- [ ] Full workflow tested end-to-end (Telegram → Response en <2s)
- [ ] Performance profiling completed
- [ ] API docs complete y claire
- [ ] All tests passing (pytest >= 70% coverage)
- [ ] Security checklist passed
- [ ] Monitoring alerts configured
- [ ] Documentaste: "Cómo desplegar esto a producción"

---

## REFERENCIA RÁPIDA: n8n + SQL + RAG Commands

```bash
# n8n
curl http://localhost:5678/api/workflows  # List workflows
curl http://localhost:5678/api/workflows/1/execute  # Run workflow

# Supabase
curl https://<project>.supabase.co/rest/v1/conversations \
  -H "Authorization: Bearer <token>"

# Chroma
curl http://localhost:8000/api/v1/collections/agencia-docs/query \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{"query_texts": ["cuál es el precio"]}'

# pytest
pytest tests/ -v  # Verbose
pytest tests/ --cov=src  # Coverage
pytest tests/test_rag.py::test_semantic_search  # Single test
```

---

## EVALUACIÓN: ¿Estoy listo?

**Checklist de competencia** (por semana):
- [ ] Sem 1: Puedo crear workflows n8n sin parar (nodes, triggers, expressions)
- [ ] Sem 2: Puedo escribir queries SQL + RLS policies sin ayuda
- [ ] Sem 3: Puedo construir RAG workflows que buscan documentos
- [ ] Sem 4: Puedo escribir tests que verifican comportamiento
- [ ] Sem 5: Puedo diseñar APIs que mi amigo (infra) pueda implementar
- [ ] Sem 6-7: Puedo deployar sistema completo sin bloqueadores

**Si todos checkean ✓** → Ready para desarrollo en paralelo con tu amigo

---

**Semana 1 start date**: Lunes 7 de abril
**Target completion**: Viernes 25 de abril
**Success metric**: Puedas construir un workflow completo (Telegram → n8n → Supabase → OpenClaw → Response) sin ayuda.

Good luck. Este es tu stack ahora.
