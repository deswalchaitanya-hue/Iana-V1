# 13 — Context Engine

## Purpose
Build immutable, authorized, budgeted snapshots for a declared task.

## Source contract
A source declares ID/version, data classes, freshness model, required grants, sensitivity, collection cost, availability, timeout, and redaction support. Initial sources include current explicit capture, selected Project state, authorized Memory, device-observed context, and supported notification records.

## Snapshot request
Contains user, principal, purpose, task type, requested source IDs, Project scope, sensitivity ceiling, provider-disclosure intent, freshness threshold, item/byte/time budgets, deadline, and cancellation.

## Snapshot
Contains snapshot ID, captured time, expiration, purpose, ordered context items, source/provenance references, freshness per item, sensitivity labels, omissions and reasons, budget usage, and policy decision. It is ephemeral and never authoritative.

## Collection
Eligible sources collect in parallel through injected ports. Each source has an independent deadline and cancellation. Results are normalized, deduplicated by identity/hash where safe, sensitivity-filtered, ranked for task relevance, and truncated deterministically.

## Freshness
Each item reports observed time and source-specific stale threshold. Stale data may be included only when marked and policy permits. Snapshot expiration is the minimum required by source, permission, and task policy.

## Failure
Source failure degrades narrowly. Results distinguish denied, unavailable, timed out, stale, budget-excluded, and cancelled sources. Required-source absence fails the snapshot; optional-source absence yields a partial result.

## Security
Authorization runs before collection and release. App exclusions and on-device-only contracts override task requests. Context does not imply provider disclosure permission.

## Invariants
No uncontrolled continuous sensing; collection is request-scoped or explicitly scheduled. The engine does not persist domain truth, mutate Memory, or execute Actions.

## References
[07_MEMORY.md](07_MEMORY.md), [24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md), [36_ANDROID_ADAPTERS.md](36_ANDROID_ADAPTERS.md)

## Future extension
New sources implement the same budget, freshness, cancellation, and sensitivity contract.
