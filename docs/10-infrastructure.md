# Infrastructure

## Global services

The MVP requires:

- Kotlin control-plane services
- PostgreSQL
- Temporal
- NATS JetStream
- S3-compatible object storage
- OCI registry
- KMS or Vault
- OpenTelemetry collector
- metrics backend
- log storage
- dashboard and CLI API endpoint

## Regional services

The initial region requires:

- Kubernetes cluster
- build node pool
- runtime node pool
- ingress controller
- certificate manager
- Zig build executor
- Zig host agent
- BuildKit
- log collector
- registry access
- object-storage access

## Node pools

### Control node pool

Runs trusted platform services.

### Runtime node pool

Runs customer applications.

### Build node pool

Runs customer Dockerfile builds.

Build and runtime workloads should not share nodes in production.

## Terraform responsibility

Terraform provisions:

- networks
- clusters
- node pools
- databases
- object storage
- registries
- load balancers
- DNS infrastructure
- NATS
- Temporal
- observability services

Terraform does not run for each customer deployment.

Customer workloads are created through the runtime adapter.

## Environments

Maintain separate:

- local
- development
- staging
- production

Staging should use the same runtime path as production.

## Backups

At minimum:

- PostgreSQL point-in-time recovery
- Temporal persistence backups
- registry retention policy
- object-storage versioning where available
- encryption-key backup and recovery procedure
