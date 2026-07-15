# Memory Contract Pack

**Module:** `iana.memory` · **Contract:** `1.0` · **Authority:** [05](../05_DOMAIN_MODEL.md), [07](../07_MEMORY.md), [11](../11_PROVENANCE_AND_RECEIPTS.md), [37](../37_PRIVACY_PRODUCT_CONTROLS.md)

## Purpose and responsibilities
Own durable user-approved or policy-permitted memory records, source links, corrections, retention, and bounded retrieval. Distinguish observation, user assertion, summary, and inference; preserve provenance and confidence. It is not raw conversation storage, a hidden profile, graph ownership, reasoning, or an authority to remember everything.

## Public APIs, inputs, outputs, and schemas
`capture(MemoryCommand)->MemoryReceipt`; `revise(MemoryId, Patch, expectedVersion)->Receipt`; `forget(MemoryId, reason, expectedVersion)->Receipt`; `get(MemoryId, Projection)->MemoryView`; `search(MemoryQuery)->MemoryPage`; `explain(MemoryId)->MemoryExplanation`.
Commands include actor, idempotency, provenance/evidence, class, content or approved structured value, confidence, sensitivity, retention policy, correlation, deadline. Search includes bounded filters, purpose, cursor, limit, and optional query representation through an injected retrieval port. Outputs identify source class, confidence, age, retention, redaction, and why selected. User correction overrides active interpretation while history remains auditable.

## Owned data and invariants
Owns memory records, revisions, tombstones, retrieval indexes/derived representations, retention metadata, and outbox. Derived indexes are rebuildable and not authoritative. Every active record has provenance and classification; inferred content cannot masquerade as user-authored; forgotten content is excluded immediately and purged per policy. Partition by user/tenant. Atomic record+outbox; versioned migrations and index rebuilds.

## Events
Publishes `memory.record-captured|revised|forgotten.v1`, `memory.retention-expired.v1`, `memory.index-rebuilt.v1`; minimized payloads use IDs/class/version/provenance. Consumes provenance invalidation, privacy deletion, and authorized source-correction events. At least once, per-record ordering, dedupe by event ID.

## Dependencies, capabilities, and prohibitions
Requires Domain Core; Kernel storage/outbox/events/config/telemetry/clock; Provenance, permission, encryption, and optional retrieval-index ports. Exposes `memory.capture.v1`, `memory.read.v1`, `memory.search.v1`, `memory.forget.v1`, `memory.explain.v1`. Forbidden: peer repositories, silent capture, provider-owned authority, direct surfaces, unbounded semantic search, retention bypass, logging content/embeddings.

## Security, permissions, and configuration
Deny by default; separate capture/read/search/revise/forget/export grants and purpose scopes. Sensitive capture may require consent; forgetting/export are auditable and consequential confirmation follows policy. Encrypt records/indexes; minimize retrieval output; secrets and credentials MUST NOT become memories. Config: content bytes, result/page limits, candidate budget, retention classes, index timeout/concurrency, confidence display policy; finite validated bounds, no secret defaults.

## Failures, observability, and performance
Errors: `PROVENANCE_REQUIRED`, `CONSENT_REQUIRED`, `INVALID_MEMORY`, `CONFLICT`, `NOT_FOUND`, `PERMISSION_DENIED`, `RETENTION_VIOLATION`, `INDEX_UNAVAILABLE`. Persist capture even if rebuildable index is degraded; retry transient idempotent indexing; never retry denial/conflict. Log IDs/class/counts/duration/outcome only. Metrics cover capture/search latency, candidate truncation, stale index, retention deletion, correction, denial, outbox lag. Search candidates/results and all work are bounded and cancellation-aware.

## Acceptance criteria and tests
Prove explicit capture policy, provenance, source-class distinction, user correction precedence, immediate forget exclusion, retention purge, output redaction, idempotent replay, atomic outbox, deterministic bounded fallback when index fails, export/delete, migration/index rebuild, and no content leakage in telemetry. Include unit, API/event contract, storage/index integration, permission/consent, crash replay, outage, migration, privacy, and max-bound performance tests.

## Examples and extension points
Success: capture a user-confirmed preference with evidence and retention class; search returns it with explanation. Denial: a plugin without purpose-scoped read receives no existence signal. Replay returns original receipt. New retrieval indexes, memory classes, and retention policies MAY be added behind ports; covert capture, indefinite default retention, or provider authority is forbidden without revised architecture.
