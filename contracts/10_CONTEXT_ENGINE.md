# Context Engine Contract Pack

**Module:** `iana.engine.context` · **Contract:** `1.0` · **Authority:** [13](../13_CONTEXT_ENGINE.md), [06](../06_CONTEXT_GRAPH.md), [07](../07_MEMORY.md)

## Purpose and responsibilities
Assemble bounded, purpose-scoped context from authorized capability queries for reasoning, actions, automation, and surfaces. Rank, truncate, redact, and explain inclusion while preserving source/provenance and freshness. It owns assembly policies, cacheable derived assemblies, and receipts—not source records or truth.

## Public APIs, inputs, outputs, and schemas
`assemble(ContextRequest)->ContextAssembly`; `refresh(AssemblyId, FreshnessPolicy)->ContextAssembly`; `explain(AssemblyId)->AssemblyExplanation`; `invalidate(SourceRef)->Outcome`.
Request includes actor/grants, purpose, roots, requested facets, time/freshness window, token/byte/item/source/depth budgets, redaction audience, correlation, deadline. Output includes assembly ID/version, ordered typed fragments, source refs/versions, provenance, confidence/source class, freshness, omissions/truncation reasons, budget use. Queries have no source side effects; same request+source versions+policy is deterministic.

## Owned data and invariants
Owns policy versions, assembly metadata, optional encrypted bounded caches, source-version map, invalidation indexes, receipts. Source values remain owned by their contexts. Every fragment is attributable and authorized; omission is explicit; inferred/derived content is labeled; stale data is marked, not silently current. Caches are derived, TTL-bound, deletable, and rebuildable.

## Events
Publishes `engine.context.assembly-created|refreshed|invalidated.v1` with IDs/policy/budget/status only. Consumes authorized context/memory/projects/commitments/attention change and privacy events for invalidation. At least once; event-ID dedupe; source version prevents stale overwrite.

## Dependencies, capabilities, and prohibitions
Requires Domain Core, Kernel config/events/telemetry/cache ports, Application query capabilities for source contexts, permission/redaction and Provenance. Exposes `engine.context.assemble.v1`, `.explain.v1`, `.invalidate.v1`. Forbidden: source repositories/writes, provider/model/UI dependencies, hidden prompt assembly, unbounded fan-out/traversal, unauthorized cache reuse.

## Security and configuration
Deny by default; intersection of caller grants, purpose, source scopes, and output audience. Reauthorize each source and cache hit; partition cache by principal/purpose/policy; encrypt and minimize. Config: source/item/token/byte/depth/page/fan-out limits, per-source timeout, total deadline, cache TTL/bytes, freshness policy versions; finite, validated, no secrets.

## Failures, observability, and performance
Errors: `INVALID_REQUEST`, `PERMISSION_DENIED`, `BUDGET_EXCEEDED`, `SOURCE_UNAVAILABLE`, `DEADLINE_EXCEEDED`, `UNSUPPORTED_POLICY`. Return explicit partial assembly only when policy allows, with omissions; retry transient idempotent source queries within deadline. Log source names/counts/budgets/duration/status, never fragment content. Metrics: assembly latency, fan-out, truncation, stale/partial/cache rates, denials. All work is budget/deadline/cancellation bounded.

## Acceptance criteria and tests
Prove provenance per fragment, deterministic budgeting/order, authorization and field redaction, no source writes, stale/partial labeling, cache isolation/invalidation, event replay, provider independence, telemetry redaction, and max-bound behavior. Test unit ranking/truncation; query/event contracts; multi-source integration; permission changes; cache replay; source timeout; invalidation storm; performance budgets.

## Examples and extension points
Success: assemble project context with two authorized memories and one due commitment, each sourced and budgeted. Partial: Memory times out and output names the omission. Denial: hidden relation never enters ranking or cache. New rankers, source adapters through capabilities, and cache implementations MAY be versioned; direct storage, covert enrichment, or provenance loss is forbidden.
