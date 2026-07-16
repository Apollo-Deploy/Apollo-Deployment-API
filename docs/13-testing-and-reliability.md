# Testing and Reliability

## Test layers

### Unit tests

Cover:

- state transitions
- authorization
- quota calculations
- event validation
- archive validation
- retry policy
- domain normalization

### Contract tests

Verify:

- Kotlin and Zig Protobuf compatibility
- OpenAPI compatibility
- event schema compatibility
- error-code stability

### Integration tests

Cover:

- source upload
- BuildKit execution
- registry push
- release creation
- Kubernetes deployment
- health checks
- route activation
- scaling
- rollback
- domain verification

### Failure tests

Simulate:

- API restart during deployment
- Temporal worker restart
- NATS restart
- build executor crash
- BuildKit crash
- Kubernetes node loss
- registry outage
- object-storage outage
- build cancellation
- duplicate commands
- certificate failure
- full build disk
- crash-looping application
- decompression bomb
- path traversal archive

## Load-test targets

Initial engineering targets:

- 100 concurrent API users
- 20 concurrent builds
- 500 applications
- 1,000 running replicas
- 10,000 log lines per second
- 1,000 status reads per second
- 100 concurrent log streams

These are internal validation targets, not customer guarantees.

## Reliability rules

- Every workflow has a timeout.
- Every external call has retry rules.
- Every queue has backlog monitoring.
- Every service has readiness and liveness checks.
- Every state change is persisted.
- Every deployment is recoverable after worker restart.
- Every build workspace is eventually cleaned.
- Every infrastructure resource has an owner label.

## Runbooks

Required runbooks:

- stuck deployment
- failed build
- unavailable build workers
- registry outage
- cluster capacity exhaustion
- certificate failure
- NATS backlog
- Temporal workflow failure
- secret rotation
- compromised agent
- node draining
- customer abuse
