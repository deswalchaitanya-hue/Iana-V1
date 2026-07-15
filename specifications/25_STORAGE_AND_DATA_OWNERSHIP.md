# 25 — Storage and Data Ownership

## Purpose
Define authoritative storage classes, ownership, projections, blobs, caches, and outbox.

## Canonical relational authority
Memory, Projects, Commitments, Attention, graph records, Provenance, Actions, operation state, security grants, and settings use mature relational storage. Each bounded context owns tables, repositories, migrations, constraints, and transactions in its namespace.

Cross-context SQL joins and foreign repository access are prohibited. Reporting uses public reads or owned projections.

## Ownership map
- Context Graph: nodes, edges, duplicate candidates.
- Memory: records, contracts, conflicts, supersession.
- Projects: aggregate state, membership, decisions, questions.
- Commitments: records, evidence references, transition history.
- Attention: candidates/items, suppression, resolution.
- Provenance: source, lineage, disclosure, receipt metadata.
- Actions/operations: plans, steps, approvals, state, idempotency.
- Security/platform: grants, audit references, settings, plugin/provider health.

## Blobs
Encrypted artifacts are content-addressed within a user security boundary and referenced from relational records. Metadata includes owner, content hash, key reference, media type, size, retention, and integrity. Database transactions use pending/finalized blob states to avoid orphan authority.

## Projections and indexes
Graph adjacency, full-text search, semantic embeddings, Project State, deadline indexes, Attention delivery views, and widget views are rebuildable. Each records source version/checkpoint and never becomes mutation authority.

## Cache
Caches store rebuildable values only, are size/time bounded, sensitivity-aware, encrypted where required, and safe to delete. No permission or confirmation decision relies solely on cache.

## Transactional outbox
State mutation and domain event insert commit in one owner transaction. Dispatch marks attempts separately and supports at-least-once delivery and idempotent consumers.

## Backup and deletion
Backups are encrypted and retention-aware. Deletion covers canonical rows, blobs, projections, indexes, queues, and backup/tombstone policy. Sanitized tombstones coordinate sync.

## Invariants
Database files never synchronize directly. Derived data is rebuildable. Migrations are owner-controlled. Personal partition keys are mandatory.

## References
[26_EVENT_SYSTEM.md](26_EVENT_SYSTEM.md), [27_SYNCHRONIZATION.md](27_SYNCHRONIZATION.md), [32_VERSIONING_MIGRATIONS_UPDATES.md](32_VERSIONING_MIGRATIONS_UPDATES.md)

## Future extension
A new storage technology must implement an owned port and cannot alter authority semantics.
