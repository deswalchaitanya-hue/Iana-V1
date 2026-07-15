# 08 — Projects

## Purpose
Define durable contexts that answer where an objective stands.

## Scope
Project lifecycle, explicit membership, decisions, open questions, and materialized state.

## Project aggregate
A Project contains identity, user, objective, title, status, policy, explicit members, artifact references, decision records, open questions, timestamps, and version. It references Memory and Commitments through public identifiers and read contracts.

## Lifecycle
`Proposed → Active → Paused → Completed | Archived`. Proposed Projects require acceptance or deterministic creation policy. Completed Projects may reopen. Archived Projects are read-only until restored. Deletion follows privacy policy and does not silently delete independently owned Memories or Commitments.

## Membership
Membership is explicit and typed: artifact, person reference, Memory reference, Commitment reference, source, or Action. Each membership has provenance, actor, confidence when inferred, and validity. Automatic assignment remains proposed until policy permits acceptance. Removing membership does not delete the member.

## Decisions
A decision contains statement, status, alternatives considered, rationale, evidence references, actor, decision time, and provenance. Status is proposed, accepted, reversed, or superseded. AI may draft; only deterministic capability flow records acceptance.

## Open questions
Questions contain statement, priority, evidence need, status, owner, and resolution reference. Status is open, answered, dismissed, or superseded.

## Project State
Project State is a rebuildable, incrementally materialized snapshot containing objective, status, recent changes, key people, accepted decisions, open questions, active Commitments, risks, and recommended next Actions. It records source event positions, generated time, freshness, and reasoning provenance. It is not authoritative over source aggregates.

## Update rules
Project commands mutate only Project-owned state. Memory and Commitment changes arrive through public events. Reasoning may propose summaries or recommendations; schema and policy validation precede publication.

## Invariants
- A Project belongs to one user in initial releases.
- Shared Projects require a future collaboration context.
- Project State can always be rebuilt.
- No Project operation mutates Memory or Commitment aggregates.
- Evidence and provenance accompany inferred membership and generated state.

## Events
Publishes created, activated, membership-added/removed, decision-recorded/reversed, question-opened/resolved, status-changed, and deleted facts.

## Assumptions
Cross-context reads are bounded public interfaces; no foreign joins are allowed.

## Rationale
Separating authoritative Project decisions from generated state keeps summaries useful and disposable.

## References
[07_MEMORY.md](07_MEMORY.md), [09_COMMITMENTS.md](09_COMMITMENTS.md), [14_REASONING_ENGINE.md](14_REASONING_ENGINE.md)

## Future extension
Collaboration, roles, and shared ownership require a separately specified context in Phase 3 or later.
