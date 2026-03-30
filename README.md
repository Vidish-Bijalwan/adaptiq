![Python](https://img.shields.io/badge/Python-3.11-blue)
![FastAPI](https://img.shields.io/badge/FastAPI-0.111-green)
![Next.js](https://img.shields.io/badge/Next.js-14-black)
![License](https://img.shields.io/badge/license-MIT-blue)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED)
![MLflow](https://img.shields.io/badge/MLflow-tracking-orange)
![Neo4j](https://img.shields.io/badge/Neo4j-graph-008CC1)
![Qdrant](https://img.shields.io/badge/Qdrant-vector--db-DC244C)
```

---

## 🌿 Branch Strategy
```
main          → stable, always deployable
dev           → active development
feature/xxx   → individual features
fix/xxx       → bug fixes
```
Protect `main` with branch rules. FAANG engineers clone and check git log — a clean branch history signals professionalism.

---

## 📋 GitHub Issues Setup

Create these labels immediately:
```
bug           → #d73a4a
enhancement   → #a2eeef
documentation → #0075ca
mlops         → #e4e669
frontend      → #7057ff
backend       → #008672
infrastructure→ #f9d0c4
good first issue → #7057ff
```

Create these milestone issues to show active development:
```
v0.1.0 — Core ingestion pipeline working
v0.2.0 — Adaptive query engine complete  
v0.3.0 — Frontend dashboard live
v1.0.0 — Full stack with MLOps loop
```

---

## 📄 Files Every Serious Repo Needs
```
adaptiq/
├── README.md              ← Main entry point
├── ARCHITECTURE.md        ← Deep system design doc
├── CONTRIBUTING.md        ← How to contribute
├── CHANGELOG.md           ← Version history
├── LICENSE                ← MIT recommended
├── .gitignore             ← Python + Node + env files
├── .env.example           ← All vars documented
└── SECURITY.md            ← Responsible disclosure policy
