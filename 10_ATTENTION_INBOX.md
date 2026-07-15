# 10 — Attention Inbox

## Purpose
Define a bounded inbox of matters requiring judgment or action, not an engagement feed.

## Scope
Candidate intake, deduplication, ranking, suppression, explanation, eligibility, and resolution.

## Intake
Attention consumes sanitized public events from Projects, Commitments, Actions, Memory, and Context. Producers do not write Attention storage. A candidate contains source event, subject reference, candidate type, urgency inputs, suggested resolution, provenance, and expiration.

Types include reply expected, deadline approaching, Commitment at risk, decision needed, relevant Project change, failed Action, Memory ambiguity, and user-requested follow-up.

## Materialization
A deterministic policy validates candidate eligibility, calculates deduplication key, and creates or updates an Attention item. Ranking models may propose scores; deterministic bounds and suppression policy remain authoritative.

## Attention item
Contains identity, user, type, subject, title, explanation, evidence references, urgency band, ranking factors, available capability references, delivery eligibility, suppression state, lifecycle, expiration, provenance, and version.

## Ranking
Ranking considers explicit due state, user priority, consequence, recency, confidence, unresolved duration, Project relevance, and prior user feedback. Sensitive attributes and engagement likelihood cannot raise rank. Every rank exposes human-readable factors.

## Suppression
Supports snooze-until, quiet window, duplicate suppression, resolved-source suppression, channel suppression, and user mute. Suppression never mutates the source aggregate. Critical product claims must not bypass user controls.

## Lifecycle
`Candidate → Eligible → Presented → Resolved | Dismissed | Invalidated | Expired`. Presentation is not resolution. Resolution references the capability or source event that satisfied the item.

## Invariants
- Attention never queries producer repositories.
- Candidate sets are user-partitioned and bounded.
- No infinite feed or engagement-based ranking exists.
- Every item explains why it exists and how to resolve or dismiss it.
- Invalidated source facts remove eligibility idempotently.

## Delivery
Surfaces query compact projections and report presentation acknowledgements. Notification delivery remains an adapter and may lag without changing item truth.

## Events
Publishes item-created, ranked, suppressed, presented, resolved, dismissed, invalidated, and expired facts.

## Assumptions
At-least-once input is expected; consumers deduplicate by event and semantic key.

## Rationale
A strict intake contract prevents notification noise from becoming another attention-maximizing stream.

## References
[09_COMMITMENTS.md](09_COMMITMENTS.md), [17_SURFACE_ENGINE.md](17_SURFACE_ENGINE.md), [26_EVENT_SYSTEM.md](26_EVENT_SYSTEM.md)

## Future extension
New candidate types require an owner, source event, resolution contract, ranking factors, and suppression behavior.
