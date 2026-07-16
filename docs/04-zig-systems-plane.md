# Zig Systems Plane

## MVP responsibilities

Zig is used where Apollo interacts closely with the host operating system.

The MVP includes two Zig services:

- build executor
- host agent

## Build executor

The build executor:

1. Claims a build command.
2. Validates the command.
3. Creates a temporary workspace.
4. Downloads the source archive.
5. Verifies its checksum and size.
6. Safely extracts the archive.
7. Creates short-lived registry credentials.
8. Starts `buildctl`.
9. Streams logs.
10. Enforces limits and timeout.
11. Handles cancellation.
12. Reports the image digest.
13. Deletes credentials.
14. Cleans the workspace.
15. Acknowledges the command.

It does not implement Dockerfile builds itself.

BuildKit remains responsible for:

- parsing Dockerfiles
- build graph execution
- layer caching
- image creation
- registry pushes

## Host agent

The host agent runs on every platform node.

MVP responsibilities:

- register the node
- report agent version
- report CPU capacity
- report memory capacity
- report disk capacity
- report pressure conditions
- report BuildKit health
- report container runtime health
- buffer events during temporary outages
- expose a local health endpoint

The MVP host agent does not start customer containers directly.

Kubernetes owns customer workload lifecycle until a later data-plane phase.

## Shared Zig modules

```text
zig/common/
├── configuration/
├── identifiers/
├── logging/
├── messaging/
├── protocol/
├── retry/
├── shutdown/
└── telemetry/
```

## Operational requirements

- graceful shutdown
- bounded memory use
- structured JSON logs
- exponential retry backoff
- no permanent credentials on disk
- atomic workspace cleanup
- command deduplication
- version reporting
- heartbeat expiry
- local event buffering
