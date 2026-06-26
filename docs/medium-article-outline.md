# Medium Article Outline

## Title Ideas

- Building an AI Kubernetes Capacity Intelligence Radar
- Finding Kubernetes Waste and Karpenter Risk with a Read-Only AI Dashboard
- How to Build a Kubernetes Capacity Radar with FastAPI, Next.js, kubectl, and Codex CLI

## Hook

Kubernetes cost and reliability problems often start quietly:

- Missing resource requests
- Over-requested workloads
- Pods restarting too often
- Nodes with poor utilization
- Karpenter NodePools with narrow constraints
- Pending pods that cannot match any capacity

The goal of this project is to make those signals visible before they become incidents or cloud cost surprises.

## Problem Statement

Platform engineers need a safe way to inspect cluster capacity posture without giving a tool permission to mutate the cluster.

Existing dashboards often show metrics, but they do not always connect:

- workload requests
- live usage
- node utilization
- pending pod scheduling evidence
- Karpenter NodePool design
- actionable recommendations

## Architecture

Describe the stack:

- Next.js dashboard
- FastAPI backend
- kubectl scanner
- resource analyzer
- node efficiency analyzer
- Karpenter analyzer
- recommendation engine
- optional Codex CLI AI explanation layer

Emphasize:

- kubectl only
- read-only by default
- AI optional
- patch previews only

## Implementation Highlights

### Context Scanner

- Reads kubeconfig contexts
- Checks reachability
- Gets server version
- Handles kubeconfig and permission problems

### Workload Scanner

- Collects pods and workload controllers
- Detects missing requests and limits
- Detects OOMKilled pods and restarts
- Uses metrics-server when available

### Node Scanner

- Maps pods to nodes
- Calculates requested CPU and memory percentages
- Detects low utilization and pressure

### Karpenter Analyzer

- Discovers NodePools and NodeClaims
- Checks capacity type flexibility
- Flags narrow instance constraints
- Finds pending pods that do not match NodePool requirements

### AI Explanation Layer

- Uses summarized findings only
- Requires strict JSON
- Must cite evidence
- Does not invent data

## Demo Walkthrough

1. Start the dashboard
2. Select a kubeconfig context
3. Run a fast scan
4. Review workload waste
5. Run a deep scan
6. Review node efficiency
7. Review Karpenter NodePools
8. Generate AI recommendations
9. Copy YAML patch preview

## Lessons Learned

- Read-only tools build trust
- Metrics-server should be optional
- Large clusters require timeouts and truncation
- AI works best after deterministic analyzers produce evidence
- Karpenter sizing should follow workload right-sizing, not replace it

## Future Improvements

- Historical scan storage
- Trend analysis
- Cost estimation
- Prometheus integration
- Multi-cluster comparison
- GitHub PR generation for patch previews
- Policy checks for resource standards

## Conclusion

Kubernetes capacity engineering is not just about adding more nodes. A useful radar connects workloads, live usage, node efficiency, and Karpenter design into one evidence-backed workflow.

