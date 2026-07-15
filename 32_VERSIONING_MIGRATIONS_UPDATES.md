# 32 — Versioning, Migrations, and Updates

## Purpose
Define independent compatibility, evolution, rollout, rollback, and emergency controls.

## Version domains
Application release, kernel, module, plugin, provider adapter, capability, SDK, event, storage schema, synchronization operation, and presentation model version independently. Manifests declare supported ranges; incompatibility is rejected explicitly.

## Compatibility
Additive optional schema changes are preferred. Required semantic change creates a major contract/type. Readers ignore unknown optional fields and reject unknown required behavior. Compatibility matrices are generated and tested.

## Schema migration
Each owner has a linear migration sequence. Production migration performs preflight, capacity/power check where relevant, backup before destructive transition, transactional change where possible, checkpointed large work, integrity verification, and status recording. Derived backfills run after compatible schema activation.

## Rollback
Application rollback relies on backward-compatible expand/migrate/contract windows, not database reversal. Destructive contraction occurs only after supported readers no longer require old shape and backup policy permits.

## Rollout
Core and plugin updates are signed and staged by cohort with health gates, pause, and rollback. New permissions or broader data use require renewed user approval before activation.

## Emergency controls
Signed, expiring compatibility policy may disable provider/capability, quarantine plugin, or reduce rollout. It cannot grant permission, collect data, rewrite domain state, or bypass confirmation.

## Failure
Interrupted migrations resume from checkpoints. Failed owners remain unavailable/degraded without exposing partially migrated writes. Diagnostics show safe remediation.

## References
[29_CONFIGURATION_SETTINGS_FLAGS.md](29_CONFIGURATION_SETTINGS_FLAGS.md), [34_BUILD_PACKAGING_RELEASES.md](34_BUILD_PACKAGING_RELEASES.md)

## Future extension
Phase 3 may define concrete support windows and release trains.
