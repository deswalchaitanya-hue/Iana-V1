# 19 — Capability System

## Purpose
Define discovery and authorized execution of narrowly scoped operations.

## Descriptor
Every descriptor contains stable ID/version, owner, input/output schema references, permissions, resource-scope schema, sensitivity, risk, execution mode, timeout, availability contract, idempotency, undo/compensation, compatibility, and error set.

## Registry
The trusted registry is immutable after kernel composition except controlled plugin lifecycle updates. Registration rejects duplicate incompatible IDs, unknown schemas, undeclared owners, invalid risk, and unsatisfied compatibility. Queries return descriptors, never executors or authority.

## Broker
The broker accepts principal, capability request, purpose, resource scope, policy evidence, and deadline. It evaluates policy, narrows scope, issues a token, resolves an executor, enforces limits, and records outcome. Plugins use IPC broker endpoints; trusted modules use the same semantics in process.

## Tokens
Short-lived, signed or process-authenticated tokens bind subject, capability/version, resource scope, purpose, sensitivity, risk, confirmation evidence, issued/expiry times, nonce, and audience. They are non-transitive, non-renewable by executors, and revocable.

## Executors
Executors validate token audience and scope, input schema, deadline, and idempotency before side effects. They expose health and availability, return stable errors, and cannot call unrelated capabilities through ambient authority.

## Availability
States are available, degraded, unavailable, permission-required, incompatible, and quarantined. Discovery reports reason and safe remediation.

## Conformance
Tests cover schemas, denial by default, scope narrowing, expiry, replay, cancellation, timeout, idempotency, error mapping, receipt completeness, and declared undo behavior.

## Invariants
A capability is not a module. Registration grants no permission. Android permission alone is insufficient. Plugins never receive the registry implementation or dependency container.

## References
[12_APPLICATION_CAPABILITIES.md](12_APPLICATION_CAPABILITIES.md), [21_PLUGIN_SYSTEM.md](21_PLUGIN_SYSTEM.md), [24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md)

## Future extension
Remote executors use identical descriptors and tokens with stronger transport authentication.
