# 03 - Capacity Analysis Engine Prompt

Add live usage and node efficiency scanning.

Metrics:

- Use `kubectl top pods -A`
- Use `kubectl top nodes`
- Continue if metrics-server is unavailable
- Return a friendly warning when metrics are missing

Analyze:

- CPU request vs CPU usage
- Memory request vs memory usage
- CPU limit vs CPU usage
- Memory limit vs memory usage

Detect:

- Over-requested CPU
- Over-requested memory
- Under-requested memory
- Possible OOM risk
- Idle workloads
- Namespace-level waste risk

Add a waste score:

- Range: `0-100`
- Higher means more likely waste or risk
- Keep formulas simple and explainable

Node efficiency scanner must collect:

- `kubectl get nodes -o json`
- `kubectl top nodes` if available
- Scheduled pods per node
- Allocatable CPU and memory
- Requested CPU and memory from pods
- Limits CPU and memory from pods

Calculate:

- Requested CPU percentage
- Requested memory percentage
- Live CPU usage percentage
- Live memory usage percentage
- Pods per node

Detect:

- Empty nodes
- Low-utilization nodes
- Nodes near memory pressure
- Nodes with high pod density

Return:

```json
{
  "nodes": [],
  "node_efficiency": {},
  "findings": [],
  "recommendations": []
}
```

