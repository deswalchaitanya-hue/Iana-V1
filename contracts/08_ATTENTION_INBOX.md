# Attention Inbox Contract Pack

**Module:** `iana.attention` · **Contract:** `1.0` · **Authority:** [05](../05_DOMAIN_MODEL.md), [10](../10_ATTENTION_INBOX.md), [16](../16_AUTOMATION_ENGINE.md)

## Purpose and responsibilities
Own bounded attention items that require user review, decision, correction, confirmation, or follow-up, plus triage state and source references. It is not a notification firehose, task/commitment store, automation queue, or reasoner. Producers cannot force attention without policy and deduplication.

## Public APIs, inputs, outputs, and schemas
`submit(AttentionCommand)->AttentionReceipt`; `triage(ItemId, TriageDecision, expectedVersion)->Receipt`; `snooze(ItemId, until, expectedVersion)->Receipt`; `dismiss(ItemId, reason, expectedVersion)->Receipt`; `get(ItemId, Projection)->AttentionView`; `list(AttentionQuery)->AttentionPage`.
Submit includes producer capability, actor, idempotency/dedup key, reason/class, priority rationale, canonical source reference, proposed choices/actions, expiry, provenance, correlation, deadline. Outputs include ID/version/state, authorized summary/choices, priority explanation, cursor. Queries are bounded; mutations optimistic/idempotent.

## Owned data and invariants
Owns attention items, triage lifecycle, dedup/suppression records, priority metadata, snooze/expiry indexes, tombstones, outbox. Every item has an accountable producer, source/provenance, reason, finite lifecycle, and user-resolvable choices. Priority inference remains explainable. Foreign state is referenced only. Atomic record+outbox; per-user/tenant partition; resumable migrations and privacy retention.

## Events
Publishes `attention.item-submitted|triaged|snoozed|dismissed|expired.v1`. Minimized payload: ID, version, class/state, producer, source ref, provenance. Consumes authorized proposal, action-confirmation-needed, automation-paused, commitment-due, correction-needed, and privacy/reference invalidation events through registered policies. At least once; dedup key plus event ID prevents storms.

## Dependencies, capabilities, and prohibitions
Requires Domain Core; Kernel store/outbox/events/scheduler/config/telemetry/clock; permission and Provenance ports. Exposes `attention.submit.v1`, `attention.read.v1`, `attention.triage.v1`. Forbidden: peer writes, direct action execution, provider/model/UI dependencies, producer-controlled bypass priority, infinite snooze, unbounded inbox, duplicate storm creation.

## Security and configuration
Deny by default. Submit requires producer grant and allowed item class; read/triage is owner/purpose/field scoped. Proposed consequential actions remain unexecutable without Action Engine confirmation. Redact sensitive source/choices; audit mutations. Config: per-producer/user queue limits, page size, dedup window, expiry/snooze bounds, priority policy version, deadlines; finite, validated, no secrets.

## Failures, observability, and performance
Errors: `INVALID_ITEM`, `PRODUCER_DENIED`, `QUEUE_LIMIT`, `DUPLICATE_SUPPRESSED`, `INVALID_TRANSITION`, `CONFLICT`, `NOT_FOUND`, `PERMISSION_DENIED`. Suppression returns a receipt, not an error leak. Retry transient idempotent storage/scheduling; retain item if schedule degrades. Log safe IDs/class/state/producer/counts/outcome, never sensitive summaries. Measure queue size/age, duplicates, expiry lag, triage latency, denials, outbox lag. All listing, ingestion, scheduling, and producer rates are bounded.

## Acceptance criteria and tests
Prove accountable source, deduplication, rate/queue bounds, lifecycle validity, priority explanation, owner isolation/redaction, no execution side effect, replay safety, atomic outbox, expiry/snooze behavior, schedule degradation, migration, and event-storm resistance. Test APIs/events, transition/property rules, permission/producer grants, duplicate/replay, burst/failure, migration, and maximum bounds.

## Examples and extension points
Success: Action Engine submits a confirmation-needed item with choices and action-plan reference. Duplicate submission returns suppression/original reference. Denial: an ungranted plugin cannot create urgent items. New item classes, prioritizers, and read models MAY be versioned; bypass queues, coercive priority, or embedded execution are forbidden.
