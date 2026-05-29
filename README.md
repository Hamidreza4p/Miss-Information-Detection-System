# 🛡️ Real-Time Misinformation Detection System

> Submit a news article URL or raw text claim — get back a structured verdict: **Real / Fake / Uncertain**, a confidence score, and a source-grounded explanation. Powered by RAG, LLMs, and a production-ready deployment stack.

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square&logo=python)
![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-009688?style=flat-square&logo=fastapi)
![Docker](https://img.shields.io/badge/Docker-ready-2496ED?style=flat-square&logo=docker)
![Kubernetes](https://img.shields.io/badge/Kubernetes-K8s-326CE5?style=flat-square&logo=kubernetes)
![License](https://img.shields.io/badge/License-GPLv3-green?style=flat-square)

---

## 🧠 What It Does

Misinformation spreads faster than corrections. This system tackles that problem by treating every claim as a query: it retrieves live web evidence via the **Tavily Search API**, passes both the claim and evidence to **Llama 3.1 8B** (via Hugging Face Inference API), and returns a structured verdict with a confidence score and a human-readable explanation grounded in real sources.

It is not a simple classifier trained on a fixed dataset — it reasons over fresh evidence at inference time, making it robust to new and evolving narratives.

---

## ⚙️ Architecture

The system is split into three layers: intelligence, interface, and infrastructure.

### 1. Backend — FastAPI
- **RAG Pipeline**: Tavily API for real-time evidence retrieval → Llama 3.1 8B for claim verification
- **Web Scraper**: BeautifulSoup4-based article extractor with basic bot-bypass handling
- **Smart Caching**: SQLite-backed global cache to reduce latency and API costs on repeated queries
- **Audit Trail**: All analyses and user feedback are logged to SQLite for traceability
- **Auth**: User authentication with bcrypt password hashing

### 2. Frontend — Streamlit Dashboard
- Input modes: paste raw text or submit a URL for automatic scraping
- Output: verdict label, confidence score, explanation with source references
- Visualizations: Plotly charts showing veracity distribution across a user's history
- Persistent analysis history filtered per user

### 3. DevOps / MLOps
- **CI/CD**: GitHub Actions pipeline — auto-builds Docker image and pushes to Amazon ECR on every push
- **Containerization**: Full app (backend + frontend) containerized in Docker
- **Orchestration**: Kubernetes manifests with defined resource limits and HostPath volumes for SQLite persistence
- **Secrets management**: API tokens handled via Kubernetes Secrets
- **Low-resource optimization**: Tuned to run an 8B parameter model via cloud Inference API, avoiding local GPU requirements

---

## 🚀 Deployment

The system supports two deployment modes:

| Mode | Description |
|------|-------------|
| **Local (Dev)** | Single Docker image, runs both backend and frontend. Uses Linux swap for low-RAM environments. |
| **Cloud (Prod)** | Deployed on Amazon EC2 via Kubernetes. CI/CD via GitHub Actions → Amazon ECR. |

For full step-by-step instructions see [DEPLOYMENT.md](./DEPLOYMENT.md).

---

## 🗂️ Project Structure

```
├── backend/
│   ├── main.py            # FastAPI app & endpoints
│   ├── rag_pipeline.py    # Tavily retrieval + LLM reasoning
│   ├── scraper.py         # URL article extractor
│   └── database.py        # SQLite cache & audit trail
├── frontend/
│   └── app.py             # Streamlit dashboard
├── k8s/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── secrets.yaml
├── Dockerfile
├── .github/workflows/     # CI/CD pipeline
└── README.md
```

---

## 🔧 Quick Start (Local)

```bash
# Clone the repo
git clone https://github.com/Hamidreza4p/Miss-Information-Detection-System.git
cd Miss-Information-Detection-System

# Set environment variables
cp .env.example .env
# Add your TAVILY_API_KEY and HF_API_TOKEN to .env

# Run with Docker
docker build -t misinfo-detector .
docker run -p 8000:8000 -p 8501:8501 misinfo-detector
```

Then open `http://localhost:8501` for the dashboard.

---

## 🛠️ Tech Stack

| Layer | Tools |
|-------|-------|
| LLM & RAG | Llama 3.1 8B (HF Inference API), Tavily Search API |
| Backend | Python, FastAPI |
| Frontend | Streamlit, Plotly |
| Storage | SQLite |
| DevOps | Docker, Kubernetes, GitHub Actions, Amazon ECR/EC2 |

---

## 📸 Screenshots

*Coming soon*

---

## ⚖️ License

This project is licensed under the **GNU GPLv3 License**. See the [LICENSE](./LICENSE) file for details.
