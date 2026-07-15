# 33 — Testing Strategy

## Purpose
Define deterministic layers and conformance gates for a decade-scale platform.

## Layers
- Domain: pure invariants, transitions, property tests, clocks/IDs controlled.
- Capability: authorization, transactions, idempotency, results/errors.
- Contract: shared kits for modules, providers, plugins, storage, Android adapters, SDKs.
- Integration: real relational/blob/outbox and event boundaries.
- Process: IPC death, mismatch, cancellation, payload/resource limits.
- Security: denial, scope, replay, redaction, isolation, rotation, audit completeness.
- Migration: every supported production snapshot, interruption, resume, compatibility.
- AI: normalized recorded responses in CI; live-model evaluation separate.
- Device: Android/One UI matrix, pressure, revocation, background/battery policy, overlay/accessibility conflict, offline.
- Architecture: forbidden imports, cycles, foreign storage, SDK leaks.

## Fixtures
Use synthetic deterministic identities, clocks, content, provider recordings, and event sequences. Production personal data is prohibited. Recorded provider fixtures store normalized contract responses, not credentials or raw sensitive prompts.

## Conformance kits
Each public contract ships producer and consumer tests covering schema, versioning, errors, cancellation, timeout, idempotency, permission, and degradation. Module Contract Packs identify mandatory suites.

## Quality gates
Required gates: unit/contract tests, architecture rules, static analysis, dependency/SBOM scan, schema/API compatibility, migration suite, security checks, and reproducible build comparison. Device suites gate risk-appropriate rollout stages.

## Nondeterminism
Flaky tests are quarantined only with owner, issue, expiry, and blocked release risk. Random/property tests persist seeds. Time and scheduling are virtualized where possible.

## References
[20_MODULE_SYSTEM.md](20_MODULE_SYSTEM.md), [38_AI_ASSISTED_DEVELOPMENT.md](38_AI_ASSISTED_DEVELOPMENT.md)

## Future extension
Phase 3 may define concrete test projects, coverage expectations, and device inventory.
