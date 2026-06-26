# AI Kubernetes Capacity Intelligence Radar - High Level Design (HLD)

## Goal

Build an AI-assisted Kubernetes capacity intelligence platform that can:

- Scan local kubeconfig contexts using `kubectl`
- Detect Kubernetes resource waste and capacity risk
- Identify workloads missing CPU or memory requests and limits
- Highlight OOMKilled, restart-heavy, pending, and crashlooping pods
- Analyze node efficiency from allocatable, requested, limit, and live usage data
- Discover Karpenter NodePools, NodeClaims, EC2NodeClasses, and warning signals
- Recommend workload right-sizing and Karpenter sizing improvements
- Generate optional YAML patch previews without mutating the cluster
- Explain findings with an optional local Codex CLI AI layer

This project is read-only by default. It must not apply fixes automatically.

---

# High Level Architecture

```text
┌────────────────────────────────────────────────────────────┐
│                    Kubernetes Clusters                     │
│                                                            │
│  Pods | Workloads | Nodes | Namespaces | Karpenter CRDs    │
│                                                            │
│  Capacity risk, waste, and scheduling evidence exists here │
└────────────────────────────────────────────────────────────┘
                              │
                              │ kubectl only
                              ▼
┌────────────────────────────────────────────────────────────┐
│                    kubectl Scanner Layer                   │
│                                                            │
│ Responsibility:                                            │
│ - Read kubeconfig contexts                                 │
│ - Run read-only kubectl commands                           │
│ - Collect JSON output                                      │
│ - Handle timeouts, unreachable clusters, RBAC errors       │
│ - Sanitize command output                                  │
│                                                            │
│ Components:                                                │
│                                                            │
│  1. Context Scanner                                        │
│     - List kubeconfig contexts                             │
│     - Detect current context                               │
│     - Check cluster reachability                           │
│     - Read Kubernetes server version                       │
│                                                            │
│  2. Workload Scanner                                       │
│     - Read pods, deployments, statefulsets, daemonsets     │
│     - Read namespaces                                      │
│     - Detect missing requests and limits                   │
│     - Detect OOMKilled, restarts, pending, crashlooping    │
│                                                            │
│  3. Metrics Scanner                                        │
│     - Use kubectl top pods                                 │
│     - Use kubectl top nodes                                │
│     - Continue gracefully if metrics-server is unavailable │
│                                                            │
│  4. Node Scanner                                           │
│     - Read nodes                                           │
│     - Map scheduled pods per node                          │
│     - Calculate requested and limit percentages            │
│     - Detect low utilization and memory pressure           │
│                                                            │
│  5. Karpenter Scanner                                      │
│     - Discover NodePools, NodeClaims, EC2NodeClasses       │
│     - Inspect readiness, drift, expiry, capacity types     │
│     - Analyze warning events                               │
└────────────────────────────────────────────────────────────┘
                              │
                              │ Structured Scan Data
                              ▼
┌────────────────────────────────────────────────────────────┐
│                 Capacity Analyzer Layer                    │
│                                                            │
│ Responsibility:                                            │
│ - Convert raw Kubernetes objects into capacity signals     │
│ - Score waste and risk                                     │
│ - Build explainable findings                               │
│                                                            │
│ Components:                                                │
│                                                            │
│  1. Workload Resource Analyzer                             │
│     - Missing CPU request                                  │
│     - Missing memory request                               │
│     - Missing CPU limit                                    │
│     - Missing memory limit                                 │
│     - Over-requested CPU and memory                        │
│     - Under-requested memory and possible OOM risk         │
│     - Idle workloads                                       │
│                                                            │
│  2. Node Efficiency Analyzer                               │
│     - Requested CPU percentage                             │
│     - Requested memory percentage                          │
│     - Live CPU and memory percentage when metrics exist    │
│     - Empty or low-utilization nodes                       │
│     - High pod density                                     │
│                                                            │
│  3. Namespace Risk Analyzer                                │
│     - Namespace-level waste score                          │
│     - Concentrated risk detection                          │
│     - Prioritized namespace triage                         │
│                                                            │
│  4. Karpenter NodePool Sizing Analyzer                     │
│     - Narrow instance constraints                          │
│     - Missing spot capacity option                         │
│     - Missing consolidation                                │
│     - Limits too low or too high                           │
│     - Pending pods that do not match NodePools             │
└────────────────────────────────────────────────────────────┘
                              │
                              │ Findings + Evidence
                              ▼
┌────────────────────────────────────────────────────────────┐
│                 Recommendation Engine                      │
│                                                            │
│ Responsibility:                                            │
│ - Generate deterministic recommendations                   │
│ - Suggest safe right-sizing actions                        │
│ - Generate YAML patch previews only                        │
│ - Never mutate the cluster                                 │
│                                                            │
│ Recommendation Types:                                      │
│                                                            │
│  1. Workload Recommendations                               │
│     - Add missing requests                                 │
│     - Add missing limits                                   │
│     - Right-size over-requested workloads                  │
│     - Raise memory requests for OOM risk                   │
│                                                            │
│  2. Node Recommendations                                   │
│     - Investigate low-utilization nodes                    │
│     - Rebalance pods                                      │
│     - Review node sizing                                   │
│     - Check memory pressure                                │
│                                                            │
│  3. Karpenter Recommendations                              │
│     - Widen instance families                              │
│     - Allow multiple zones                                 │
│     - Tune consolidation and disruption                    │
│     - Adjust NodePool CPU and memory limits                │
│     - Align selectors, affinities, and tolerations         │
└────────────────────────────────────────────────────────────┘
                              │
                              │ Optional Summarized Findings
                              ▼
┌────────────────────────────────────────────────────────────┐
│                 Optional AI Explanation Layer              │
│                                                            │
│ Responsibility:                                            │
│ - Use AI only when explicitly configured                   │
│ - Work without any AI provider                             │
│ - Explain evidence like a senior platform engineer         │
│ - Return strict JSON                                       │
│ - Never invent data                                        │
│                                                            │
│ Provider:                                                  │
│                                                            │
│  AI_PROVIDER=codex_cli                                     │
│                                                            │
│  - Uses local Codex CLI device login                       │
│  - Sends summarized findings only                          │
│  - Uses timeout                                            │
│  - Requires strict JSON output                             │
└────────────────────────────────────────────────────────────┘
                              │
                              │ API Response
                              ▼
┌────────────────────────────────────────────────────────────┐
│                    Next.js Dashboard                       │
│                                                            │
│ Responsibility:                                            │
│ - Select kubeconfig context and namespace                  │
│ - Run fast or deep scans                                   │
│ - Show scan progress                                       │
│ - Display workload, node, Karpenter, and AI signals        │
│ - Copy kubectl commands                                    │
│ - Copy YAML patch previews                                 │
│ - Show recent scan history                                 │
│                                                            │
│ UI Style:                                                  │
│                                                            │
│  Futuristic AI capacity command deck                       │
│  Dark cockpit UI                                           │
│  Animated radar panels                                     │
│  Evidence-first recommendation cards                       │
└────────────────────────────────────────────────────────────┘
```

