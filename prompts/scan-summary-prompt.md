# Scan Summary Prompt

Summarize the Kubernetes capacity scan for AI explanation.

Include only:

- Metrics availability
- Highest severity workload findings
- Namespace waste risks
- Node efficiency summary
- Node pressure findings
- Karpenter enabled state
- Karpenter findings
- Pending pod evidence
- Existing YAML patch previews

Exclude:

- Secrets
- Raw environment variables
- Full pod specs
- Full logs
- Service account tokens
- Certificate data
- kubeconfig contents

The summary must preserve enough evidence for the AI to cite findings accurately.

