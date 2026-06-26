# 01 - Project Setup Prompt

Create a monorepo named `kubernetes-capacity-radar`.

Goal:

Build a read-only Kubernetes Capacity Intelligence Radar that helps DevOps and platform engineers find resource waste, capacity risk, and Karpenter sizing issues.

Tech stack:

Backend:

- FastAPI
- Python 3.12+
- Uvicorn
- Pydantic
- Loguru
- HTTPX

Frontend:

- Next.js
- TypeScript
- Tailwind CSS
- Axios
- React Query

Infrastructure:

- Docker
- Docker Compose

Create structure:

```text
kubernetes-capacity-radar/
├── backend/
├── frontend/
├── docs/
├── manifests/
├── prompts/
├── docker-compose.yml
└── README.md
```

Backend folders:

- api/
- core/
- kubernetes/
- analyzers/
- recommendations/
- ai/
- models/
- services/

Frontend folders:

- components/
- services/
- hooks/
- types/

Add:

- GET `/health`
- Environment loading
- CORS
- Logging
- Dockerfile for backend
- Dockerfile for frontend
- Docker Compose with backend on `8000` and frontend on `3000`

Do not implement Kubernetes scanning in this step.

