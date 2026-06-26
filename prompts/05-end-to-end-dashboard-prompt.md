# 05 - End-to-End Dashboard Prompt

Build the frontend dashboard as a futuristic AI capacity command deck.

Style:

- Futuristic but professional
- Dark cockpit-style UI
- Animated radar panels
- Clean cards and command-console navigation
- No childish visuals
- No marketing landing page
- First screen is the actual dashboard

Sections:

- Cluster Context Selector
- Scan Control
- Capacity Overview
- Workload Waste
- Node Efficiency
- Karpenter NodePools
- NodeClaims
- AI Recommendations
- YAML Patch Preview
- Recent Scans

Features:

- Select kubeconfig context
- Select namespace or all namespaces
- Fast scan vs deep scan
- Scan progress
- Loading state
- Empty state
- Error state
- Confidence and risk badges
- Copy kubectl command
- Copy YAML patch

Reliability:

- Handle kubectl missing
- Handle kubeconfig missing
- Handle cluster unreachable
- Handle metrics-server unavailable
- Handle Karpenter not installed
- Handle no unhealthy workloads
- Handle large clusters
- Handle command timeout
- Handle permission denied
- Handle empty namespaces

Documentation:

- README
- Architecture
- Scan flow
- Karpenter analysis
