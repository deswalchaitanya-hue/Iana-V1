# 31 — Error Model

## Purpose
Define domain errors, stable boundary envelopes, retryability, correlation, and narrow degradation.

## Internal errors
Contexts own typed errors for invalid values, invariant violations, transitions, conflicts, stale versions, and missing entities. Internal causes and stack traces do not cross trust boundaries.

## Boundary envelope
Contains stable code, category, safe message key/arguments, retryability, required user action, correlation ID, occurrence time, optional field violations, operation/capability reference, and internal cause reference. It contains no secrets or unredacted personal content.

## Categories
Validation, authentication, permission, policy denial, unavailable capability, provider, network, conflict, timeout, cancellation, storage, plugin, compatibility, migration, resource limit, and invariant violation.

## Retryability
One of never, immediate-once, backoff, after-user-action, or after-system-change. Retry policy additionally requires idempotency and deadline. Clients do not infer retryability from transport status alone.

## User action
Stable actions include correct-input, grant-permission, review-scope, reconnect, wait, choose-provider, update-component, resolve-conflict, free-space, or contact-support. Denials avoid revealing hidden resources.

## Correlation
Opaque correlation follows the operation across modules, IPC, and network. It conveys no authority and contains no personal identifiers.

## Degradation
Failures degrade the smallest boundary: provider failure leaves Memory available; index failure falls back to bounded canonical queries or marks search unavailable; plugin failure quarantines plugin; overlay failure falls back to Samsung Mode; sync failure preserves local authority.

## Handling rules
Invariant violation stops affected mutation and raises operational alert. Cancellation is not failure. Partial Action completion has a specific result and receipt. Unknown external errors normalize to safe boundary codes.

## References
[15_ACTION_ENGINE.md](15_ACTION_ENGINE.md), [30_OBSERVABILITY_AND_DIAGNOSTICS.md](30_OBSERVABILITY_AND_DIAGNOSTICS.md)

## Future extension
Code registries and localization catalogs may be generated from this contract.
