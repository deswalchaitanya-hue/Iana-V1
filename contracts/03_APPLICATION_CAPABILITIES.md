# Application Capabilities Contract Pack

**Module:** `iana.application` · **Contract:** `1.0` · **Authority:** [03](../03_ARCHITECTURE.md), [12](../12_APPLICATION_CAPABILITIES.md), [19](../19_CAPABILITY_SYSTEM.md), [24](../24_SECURITY_AND_PERMISSIONS.md)

## Purpose, ownership, and non-goals
Application Capabilities expose stable use cases to surfaces, engines, automations, plugins, and adapters. The module owns orchestration, boundary validation, authorization coordination, idempotency, unit-of-work scope, and DTO mapping. It owns no domain aggregate schema, rendering, provider implementation, or reasoning policy.

## Responsibilities and boundary
Authenticate request context; check grants and consent; validate commands/queries; invoke one or more owner ports without foreign writes; coordinate transaction or compensation; persist idempotency outcomes; return minimized DTOs and receipts; publish completed facts through owner outboxes.

## Public APIs, inputs, and outputs
Generic contract:
- `execute(CommandEnvelope<T>) -> CommandResult<R>`
- `query(QueryEnvelope<T>) -> QueryResult<R>`
- `describe(CapabilityId) -> CapabilityDescriptor`
- `authorize(CapabilityRequest) -> AuthorizationDecision`
Envelopes contain `schemaVersion`, operation/capability ID, actor, grants, idempotency key for commands, correlation/causation IDs, provenance, deadline, and bounded payload. Results contain status, safe DTO/receipt, version, warnings, and typed errors. Commands are idempotent; queries have no side effects. Deadlines/cancellation propagate.

## Data and invariants
Owned data: capability descriptors, grants/delegations references, idempotency records, orchestration/compensation receipts. Aggregate data stays with context owners. Same actor+capability+idempotency key+payload hash returns the stored outcome; mismatched reuse fails. Partial cross-context operations are explicit and compensatable; no distributed transaction is assumed. Retention is configurable and privacy-aware.

## Events
Publishes `application.capability-invoked.v1`, `application.capability-denied.v1`, `application.operation-completed.v1`, and `application.operation-compensated.v1` with minimized operational metadata. Consumes owner events only for orchestration/read-model completion registered by a capability. Delivery is at least once and deduplicated by event ID.

## Dependencies and capabilities
Requires Domain Core, Kernel capability/event/config/telemetry ports, identity/permission evaluator, context-owned command/query ports, provenance recording, and unit-of-work/idempotency ports. Exposes versioned capabilities registered by owners plus `application.describe.v1`. Forbidden: foreign repositories, renderer/UI code, direct provider/network/database clients, hidden ambient authority, direct engine internals.

## Security and permissions
Deny by default. Every operation declares required grants, data scopes, confirmation class, and audit class. Reauthorize at execution time; do not trust UI checks. Destructive/external/irreversible/material actions require explicit scoped confirmation. Minimize outputs by audience and prevent existence leaks. Credentials remain adapter-owned.

## Configuration
`application.maxCommandBytes`, `maxQueryBytes`, `maxPageSize`, `defaultDeadline`, `idempotencyRetention`, `maxCompensationAttempts`, and per-capability enablement/limits. Bounds are finite, validated, owner-scoped, and reloadable only when safe. Secrets are prohibited.

## Failure handling and observability
Errors: `VALIDATION_FAILED`, `UNAUTHENTICATED`, `PERMISSION_DENIED`, `CONFIRMATION_REQUIRED`, `CONFLICT`, `NOT_FOUND`, `DEADLINE_EXCEEDED`, `DEPENDENCY_UNAVAILABLE`, `COMPENSATION_REQUIRED`, `UNSUPPORTED_VERSION`. Retry transient idempotent dependencies only; store stable outcomes; compensate completed external steps in reverse order. Log capability, actor pseudonymous ID, outcome, duration, correlation, error, and confirmation class—not payloads or secrets. Measure latency, denial/conflict/error rates, idempotency hits, compensation, and dependency health.

## Performance and acceptance
Payload, page, fan-out, retries, concurrency, and deadlines are bounded per capability. Acceptance: queries are side-effect free; commands replay safely; unauthorized calls make no mutations; context ownership is preserved; confirmations cannot be replayed outside scope; partial failures are receipted; DTOs omit unauthorized fields.

## Test cases
Unit: validation/mapping/authorization/idempotency. Contract: every capability request/result schema. Integration: context ports, outbox, provenance, compensation. Architecture: no repositories/provider SDKs. Security: grant matrix, stale confirmation, confused deputy, existence leak. Migration/version: old envelopes. Replay/failure/performance: duplicate command, timeout, dependency outage, fan-out/page bounds.

## Examples
Success: `projects.create.v1` validates, authorizes, invokes the Projects owner, records provenance, and returns a receipt. Denial: an automation without `commitments.write` receives `PERMISSION_DENIED` and no record detail. Replay: identical command key returns the original receipt without a second event.

## Extension points
New owner-declared capabilities and policy evaluators MAY be registered. New ambient authority, bypass APIs, foreign schema access, unversioned envelopes, or silent best-effort writes are forbidden; changes to orchestration semantics require a contract version and ADR.
