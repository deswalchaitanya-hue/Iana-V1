# 22 — SDK and Developer API

## Purpose
Define narrow public extension contracts without exposing internals.

## SDK partitions
- Capability SDK: descriptors, executors, triggers, context sources.
- Surface SDK: approved cards, semantic presentation models, intents.
- Provider SDK: normalized inference tasks and provider extensions.
- Integration SDK: external-service adapters and credentials references.
- Testing SDK: fixtures, fakes, broker and IPC conformance.

Dependencies between SDKs are explicit and minimal. Internal repository, engine, kernel, domain aggregate, and database types are never public.

## Stability
Every API is experimental, preview, stable, deprecated, or removed. Stable APIs follow semantic versioning and additive schema evolution where possible. Experimental APIs cannot process production personal data unless explicitly approved.

## Contract requirements
Public APIs declare identity/version, typed schema, permission and purpose semantics, lifecycle, threading/process rules, cancellation, deadlines, payload limits, idempotency, error envelope, compatibility, privacy classification, examples, and conformance tests.

## Permissions
SDK calls use broker-issued tokens. Libraries do not request broad Android permission on behalf of plugins and never cache grants beyond token lifetime.

## Errors
All boundary failures use [31_ERROR_MODEL.md](31_ERROR_MODEL.md). Unknown optional fields are ignored; unknown required semantics fail compatibility checks.

## Testing and publication
SDK artifacts are reproducible, signed, documented, SBOM-covered, and published separately from implementation. Reference fixtures contain synthetic data only. Compatibility tests run against supported host ranges.

## Deprecation
Deprecation declares replacement, migration guide, first deprecated version, supported window, telemetry-safe usage signal where allowed, and removal release. Security revocation may shorten windows through signed emergency policy.

## Invariants
SDK convenience cannot grant authority, hide network access, or reproduce business rules. Public SDK and marketplace remain delayed until internal contracts stabilize.

## References
[20_MODULE_SYSTEM.md](20_MODULE_SYSTEM.md), [21_PLUGIN_SYSTEM.md](21_PLUGIN_SYSTEM.md), [34_BUILD_PACKAGING_RELEASES.md](34_BUILD_PACKAGING_RELEASES.md)

## Future extension
Phase 3 may define language bindings and publication operations without changing these partitions.
