# AlgoLens Azure

[![Azure deployment](https://github.com/kesava-bobbili/algolens-azure/actions/workflows/main_algolens-api.yml/badge.svg)](https://github.com/kesava-bobbili/algolens-azure/actions/workflows/main_algolens-api.yml)

AlgoLens is an AI-powered developer productivity and code-intelligence platform for practicing data structures and algorithms. This repository contains the Azure deployment of AlgoLens.

## Live deployment

- Frontend: [algolens-azure-pied.vercel.app](https://algolens-azure-pied.vercel.app)
- Backend API: [Azure App Service](https://algolens-api-asahavd6ard6hahc.centralindia-01.azurewebsites.net)
- API documentation: [Swagger UI](https://algolens-api-asahavd6ard6hahc.centralindia-01.azurewebsites.net/docs)

The frontend is deployed separately on Vercel and communicates with the FastAPI backend hosted on Azure App Service.

## Features

- DSA problem classification with complexity estimates and Groq-generated explanations
- Python execution tracing and visualization
- Mock technical-interview workflow
- GitHub repository analysis
- Knowledge-base ingestion and retrieval-augmented answers using LangGraph and ChromaDB
- File ingestion for text, code, PDF, DOCX, PPTX, XLSX, CSV, JSON, and Markdown files

## Architecture

```text
Next.js frontend (Vercel)
        |
        | HTTPS / REST
        v
FastAPI backend (Azure App Service)
        |
        +-- Groq LLM API
        +-- LangGraph retrieval workflow
        +-- ChromaDB vector store
        +-- Hugging Face embedding API
```

## Repository layout

```text
app/                 FastAPI application, API routes, services, and schemas
apps/web/            Next.js frontend
tests/               Backend pytest suite
.github/workflows/   Azure deployment workflow
```

## Local development

### Backend

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```

The API is then available at `http://localhost:8000` and documentation at `http://localhost:8000/docs`.

### Frontend

```bash
cd apps/web
npm install
NEXT_PUBLIC_API_URL=http://localhost:8000 npm run dev
```

Open `http://localhost:3000`.

## Configuration

Create a local `.env` file from `.env.example`. Do not commit credentials.

| Variable | Purpose |
| --- | --- |
| `GROQ_API_KEY` | Enables AI explanations and interview responses. |
| `LLM_MODEL` | Groq model identifier. The Azure deployment uses `openai/gpt-oss-20b`. |
| `HF_TOKEN` | Enables Hugging Face embeddings for knowledge-base ingestion. |
| `GITHUB_TOKEN` | Enables GitHub repository analysis with higher API limits. |
| `CORS_ORIGINS` | Comma-separated frontend origins allowed by the backend. |
| `NEXT_PUBLIC_API_URL` | Public API URL used by the Next.js frontend at build time. |

For the deployed Azure frontend, `NEXT_PUBLIC_API_URL` is set in Vercel to:

```text
https://algolens-api-asahavd6ard6hahc.centralindia-01.azurewebsites.net
```

For Azure App Service, `CORS_ORIGINS` includes:

```text
https://algolens-azure-pied.vercel.app
```

## Testing

Install development dependencies and run the backend test suite:

```bash
pip install -r requirements-dev.txt
pytest
```

Run coverage locally:

```bash
pytest --cov=app --cov-report=term-missing
```

Run frontend checks from `apps/web`:

```bash
npm run lint
npx tsc --noEmit
npm run build
```

## Deployment

- Pushing to `main` runs the GitHub Actions workflow that deploys the backend to Azure App Service `algolens-api`.
- Vercel deploys the `apps/web` Next.js application from this repository.
- The Azure API health endpoint is `GET /`.

## Deployment boundary

This is an isolated Azure/Vercel deployment. It does not modify the original AlgoLens Render backend or its original Vercel frontend.
