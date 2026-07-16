# Definition of Done

Apollo Deploy `v0.1` is complete only when the complete customer journey works without direct operator intervention.

## Customer journey

A new user can:

1. Create an account.
2. Create an organization.
3. Install the CLI.
4. Authenticate the CLI.
5. Create an application.
6. Add encrypted secrets.
7. Deploy a Dockerfile.
8. Stream build logs.
9. Receive an HTTPS platform domain.
10. View runtime logs.
11. Scale replicas.
12. Deploy a second release without downtime.
13. Roll back.
14. Add a custom domain.
15. View basic usage.

## Required CLI flow

```bash
apollo login
apollo apps create example-api
apollo secrets set DATABASE_URL=...
apollo deploy
apollo logs --follow
apollo scale --replicas 3
apollo deploy
apollo rollback
```

## Functional acceptance

- A valid Dockerfile builds successfully.
- The image is stored by immutable digest.
- A release is immutable.
- A deployment survives worker restarts.
- Failed health checks do not replace healthy traffic.
- Rollback creates a new deployment.
- Platform HTTPS is automatic.
- Custom-domain ownership is verified.
- Secret values are not retrievable.
- Tenant authorization protects every resource.
- Usage ingestion is idempotent.
- Deleting an application cleans active infrastructure.

## Operational acceptance

- API readiness and liveness checks work.
- Build and runtime failures produce stable error codes.
- Critical queues and workflows are monitored.
- PostgreSQL recovery is documented and tested.
- Operators can suspend an application.
- Operators can drain a node.
- Operators can pause deployments.
- Audit events exist for sensitive actions.
- Staging follows the production runtime path.

## Not required for v0.1

The release is not blocked by:

- multi-region support
- Firecracker
- persistent volumes
- managed databases
- TCP or UDP services
- autoscaling
- scale to zero
- custom edge proxy
- global Anycast
- GPUs
- preview environments
