# Domain Model

## Core resources

### Organization

Top-level tenant and billing boundary.

### Project

Logical grouping of applications.

### Application

User-facing deployed product.

### Service

Runnable HTTP component of an application.

The MVP permits one service per application.

### Build

Source-to-image execution.

### Release

Immutable combination of:

- OCI image digest
- runtime configuration version
- secret version references
- service configuration

### Deployment

Operation that rolls out a release.

### Instance

Observed running replica of a service.

### Route

Hostname-to-service mapping.

### Domain

User-owned hostname and verification state.

### Region

Geographic execution location.

### Node

Compute host capable of running builds or workloads.

## Important distinctions

```text
Build
  = produces an image

Release
  = immutable thing that can run

Deployment
  = operation that rolls out a release

Instance
  = one running replica
```

## Suggested tables

```text
users
organizations
organization_members
projects
applications
services
builds
releases
deployments
deployment_events
instances
regions
nodes
routes
domains
certificates
secret_versions
usage_events
usage_aggregates
idempotency_keys
audit_events
```

## Common columns

Mutable tenant resources should include:

```text
id
organization_id
version
created_at
updated_at
deleted_at
```

Operational resources should include:

```text
desired_generation
observed_generation
status
status_reason
```

## Release immutability

A release must never be edited after creation.

Changing any of the following produces a new release:

- image digest
- environment configuration
- secret references
- CPU
- memory
- internal port
- health check
- start command

## Rollbacks

A rollback creates a new deployment referencing an existing older release.

It must not mutate a failed deployment.

```text
release 5 active
release 6 failed
rollback deployment created for release 5
```
