# 07 — Memory

## Purpose
Define user-owned retained knowledge with verification, scope, retention, correction, and deletion.

## Scope
Memory records and contracts; graph relationships and provenance are referenced, not owned.

## Memory record
A record contains identity, user, subject, canonical assertion, original wording where applicable, kind, scope, sensitivity, confidence, verification state, provenance, source links, retention contract, created/updated times, expiration, supersession, and version.

Kinds include fact, preference, person context, procedure, decision reference, and user instruction. Projects, Commitments, and artifacts remain their own aggregates.

## Memory contracts
A retention contract is one of session-only, Project-scoped, until-date, permanent, or on-device-only. It declares purpose, allowed consumers, provider-disclosure rule, expiration behavior, exportability, and deletion behavior. Permanent means no automatic expiration, not irrevocable storage.

## Lifecycle
`Proposed → Verified | Rejected`; verified records may become corrected, superseded, expired, redacted, or deleted. User-authored assertions may be verified directly. Inferred candidates require policy and, where confidence or sensitivity demands, user confirmation.

## Contradiction
Contradictions are preserved as a conflict set. The system MUST NOT overwrite a verified record through inference. Resolution may affirm one assertion, narrow validity intervals, preserve contextual alternatives, or mark unresolved. Resolution records provenance and actor.

## Retrieval
Queries declare user, purpose, scope, sensitivity ceiling, recency, result budget, and provider-disclosure intent. Results include confidence, verification, freshness, and provenance. Search and embeddings are rebuildable indexes and cannot create records.

## Correction and supersession
Correction creates a new version or record and links the prior record. Original evidence remains auditable subject to retention. Derived Project state and graph projections receive events and rebuild.

## Expiration and deletion
Expiration makes content unavailable to ordinary retrieval and schedules policy-governed removal. Deletion removes canonical content, blobs, and derived indexes, emits sanitized tombstones for sync, and preserves only legally and operationally required hashes and audit metadata. On-device-only records never enter sync or remote provider envelopes.

## Invariants
- No record lacks a retention contract and provenance.
- Scope cannot broaden through inference.
- Provider transmission requires explicit contract permission and policy approval.
- Deleted content cannot be reconstructed from indexes or diagnostics.
- Memory never calls providers or Actions.

## Events
Publishes proposed, verified, corrected, superseded, expired, redacted, and deleted facts with identifiers and safe metadata.

## Assumptions
Canonical records are relational; encrypted artifacts and embeddings are separate.

## Rationale
Explicit contracts make memory useful without turning passive collection into hidden surveillance.

## References
[06_CONTEXT_GRAPH.md](06_CONTEXT_GRAPH.md), [11_PROVENANCE_AND_RECEIPTS.md](11_PROVENANCE_AND_RECEIPTS.md), [37_PRIVACY_PRODUCT_CONTROLS.md](37_PRIVACY_PRODUCT_CONTROLS.md)

## Future extension
Additional Memory kinds require distinct validation and retention semantics.
