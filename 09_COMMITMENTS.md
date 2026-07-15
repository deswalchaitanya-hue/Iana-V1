# 09 — Commitments

## Purpose
Define authoritative obligations, expectations, deadlines, and follow-ups.

## Scope
Commitment evidence, due conditions, lifecycle, conflict, and follow-up policy.

## Commitment aggregate
Contains identity, user, kind, statement, owner, optional counterparty, Project reference, evidence set, due condition, confidence, status, follow-up policy, next Action reference, provenance, timestamps, and version.

Kinds distinguish user obligation, counterparty expectation, deadline, promised follow-up, and awaiting response.

## Lifecycle
`Proposed → Confirmed → Active → Completed | Dismissed | Expired | Superseded`.

Only confirmed Commitments become active. Completion requires actor and evidence or an explicit user assertion. Dismissal records reason. Expiration follows due-condition policy; it is not equivalent to completion. Supersession links the replacement.

## Evidence
Evidence references immutable sources or provenance records and records quoted span/hash where allowed. Multiple weak sources do not automatically become verified truth. Evidence removed by retention may leave hash, source identity, and explanatory limitation.

## Due conditions
A due condition is exact instant, local date, interval, event-relative condition, response expectation, or explicitly unknown. It carries timezone and uncertainty. Derived reminder times and deadline indexes are projections.

## Conflict
Conflicts include competing owners, incompatible due conditions, contradictory status evidence, and duplicate candidates. Conflict state prevents autonomous escalation. Resolution preserves alternatives and actor rationale.

## Follow-up policy
Declares earliest follow-up, cadence, maximum attempts, suppression windows, channel preference, expiration, and confirmation requirements. It proposes Attention candidates; it never sends notifications or messages.

## Invariants
- Proposed inference cannot become active without confirmation policy.
- Every Commitment has evidence and provenance.
- Status transitions are monotonic except explicit reopen from completed where policy permits and history is preserved.
- Commitment state is authoritative over deadline projections.
- High-risk follow-up cannot bypass Action policy.

## Events
Publishes proposed, confirmed, activated, due-changed, conflicted, completed, dismissed, expired, superseded, and follow-up-eligible facts.

## Assumptions
People and Projects are references. Delivery belongs to surfaces; execution belongs to Actions.

## Rationale
Commitments preserve why work exists, not merely when a reminder fires.

## References
[08_PROJECTS.md](08_PROJECTS.md), [10_ATTENTION_INBOX.md](10_ATTENTION_INBOX.md), [15_ACTION_ENGINE.md](15_ACTION_ENGINE.md)

## Future extension
Negotiated multi-party commitments require collaboration and identity contracts not defined here.
