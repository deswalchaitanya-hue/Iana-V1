# 05 — Domain Model

## Purpose
Define shared primitives and aggregate boundaries without creating a universal model.

## Scope
Canonical identities, value objects, state conventions, and cross-context invariants. Context-specific semantics remain in `06`–`11`.

## Canonical primitives
- `UserId`, `DeviceId`, `ProjectId`, `MemoryId`, `CommitmentId`, `AttentionItemId`, `ActionId`, `OperationId`, `SourceId`, `ProvenanceId`, `CapabilityId`, and `CorrelationId` are opaque, immutable, globally unique values.
- `Instant` is UTC; display zones are presentation concerns. `LocalDate`, `TimeZoneId`, and uncertain time ranges remain explicit.
- `Confidence` is bounded from 0 through 1 and carries method and calibration version; it never authorizes mutation.
- `Sensitivity` is one of public, personal, sensitive, or restricted.
- `EntityRef` contains type and identifier only; it does not expose a foreign aggregate.
- `ContentHash` identifies exact bytes or canonicalized content; it is not identity.
- `IdempotencyKey` is caller-scoped and operation-scoped.

## Common value contracts
`SourceRef`, `ProvenanceRef`, `RetentionContractRef`, `PolicyDecisionRef`, and `ReceiptRef` are immutable references. User-authored text preserves original content and locale. Normalized text is derived and separately attributed.

## Aggregate rules
Each aggregate has one owner, validates its own transitions, increments a monotonic local version, and emits facts atomically with mutation. Repositories load and save only their owning aggregate. Cross-context references use canonical identifiers.

Owned aggregate roots are Context Graph records, Memory records, Projects, Commitments, Attention items, Provenance chains, and Action plans. No root contains another root.

## Invariants
1. Every personal record is partitioned by `UserId`.
2. Every inferred record references provenance and confidence.
3. AI output is a candidate until deterministic policy accepts it.
4. Created and updated instants never move backward within one aggregate version history.
5. Deleted identity is not silently reused.
6. Domain transitions are explicit; arbitrary status assignment is prohibited.
7. Sensitive values never appear in equality, logs, or identifiers.
8. Foreign aggregate availability is not assumed during local validation.

## Errors
Domain errors are stable typed values: invalid value, invariant violation, invalid transition, missing evidence, stale version, conflict, and not found. Boundary translation follows [31_ERROR_MODEL.md](31_ERROR_MODEL.md).

## Transactions
A transaction changes aggregates owned by one bounded context and writes its outbox. Cross-context workflows use capabilities and events; they do not use distributed transactions.

## Assumptions
Identifiers and clocks are injected. Canonical state uses relational authority described in [25_STORAGE_AND_DATA_OWNERSHIP.md](25_STORAGE_AND_DATA_OWNERSHIP.md).

## Rationale
A small vocabulary enables stable contracts while preserving separate domain ownership.

## References
[02_PRINCIPLES.md](02_PRINCIPLES.md), [03_ARCHITECTURE.md](03_ARCHITECTURE.md), [40_GLOSSARY.md](40_GLOSSARY.md)

## Future extension
New shared primitives require evidence that at least two contexts need identical semantics; convenience alone is insufficient.
