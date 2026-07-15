# Platform Kernel Contract Pack

**Module:** `iana.kernel` · **Contract:** `1.0` · **Authority:** [03](../03_ARCHITECTURE.md), [04](../04_PLATFORM.md), [19](../19_CAPABILITY_SYSTEM.md), [24](../24_SECURITY_AND_PERMISSIONS.md), [26](../26_EVENT_SYSTEM.md)

## Purpose, ownership, and non-goals
The Kernel supplies composition, lifecycle, capability discovery, event transport abstractions, scheduling primitives, configuration access, health, and stable runtime contracts. It owns runtime registrations and operational metadata, not user domain records. It MUST NOT contain product workflows, reasoning policy, renderer policy, provider-specific logic, or cross-module data access.

## Responsibilities and boundary
Validate manifests; compose modules through explicit ports; start and stop in dependency order; enforce capability grants; propagate cancellation/deadlines/correlation; expose health; route versioned events; isolate adapter failures. Infrastructure implements Kernel-owned ports.

## Public APIs, inputs, and outputs
All calls use `RequestContext{actor, grants, correlationId, causationId?, deadline, locale?}` and return `Result<T, KernelError>`.
- `registerModule(ModuleManifest) -> RegistrationReceipt`
- `resolveCapability(CapabilityId, Principal) -> CapabilityHandle`
- `publish(EventEnvelope) -> PublishReceipt`
- `subscribe(EventFilter, ConsumerDescriptor) -> SubscriptionHandle`
- `schedule(JobDescriptor) -> ScheduleReceipt`; `cancelSchedule(ScheduleId) -> Outcome`
- `readConfig(ConfigKey) -> ConfigValue`; `health() -> HealthReport`
- Lifecycle: `initialize()`, `start()`, `quiesce(deadline)`, `stop(deadline)`.

Inputs MUST be schema-versioned, bounded, authenticated, and validated before side effects. Outputs include stable IDs, versions, timestamps, and provenance references. Registration and publish operations are idempotent by supplied key. Cancellation and deadlines propagate to adapters.

## Data, invariants, and lifecycle
Owned data: module/capability registrations, subscription cursors, schedule metadata, config metadata, health state. Secrets are references, never values in general configuration. A capability has one semantic owner; duplicate incompatible registrations fail. State transitions: `discovered → validated → initialized → running → quiescing → stopped|failed`. Data plus outbox writes are atomic; migrations are versioned, resumable, and reversible where feasible.

## Events
Publishes `kernel.module-registered.v1`, `kernel.module-started.v1`, `kernel.module-stopped.v1`, `kernel.module-failed.v1`, `kernel.schedule-triggered.v1`. Payloads contain IDs/status only. Consumes no product events; adapters may deliver registered envelopes. Ordering is per aggregate where available; delivery is at least once; event ID deduplicates.

## Dependencies and capabilities
Requires clock, ID generator, durable store, transactional outbox, event transport, scheduler, secret/config provider, logger/metrics/tracer—all injected ports. Exposes `kernel.lifecycle.v1`, `kernel.events.v1`, `kernel.scheduler.v1`, `kernel.configuration.v1`, `kernel.health.v1`. Forbidden: bounded-context repositories, UI/renderers, model providers, direct network clients, mutable global service locators.

## Security and permissions
Deny unregistered capabilities. Registration/config/schedule administration requires explicit administrative grants; publish requires producer ownership; subscribe requires event and data-scope grants. Validate signed module metadata where configured. Encrypt persisted metadata; minimize actor and payload data. Administrative changes emit immutable audit receipts.

## Configuration
`kernel.startupDeadline`, `kernel.shutdownDeadline`, `kernel.event.maxPayloadBytes`, `kernel.delivery.maxAttempts`, `kernel.delivery.backoff`, `kernel.health.timeout`, and queue/concurrency bounds. Values have safe finite defaults, validated ranges, and reload only when declared dynamic. Secret values have no defaults and are never returned by `readConfig`.

## Failure handling and observability
Errors: `INVALID_MANIFEST`, `DUPLICATE_OWNER`, `CAPABILITY_DENIED`, `DEPENDENCY_UNAVAILABLE`, `DEADLINE_EXCEEDED`, `PUBLISH_FAILED`, `CONFIG_INVALID`, `LIFECYCLE_CONFLICT`. Retry only transient idempotent adapter failures within bounds; quarantine poison events; fail startup atomically or report degraded optional capability. Log module, operation, result, duration, safe IDs, correlation, and error code; never secrets/event bodies. Measure lifecycle duration, queue depth, delivery lag/retries/dead letters, denial count, and health.

## Performance and acceptance
Startup/shutdown, queues, payloads, subscriptions, schedules, retries, and concurrency MUST be bounded and cancellation-aware. Acceptance requires: deterministic dependency ordering; cycle rejection; single capability ownership; denied ungranted resolution; atomic outbox; replay deduplication; graceful quiescence; secret redaction; degraded optional-adapter reporting.

## Test cases
Unit: manifest/schema/state transitions. Contract: each injected port and envelope version. Integration: store/outbox/transport and crash replay. Architecture: no outward/domain dependencies. Security: grant matrix, spoofed producer, secret redaction. Migration: restart mid-migration. Failure: transport outage and poison event. Performance: configured queue/concurrency/deadline limits.

## Examples
Success: register `iana.memory`, resolve its granted query capability, publish a minimized registration fact. Denial: an ungranted renderer resolving `commitments.write.v1` receives `CAPABILITY_DENIED` with no existence leak. Replay: the same event ID advances a consumer cursor once.

## Extension points
New scheduler, transport, store, telemetry, and configuration adapters MAY implement existing ports. New lifecycle states, bypass grants, global mutable registries, or changed delivery semantics require a versioned contract and ADR.
