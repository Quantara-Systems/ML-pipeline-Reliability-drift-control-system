<div align="center">

<img src="https://img.shields.io/badge/Quantara%20Systems-ML%20Platform-0066CC?style=for-the-badge&logo=data:image/svg+xml;base64,..." alt="Quantara Systems" />

# ML Pipeline Reliability & Drift Control System

**Production-grade ML monitoring — track model performance, detect data drift, and fire intelligent alerts in real time.**

[![CI](https://github.com/Quantara-Systems/ML-pipeline-Reliability-drift-control-system/actions/workflows/ci.yml/badge.svg)](https://github.com/Quantara-Systems/ML-pipeline-Reliability-drift-control-system/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)](https://react.dev)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-4169E1?logo=postgresql&logoColor=white)](https://postgresql.org)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)](https://docker.com)

</div>

---

## Overview

ML models degrade silently in production. Predictions drift, data distributions shift, and accuracy erodes — often without any visible signal until it's too late. This system gives your team **full observability** into every deployed model, from the moment a prediction is made to the moment performance degrades.

Built by [Quantara Systems](https://github.com/Quantara-Systems), this platform covers the entire monitoring lifecycle:

- Detect **data drift** before it becomes an accuracy problem
- Track **model confidence** and performance metrics over time
- Compute **composite health scores** with root-cause breakdowns
- Fire **automated alerts** via email and Slack
- Upload and manage models through a **clean React dashboard**

---

## Features

| Feature | Description |
|---|---|
| 📊 **Real-time Dashboard** | Live KPIs, drift charts, and performance trends via React + Recharts + Ant Design |
| 🔍 **Drift Detection** | Evidently AI-powered statistical analysis (KS-test, PSI) per column and dataset |
| 🧠 **Prediction API** | Single, batch, and manual prediction endpoints with confidence scoring |
| 🚨 **Alert System** | Severity-tiered alerts (info → warning → critical) via email (SMTP) and Slack webhooks |
| 🏥 **Health Monitor** | Composite 0–100 health score per model with automated issue diagnosis |
| 📁 **Model Upload** | Upload `.pkl` / `.joblib` models and Parquet datasets through the dashboard |
| 🔐 **Authentication** | JWT Bearer tokens + API Key (`X-API-Key`) dual-auth with role-based access |
| 🐳 **Docker Ready** | Full `docker-compose` stack — Postgres, Redis, Kafka, backend, and frontend |
| ⚙️ **CI/CD** | GitHub Actions pipeline: lint (Ruff + ESLint), unit tests, dependency security audit |
| 📦 **Task Queue** | Celery + Redis for async monitoring jobs; Kafka for streaming ingestion |

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                        React Frontend                            │
│        Dashboard  ·  Alerts  ·  Metrics  ·  Predictions         │
│        Ant Design  ·  Recharts  ·  TypeScript  ·  Vite           │
└───────────────────────────┬──────────────────────────────────────┘
                            │  REST API  /api/v1
┌───────────────────────────▼──────────────────────────────────────┐
│                      FastAPI Backend                             │
│   /predictions  /monitoring  /alerts  /health  /models  /data    │
├────────────────┬───────────────┬─────────────────────────────────┤
│  Model         │  Drift        │  Health        Alert            │
│  Service       │  Detector     │  Monitor       Manager          │
│  (joblib)      │  (Evidently)  │  (composite    (SMTP /          │
│                │               │   scoring)     Slack)           │
├────────────────┴───────────────┴─────────────────────────────────┤
│  Authentication: JWT Bearer  +  X-API-Key header                 │
└─────────────────┬───────────────┬──────────────────┬─────────────┘
                  ▼               ▼                  ▼
            PostgreSQL          Redis             Kafka
            (SQLAlchemy)    (cache + Celery)   (streaming)
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Backend** | Python 3.11, FastAPI 0.104, SQLAlchemy 2.0, Alembic |
| **Frontend** | React 18, TypeScript, Vite, Ant Design 5, Recharts |
| **ML / Data** | scikit-learn, pandas, numpy, Evidently AI, MLflow |
| **Database** | PostgreSQL 15 (prod), SQLite (local dev) |
| **Messaging** | Kafka 7.4, Zookeeper, kafka-python |
| **Cache / Queue** | Redis 7, Celery 5 |
| **Auth** | JWT (python-jose), bcrypt (passlib), API Keys |
| **Infra** | Docker, Docker Compose, GitHub Actions |
| **Observability** | MLflow experiment tracking, structured JSON logging |

---

## Project Structure

```
ML-pipeline-Reliability-drift-control-system/
│
├── .github/
│   └── workflows/
│       └── ci.yml                    # Lint → Test → Security audit
│
├── backend/
│   ├── api/                          # FastAPI route handlers
│   │   ├── alerts.py                 #   Alert CRUD + resolve
│   │   ├── data.py                   #   Inference log endpoints
│   │   ├── health.py                 #   Health score endpoints
│   │   ├── models.py                 #   Model management
│   │   ├── monitoring.py             #   Drift & performance
│   │   ├── predictions.py            #   Single / batch / manual predict
│   │   ├── routes.py                 #   Router aggregation
│   │   └── uploads.py                #   File upload (.pkl / .parquet)
│   │
│   ├── alerts/
│   │   └── alert_manager.py          # SMTP + Slack alert dispatch
│   │
│   ├── database/
│   │   ├── connection.py             # SQLAlchemy engine & session factory
│   │   └── models.py                 # ORM table definitions
│   │
│   ├── models/                       # Saved ML artifacts (gitignored in prod)
│   │   ├── credit_model.pkl
│   │   └── scaler.pkl
│   │
│   ├── monitoring/
│   │   ├── drift_detector.py         # Evidently AI drift analysis
│   │   └── performance_monitor.py    # Accuracy / F1 / MAE / R² metrics
│   │
│   ├── services/
│   │   ├── health_monitor.py         # Composite 0-100 health scoring
│   │   ├── model_service.py          # Model load, predict, manage
│   │   └── scheduler.py             # Background job scheduler
│   │
│   ├── scripts/
│   │   ├── monitor_data_drift.py     # Standalone drift runner
│   │   └── monitor_model_health.py   # Standalone health runner
│   │
│   ├── tests/
│   │   ├── test_monitoring.py
│   │   └── test_predictions.py
│   │
│   ├── auth.py                       # JWT + API key authentication
│   ├── main.py                       # FastAPI app entry point
│   ├── Dockerfile
│   └── requirements.txt
│
├── frontend/
│   ├── public/
│   │   └── index.html
│   └── src/
│       ├── components/               # Reusable UI components
│       ├── config/
│       │   └── api.ts                # Centralized API endpoint config
│       ├── hooks/                    # Custom React hooks
│       ├── pages/
│       │   ├── Dashboard.tsx         # Main overview page
│       │   ├── Alerts.tsx            # Alert management
│       │   ├── Metrics.tsx           # Performance charts
│       │   ├── Prediction.tsx        # Make predictions
│       │   └── Settings.tsx          # Configuration
│       ├── types/                    # TypeScript type definitions
│       ├── utils/                    # Helper utilities
│       ├── App.tsx
│       └── main.tsx
│
├── docker/
│   └── docker-compose.yml            # Full-stack orchestration (with Kafka)
│
├── config/
│   └── settings.env.example
│
├── tests/
│   └── test_api.py                   # Integration tests
│
├── .env.example                      # Environment variable template
├── .gitignore
├── CHANGELOG.md
├── CONTRIBUTING.md
├── docker-compose.yml                # Simplified compose (no Kafka)
├── LICENSE                           # MIT License
└── README.md
```

---

## Quick Start

### Prerequisites

- **Python** 3.11+
- **Node.js** 18+ and npm
- **Git**
- **Docker** + Docker Compose *(for containerized setup)*

---

### Option A — Local Development

#### 1. Clone the repo

```bash
git clone https://github.com/Quantara-Systems/ML-pipeline-Reliability-drift-control-system.git
cd ML-pipeline-Reliability-drift-control-system
```

#### 2. Backend setup

```bash
cd backend
python -m venv venv

# Windows
venv\Scripts\activate
# macOS / Linux
source venv/bin/activate

pip install -r requirements.txt
```

Copy and configure your environment:

```bash
cp ../.env.example .env
# Open .env and set SECRET_KEY, DATABASE_URL, etc.
```

Start the API server:

```bash
uvicorn main:app --reload --port 8001
```

Interactive API docs → [http://localhost:8001/docs](http://localhost:8001/docs)

#### 3. Frontend setup

```bash
cd ../frontend
npm install
npm start
```

Dashboard → [http://localhost:3000](http://localhost:3000)

---

### Option B — Docker (Recommended)

Spins up the full stack: backend, frontend, PostgreSQL, Redis, Kafka.

```bash
# 1. Configure environment
cp .env.example .env
# Edit .env — set SECRET_KEY and any optional services

# 2. Build and start all services
docker compose -f docker/docker-compose.yml up --build

# 3. Stop all services
docker compose -f docker/docker-compose.yml down

# Remove volumes too (full reset)
docker compose -f docker/docker-compose.yml down -v
```

#### Service URLs

| Service | URL |
|---|---|
| React Dashboard | http://localhost:3000 |
| FastAPI Backend | http://localhost:8001 |
| Swagger / OpenAPI Docs | http://localhost:8001/docs |
| ReDoc | http://localhost:8001/redoc |
| PostgreSQL | localhost:5432 |
| Redis | localhost:6379 |
| Kafka | localhost:9092 |

---

## API Reference

Full interactive docs available at `http://localhost:8001/docs`.

### Authentication

All protected endpoints accept either:
- `Authorization: Bearer <jwt_token>` header
- `X-API-Key: <api_key>` header

### Endpoints

#### Predictions

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/v1/predictions/predict` | Single prediction from feature input |
| `POST` | `/api/v1/predictions/batch` | Batch prediction from Parquet dataset |
| `POST` | `/api/v1/predictions/manual` | Manual feature-by-feature prediction |

#### Monitoring

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/v1/monitoring/drift` | Current drift metrics (all columns) |
| `GET` | `/api/v1/monitoring/performance/{model_id}` | Model performance metrics |
| `GET` | `/api/v1/monitoring/check` | Trigger full monitoring check |

#### Health

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/v1/health/score` | Composite health score (0–100) |
| `GET` | `/api/v1/health/status` | System-level health status |

#### Alerts

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/v1/alerts/` | List all alerts (filterable by severity) |
| `POST` | `/api/v1/alerts/{id}/resolve` | Mark alert as resolved |
| `GET` | `/api/v1/alerts/summary` | Alert count by severity |

#### Models

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/v1/models/` | List registered models |
| `POST` | `/api/v1/uploads/model` | Upload a `.pkl` / `.joblib` model file |
| `DELETE` | `/api/v1/models/{id}` | Remove a registered model |

---

## Environment Variables

Copy `.env.example` to `.env` and configure. Key variables:

| Variable | Default | Description |
|---|---|---|
| `SECRET_KEY` | *(required)* | JWT signing secret — **always change in production** |
| `DATABASE_URL` | `sqlite:///./ml_monitoring.db` | DB connection string |
| `REACT_APP_API_URL` | `http://localhost:8001` | Frontend → backend URL |
| `REDIS_URL` | `redis://localhost:6379/0` | Redis connection |
| `SLACK_WEBHOOK_URL` | *(optional)* | Slack alerts webhook URL |
| `SMTP_SERVER` | `smtp.gmail.com` | SMTP server for email alerts |
| `ALERT_RECIPIENTS` | *(optional)* | Comma-separated alert email list |
| `DRIFT_THRESHOLD` | `0.05` | PSI drift detection threshold |
| `MONITORING_INTERVAL` | `60` | Monitoring check interval (seconds) |
| `LOG_LEVEL` | `INFO` | Logging verbosity |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | `1440` | JWT token lifetime (24 hours) |

> **Security note:** Generate a strong `SECRET_KEY` before any deployment:
> ```bash
> python -c "import secrets; print(secrets.token_hex(32))"
> ```

---

## Running Tests

```bash
# Backend unit tests
cd backend
pytest tests/ -v

# Integration tests
cd ..
pytest tests/test_api.py -v
```

The CI pipeline runs lint, tests, and a `pip-audit` security scan on every push and pull request.

---

## CI/CD Pipeline

`.github/workflows/ci.yml` runs four jobs on every push/PR:

| Job | Tool | What it checks |
|---|---|---|
| Backend Lint | `ruff` | PEP 8, unused imports, undefined names |
| Backend Tests | `pytest` | Unit tests with SQLite in-memory DB |
| Frontend Lint | `eslint` | React/TypeScript code quality |
| Security Audit | `pip-audit` | Known CVEs in Python dependencies |

---

## Contributing

Contributions are welcome. Please read [CONTRIBUTING.md](./CONTRIBUTING.md) before opening a pull request.

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Follow [Conventional Commits](https://www.conventionalcommits.org/): `feat: add your feature`
4. Push your branch and open a PR against `main`
5. All CI checks must pass before merging

---

## Security

Found a vulnerability? Please report it privately via [GitHub Security Advisories](https://github.com/Quantara-Systems/ML-pipeline-Reliability-drift-control-system/security/advisories) rather than opening a public issue. See [SECURITY.md](./SECURITY.md) for the full policy.

---

## License

This project is licensed under the [MIT License](./LICENSE).

Copyright © 2024 [Quantara Systems](https://github.com/Quantara-Systems)

---

## Acknowledgements

- [Evidently AI](https://www.evidentlyai.com/) — data drift and model monitoring framework
- [FastAPI](https://fastapi.tiangolo.com/) — modern Python web framework
- [Ant Design](https://ant.design/) — enterprise React UI components
- [Recharts](https://recharts.org/) — composable charting library for React
- [MLflow](https://mlflow.org/) — ML experiment tracking

---

<div align="center">
  Built with care by <a href="https://github.com/Quantara-Systems">Quantara Systems</a>
</div>
