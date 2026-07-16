# Runtime and Deployments

## Initial runtime

The MVP uses:

- Kubernetes
- containerd
- OCI images
- Kubernetes Services
- an ingress or Gateway API implementation

Apollo users do not receive Kubernetes access.

## Generated Kubernetes resources

Each application service produces:

- Namespace or tenant grouping
- Deployment
- Service
- ConfigMap
- Secret
- NetworkPolicy
- ServiceAccount
- resource limits
- readiness probe
- liveness probe
- pod security settings

## Deployment workflow

```text
Validate release
  → generate runtime specification
  → apply Kubernetes resources
  → wait for replicas
  → wait for readiness
  → activate route
  → verify public endpoint
  → mark deployment active
  → drain previous generation
```

## Rolling deployment defaults

```text
maxUnavailable = 0
maxSurge = 1
startupTimeout = 5 minutes
healthVerification = 30 seconds
drainTimeout = 30 seconds
```

## Health checks

The MVP supports an HTTP health check:

```text
path
interval_seconds
timeout_seconds
success_threshold
failure_threshold
```

A release must not receive traffic until readiness succeeds.

## Failed deployments

When a deployment fails:

- the previous healthy release remains active
- the failed release remains inspectable
- logs remain available
- a stable failure reason is recorded
- temporary runtime resources are cleaned

## Scaling

Manual scaling changes desired replicas.

```bash
apollo scale --replicas 3
```

The reconciler applies the new replica count and reports observed replicas.

## Deletion

Deleting an application must remove:

- routes
- Kubernetes resources
- runtime secrets
- active workload references

Historical build, release, deployment, usage, and audit data may be retained according to policy.
