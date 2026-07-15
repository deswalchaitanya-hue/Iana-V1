# Domain Core Contract Pack

**Module:** `iana.domain` · **Contract:** `1.0` · **Authority:** [02](../02_PRINCIPLES.md), [03](../03_ARCHITECTURE.md), [05](../05_DOMAIN_MODEL.md), [31](../31_ERROR_MODEL.md)

## Purpose, ownership, and non-goals
Domain Core defines pure, stable value objects, identifiers, invariant policies, state-transition primitives, and domain error types shared across bounded contexts. It owns no persistence and performs no I/O, orchestration, rendering, provider calls, or cross-context mutation.

## Responsibilities and boundary
Provide canonical IDs/references, timestamps/intervals, actor and provenance references, confidence and priority values, lifecycle transition validation, pagination bounds, command/event metadata, and pure policy evaluators. Context-specific aggregates remain owned by their contexts.

## Public APIs, inputs, and outputs
Pure versioned APIs return `DomainResult<T, DomainError>`:
- `CanonicalId.parse(type, value)`; `EntityRef.create(context, type, id, version?)`
- `UtcInstant.parse(value)`; `TimeRange.create(start, end)`
- `Confidence.create(value)`; `Priority.create(value)`
- `PageRequest.create(cursor?, limit)`
- `CommandMeta.create(idempotencyKey, actor, correlationId, provenanceRef)`
- `validateTransition(machine, from, to) -> TransitionDecision`
- `redact(classification, audience) -> RedactionPlan`
Inputs are immutable, explicitly versioned, normalized without losing original provenance, and bounded. Outputs are immutable and serializable. Pure operations are deterministic and side-effect free.

## Owned data and invariants
Owned definitions, not records: ID formats, value ranges, classification labels, transition decision shape, stable error taxonomy. IDs are opaque; references never embed foreign aggregates. UTC is canonical while original timezone MAY be retained as metadata. Confidence is not truth. User-authored values cannot be silently replaced by inference. Changes are library/schema versions; migrations belong to record owners.

## Events and capabilities
Publishes and consumes no runtime events. Exposes `domain.primitives.v1`, `domain.transitions.v1`, `domain.redaction.v1`. Consumers compile against contracts rather than storage. No capability delegates mutation.

## Dependencies and forbidden dependencies
May depend only on language/runtime standard primitives and generated schema validators with deterministic behavior. Forbidden: Kernel runtime, application layer, bounded contexts, database, filesystem, network, clock reads, random global IDs, UI, AI/model/provider SDKs.

## Security and permissions
No authorization decisions based on ambient identity. It defines classifications and policy inputs; callers enforce grants. Parsers reject malformed, oversized, ambiguous, or unsafe values. Error messages reveal no secret/raw sensitive input. No secrets exist in configuration.

## Configuration
None at runtime. Bounds such as maximum page size are explicit constructor/policy inputs owned by the calling application configuration, never ambient globals.

## Failure handling and observability
Errors: `INVALID_VALUE`, `OUT_OF_RANGE`, `INVALID_REFERENCE`, `INVALID_INTERVAL`, `INVALID_TRANSITION`, `UNSUPPORTED_VERSION`. No retries or compensation. Domain Core emits no logs, metrics, or traces; callers observe boundary operations and MUST NOT log rejected raw values.

## Performance and acceptance
Every operation is bounded, deterministic, terminating, and free of I/O. Parsing is linear in bounded input length; transition lookup is bounded by supplied machine size. Acceptance: same input gives same output; invalid states are unrepresentable or rejected; serialization round-trips; version incompatibility is explicit; no dependency points outward.

## Test cases
Unit/property: ID/value boundaries, time ordering, serialization round-trips, transition tables, redaction monotonicity. Contract: schema fixtures across supported versions. Architecture: dependency scan proves purity. Security: oversized/unicode-confusable/malformed input and error redaction. Performance: maximum configured value sizes terminate within caller budget.

## Examples
Success: `EntityRef.create("projects","project","p_123")` returns an opaque reference. Failure: a time range ending before it starts returns `INVALID_INTERVAL`. Denial policy example: `redact(SECRET, PUBLIC)` removes the value rather than masking incompletely.

## Extension points
Additive value objects and transition policies MAY be introduced without weakening existing invariants. Context aggregates, storage annotations, provider types, or product policy MUST NOT be added. Breaking serialization or semantics requires a major version and ADR.
