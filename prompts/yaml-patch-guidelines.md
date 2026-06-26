# YAML Patch Guidelines

YAML patch previews are advisory and must not be applied automatically.

Good patch previews:

- Are minimal
- Include only the affected object
- Use clear placeholder values when exact values are uncertain
- Preserve existing selectors and labels
- Avoid deleting user configuration
- Include comments only when useful

Patch preview examples:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
  namespace: production
spec:
  template:
    spec:
      containers:
        - name: api
          resources:
            requests:
              cpu: "250m"
              memory: "512Mi"
            limits:
              cpu: "500m"
              memory: "1Gi"
```

Do not generate:

- `kubectl apply` commands
- destructive changes
- secret values
- unverified production values

