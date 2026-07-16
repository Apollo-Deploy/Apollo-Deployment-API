# Kotlin Control Plane

## Responsibilities

Kotlin owns platform intent, policy, workflows, APIs, and persistent state.

## Services

### Platform API

Responsible for:

- authentication
- authorization
- organizations
- projects
- applications
- services
- secrets
- builds
- releases
- deployments
- scaling
- domains
- logs
- usage
- administrative actions

### Deployment worker

Runs Temporal workflows for:

- builds
- deployments
- rollbacks
- cancellation
- application deletion
- domain provisioning

### Build coordinator

Responsible for:

- source upload URLs
- build queueing
- build worker selection
- build command dispatch
- build result validation
- release creation

### Runtime reconciler

Compares Apollo desired state with Kubernetes observed state.

It handles:

- missing workloads
- incorrect replica counts
- deleted services
- failed pods
- stale deployment generations
- orphaned resources

### Domain service

Responsible for:

- domain normalization
- ownership verification
- route creation
- certificate status
- domain conflict protection
- domain deletion

### Usage aggregator

Responsible for:

- usage event ingestion
- deduplication
- aggregation
- quota evaluation
- billing export

## Suggested Kotlin module layout

```text
kotlin/
├── domain/
│   ├── application/
│   ├── build/
│   ├── release/
│   ├── deployment/
│   ├── domain/
│   └── usage/
├── persistence/
├── temporal/
├── messaging/
├── kubernetes/
├── security/
└── observability/
```

## Runtime abstraction

```kotlin
interface RuntimeProvider {
    suspend fun applyWorkload(spec: WorkloadSpec): RuntimeWorkload
    suspend fun getWorkload(id: WorkloadId): RuntimeWorkload?
    suspend fun deleteWorkload(id: WorkloadId)
    suspend fun scaleWorkload(id: WorkloadId, replicas: Int)
    suspend fun getInstances(id: WorkloadId): List<RuntimeInstance>
}
```

The MVP implementation is:

```text
KubernetesRuntimeProvider
```

Future implementations may include:

```text
ContainerdRuntimeProvider
FirecrackerRuntimeProvider
```

## API design rules

- All state-changing endpoints accept idempotency keys.
- All IDs are opaque.
- Secret values are never returned after creation.
- Releases are immutable.
- Pagination is cursor based.
- Errors use a stable machine-readable code.
- Every response includes a request or trace ID.
- Authorization always includes organization ownership.
