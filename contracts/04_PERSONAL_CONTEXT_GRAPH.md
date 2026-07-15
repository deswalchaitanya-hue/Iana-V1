# Personal Context Graph Contract Pack

**Module:** `iana.context` · **Contract:** `1.0` · **Authority:** [05](../05_DOMAIN_MODEL.md), [06](../06_CONTEXT_GRAPH.md), [24](../24_SECURITY_AND_PERMISSIONS.md), [25](../25_STORAGE_AND_DATA_OWNERSHIP.md)

## Purpose and responsibilities
Own the user-controlled graph of contextual entities, typed relations, attributes, evidence references, and bounded snapshots used by other modules. It MUST validate graph invariants, preserve provenance and user corrections, provide bounded traversal, and expose only authorized projections. It does not own memories, projects, commitments, attention items, inference, action execution, or rendering.

## Public APIs, inputs, outputs, and schemas
`upsertEntity(EntityCommand)->EntityReceipt`; `archiveEntity(EntityId, expectedVersion)->Receipt`; `relate(RelationCommand)->RelationReceipt`; `removeRelation(RelationId, expectedVersion)->Receipt`; `getEntity(EntityId, Projection)->EntityView`; `traverse(TraversalQuery)->GraphPage`; `snapshot(SnapshotQuery)->ContextSnapshot`.
Commands carry actor, idempotency key, expected version, provenance, correlation, deadline, and bounded typed attributes. Traversal declares roots, allowed relation types, direction, depth, node/edge/page limits, and audience. Outputs include canonical IDs, versions, source/confidence distinctions, redactions, and cursors. Optimistic concurrency and idempotent replay are mandatory.

## Owned data and invariants
Owns entities, relations, graph-local attributes, correction history, indexes, tombstones, snapshots, and outbox. Relation endpoints use canonical references; foreign aggregates are never embedded. No dangling active relation; relation types and attribute schemas are versioned; user assertions remain distinguishable from inference; archive preserves auditability. Partition by user/tenant boundary. Data+outbox is transactional; migrations are resumable; retention/export/deletion obey privacy controls.

## Events
Publishes `context.entity-created|updated|archived.v1`, `context.relation-created|removed.v1`, `context.correction-recorded.v1`. Minimum payload: IDs, versions, changed field names, classification, provenance reference—never unnecessary values. Consumes provenance revocation/correction and authorized deletion-policy events. At least once; per-aggregate order; event-ID dedupe.

## Dependencies, capabilities, and prohibitions
Requires Domain Core; Kernel store/outbox/events/config/telemetry/clock; identity/permission and Provenance ports. Exposes `context.entities.read|write.v1`, `context.graph.traverse.v1`, `context.snapshot.read.v1`. Forbidden: peer repositories, direct model/provider/network/UI access, inferred fact promotion without evidence/user policy, unbounded traversal, cascading foreign deletes.

## Security, permissions, and configuration
Deny by default with owner/tenant, entity classification, field, relation, and purpose scopes. Sensitive relations require explicit grants; mutation is audited; export/delete requires confirmation where consequential. Encrypt at rest/in transit and redact unauthorized fields before traversal expansion. Config: entity/relation/attribute byte limits, traversal depth/node/edge/page bounds, snapshot TTL, retention, query deadline; finite validated defaults, dynamic only where safe, no secrets.

## Failures, logging, and performance
Errors: `INVALID_ENTITY`, `INVALID_RELATION`, `CONFLICT`, `NOT_FOUND`, `PERMISSION_DENIED`, `TRAVERSAL_LIMIT`, `PROVENANCE_REQUIRED`, `DEPENDENCY_UNAVAILABLE`. Retry only transient idempotent operations; never retry conflicts/denials; rollback atomic writes. Log operation, safe IDs, counts, limits, duration, correlation, outcome—not graph values. Metrics: size, traversal truncation/latency, conflicts, denials, outbox lag. Work is bounded by declared limits and deadline.

## Acceptance criteria and tests
Must prove ownership isolation, no dangling relation, optimistic concurrency, replay dedupe, provenance retention, correction precedence without history loss, authorization/redaction at every hop, deterministic bounded traversal, atomic outbox, migration restart, deletion/export policy, and no peer-schema access. Test unit invariants; API/event contracts; storage/outbox integration; malformed/cyclic/oversized graphs; permission/existence leaks; crash replay; adapter outage; maximum configured traversal.

## Examples and extension points
Success: create a person entity, then a typed relation using returned IDs and evidence reference. Denial: a surface lacking relation scope gets a redacted view and cannot infer hidden edge existence. Replay returns the first receipt. New entity/relation schemas, indexes, and projection policies MAY be added compatibly; global graph authority, hidden inference writes, or peer record embedding require an ADR and new contract.
