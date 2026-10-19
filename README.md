# 🛡️ Sentriq — Autonomous AI-Powered Security Operations Center (AI-SOC)

> **Academic Major Project** | Final Year | AI · Data Security · Data Engineering
> Aligned with **NIST SP 800-61** and the **MITRE ATT&CK Framework**

---

## 📖 Overview

**Sentriq** is an intelligent, end-to-end Security Operations Center (SOC) platform that transforms raw network telemetry into actionable security incidents — without requiring analysts to manually review every alert.

The platform ingests high-velocity security event data, applies machine-learning-based threat detection, correlates related events into meaningful incidents, and presents everything through a real-time professional SOC dashboard.

---

## ✨ Key Features

| Feature | Description |
|---|---|
| 🔴 **Real-Time Telemetry** | WebSocket-powered live event stream with animated SOC dashboard |
| 🤖 **ML Threat Detection** | Random Forest classifier — **96.67% accuracy**, **0.9669 F1-score**, trained on UNSW-NB15 benchmark data |
| 🏷️ **Attack Classification** | 6-class attack categorizer (Brute Force, DoS, Exploits, Fuzzing, Reconnaissance, Generic) |
| 🔍 **Explainable AI (XAI)** | SHAP TreeExplainer for transparent, human-readable threat attribution |
| 🔗 **Incident Correlation** | 15-minute sliding window groups related alerts into unified incidents |
| 📊 **4-Factor Risk Scoring** | Transparent risk prioritization based on measurable factors |
| 🤝 **Multi-Agent Triage** | AI agents for IOC extraction and blast-radius estimation |
| ✅ **Human-in-the-Loop (HITL)** | Analyst-approved response actions (e.g., firewall DROP rules) |
| 📄 **Executive Dossier** | One-click incident report and investigation export |

---

## 🏗️ Architecture

```
+-------------------------------------------------------------+
|                     Sentriq AI-SOC                          |
+--------------------+----------------------------------------+
|   Frontend (React) |         Backend (FastAPI)              |
|   TypeScript + Vite|  +----------------------------------+  |
|   Tailwind CSS     |  |  API Layer (REST + WebSocket)    |  |
|   Real-time WS     |  +----------------------------------+  |
|                    |  |  ML Engine (Random Forest/SHAP)  |  |
|   :5173            |  +----------------------------------+  |
|                    |  |  Incident Correlation Engine      |  |
|                    |  +----------------------------------+  |
|                    |  |  AI Agents (Triage & Response)   |  |
|                    |  +----------------------------------+  |
|                    |           :8000                        |
+--------------------+----------------------------------------+
|              PostgreSQL 16 (sentriq_soc DB)   :5432         |
+-------------------------------------------------------------+
```

---

## 🛠️ Tech Stack

### Backend
- **FastAPI** — Async REST API + WebSocket server
- **SQLAlchemy 2.0** + **asyncpg** — Async ORM with PostgreSQL
- **scikit-learn** — Random Forest threat classifier
- **SHAP** — Explainable AI feature attribution (TreeExplainer)
- **Pandas / NumPy** — Feature engineering pipeline
- **Uvicorn** — ASGI server

### Frontend
- **React + TypeScript** — Component-based UI
- **Vite** — Fast dev server and bundler
- **Tailwind CSS** — Utility-first styling
- **WebSockets** — Real-time event streaming

### Infrastructure
- **PostgreSQL 16** — Primary data store
- **Docker + Docker Compose** — Containerized deployment
- **Nginx** — Frontend reverse proxy
- **pgAdmin 4** — Optional DB management UI

---

