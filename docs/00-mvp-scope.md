# MVP Scope

## Objective

The initial Apollo Deploy MVP must provide a complete, reliable application deployment experience without attempting to replicate every Fly.io capability.

The primary user journey is:

```text
Source code
  → Dockerfile build
  → OCI image
  → immutable release
  → Kubernetes workload
  → health verification
  → HTTPS route
```

## Supported capabilities

### Accounts and tenancy

- User authentication
- Organizations
- Organization membership
- Projects
- Applications
- Basic organization roles

### Application configuration

- One HTTP service per application
- Internal application port
- CPU allocation
- Memory allocation
- Replica count
- Environment variables
- Encrypted secrets
- HTTP health check

### Builds

- Source archive upload
- Dockerfile builds
- Build arguments
- Build logs
- Build cancellation
- Build timeout
- OCI image publishing
- Immutable image digest capture

### Deployments

- Immutable releases
- Deployment history
- Rolling deployments
- Health verification
- Graceful draining
- Failed deployment protection
- Manual rollback
- Manual scaling

### Networking

- Automatic platform subdomain
- Automatic HTTPS
- Custom domains
- Domain ownership verification
- Certificate status
- HTTP and WebSocket traffic

### Operations

- Runtime logs
- Build logs
- Basic resource usage
- Application suspension
- Organization quotas
- Audit events
- Administrative inspection

## Not included

The MVP excludes:

- multiple deployment regions
- user-selectable regional redundancy
- Firecracker microVMs
- persistent storage
- managed databases
- raw TCP services
- UDP services
- IPv6 private tenant networking
- scale to zero
- request-triggered startup
- autoscaling
- GPUs
- cron jobs
- worker process groups
- custom edge networking
- global Anycast
- customer Kubernetes access
- preview environments
- GitHub App deployments
- buildpacks
- Nixpacks
- custom Terraform execution

## Product constraints

### One application service

An application has one HTTP service in the MVP.

```text
Application
  └── HTTP service
      ├── one image
      ├── one internal port
      ├── one replica count
      └── one health check
```

Multiple process groups can be introduced after the deployment model is stable.

### Stateless workloads

Applications must be treated as stateless.

Local container filesystems may be destroyed during:

- deployments
- rescheduling
- node replacement
- scaling
- maintenance

### Single region

All workloads run in one configured region.

The domain model should still include a `region_id` so that multi-region support can be added without redesigning the core resources.

## MVP completion test

The MVP is complete when this flow works without operator intervention:

```bash
apollo login
apollo apps create example-api
apollo secrets set DATABASE_URL=...
apollo deploy
apollo logs --follow
apollo scale --replicas 3
apollo deploy
apollo rollback
```
