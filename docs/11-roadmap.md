# Implementation Roadmap

## Phase 0 — Architecture contract

### Week 1

Deliver:

- architecture decision records
- deployment state machine
- API and event conventions
- Protobuf package
- local development stack
- CI foundation

Exit condition:

All core dependencies start locally through one documented command.

## Phase 1 — Platform foundation

### Weeks 2–3

Deliver:

- authentication
- organizations
- projects
- database migrations
- authorization
- idempotency
- audit interface
- Kotlin service foundation
- Zig shared foundation
- development infrastructure

Exit condition:

Kotlin and Zig exchange compatible protocol fixtures.

## Phase 2 — Application and release model

### Weeks 4–5

Deliver:

- applications
- services
- secrets
- builds
- releases
- deployments
- state transitions
- initial API surface

Exit condition:

The full deployment domain exists without executing builds or workloads.

## Phase 3 — Build pipeline

### Weeks 6–7

Deliver:

- source uploads
- build workflow
- build queue
- Zig build executor
- BuildKit integration
- build logs
- cancellation
- timeouts
- OCI publishing
- release creation

Exit condition:

`apollo deploy` produces an immutable image-backed release.

## Phase 4 — Runtime and deployments

### Weeks 8–10

Deliver:

- Kubernetes runtime adapter
- runtime reconciler
- deployment Temporal workflow
- health checks
- rolling deployments
- scaling
- rollbacks
- Zig host agent

Exit condition:

Apollo builds and runs a Dockerfile application in one region.

## Phase 5 — Routing and TLS

### Weeks 11–12

Deliver:

- platform domains
- custom domains
- DNS verification
- automatic certificates
- route activation
- public endpoint verification

Exit condition:

Customers can access deployed applications over HTTPS.

## Phase 6 — Operations and security

### Weeks 13–14

Deliver:

- runtime logs
- usage events
- usage aggregation
- quotas
- audit events
- workload restrictions
- secret hardening
- admin suspension controls

Exit condition:

The platform is suitable for a controlled private beta.

## Phase 7 — Hardening

### Weeks 15–16

Deliver:

- failure testing
- load testing
- runbooks
- backup verification
- beta limits
- alerting
- operational dashboards

Exit condition:

The definition-of-done user journey works without manual intervention.
