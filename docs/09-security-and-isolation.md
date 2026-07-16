# Security and Isolation

## Threat model

Apollo runs untrusted customer code.

The MVP must assume customer applications and Dockerfile build steps may be malicious.

## Runtime restrictions

Reject workloads requesting:

- privileged containers
- host networking
- host PID namespace
- host IPC namespace
- host path mounts
- unsafe Linux capabilities
- unrestricted root execution where avoidable

Require:

- CPU limits
- memory limits
- ephemeral storage limits
- network policies
- immutable image digests
- non-root execution where supported
- restricted service accounts
- read-only root filesystem where compatible

## Build restrictions

Build workers must not have:

- platform database credentials
- production workload credentials
- cloud administrator credentials
- unrestricted access to internal control-plane services

Build credentials must be:

- scoped
- short-lived
- removed after execution

## Secrets

- Secret values are encrypted at rest.
- Envelope encryption should use a KMS or Vault-managed key.
- Secret values are never returned after creation.
- Logs must avoid printing secret values.
- Runtime resources reference immutable secret versions.
- Secret access creates audit events where practical.

## Service communication

Internal communication must use authenticated identities.

Potential mechanisms:

- mTLS
- signed service tokens
- workload identity
- short-lived credentials

## Multi-tenancy

Every database query involving tenant data must include organization ownership.

Every log, build, deployment, domain, and usage endpoint must verify tenant access.

## Administrative controls

Provide:

- organization suspension
- application suspension
- deployment pause
- node maintenance mode
- build queue pause
- audit trail
- emergency credential rotation
