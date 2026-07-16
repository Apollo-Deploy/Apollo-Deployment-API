# Build Pipeline

## User flow

```text
CLI packages source
  → CLI requests upload URL
  → source uploaded to object storage
  → build created
  → Temporal workflow starts
  → build command dispatched
  → Zig build executor invokes BuildKit
  → image pushed to registry
  → digest returned
  → immutable release created
```

## Source upload

The CLI should create a deterministic archive while respecting:

- `.gitignore`
- `.apolloignore`
- maximum archive size
- symbolic link restrictions
- file permission normalization

The API returns a signed upload URL and source identifier.

## Build states

```text
QUEUED
  → ASSIGNED
  → PREPARING
  → BUILDING
  → PUSHING
  → SUCCEEDED
```

Failure and terminal states:

```text
FAILED
TIMED_OUT
CANCELLED
LOST
```

## Build command

A build command should include:

```text
command_id
build_id
organization_id
region
source_url
source_sha256
source_size_bytes
dockerfile_path
build_context_path
target_image
timeout_seconds
build_arguments
```

## Build isolation

The build environment requires:

- dedicated build node pool
- no production workload credentials
- short-lived registry credentials
- CPU limit
- memory limit
- ephemeral disk limit
- build timeout
- source size limit
- safe archive extraction
- restricted access to internal services
- automatic workspace cleanup
- no host Docker socket exposure

## Failure behavior

A build must produce a stable error code such as:

```text
SOURCE_DOWNLOAD_FAILED
SOURCE_CHECKSUM_MISMATCH
SOURCE_TOO_LARGE
UNSAFE_ARCHIVE
DOCKERFILE_NOT_FOUND
BUILD_TIMEOUT
BUILD_CANCELLED
BUILD_PROCESS_FAILED
REGISTRY_PUSH_FAILED
WORKER_LOST
```

## Build completion

A successful result includes:

```text
build_id
image_reference
image_digest
duration_ms
worker_id
cache_hit_summary
```

Release creation happens only after the control plane validates the digest.
