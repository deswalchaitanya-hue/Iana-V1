# 29 — Configuration, Settings, and Flags

## Purpose
Separate operator configuration, user preferences, temporary flags, and signed compatibility controls.

## Configuration
Operator-owned technical values are typed, validated at startup, environment-scoped, secret-referenced, and immutable during a process unless explicitly reloadable. Missing security-critical configuration fails closed.

## Settings
User-owned preferences have stable ID/version, schema, default, scope, provenance, sensitivity, sync policy, validation, and change history. Examples include presentation mode, proactivity, notification frequency, provider mode, privacy controls, and confirmation preference. Settings cannot override mandatory policy.

## Feature flags
A flag declares key/type, owner, purpose, default, targeting scope, created/expiry dates, removal issue, offline behavior, telemetry purpose, and emergency behavior. Flags are temporary release, experiment, emergency, or compatibility controls.

Flags cannot grant permissions, collect new data silently, weaken Action risk, or permanently replace configuration. Expired flags fail build or operational checks until removed/renewed by approval.

## Evaluation
Evaluation is deterministic from signed definitions and explicit context. Local defaults are safe offline. Decisions record flag/version and correlation without personal payload.

## Signed compatibility policy
May disable a provider, quarantine plugin, disable dangerous capability, constrain compatibility, or reduce rollout. It is authenticated, versioned, expiring, rollback-capable, and audited. It cannot enable collection or grant authority.

## Sync and conflicts
Settings sync according to per-setting owner policy. Security/privacy controls choose stricter effective behavior on unresolved conflict; user-visible resolution is required before relaxation.

## References
[24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md), [32_VERSIONING_MIGRATIONS_UPDATES.md](32_VERSIONING_MIGRATIONS_UPDATES.md)

## Future extension
Experiment governance may be detailed operationally without changing prohibited uses.
