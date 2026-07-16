# Apollo Deploy

Apollo Deploy is an API-first application deployment platform inspired by platforms such as Fly.io and Vercel.

The initial MVP focuses on one reliable workflow:

> Build a Dockerfile application, deploy it to a single region, expose it over HTTPS, stream logs, scale replicas, and roll back safely.

The platform uses:

- **Kotlin and Ktor** for the control plane and business logic.
- **Zig** for host-level agents and build execution.
- **Kubernetes and containerd** as the initial workload runtime.
- **BuildKit** for OCI image builds.
- **Temporal** for durable deployment workflows.
- **NATS JetStream** for commands and asynchronous events.
- **PostgreSQL** for platform state.
- **S3-compatible object storage** for source archives and artifacts.
- **An OCI registry** for application images.

## MVP goal

A developer should be able to run:

```bash
apollo login
apollo apps create example-api
apollo secrets set DATABASE_URL=...
apollo deploy
```

And receive:

```text
✓ Source uploaded
✓ Image built
✓ Release created
✓ Workload started
✓ Health checks passed
✓ HTTPS route active

https://example-api.apollo.run
```

The following must also work:

```bash
apollo logs --follow
apollo scale --replicas 3
apollo deploy
apollo rollback
```

## Architecture principle

Apollo Deploy is separated into two primary planes:

### Kotlin control plane

Kotlin owns platform intent and business rules:

- APIs
- authentication and authorization
- organizations and projects
- applications and services
- builds and releases
- deployment workflows
- scheduling policy
- Kubernetes reconciliation
- domains and certificates
- usage aggregation
- billing enforcement
- auditing

### Zig systems plane

Zig owns machine-level execution:

- build supervision
- workspace lifecycle
- BuildKit process management
- build cancellation and timeouts
- node registration
- host health and capacity
- local event buffering
- system telemetry

The core rule is:

> Kotlin decides what should exist. Zig safely performs host-level work and reports what actually happened.

## Repository layout

```text
apollo-deploy/
├── apps/
│   ├── platform-api/
│   ├── deployment-worker/
│   ├── build-coordinator/
│   ├── runtime-reconciler/
│   ├── usage-aggregator/
│   ├── admin-api/
│   ├── dashboard/
│   └── cli/
│
├── kotlin/
│   ├── domain/
│   ├── persistence/
│   ├── temporal/
│   ├── messaging/
│   ├── kubernetes/
│   ├── security/
│   └── observability/
│
├── zig/
│   ├── build-executor/
│   ├── host-agent/
│   └── common/
│
├── schemas/
│   ├── protobuf/
│   ├── openapi/
│   ├── events/
│   └── fixtures/
│
├── infrastructure/
│   ├── terraform/
│   ├── kubernetes/
│   ├── local/
│   └── monitoring/
│
├── integration-tests/
│   ├── deployment/
│   ├── builds/
│   ├── domains/
│   └── failure/
│
└── docs/
```

## Documentation

Start with the MVP scope, then follow the documents in implementation order.

1. [MVP scope](docs/00-mvp-scope.md)
2. [System architecture](docs/01-architecture.md)
3. [Domain model](docs/02-domain-model.md)
4. [Kotlin control plane](docs/03-kotlin-control-plane.md)
5. [Zig systems components](docs/04-zig-systems-plane.md)
6. [Build pipeline](docs/05-build-pipeline.md)
7. [Runtime and deployments](docs/06-runtime-and-deployments.md)
8. [Routing, domains, and TLS](docs/07-routing-domains-and-tls.md)
9. [Observability, usage, and billing](docs/08-observability-usage-and-billing.md)
10. [Security and isolation](docs/09-security-and-isolation.md)
11. [Infrastructure](docs/10-infrastructure.md)
12. [Implementation roadmap](docs/11-roadmap.md)
13. [Protocols and events](docs/12-protocols-and-events.md)
14. [Testing and reliability](docs/13-testing-and-reliability.md)
15. [Definition of done](docs/14-definition-of-done.md)

## MVP boundaries

The MVP supports:

- one region
- Dockerfile deployments
- one HTTP service per application
- automatic platform domains
- custom domains
- automatic HTTPS
- manual replica scaling
- health checks
- deployment and runtime logs
- deployment history
- rollbacks
- basic usage limits

The MVP does not include:

- multi-region deployments
- Firecracker
- persistent volumes
- managed databases
- TCP or UDP services
- scale to zero
- autoscaling
- GPUs
- private tenant networks
- custom eBPF networking
- a custom edge proxy
- global Anycast
- buildpacks or Nixpacks

## Local development

The local environment should start these dependencies:

- PostgreSQL
- Temporal
- NATS JetStream
- S3-compatible object storage
- local OCI registry
- local Kubernetes cluster

The implementation should expose one documented command, such as:

```bash
make dev
```

or:

```bash
task dev
```

## Initial release target

Apollo Deploy `v0.1` is ready for private beta when a new user can:

1. Create an account and organization.
2. Install and authenticate the CLI.
3. Create an application.
4. configure secrets.
5. Deploy a Dockerfile.
6. Watch build output.
7. Access the application over HTTPS.
8. View runtime logs.
9. Scale replicas.
10. deploy a second release without downtime.
11. Roll back.
12. Add a custom domain.
13. View basic usage.

No step may require direct database edits or manual Kubernetes operations.