## 🚀 Getting Started

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- [Node.js 18+](https://nodejs.org/) (for local frontend development)
- [Python 3.11+](https://www.python.org/) (for local backend development)

---

### Option A — Docker Compose (Recommended)

```bash
# 1. Clone the repository
git clone https://github.com/ThanuSuthapalli/Sentriq---AISOC.git
cd Sentriq---AISOC

# 2. Copy and configure environment variables
cp .env.example .env

# 3. Start all services
docker compose up --build

# 4. Open the SOC Dashboard
#    Frontend:    http://localhost:5173
#    Backend API: http://localhost:8000/docs
#    pgAdmin:     http://localhost:5050  (use --profile tools)
```

To start with pgAdmin:
```bash
docker compose --profile tools up --build
```

---

### Option B — Local Development

#### Backend

```bash
cd backend

# Create virtual environment
python -m venv .venv
.venv\Scripts\activate        # Windows
# source .venv/bin/activate   # macOS/Linux

# Install dependencies
pip install -r requirements.txt

# Configure environment
cp ../.env.example ../.env
# Edit .env with your local PostgreSQL credentials

# Start the backend
uvicorn app.main:app --reload --port 8000
```

#### Frontend

```bash
cd frontend

# Install dependencies
npm install

# Start dev server
npm run dev
# Open http://localhost:5173
```

> Or use the provided PowerShell scripts:
> ```powershell
> .\start-dev.ps1   # Development mode
> .\start.ps1       # Production mode
> ```

---

## ⚙️ Environment Variables

Copy `.env.example` to `.env` and update the values:

```env
# Database
POSTGRES_DB=sentriq_soc
POSTGRES_USER=sentriq_admin
POSTGRES_PASSWORD=sentriq_secure_pass
POSTGRES_HOST=localhost
POSTGRES_PORT=5432

DATABASE_URL=postgresql+asyncpg://sentriq_admin:sentriq_secure_pass@localhost:5432/sentriq_soc
SYNC_DATABASE_URL=postgresql+psycopg2://sentriq_admin:sentriq_secure_pass@localhost:5432/sentriq_soc

# Application
PROJECT_NAME=Sentriq
APP_ENV=development
API_V1_PREFIX=/api/v1
CORS_ORIGINS=["http://localhost:5173"]

# ML & Models
MODELS_DIR=../models_store
DATA_DIR=../data
```

---

## 📡 API Reference

The backend exposes a full REST API documented at:

```
http://localhost:8000/docs        Swagger UI
http://localhost:8000/redoc       ReDoc
```

### Key Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/v1/health` | System health and DB status |
| `GET` | `/api/v1/events` | List security events |
| `POST` | `/api/v1/events/simulate` | Inject simulated attack scenario |
| `POST` | `/api/v1/ml/analyze-event/{id}` | Run ML threat inference |
| `GET` | `/api/v1/ml/shap/{id}` | Get SHAP feature attribution |
| `GET` | `/api/v1/incidents` | List correlated incidents |
| `GET` | `/api/v1/incidents/{id}` | Incident detail and timeline |
| `WS` | `/api/v1/ws/telemetry` | Real-time event stream |

---

## 🎬 Running the Demo

To run the full end-to-end faculty demonstration (requires backend running):

```bash
cd backend
python scripts/demo_orchestrator.py
```

This orchestrates a 9-step live attack lifecycle:
1. ✅ System health check
2. 📡 Inject benign baseline traffic
3. 💥 Simulate Brute Force attack
4. 🤖 ML threat classification
5. 🔍 SHAP explainability report
6. 🔗 Incident correlation
7. 📊 Risk score calculation
8. 🤝 Multi-agent triage
9. 📄 Executive dossier generation

---

## 🧪 Running Tests

```bash
cd backend

# Run all tests
pytest

# Run with verbose output
pytest -v

# Run a specific test file
pytest tests/test_api.py
```

---

## 📁 Project Structure

```
Sentriq---AISOC/
├── backend/
│   ├── app/
│   │   ├── agents/          # AI triage agents
│   │   ├── api/             # FastAPI route handlers
│   │   ├── core/            # Config and DB setup
│   │   ├── ml/              # ML models and SHAP inference
│   │   ├── models/          # SQLAlchemy ORM models
│   │   ├── schemas/         # Pydantic request/response schemas
│   │   ├── services/        # Business logic layer
│   │   └── main.py          # App entrypoint
│   ├── scripts/
│   │   └── demo_orchestrator.py   # E2E demo script
│   ├── tests/               # Pytest test suite
│   ├── Dockerfile
│   └── requirements.txt
├── frontend/
│   ├── src/                 # React TypeScript source
│   ├── Dockerfile
│   └── package.json
├── data/                    # Training datasets (UNSW-NB15)
├── models_store/            # Trained ML model artifacts
├── docs/                    # Architecture and evaluation docs
├── docker-compose.yml
├── .env.example
├── start.ps1                # Production launcher
├── start-dev.ps1            # Dev launcher
└── PRD.md                   # Full Product Requirements Document
```

---

## 📊 ML Model Performance

Trained on the **UNSW-NB15** benchmark network intrusion dataset:

| Metric | Value |
|--------|-------|
| Accuracy | **96.67%** |
| F1-Score | **0.9669** |
| False Positive Rate | **0.0%** |
| Inference Latency | **< 12ms** |

---

## 📚 References

- [NIST SP 800-61](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final) — Computer Security Incident Handling Guide
- [MITRE ATT&CK Framework](https://attack.mitre.org/) — Adversarial Tactics and Techniques
- [UNSW-NB15 Dataset](https://research.unsw.edu.au/projects/unsw-nb15-dataset) — Network Intrusion Benchmark
- [SHAP (Lundberg & Lee, 2017)](https://arxiv.org/abs/1705.07874) — Explainable AI Framework

---

## 👤 Author

**Thanu Suthapalli**
Final Year Major Project — Autonomous AI Security Operations Center
[GitHub](https://github.com/ThanuSuthapalli)

---

## 📄 License

This project is developed for academic purposes.
