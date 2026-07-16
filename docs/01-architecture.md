# System Architecture

## Overview

Apollo Deploy contains a global control plane and a regional execution environment.

```text
Developer
  │
  ├── Dashboard
  └── CLI
       │
       ▼
Kotlin control plane
  ├── Platform API
  ├── Deployment worker
  ├── Build coordinator
  ├── Runtime reconciler
  ├── Domain service
  └── Usage aggregator
       │
       ├── PostgreSQL
       ├── Temporal
       ├── NATS JetStream
       ├── Object storage
       └── OCI registry
              │
              ▼
Regional environment
  ├── Zig build executor
  ├── Zig host agent
  ├── BuildKit
  ├── Kubernetes
  ├── containerd
  ├── ingress controller
  └── log collector
```

## Control plane

The control plane stores desired state.

Examples:

```text
Application should run release rel_123.
Application should have three replicas.
Domain api.example.com should route to service svc_123.
```

The control plane must not depend on direct SSH operations.

## Execution plane

The execution environment reports observed state.

Examples:

```text
Deployment has two of three replicas ready.
Build worker is out of disk.
Application instance exited with status 137.
Certificate is not ready.
```

## Desired and observed state

Resources that interact with infrastructure should contain:

```text
desired_generation
observed_generation
status
status_reason
```

A resource is converged when:

```text
desired_generation == observed_generation
```

## Deployment lifecycle

```text
CREATED
  → BUILDING
  → BUILD_SUCCEEDED
  → RELEASE_CREATED
  → DEPLOYING
  → VERIFYING
  → ROUTING
  → ACTIVE
```

Terminal or failure states include:

```text
BUILD_FAILED
DEPLOY_FAILED
HEALTH_CHECK_FAILED
CANCELLED
ROLLED_BACK
SUPERSEDED
```

## Reliability principles

- Every command is idempotent.
- Every external operation has a timeout.
- Every workflow can be retried.
- Every state transition is persisted.
- Every asynchronous action has a correlation ID.
- Infrastructure state is reconciled periodically.
- The database is never queried in the HTTP request routing path.
- Releases refer to immutable image digests.
