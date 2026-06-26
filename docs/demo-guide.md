# Demo Guide

## Demo Goal

Show how the radar identifies Kubernetes resource waste, workload risk, node inefficiency, and Karpenter sizing issues without mutating the cluster.

## Prerequisites

- A Kubernetes cluster
- `kubectl` configured locally
- Optional metrics-server
- Optional Karpenter
- Optional Codex CLI login for AI explanations

## Demo Setup

Apply demo manifests:

```bash
kubectl apply -f manifests/demo/
```

The manifests include:

- Deployment missing requests and limits
- Pending pod with impossible node selector
- Karpenter NodePool examples

## Demo Script

1. Open the dashboard
2. Select kubeconfig context
3. Select namespace or all namespaces
4. Run Fast Scan
5. Show workload findings
6. Run Deep Scan
7. Show node efficiency and Karpenter tabs
8. Click AI Recommendations if enabled
9. Show YAML Patch Preview
10. Copy a kubectl command or YAML preview

## Expected Findings

The demo should show:

- Missing CPU request
- Missing memory request
- Missing CPU limit
- Missing memory limit
- Pending pod due to impossible node selector
- Karpenter flexibility recommendations

## Cleanup

```bash
kubectl delete -f manifests/demo/
```