# End-to-End Workflow

```text
User opens dashboard
        │
        ▼
Frontend loads kubeconfig contexts
        │
        ▼
User selects context and namespace
        │
        ▼
User runs Fast Scan or Deep Scan
        │
        ▼
FastAPI Backend
  read-only orchestration layer
        │
        ├── Context reachability check
        ├── Workload resource scan
        ├── Optional live metrics scan
        ├── Node efficiency scan
        └── Karpenter capacity scan
        │
        ▼
Capacity Analyzer
        │
        ├── Workload waste findings
        ├── Node efficiency findings
        ├── Namespace waste score
        └── NodePool sizing findings
        │
        ▼
Recommendation Engine
        │
        ├── Right-sizing guidance
        ├── Karpenter tuning guidance
        └── YAML patch previews
        │
        ▼
Optional AI Explanation
        │
        ├── Executive summary
        ├── Top risks
        ├── Savings opportunities
        ├── Karpenter recommendations
        └── Assumptions
        │
        ▼
Dashboard renders command deck
```

# Example Capacity Flow

```text
Issue:
AWS EKS cluster has high cost and frequent pod restarts.

Radar Scan:

✓ Context reachability checked
✓ Workloads scanned
✓ Metrics collected when available
✓ Nodes analyzed
✓ Karpenter NodePools inspected

Detected Problems:

- 244 workload findings
- Multiple containers missing memory requests
- Several pods with high restart counts
- NodePools have narrow instance constraints
- Some NodePools allow only one capacity type

Evidence:

- Deployment api has no memory request
- Pod worker-xyz restarted 18 times
- NodePool general only allows one instance family
- Pending pod does not match any NodePool requirements

Recommendations:

- Add memory requests based on observed usage
- Right-size over-requested workloads before changing NodePools
- Widen instance family constraints
- Allow multiple zones
- Enable or tune consolidation
- Generate patch previews for safe review

Confidence:
Evidence-backed deterministic scan with optional AI explanation.
```

