# Surface Engine Contract Pack

**Module:** `iana.engine.surface` · **Contract:** `1.0` · **Authority:** [17](../17_SURFACE_ENGINE.md), [18](../18_SURFACES.md), [12](../12_APPLICATION_CAPABILITIES.md)

## Purpose and responsibilities
Produce renderer-neutral, accessible, privacy-aware surface models and interaction intents from authorized application DTOs. Own navigation/presentation policies, component semantics, disclosure states, and view-model caching—not platform widgets, domain data, authorization truth, or business side effects. Renderers remain replaceable adapters.

## Public APIs, inputs, outputs, and schemas
`compose(SurfaceRequest)->SurfaceModel`; `handle(InteractionIntent)->InteractionResult`; `refresh(SurfaceId, Revision)->SurfacePatch`; `describe(SurfaceType)->SurfaceDescriptor`.
Requests include actor/grants, surface type, route/context refs, viewport/accessibility/locale preferences, data freshness, correlation, deadline. Models are versioned semantic trees with stable node IDs, roles/labels, actions as capability intents, loading/empty/error/redacted states, provenance/explanation links, revision. Interactions carry surface/revision/node/action IDs, bounded validated values, idempotency for commands; results return patch/navigation/confirmation/error. Stale revisions fail or reconcile safely.

## Owned data and invariants
Owns surface descriptors, presentation policy versions, ephemeral view state, derived caches, interaction receipts, outbox if durable interaction facts are required. Domain/application DTOs remain external. Every actionable control maps to a declared capability and accessible name; hidden/redacted data is not embedded; render model is deterministic for inputs/policy; view caches are principal/purpose partitioned and rebuildable.

## Events
Publishes `engine.surface.composed.v1`, `engine.surface.interaction-invoked|failed.v1` with IDs/type/revision/capability/status only. Consumes authorized application/read-model invalidation and privacy events for refresh; renderers receive patches via ports, not domain events. At least once where durable; event-ID dedupe and revision ordering.

## Dependencies, capabilities, and prohibitions
Requires Domain Core; Kernel config/events/telemetry/cache; Application query/command capabilities; permission/redaction; Context/Provenance explanation capabilities as needed. Exposes `engine.surface.compose.v1`, `.interact.v1`, `.refresh.v1`. Forbidden: repositories, domain mutation, provider/model SDKs, platform-specific widgets in contracts, client-only authorization, concealed consequential action, inaccessible semantic output, unbounded trees/lists.

## Security and configuration
Deny by default; models contain only fields permitted for actor/purpose/surface. Server/application reauthorizes interactions. Sensitive values default concealed; external/destructive actions show consequence and confirmation state. Prevent injection by typed text/URI/action schemas and renderer escaping. Config: node/depth/list/page/cache/payload/deadline bounds, surface policy versions, supported locales/accessibility modes; finite, validated, no secrets.

## Failures, observability, and performance
Errors: `UNKNOWN_SURFACE`, `INVALID_INTENT`, `STALE_REVISION`, `PERMISSION_DENIED`, `CONFIRMATION_REQUIRED`, `DATA_UNAVAILABLE`, `MODEL_LIMIT`, `DEADLINE_EXCEEDED`. Return explicit safe error/partial states; retry only idempotent queries; command results rely on Application idempotency. Log surface/type/revision/node/capability/status/duration—not field values. Metrics: compose/interaction latency, model size, stale revisions, redactions, accessibility validation, errors/cache.

## Acceptance criteria and tests
Prove renderer neutrality, complete semantic/accessibility labels and states, capability mapping, server reauthorization, redaction/no hidden embedding, stale-revision safety, command replay, bounded trees/lists, deterministic composition, responsive preference representation, cache isolation/invalidation, injection defense, and telemetry redaction. Test model schemas/snapshots, renderer contracts, Application integration, permission changes, stale/replay, unavailable data, accessibility automation, localization, max-model performance.

## Examples and extension points
Success: compose an inbox model with accessible triage intents and pagination. Denial: unauthorized memory detail is absent, not merely visually hidden. Stale interaction returns refresh patch without executing. New surface types, renderer adapters, semantic components, and presentation policies MAY be versioned; platform coupling, embedded business logic, or client authorization is forbidden.
