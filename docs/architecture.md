# Architecture

## Purpose

AI Kubernetes Capacity Intelligence Radar is a read-only Kubernetes capacity intelligence system. It scans local kubeconfig contexts, detects resource waste and capacity risk, analyzes Karpenter sizing posture, and optionally uses Codex CLI to explain findings.

## Design Goals

- Prefer safe read-only discovery
- Use `kubectl`, not the Kubernetes Python SDK
- Work without AI
- Avoid secrets in responses and AI prompts
- Keep recommendations explainable
- Generate YAML patch previews only
- Make large-cluster behavior resilient with timeouts and truncation

## System Components

```text
Next.js Dashboard
    │
    ▼
FastAPI Backend
    │
    ▼
Kubectl Executor
    │
    ├── Context Scanner
    ├── Workload Scanner
    ├── Metrics Scanner
    ├── Node Efficiency Scanner
    └── Karpenter Scanner
            │
            ▼
Analyzers + Recommendation Engine
            │
            ▼
Optional Codex CLI Explanation Layer
```

## Backend Responsibilities

The backend orchestrates scan steps and converts Kubernetes API data into structured capacity intelligence.

Core backend modules:

- API routes for health, contexts, scans, and AI explanations
- Config and environment loading
- Structured logging
- CORS
- Kubectl subprocess executor
- Workload resource scanner
- Node efficiency scanner
- Karpenter scanner
- Recommendation engine
- Optional AI explanation service

## Frontend Responsibilities

The frontend is the command deck for platform engineers.

Key UI areas:

- Cluster context selector
- Namespace selector
- Fast scan and deep scan controls
- Capacity radar overview
- Workload waste findings
- Node efficiency findings
- Karpenter NodePools and NodeClaims
- AI recommendations
- YAML patch previews
- Recent scan history

## Data Flow

```text
User selects context
    │
    ▼
Frontend calls /scan/context
    │
    ▼
Backend validates reachability
    │
    ▼
Frontend runs workload/node/Karpenter scans
    │
    ▼
Backend executes kubectl read-only commands
    │
    ▼
Analyzers produce findings and recommendations
    │
    ▼
Optional AI layer summarizes evidence
    │
    ▼
Dashboard renders prioritized guidance
```

## Failure Handling

The system should handle:

- `kubectl` missing
- kubeconfig missing
- cluster unreachable
- metrics-server unavailable
- Karpenter not installed
- command timeout
- RBAC permission denied
- empty namespaces
- large clusters
- no unhealthy workloads

## Security Posture

- No automatic remediation
- No `kubectl apply`
- No secret exposure
- No raw secrets in AI prompts
- AI receives summarized findings only
- YAML patch previews require human review

