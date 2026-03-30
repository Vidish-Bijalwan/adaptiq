# AdaptIQ 🧠

> **Production-grade multi-modal document intelligence platform with adaptive AI query routing**

![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.111-009688?style=flat-square&logo=fastapi&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-14-000000?style=flat-square&logo=next.js&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-22c55e?style=flat-square)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat-square&logo=docker&logoColor=white)
![MLflow](https://img.shields.io/badge/MLflow-tracking-0194E2?style=flat-square&logo=mlflow&logoColor=white)
![Neo4j](https://img.shields.io/badge/Neo4j-graph-008CC1?style=flat-square&logo=neo4j&logoColor=white)
![Qdrant](https://img.shields.io/badge/Qdrant-vector--db-DC244C?style=flat-square)

---

## What is AdaptIQ?

Most RAG systems blindly embed → retrieve → generate. **AdaptIQ is different.**

Its core innovation is an **Adaptive Query Planner** that classifies every incoming query and dynamically routes it to the most cost-efficient, accurate strategy — instead of running the same pipeline every time.

| Query Type | Strategy Used | Why |
|---|---|---|
| Simple factual | Lightweight RAG | Fast, cheap, accurate enough |
| Multi-hop reasoning | LangGraph + Neo4j traversal | Needs relationship-aware reasoning |
| Domain-specific | LoRA fine-tuned model | Specialized knowledge required |
| Ambiguous / complex | Hybrid RAG + Reasoning | Best of both worlds |

Every routing decision is logged, monitored, and fed back into a continuous improvement loop via **MLflow**.

---

## Architecture

```
Documents (PDF / Image / Table / Text / Email)
                    │
         ┌──────────▼──────────┐
         │   Ingestion Layer   │  ← Kafka streaming + Celery async
         │  OCR · Layout · NER │  ← Qwen2.5-VL · PaddleOCR · spaCy
         └──────────┬──────────┘
                    │
         ┌──────────▼──────────┐
         │  Processing Layer   │  ← PII detection · Entity extraction
         │  BGE-M3 Embeddings  │  ← Chunking · Indexing
         └──────┬──────────┬───┘
                │          │
         ┌──────▼──┐  ┌────▼──────┐
         │  Qdrant │  │   Neo4j   │  ← Vector store + Knowledge graph
         │ vectors │  │   graph   │
         └──────┬──┘  └────┬──────┘
                │          │
         ┌──────▼──────────▼───┐
         │  Adaptive Query     │
         │  Planner (LangGraph)│  ← Classifies → Routes → Scores
         └──────────┬──────────┘
                    │
          ┌─────────┼──────────┐
     ┌────▼───┐ ┌───▼────┐ ┌──▼──────┐
     │  RAG   │ │LangGraph│ │  LoRA   │
     │Strategy│ │Reasoning│ │Fine-tune│
     └────────┘ └────────┘ └─────────┘
                    │
         ┌──────────▼──────────┐
         │   FastAPI Backend   │  ← REST + SSE streaming
         │   Next.js Frontend  │  ← 3-panel query UI
         └──────────┬──────────┘
                    │
         ┌──────────▼──────────┐
         │  MLOps Loop         │  ← MLflow · Feedback · Retraining
         │  Observability      │  ← Prometheus · Grafana · Jaeger
         └─────────────────────┘
```

---

## Tech Stack

### Backend
| Layer | Technology | Reason |
|---|---|---|
| Framework | FastAPI | Async-first, auto OpenAPI, type-safe |
| LLM | Llama 4 Scout (Ollama) | Open source, 10M context, self-hostable |
| Vision / OCR | Qwen2.5-VL | Multimodal doc parsing — images + tables |
| Embeddings | BGE-M3 (BAAI) | Outperforms OpenAI ada, multilingual |
| Vector DB | Qdrant | Production-grade, faster than FAISS at scale |
| Graph DB | Neo4j | Entity relationship reasoning across documents |
| Orchestration | LangGraph | Stateful multi-agent query routing |
| Task Queue | Celery + Redis | Async document processing jobs |
| Messaging | Apache Kafka | Streaming ingestion events |
| Metadata DB | MongoDB | Flexible schema for document metadata |
| Object Storage | MinIO (S3-compatible) | Local dev → swap to AWS S3 in prod |
| MLOps | MLflow | Experiment tracking, model registry, metrics |
| Monitoring | Prometheus + Grafana | System + LLM cost metrics |
| Tracing | OpenTelemetry + Jaeger | Distributed request tracing |
| Fine-tuning | HuggingFace PEFT + LoRA | Domain-specific model adaptation |

### Frontend
| Layer | Technology |
|---|---|
| Framework | Next.js 14 (App Router) |
| Language | TypeScript (strict mode) |
| Styling | Tailwind CSS + ShadCN UI |
| Animation | Framer Motion |
| State | Zustand |
| Data Fetching | TanStack Query |
| Streaming | Server-Sent Events (SSE) |

---

## Features

- 📄 **Multi-modal ingestion** — PDFs, images, scanned docs, tables, plain text, emails
- 🧠 **Adaptive query routing** — automatically selects RAG, reasoning, fine-tuned, or hybrid
- 🔍 **Semantic search** — BGE-M3 embeddings + Qdrant vector store
- 🕸️ **Knowledge graph** — Neo4j entity-relationship reasoning across documents
- 💬 **Streaming responses** — token-by-token SSE streaming in the UI
- 📊 **Confidence scoring** — every answer comes with a reliability score
- 🏷️ **Strategy badges** — UI shows which AI strategy was used per query
- 🔐 **Enterprise security** — JWT auth, RBAC, PII detection and redaction
- 📈 **MLOps loop** — feedback → evaluation → retraining pipeline via MLflow
- 🔭 **Full observability** — Prometheus metrics, Grafana dashboards, Jaeger traces
- ⚡ **Circuit breakers** — fault tolerance around all LLM and DB calls

---

## Quick Start

**Prerequisites:** Docker + Docker Compose

```bash
# 1. Clone the repo
git clone https://github.com/Vidish-Bijalwan/adaptiq.git
cd adaptiq

# 2. Copy environment variables
cp backend/.env.example backend/.env

# 3. Start the full stack
cd infra && docker compose up -d
```

| Service | URL |
|---|---|
| Frontend | http://localhost:3000 |
| Backend API | http://localhost:8000 |
| API Docs (Swagger) | http://localhost:8000/docs |
| Grafana | http://localhost:3001 |
| Jaeger Tracing | http://localhost:16686 |
| MLflow | http://localhost:5000 |
| Kafka UI | http://localhost:8080 |

---

## Project Structure

```
adaptiq/
├── backend/
│   ├── app/
│   │   ├── api/            # REST endpoints (documents, query, feedback, analytics)
│   │   ├── core/           # Security, logging, middleware, exceptions
│   │   ├── ingestion/      # Parsers, chunker, Kafka producer, pipeline
│   │   ├── processing/     # Embedder, entity extractor, PII detector, indexer
│   │   ├── query_engine/   # Adaptive planner, classifier, strategies
│   │   ├── db/             # MongoDB, Qdrant, Neo4j, Redis, MinIO clients
│   │   └── mlops/          # MLflow tracker, evaluator, feedback loop
│   └── tests/              # Unit + integration tests
├── frontend/
│   ├── src/
│   │   ├── app/            # Next.js App Router pages
│   │   ├── components/     # Ingestion, explorer, query, analytics components
│   │   ├── store/          # Zustand state stores
│   │   ├── hooks/          # Custom React hooks
│   │   └── types/          # TypeScript types
├── infra/
│   ├── docker-compose.yml  # Full local stack (15 services)
│   ├── prometheus/         # Scrape configs
│   ├── grafana/            # Pre-built dashboards
│   └── k8s/                # Kubernetes manifests
└── scripts/
    ├── generate_keys.py    # RS256 key pair generation
    ├── seed_db.py          # MongoDB indexes + Qdrant collection setup
    ├── finetune.py         # LoRA training on sample data
    └── load_sample_docs.py # Seeds sample documents for demo
```

---

## API Overview

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/v1/documents/upload` | Upload document (multipart) |
| `GET` | `/api/v1/documents` | List documents with filters |
| `GET` | `/api/v1/documents/{id}` | Document detail + processing status |
| `DELETE` | `/api/v1/documents/{id}` | Soft delete document |
| `POST` | `/api/v1/query` | Synchronous query |
| `POST` | `/api/v1/query/stream` | Streaming query via SSE |
| `POST` | `/api/v1/feedback` | Submit answer rating |
| `GET` | `/api/v1/analytics/queries` | Query volume over time |
| `GET` | `/api/v1/analytics/costs` | Token costs by strategy |

Full interactive docs at `/docs` when running locally.

---

## Adaptive Query Engine

The core innovation of AdaptIQ. The `QueryPlanner` follows this decision flow:

```
Incoming Query
      │
      ▼
 Classifier (LLM-based)
      │
      ├── SIMPLE_FACTUAL    → RAG Strategy
      │                        (BGE-M3 → Qdrant → Llama 4 Scout)
      │
      ├── MULTI_HOP         → Reasoning Strategy
      │                        (LangGraph + Neo4j graph traversal)
      │
      ├── DOMAIN_SPECIFIC   → Fine-tuned Strategy
      │                        (LoRA model → fallback to RAG)
      │
      └── AMBIGUOUS         → Hybrid Strategy
                               (RAG context + LangGraph reasoning)
                                      │
                               Confidence Scorer
                               Cost Tracker
                               MLflow Logger
                                      │
                               QueryResponse + Citations
```

---

## Environment Variables

See [`backend/.env.example`](backend/.env.example) for the full reference. Key variables:

```env
# LLM
OLLAMA_HOST=http://localhost:11434
LLM_MODEL=llama4-scout

# Databases
MONGODB_URI=mongodb://localhost:27017/adaptiq
QDRANT_HOST=localhost
NEO4J_URI=bolt://localhost:7687
REDIS_URL=redis://localhost:6379

# Storage
MINIO_ENDPOINT=localhost:9000
MINIO_BUCKET=adaptiq-documents

# Auth
JWT_ALGORITHM=RS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
```

---

## Roadmap

- [x] Core ingestion pipeline (PDF, image, table, text)
- [x] BGE-M3 embeddings + Qdrant indexing
- [x] Neo4j knowledge graph construction
- [x] Adaptive query planner (RAG + Reasoning + LoRA)
- [x] FastAPI backend with SSE streaming
- [x] Next.js frontend with 3-panel query UI
- [x] MLflow tracking + feedback loop
- [x] Full Docker Compose stack
- [ ] Kubernetes production deployment
- [ ] Fine-tuning pipeline with sample domain data
- [ ] Multi-tenant support
- [ ] Document versioning and change detection
- [ ] Voice query input (Whisper integration)

---

## Contributing

Contributions are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a PR.

```bash
# Run backend tests
cd backend && pytest tests/ -v --cov=app

# Run frontend type check
cd frontend && npx tsc --noEmit

# Run linting
cd backend && ruff check app/
```

---

## License

MIT © [Vidish Bijalwan](https://github.com/Vidish-Bijalwan)

---

<p align="center">
  Built by <a href="https://www.linkedin.com/in/vidish-bijalwan/">Vidish Bijalwan</a> · 
  <a href="https://github.com/Vidish-Bijalwan">GitHub</a> · 
  <a href="https://vidish-bijalwan.vercel.app/">Portfolio</a>
</p>
