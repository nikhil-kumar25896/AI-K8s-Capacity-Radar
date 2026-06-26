# 04 - Karpenter and AI Recommendation Prompt

Add Karpenter discovery, NodePool sizing analysis, and optional AI explanations.

Karpenter discovery:

- Check API resources
- Detect `nodepools.karpenter.sh`
- Detect `nodeclaims.karpenter.sh`
- Detect `ec2nodeclasses.karpenter.k8s.aws`
- Collect Karpenter controller pods
- Collect Karpenter warning events

If Karpenter is not installed:

```json
{
  "karpenter": {
    "enabled": false
  }
}
```

Do not fail the scan.

Analyze:

- NodePool readiness conditions
- NodePool limits
- NodePool disruption settings
- NodePool requirements
- Capacity types
- Instance family/category flexibility
- Zones
- Architecture
- NodeClaim readiness
- Drifted NodeClaims
- Unhealthy NodeClaims
- Expired or old NodeClaims

NodePool sizing analyzer input:

- NodePools
- NodeClaims
- Nodes
- Pods
- Resource requests
- Metrics if available

Detect:

- Too narrow instance constraints
- Only one capacity type
- Missing spot option where appropriate
- Missing consolidation
- Overly aggressive disruption
- Limits too low for pending workloads
- Limits far above actual usage
- Many underutilized nodes
- NodeClaims not ready
- Pending pods that do not match any NodePool

Generate recommendations:

- Widen instance families
- Allow multiple zones
- Allow arm64 only if workloads support it
- Enable or tune consolidation
- Adjust NodePool CPU/memory limits
- Split workloads into separate NodePools when needed
- Align tolerations/selectors
- Right-size workload requests before changing NodePools

Optional AI:

- App must work without AI.
- Support `AI_PROVIDER=codex_cli`.
- Use local Codex CLI device login.
- Shell out safely.
- Pass only summarized scan findings.
- Use timeout.
- Require strict JSON output.
- AI must not invent data.
- AI must cite evidence.

