# Protocols and Events

## Contract ownership

Kotlin and Zig communicate through versioned Protobuf contracts.

Neither side should import the other side's internal domain models.

## Command envelope

Every command includes:

```text
command_id
command_type
schema_version
correlation_id
causation_id
organization_id
resource_id
desired_generation
issued_at
expires_at
payload
```

## Event envelope

Every event includes:

```text
event_id
event_type
schema_version
source
occurred_at
correlation_id
causation_id
organization_id
resource_id
payload
```

## Build subjects

```text
apollo.commands.build.<region>
apollo.events.build
```

Build event types:

```text
build.assigned.v1
build.started.v1
build.log.v1
build.completed.v1
build.failed.v1
build.cancelled.v1
```

## Agent subjects

```text
apollo.events.agent
```

Agent event types:

```text
agent.registered.v1
agent.heartbeat.v1
agent.capacity.v1
agent.health.v1
agent.disconnected.v1
```

## Usage subjects

```text
apollo.events.usage
```

Usage event types:

```text
usage.runtime.cpu.v1
usage.runtime.memory.v1
usage.network.ingress.v1
usage.network.egress.v1
usage.build.duration.v1
```

## Delivery semantics

Assume at-least-once message delivery.

Consumers must handle:

- duplicate messages
- delayed messages
- reordered messages
- redelivery after crashes
- expired commands
- stale generations

## Deduplication

Commands are deduplicated by `command_id`.

Resource mutations also validate `desired_generation`.

Usage events are deduplicated by `event_id`.

## Compatibility

- Never reuse a field number.
- Add fields as optional.
- Version breaking message shapes.
- Keep protocol fixtures for Kotlin and Zig tests.
- Record the minimum supported agent version.
