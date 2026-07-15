# 06 — Personal Context Graph

## Purpose
Specify typed, temporal, provenance-backed relationships without replacing bounded contexts.

## Scope
Node and edge descriptors, duplicate candidates, traversal contracts, and graph projections.

## Node descriptor
A node contains `NodeId`, `UserId`, canonical `EntityRef`, node type, created time, optional validity interval, provenance references, confidence, and lifecycle state. Supported initial types are person, Project, artifact, decision, Commitment, Action, source, and Memory subject. Nodes reference; they do not copy foreign aggregate state.

## Edge descriptor
An edge contains `EdgeId`, user partition, typed source and target, relationship type and version, direction, validity interval, observation time, provenance, confidence, and lifecycle state. Initial relationships include belongs-to, concerns, evidence-for, made-by, involves, depends-on, supersedes, produced-by, references, and resulted-in.

Unknown types are rejected unless registered through a compatible graph contract. Self-edges require relationship-specific permission. Duplicate edge candidates are merged only by declared type policy.

## Temporal semantics
`observedAt` states when IANA learned a relation. `validFrom` and `validUntil` state when it applies. Absence of `validUntil` means open-ended, not permanent. Supersession closes validity without erasing history.

## Traversal contract
Every query declares user, allowed node and edge types, direction, maximum depth, maximum visited nodes, maximum returned paths, time window, minimum confidence, sensitivity ceiling, deadline, and cancellation token. Defaults are depth 2 and bounded result sets. Cross-user traversal is prohibited.

Traversal returns paths plus truncation reason, freshness, and provenance coverage. Budget exhaustion is a valid partial result, never an invitation to continue unbounded.

## Mutation contracts
Graph writes originate from validated capabilities or trusted domain events. Proposed nodes and edges remain candidates until deterministic validation. Foreign contexts cannot edit graph storage.

## Projection and rebuild
Adjacency indexes, reverse edges, neighborhood summaries, and centrality hints are projections. Canonical graph records remain relational and rebuild projections from records and events.

## Invariants
- Every edge endpoint exists or is represented by a declared unresolved reference.
- Every inferred edge has confidence and provenance.
- Security filters run before traversal and again before result release.
- Graph deletion follows source retention and signed tombstone policy.
- Graph results never confer authorization to access referenced entities.

## Events
Publishes node-recorded, edge-recorded, edge-superseded, duplicate-proposed, and relation-removed facts. Payloads contain references, not personal bodies.

## Assumptions
The graph is initially adjacency-oriented and user-partitioned; no dedicated graph database is authoritative.

## Rationale
Typed budgeted traversal supplies useful context while preventing a global god model and runaway queries.

## References
[05_DOMAIN_MODEL.md](05_DOMAIN_MODEL.md), [07_MEMORY.md](07_MEMORY.md), [25_STORAGE_AND_DATA_OWNERSHIP.md](25_STORAGE_AND_DATA_OWNERSHIP.md)

## Future extension
New relationship vocabularies are additive and versioned. Analytics projections remain disposable.
