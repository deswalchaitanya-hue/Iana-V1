# Projects Contract Pack

**Module:** `iana.projects` · **Contract:** `1.0` · **Authority:** [05](../05_DOMAIN_MODEL.md), [08](../08_PROJECTS.md), [26](../26_EVENT_SYSTEM.md)

## Purpose and responsibilities
Own projects, intended outcomes, project-local state, milestones or structural references authorized by the project schema, lifecycle, and project views. It coordinates neither commitments nor actions by direct write; those remain canonical references in their owners. It does not reason, automate, render, or own context graph entities.

## Public APIs and schemas
`create(ProjectCommand)->ProjectReceipt`; `update(ProjectId, Patch, expectedVersion)->Receipt`; `transition(ProjectId, targetState, expectedVersion)->Receipt`; `get(ProjectId, Projection)->ProjectView`; `list(ProjectQuery)->ProjectPage`; `linkReference(ProjectId, EntityRef, relation, expectedVersion)->Receipt`; `unlinkReference(...)`.
Inputs include actor, idempotency, title/outcome and bounded metadata, lifecycle target, provenance, correlation, deadline. Outputs include ID, version, state, authorized fields/references, timestamps, warnings, cursor. Queries are side-effect free; writes use optimistic concurrency and idempotency.

## Owned data and invariants
Owns projects, outcomes, project-local metadata, lifecycle history, canonical foreign references, tombstones, indexes, and outbox. A project has an owner boundary and valid state transition; references never copy foreign aggregate state; archive preserves receipts. Derived summaries are labeled and rebuildable. Partition per user/tenant; record+outbox atomic; migrations versioned/resumable.

## Events
Publishes `projects.project-created|updated|state-changed|archived.v1`, `projects.reference-linked|unlinked.v1`. Payloads are IDs, versions, states/relation names, classification, provenance—not private descriptions unless explicitly required and authorized. Consumes authorized deletion/reference-invalidated events. At least once, per-project order, event-ID dedupe.

## Dependencies, capabilities, and prohibitions
Requires Domain Core; Kernel store/outbox/events/config/telemetry/clock; permission and Provenance ports. Exposes `projects.create.v1`, `projects.read.v1`, `projects.update.v1`, `projects.transition.v1`, `projects.references.v1`. Forbidden: commitment/context/memory repositories, provider/network/UI/model access, foreign cascades, implicit commitment completion, unbounded listing.

## Security and configuration
Deny by default with owner/tenant, project, field, and operation scopes. Archive/delete/export and broad sharing require confirmation per consequence. Enforce projection redaction and audit mutations. Encrypt data. Config: title/description/reference limits, page size, lifecycle policy version, retention, deadlines; finite validated defaults, no secrets.

## Failures, observability, and performance
Errors: `INVALID_PROJECT`, `INVALID_TRANSITION`, `CONFLICT`, `NOT_FOUND`, `PERMISSION_DENIED`, `REFERENCE_INVALID`, `DEPENDENCY_UNAVAILABLE`. Retry transient idempotent adapters only. Log safe IDs/state/counts/duration/outcome, never private descriptions. Metrics: operation latency, state transitions, conflicts, denials, page truncation, outbox lag. Lists, references, batches, and waits are bounded/cancellable.

## Acceptance criteria and tests
Prove lifecycle tables, owner isolation, reference-only foreign links, optimistic concurrency, replay dedupe, atomic outbox, field redaction, no peer writes, archive/history, migration restart, event compatibility, and configured page/reference limits. Test unit transitions; API/events; store/outbox; permission/existence leak; stale version; duplicate command; invalid reference; outage; migration; performance bounds.

## Examples and extension points
Success: create an active project with an outcome and link a commitment reference through its canonical ID. Denial: caller without field scope receives a minimized view. Conflict: stale update returns current version metadata without private fields. New project-local schemas, states, and read models MAY be versioned; absorbing commitments or bypassing application capabilities is forbidden.