## Supported Kubernetes Capacity Problems

- Missing CPU requests
- Missing memory requests
- Missing CPU limits
- Missing memory limits
- Over-requested CPU
- Over-requested memory
- Under-requested memory
- Possible OOM risk
- OOMKilled pods
- High restart counts
- CrashLoopBackOff pods
- Pending pods
- Idle workloads
- Namespace-level waste risk
- Empty or low-utilization nodes
- Nodes near memory pressure
- High pod density
- Karpenter not installed
- NodePool readiness issues
- NodeClaim not ready
- Drifted NodeClaims
- Expired or old NodeClaims
- Narrow instance type constraints
- Single capacity type NodePools
- Missing spot option where appropriate
- Missing consolidation
- Overly aggressive disruption
- NodePool limits too low for pending workloads
- NodePool limits far above actual usage
- Pending pods that do not match any NodePool

## Tech Stack

### Backend

- FastAPI
- Python 3.12+
- Uvicorn
- Pydantic
- Loguru
- HTTPX
- kubectl via subprocess

### Frontend

- Next.js
- TypeScript
- Tailwind CSS
- Axios
- React Query

### Infrastructure

- Docker
- Docker Compose

### Optional AI

- Codex CLI through `AI_PROVIDER=codex_cli`

## Repository Structure

```text
AI-k8s-Capacity-Radar/
├── README.md
├── LICENSE
├── .gitignore
├── docs/
│   ├── architecture.md
│   ├── scan-flow.md
│   ├── karpenter-analysis.md
│   ├── demo-guide.md
│   └── medium-article-outline.md
├── prompts/
│   ├── 01-project-setup-prompt.md
│   ├── 02-kubectl-scanner-prompt.md
│   ├── 03-capacity-analysis-engine-prompt.md
│   ├── 04-karpenter-ai-recommendation-prompt.md
│   ├── 05-end-to-end-dashboard-prompt.md
│   ├── ai-explanation-system-prompt.md
│   ├── scan-summary-prompt.md
│   └── yaml-patch-guidelines.md
└── manifests/
    └── demo/
        ├── missing-requests-limits.yaml
        ├── pending-impossible-node-selector.yaml
        └── karpenter-nodepool-examples.yaml
```

## Safety Principles

- Use `kubectl` only
- Read-only by default
- Do not apply fixes automatically
- Do not expose secrets
- Do not send raw cluster objects to AI
- AI must cite scan evidence
- AI must not invent data
- YAML output is a patch preview only

## Environment

```env
KUBECONFIG_PATH=
KUBECTL_TIMEOUT_SECONDS=60
AI_PROVIDER=
AI_TIMEOUT_SECONDS=180
AI_MAX_FINDINGS=20
CODEX_CLI_PATH=codex
```

To enable optional AI explanations:

```env
AI_PROVIDER=codex_cli
CODEX_CLI_PATH=/Applications/Codex.app/Contents/Resources/codex
```

## API Surface

```text
GET  /health
GET  /contexts
GET  /contexts/{context_name}/namespaces
POST /scan/context
POST /scan/workloads
POST /scan/nodes
POST /scan/karpenter
POST /ai/explain
```

## Scan Output Shape

```json
{
  "summary": {},
  "namespaces": [],
  "workloads": [],
  "findings": []
}
```

Each finding includes:

```json
{
  "severity": "low | medium | high | critical",
  "namespace": "default",
  "workload_name": "api",
  "workload_kind": "Deployment",
  "container_name": "api",
  "finding_type": "missing_memory_request",
  "evidence": "container has no memory request",
  "recommendation": "set memory request from observed usage"
}
```

## AI Output Schema

```json
{
  "executive_summary": "",
  "top_risks": [],
  "top_savings_opportunities": [],
  "karpenter_recommendations": [],
  "workload_rightsizing_recommendations": [],
  "yaml_patch_previews": [],
  "confidence": 0,
  "assumptions": []
}
```

## About

Kubernetes Capacity Intelligence Radar helps platform and DevOps engineers find cost waste, reliability risk, and Karpenter sizing opportunities before capacity problems become incidents.
