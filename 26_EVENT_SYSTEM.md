# 26 — Event System

## Purpose
Define trusted domain facts and sanitized integration contracts with reliable bounded delivery.

## Event envelope
Contains event ID, type/version, aggregate type/ID/version, owner module, user partition, occurred time, correlation/causation IDs, schema reference, sensitivity, provenance reference, and payload.

## Domain vs integration
Domain events are internal trusted facts owned by a context. Integration events are separately versioned, minimized contracts safe across process/plugin/network boundaries. Translation is explicit; the two are never interchangeable.

## Registry
The registry records owner, schema versions, publishers, consumers, ordering key, retention, replay policy, sensitivity, compatibility, and conformance fixtures. Unregistered publication or consumption fails build/configuration.

## Delivery
Owner transaction writes outbox atomically. Dispatch is at least once. Consumers keep idempotency/inbox state and commit effects with acknowledgement where feasible. Ordering exists only per declared aggregate/partition; no global order.

## Replay
Replay requires owner approval, purpose, event/time range, consumer version, dry run, rate limit, and audit. Consumers distinguish live and replay only for operational behavior, not domain truth.

## Dead letters
After bounded retries, failures enter a dead-letter record containing safe envelope metadata, error, attempts, next action, and payload reference under original security. Operators may retry, skip with approval, or repair through a new event; they do not edit history.

## Evolution
Add optional fields where semantics remain. Consumers ignore unknown optional fields. Breaking semantics use new type or major version with adapters and support window. Large binaries remain blob references.

## Prohibitions
Events do not perform immediate validation/authorization, request-response queries, hide required synchronous dependencies, or carry UI state.

## References
[20_MODULE_SYSTEM.md](20_MODULE_SYSTEM.md), [31_ERROR_MODEL.md](31_ERROR_MODEL.md), [33_TESTING_STRATEGY.md](33_TESTING_STRATEGY.md)

## Future extension
Cloud transport may carry integration events through encrypted envelopes without changing ownership.
