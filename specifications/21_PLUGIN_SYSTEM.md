# 21 — Plugin System

## Purpose
Define restricted, separately versioned extensions isolated from Core authority.

## Manifest
Contains plugin ID/version, publisher/signing identity, SDK/host compatibility, components/services, requested capabilities and data scopes, exposed capability descriptors, IPC schemas, migrations, resource limits, network destinations, retention declarations, update policy, and security classification.

## Verification and install
Discovery verifies package integrity, publisher trust, signature continuity, manifest/schema validity, compatibility, prohibited permissions, and review status. Installation does not grant capabilities. Permission review is explicit and renewed for material changes.

## Lifecycle
`Discovered → Verified → Installed → Disabled → Permissioned → Started → Healthy | Degraded | Quarantined → Updated | Removed`.
Kernel and broker persist lifecycle state. Crash loops, policy violations, signature failures, incompatible schemas, or resource abuse trigger quarantine.

## IPC
Versioned request/response messages carry correlation, schema version, deadline, cancellation, payload size, and explicit errors. Binder/bound-service identity is verified. Payloads use public schemas; domain objects never cross IPC.

## Permission model
Plugins receive narrow capability tokens for purpose, resource, and time. Grants are non-transitive. Plugins cannot access databases, Context Graph, unrestricted files, arbitrary providers, trusted domain event publication, unrestricted overlays, or other plugins.

## Resource governance
Host enforces concurrent calls, CPU/time budgets where observable, payload/storage quotas, network policy, retention, and background limits. Process isolation is treated as containment, not a complete hostile-code sandbox.

## Updates and removal
Updates verify signature continuity, compatibility, permission deltas, and migrations before staged activation. Removal revokes tokens, stops services, applies declared data disposition, and preserves required receipts.

## Health
Health reports availability, latency/error bands, compatibility, permission state, crash-loop status, and safe diagnostics. Plugin claims remain untrusted until host validation.

## References
[19_CAPABILITY_SYSTEM.md](19_CAPABILITY_SYSTEM.md), [22_SDK_AND_DEVELOPER_API.md](22_SDK_AND_DEVELOPER_API.md), [32_VERSIONING_MIGRATIONS_UPDATES.md](32_VERSIONING_MIGRATIONS_UPDATES.md)

## Future extension
Marketplace distribution is Version 3 work and cannot weaken verification or grants.
