# Observability, Usage, and Billing

## Logs

The platform exposes two log streams:

### Build logs

Associated with a build.

Requirements:

- ordered
- timestamped
- streamable while active
- retained after completion
- size limited
- protected by organization authorization

### Runtime logs

Associated with:

- application
- service
- deployment
- instance
- timestamp

CLI examples:

```bash
apollo logs
apollo logs --follow
apollo logs --deployment dep_123
apollo logs --since 30m
```

## Metrics

Track:

- API request rate
- API latency
- build queue depth
- build duration
- build failures
- deployment duration
- deployment failures
- ready replicas
- container restarts
- NATS consumer lag
- Temporal workflow failures
- certificate failures
- node capacity
- node pressure

## Usage events

Initial usage types:

```text
runtime_cpu_seconds
runtime_memory_byte_seconds
network_ingress_bytes
network_egress_bytes
build_cpu_seconds
build_duration_seconds
build_storage_byte_seconds
```

Each usage event includes:

```text
event_id
organization_id
resource_id
resource_type
usage_type
quantity
started_at
ended_at
region
source
schema_version
```

## Idempotency

Usage ingestion must deduplicate by `event_id`.

Aggregation must be reproducible from raw usage events.

## MVP limits

The MVP may enforce:

- maximum applications
- maximum replicas per application
- maximum CPU per service
- maximum memory per service
- monthly build minutes
- monthly runtime hours
- organization build concurrency

## Billing boundary

Zig and infrastructure collectors produce raw usage.

Kotlin:

- validates
- deduplicates
- aggregates
- applies plan rules
- exports billable usage
