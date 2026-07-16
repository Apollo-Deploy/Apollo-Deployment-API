# Routing, Domains, and TLS

## Platform domains

Each application receives a platform hostname:

```text
<application-slug>.apollo.run
```

Application slugs must be unique within the platform hostname namespace.

## Route model

```text
Route
├── id
├── hostname
├── application_id
├── service_id
├── active_release_id
├── destination_port
├── generation
└── status
```

## Custom domain flow

```text
User adds domain
  → Apollo validates hostname
  → Apollo provides DNS instruction
  → user changes DNS
  → Apollo verifies ownership
  → certificate is issued
  → route becomes active
```

## Domain states

```text
PENDING_VERIFICATION
VERIFYING
VERIFIED
CERTIFICATE_PENDING
ACTIVE
FAILED
DELETING
```

## MVP routing implementation

Use an existing ingress or Gateway API implementation.

The Kotlin control plane creates routing resources.

The routing layer must not query PostgreSQL for every request.

## Domain safety

- Domain names are normalized before storage.
- Two organizations cannot claim the same hostname.
- Wildcard domains are disabled initially.
- Ownership must be rechecked when required.
- Deleting a domain removes its route.
- Certificate failures are visible to users.
- Platform domains remain available if a custom domain fails.

## Traffic activation

A new deployment route becomes active only after:

- required replicas are ready
- readiness checks pass
- the route has been created
- a public verification request succeeds
