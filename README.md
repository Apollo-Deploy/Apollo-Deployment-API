# Apollo Deployment API

**Repository:** [github.com/Apollo-Deploy/Apollo-Deployment-API](https://github.com/Apollo-Deploy/Apollo-Deployment-API)

Apollo Deploy is an API-first application deployment platform inspired by Fly.io and Vercel.

The initial MVP focuses on one reliable workflow:

> Build a Dockerfile application, deploy it to a single region, expose it over HTTPS, stream logs, scale replicas, and roll back safely.

---

## Tech stack

| Layer | Technology | Role |
|-------|-----------|------|
| Control plane | Kotlin + Ktor | Platform intent, business rules, APIs |
| Systems plane | Zig | Host-level agents, build execution |
| Workload runtime | Kubernetes + containerd | Container scheduling and lifecycle |
| Build system | BuildKit | OCI image builds |
| Workflow engine | Temporal | Durable deployment workflows |
| Messaging | NATS JetStream | Commands and async events |
| Database | PostgreSQL | Platform state |
| Artifact storage | S3-compatible | Source archives, build artifacts |
| Image registry | OCI registry | Application images |

---

## MVP goal

A developer runs:

```bash
apollo login
apollo apps create example-api
apollo secrets set DATABASE_URL=postgres://...
apollo deploy
```

And receives:

```text
✓ Source uploaded
✓ Image built
✓ Release created
✓ Workload started
✓ Health checks passed
✓ HTTPS route active

https://example-api.apollo.run
```

Day-two operations also work out of the box:

```bash
apollo logs --follow
apollo scale --replicas 3
apollo deploy        # zero-downtime re-deploy
apollo rollback
```

---

## Architecture

Apollo Deploy is split into two planes with a strict boundary.

### Control plane (Kotlin)

Kotlin owns platform intent and business rules:

- REST and gRPC APIs
- Authentication and authorization
- Organizations and projects
- Applications and services
- Builds and releases
- Deployment workflow orchestration
- Scheduling policy
- Kubernetes reconciliation
- Domains and certificates
- Usage aggregation
- Billing enforcement
- Audit logging

### Systems plane (Zig)

Zig owns machine-level execution:

- Build supervision
- Workspace lifecycle
- BuildKit process management
- Build cancellation and timeouts
- Node registration
- Host health and capacity reporting
- Local event buffering
- System telemetry

**Core rule:** Kotlin decides what should exist. Zig safely performs host-level work and reports what actually happened.

---

## Repository layout

```text
apollo-deployment-api/
├── apps/
│   ├── platform-api/          # Core control-plane API
│   ├── deployment-worker/     # Temporal worker for deployment workflows
│   ├── build-coordinator/     # Manages build queues and nodes
│   ├── runtime-reconciler/    # Kubernetes state reconciliation
│   ├── usage-aggregator/      # Collects and rolls up usage metrics
│   ├── admin-api/             # Internal ops API
│   ├── dashboard/             # Web UI
│   └── cli/                   # apollo CLI
│
├── kotlin/
│   ├── domain/                # Core domain models and logic
│   ├── persistence/           # Database access layer
│   ├── temporal/              # Workflow and activity definitions
│   ├── messaging/             # NATS publishers and consumers
│   ├── kubernetes/            # K8s client and reconcilers
│   ├── security/              # Auth, JWT, RBAC
│   └── observability/         # Metrics, tracing, structured logging
│
├── zig/
│   ├── build-executor/        # BuildKit supervision and execution
│   ├── host-agent/            # Node registration and health reporting
│   └── common/                # Shared Zig utilities
│
├── schemas/
│   ├── protobuf/              # Service-to-service contracts
│   ├── openapi/               # Public API specs
│   ├── events/                # NATS event schemas
│   └── fixtures/              # Test data
│
├── infrastructure/
│   ├── terraform/             # Cloud provisioning
│   ├── kubernetes/            # K8s manifests and Helm charts
│   ├── local/                 # Local dev stack (Docker Compose / Task)
│   └── monitoring/            # Grafana, Prometheus, alerting
│
├── integration-tests/
│   ├── deployment/
│   ├── builds/
│   ├── domains/
│   └── failure/
│
└── docs/                      # Architecture and implementation docs
```

---

## Documentation

Follow these documents in implementation order:

| # | Document |
|---|----------|
| 0 | [MVP scope](docs/00-mvp-scope.md) |
| 1 | [System architecture](docs/01-architecture.md) |
| 2 | [Domain model](docs/02-domain-model.md) |
| 3 | [Kotlin control plane](docs/03-kotlin-control-plane.md) |
| 4 | [Zig systems plane](docs/04-zig-systems-plane.md) |
| 5 | [Build pipeline](docs/05-build-pipeline.md) |
| 6 | [Runtime and deployments](docs/06-runtime-and-deployments.md) |
| 7 | [Routing, domains, and TLS](docs/07-routing-domains-and-tls.md) |
| 8 | [Observability, usage, and billing](docs/08-observability-usage-and-billing.md) |
| 9 | [Security and isolation](docs/09-security-and-isolation.md) |
| 10 | [Infrastructure](docs/10-infrastructure.md) |
| 11 | [Implementation roadmap](docs/11-roadmap.md) |
| 12 | [Protocols and events](docs/12-protocols-and-events.md) |
| 13 | [Testing and reliability](docs/13-testing-and-reliability.md) |
| 14 | [Definition of done](docs/14-definition-of-done.md) |

---

## MVP scope

### In scope

- Single-region deployments
- Dockerfile-based builds
- One HTTP service per application
- Automatic `*.apollo.run` domains
- Custom domains with automatic HTTPS
- Manual replica scaling
- Health checks
- Deployment and runtime log streaming
- Deployment history and rollbacks
- Basic usage limits

### Out of scope (post-MVP)

- Multi-region deployments
- Firecracker microVMs
- Persistent volumes
- Managed databases
- TCP/UDP services
- Scale to zero / autoscaling
- GPU support
- Private tenant networks
- Custom eBPF networking
- Custom edge proxy
- Global Anycast
- Buildpacks or Nixpacks

---

## Local development

Start the full local stack with:

```bash
make dev
```

This brings up:

- PostgreSQL
- Temporal (+ Temporal UI)
- NATS JetStream
- S3-compatible object storage (MinIO)
- Local OCI registry
- Local Kubernetes cluster (k3s or kind)

---

## v0.1 private beta checklist

`v0.1` ships when a new user can complete all of the following without touching the database or running kubectl:

- [ ] Create an account and organization
- [ ] Install and authenticate the CLI
- [ ] Create an application
- [ ] Set secrets
- [ ] Deploy a Dockerfile
- [ ] Watch live build output
- [ ] Access the application over HTTPS
- [ ] View runtime logs
- [ ] Scale replicas
- [ ] Deploy a second release without downtime
- [ ] Roll back to a previous release
- [ ] Add a custom domain
- [ ] View basic usage
