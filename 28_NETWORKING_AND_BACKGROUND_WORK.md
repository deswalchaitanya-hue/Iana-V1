# 28 — Networking and Background Work

## Purpose
Define bounded network adapters, offline queues, durable jobs, and energy policy.

## Network clients
Only adapters access HTTP transports. Provider, synchronization, plugin service, and product backend traffic use separate clients/policies. Transport enforces authentication, certificate validation, signing where required, timeout, bounded payload, safe redirect, bandwidth policy, and tracing.

## Data classification
Before transmission, payloads are classified, minimized, redacted, encrypted as required, and checked against purpose/grants. Restricted or on-device-only data cannot enter remote queues.

## Retries
Retry only safe/idempotent operations. Policy declares maximum attempts, exponential backoff with jitter, retryable categories, deadline, connectivity constraints, and user-visible terminal state. Authentication/policy/validation failures do not retry automatically.

## Offline queues
Queue records contain operation, destination class, encrypted payload reference, idempotency key, constraints, earliest/deadline times, attempt state, retention, and cancellation. Queue storage cannot outlive source retention.

## Job descriptor
Every job declares ID/version, owner, input schema, constraints, deadline, retry/idempotency, energy class, network requirement, privacy class, foreground requirement, user-visible status, and result/error contract.

## Work categories
Immediate foreground, deferrable durable, periodic maintenance, user-visible long-running, and event-triggered. Android-supported scheduling is authoritative; permanent background services are prohibited.

## Energy policy
Classes are trivial, normal, intensive, and user-initiated-exempt where Android permits. Intensive work requires charging/unmetered constraints unless explicitly user initiated. Battery saver and thermal state may defer nonessential work.

## Recovery
Jobs persist checkpoints, tolerate duplicate launch and process death, verify authorization before side effects, and release partial resources. Cancellation is explicit.

## References
[36_ANDROID_ADAPTERS.md](36_ANDROID_ADAPTERS.md), [31_ERROR_MODEL.md](31_ERROR_MODEL.md)

## Future extension
Cloud workers may implement compatible job contracts while authority remains explicit.
