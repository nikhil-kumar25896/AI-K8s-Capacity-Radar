# Scan Flow

## Scan Modes

### Fast Scan

Fast scan is designed for quick workload capacity feedback.

Steps:

1. Check selected kubeconfig context reachability
2. Collect workloads and namespaces
3. Detect missing requests and limits
4. Detect pod health signals
5. Collect pod metrics if metrics-server is available
6. Return workload waste findings and namespace risk

### Deep Scan

Deep scan performs cluster capacity and Karpenter analysis.

Steps:

1. Check selected kubeconfig context reachability
2. Run workload scan
3. Run node efficiency scan
4. Run Karpenter discovery and NodePool sizing analysis
5. Generate deterministic recommendations
6. Optionally run AI explanations

## Workload Scan

Resources collected:

- Pods
- Deployments
- StatefulSets
- DaemonSets
- Namespaces
- Pod metrics from `kubectl top pods`

Findings:

- Missing CPU request
- Missing memory request
- Missing CPU limit
- Missing memory limit
- OOMKilled container
- High restart count
- Pending pod
- CrashLoopBackOff
- Over-requested CPU
- Over-requested memory
- Under-requested memory
- Idle workload

## Node Scan

Resources collected:

- Nodes
- Pods scheduled per node
- Node metrics from `kubectl top nodes`

Calculations:

- Requested CPU percentage
- Requested memory percentage
- Limit CPU and memory
- Live CPU usage percentage
- Live memory usage percentage
- Pods per node

Findings:

- Empty node
- Low-utilization node
- Node near memory pressure
- High pod density

## Karpenter Scan

Resources collected:

- API resources
- NodePools
- NodeClaims
- EC2NodeClasses when AWS provider exists
- Karpenter controller pods
- Karpenter warning events
- Nodes
- Pods
- Metrics when available

Findings:

- NodePool not ready
- Missing limits
- Narrow requirements
- Single capacity type
- Missing spot option where appropriate
- Missing consolidation
- Aggressive disruption
- NodeClaims not ready
- Drifted NodeClaims
- Old or expired NodeClaims
- Pending pod does not match NodePool requirements

## AI Explanation Flow

AI is optional.

If `AI_PROVIDER` is empty:

```json
{
  "enabled": false,
  "error": "AI is disabled. Set AI_PROVIDER=codex_cli to enable explanations."
}
```

If enabled:

1. Backend summarizes scan findings
2. Backend calls Codex CLI with strict JSON schema
3. AI returns executive summary, risks, savings, recommendations, patch previews, confidence, and assumptions
4. Frontend renders the response as evidence-cited guidance

