# Karpenter Analysis

## Goal

The Karpenter analyzer helps platform engineers understand whether NodePools and NodeClaims are sized and constrained in a way that supports reliability and cost efficiency.

## Discovery

Karpenter is detected through API resources and objects:

- `nodepools.karpenter.sh`
- `nodeclaims.karpenter.sh`
- `ec2nodeclasses.karpenter.k8s.aws`

If Karpenter is not installed, the scan returns:

```json
{
  "karpenter": {
    "enabled": false
  }
}
```

The scan must not fail simply because Karpenter is absent.

## NodePool Analysis

NodePool signals:

- Readiness conditions
- CPU and memory limits
- Disruption settings
- Consolidation settings
- Requirements
- Capacity types
- Instance categories
- Instance families
- Zones
- Architectures

Risk patterns:

- Only one instance family or category
- Only one capacity type
- Missing spot option where appropriate
- Missing consolidation
- Too few zones
- Limits too low for pending workloads
- Limits far above actual usage
- Aggressive disruption windows or budgets

## NodeClaim Analysis

NodeClaim signals:

- Readiness
- Drift state
- Expiry state
- Age
- Instance type
- Capacity type
- Zone
- Architecture
- Node mapping

Risk patterns:

- NodeClaim not ready
- Drifted NodeClaim
- Expired NodeClaim
- Very old NodeClaim
- NodeClaims concentrated in one capacity type

## Pending Pod Matching

The analyzer compares pending pods against NodePool requirements, labels, and taints.

Common mismatch causes:

- Pod `nodeSelector` does not match any NodePool
- Node affinity conflicts with NodePool requirements
- Missing toleration for NodePool taint
- Architecture mismatch
- Zone mismatch
- Capacity type mismatch

## Recommendations

The recommendation engine can suggest:

- Widen instance families
- Allow multiple zones
- Add spot alongside on-demand where safe
- Enable or tune consolidation
- Adjust NodePool CPU or memory limits
- Split workload classes into separate NodePools
- Align pod selectors and tolerations
- Right-size workload requests before changing NodePools

## Patch Preview Policy

Patch previews are advisory only.

The app must not:

- Apply YAML automatically
- Delete NodePools
- Mutate live resources
- Hide uncertainty

The app should:

- Show evidence
- Explain assumptions
- Let humans copy and review YAML

