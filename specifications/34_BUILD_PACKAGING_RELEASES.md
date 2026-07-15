# 34 — Build, Packaging, and Releases

## Purpose
Define package graph, reproducibility, signing, provenance, delivery, and staged release.

## Package graph
Separate Domain Core, bounded-context contracts/implementations, application capabilities, engine contracts/implementations, platform contracts, Android adapters, surfaces, first-party plugins, SDKs, and test kits. Dependencies follow [03_ARCHITECTURE.md](03_ARCHITECTURE.md); cycles fail builds.

## Reproducibility
Toolchains and dependencies are locked and checksummed. Builds use controlled inputs, hermetic steps where practical, deterministic generated code, and recorded provenance. Rebuild comparison covers release artifacts.

## Supply chain
Every artifact has publisher, source revision, build identity, signature, provenance attestation, dependency inventory, and SBOM. Static, secret, license, vulnerability, and policy scans gate release.

## Packaging
Trusted modules ship in the main app unless isolation requires a service process. Large optional features may use verified dynamic delivery. Security-critical executable code is never fetched opaquely. Plugins are separate signed packages.

## Signing
Application, plugin, SDK, compatibility policy, and cloud artifacts use separated protected signing identities and rotation/recovery procedures. Signature continuity is verified during update.

## Release
Stages are internal, controlled test, limited production, expanded production, and general availability. Gates monitor crash/error, migration, security, provider/plugin, battery, and Action outcomes. Rollout can pause or revert compatible binaries.

## Promotion
Identical signed artifacts are promoted; they are not rebuilt per environment. Configuration supplies environment differences without changing authority.

## References
[32_VERSIONING_MIGRATIONS_UPDATES.md](32_VERSIONING_MIGRATIONS_UPDATES.md), [33_TESTING_STRATEGY.md](33_TESTING_STRATEGY.md)

## Future extension
Phase 3 defines repository layout, CI pipelines, signing operations, and release runbooks.
