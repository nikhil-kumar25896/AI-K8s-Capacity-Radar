# 02 - kubectl Scanner Prompt

Implement Kubernetes context and workload scanning.

Constraints:

- Use `kubectl` only.
- Do not use Kubernetes Python SDK.
- Use subprocess safely.
- Support `KUBECONFIG_PATH`.
- Add timeout handling.
- Return structured stdout, stderr, status, and errors.
- Never expose secrets.

Endpoints:

- GET `/contexts`
- GET `/contexts/{context_name}/namespaces`
- POST `/scan/context`
- POST `/scan/workloads`

Context scanner should discover:

- Context name
- Cluster name
- User name
- Current context
- Reachability
- Kubernetes server version

Workload scanner should collect:

- Pods
- Deployments
- StatefulSets
- DaemonSets
- Namespaces

Detect:

- Missing CPU requests
- Missing memory requests
- Missing CPU limits
- Missing memory limits
- OOMKilled pods
- High restart counts
- Pending pods
- CrashLoopBackOff pods

Return:

```json
{
  "summary": {},
  "namespaces": [],
  "workloads": [],
  "findings": []
}
```

Each finding must include severity, namespace, workload name, workload kind, container name, finding type, evidence, and recommendation.

