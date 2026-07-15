# Commitments Contract Pack

**Module:** `iana.commitments` · **Contract:** `1.0` · **Authority:** [05](../05_DOMAIN_MODEL.md), [09](../09_COMMITMENTS.md), [15](../15_ACTION_ENGINE.md)

## Purpose and responsibilities
Own explicit commitments, parties/references, due constraints, lifecycle, fulfillment/cancellation evidence, and commitment queries. A commitment represents an obligation, not merely a task suggestion. This module does not execute actions, schedule automation, own projects, or infer obligations without confirmation.

## Public APIs, inputs, outputs, and schemas
`create(CommitmentCommand)->CommitmentReceipt`; `revise(CommitmentId, Patch, expectedVersion)->Receipt`; `transition(CommitmentId, targetState, evidence?, expectedVersion)->Receipt`; `get(CommitmentId, Projection)->CommitmentView`; `list(CommitmentQuery)->CommitmentPage`; `dueWindow(TimeRange, cursor, limit)->CommitmentPage`.
Commands include actor, idempotency, obligation, party/entity references, due/timezone metadata, source/provenance, confirmation when inferred/external, correlation, deadline. Outputs include ID/version/state/due semantics, authorized references, evidence receipt, warnings/cursor. Optimistic concurrency, explicit transitions, idempotent commands, and cancellation apply.

## Owned data and invariants
Owns commitment aggregate, revisions, lifecycle/evidence history, due indexes, tombstones, outbox. Valid lifecycle follows the governing spec; fulfilled/cancelled states require authorized transition and evidence/reason. Inferred proposals are not commitments until confirmed. Foreign records are canonical references only. Partition by user/tenant; atomic aggregate+outbox; resumable migrations.

## Events
Publishes `commitments.commitment-created|revised|fulfilled|cancelled|became-due.v1`. Minimum payload: ID, version, state, due class/time if authorized, reference IDs, provenance/evidence reference. Consumes authorized reference invalidation and privacy events; time-trigger handling comes through Kernel scheduling, not polling foreign stores. At least once; per-commitment order; dedupe.

## Dependencies, capabilities, and prohibitions
Requires Domain Core; Kernel store/outbox/events/scheduler/config/telemetry/clock; permission and Provenance ports. Exposes `commitments.create.v1`, `commitments.read.v1`, `commitments.transition.v1`, `commitments.due.read.v1`. Forbidden: Projects/Attention repositories, direct external action/provider/model/UI calls, silent inferred creation, automatic fulfillment without receipt, unbounded due scans.

## Security and configuration
Deny by default with party, owner, field, operation, and purpose scopes. Creating obligations for others, external notification, cancellation, and consequential transitions require policy-specific confirmation. Redact party/private obligation fields; audit every mutation. Config: text/reference/page limits, schedule horizon, timezone policy, grace rules, retention, deadlines; bounded, validated, no secrets.

## Failures, observability, and performance
Errors: `INVALID_COMMITMENT`, `CONFIRMATION_REQUIRED`, `INVALID_TRANSITION`, `EVIDENCE_REQUIRED`, `CONFLICT`, `NOT_FOUND`, `PERMISSION_DENIED`, `SCHEDULE_UNAVAILABLE`. A schedule failure leaves durable state and reports degraded due signaling; retry transient idempotent scheduling. Log safe IDs/state/due class/counts/outcome, never obligation text. Measure due lag, transition latency, conflicts, denials, schedule failures, outbox lag. Due queries/schedules are bounded/cancellable.

## Acceptance criteria and tests
Prove no inferred commitment without confirmation, valid lifecycle/evidence, timezone/due correctness, owner isolation, replay dedupe, stale-version conflict, schedule degradation, atomic outbox, redaction, no peer writes, migration restart, and bounded due queries. Include unit/property time tests, API/event contracts, store/scheduler integration, permission/confirmation, replay, outage, migration, and configured-limit performance tests.

## Examples and extension points
Success: create a user-confirmed commitment and fulfill it with evidence receipt. Denial: an engine proposal lacking confirmation cannot create it. Replay: duplicate fulfillment produces one fact. New due policies, commitment classes, or read models MAY be versioned; hidden obligations or direct execution are forbidden.
