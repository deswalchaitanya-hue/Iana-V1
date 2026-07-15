# IANA Platform Engineering Specifications

## Purpose

This repository is the authoritative engineering specification for IANA, a user-owned AI Operating Layer for Android and Samsung One UI. It converts the finalized Product Strategy and Architecture Blueprint into stable, cross-referenced requirements for human engineers and AI coding assistants.

## Scope

Phase 1 (`00`–`04`) defines the product vision, strategy, governing principles, system architecture, and platform model. Phase 2 (`05`–`40`) defines the domain, engine, platform, security, delivery, governance, decision-index, and terminology contracts that conform to that foundation. Concrete implementation schemas, APIs, deployment topology, and operational runbooks remain later-phase work.

## Authority and precedence

The source Product Strategy and Architecture Blueprint are immutable. This repository documents them without redesign or optimization. If documents appear inconsistent, apply this precedence:

1. Architecture Blueprint for architectural and technical decisions.
2. Product Strategy for product identity, scope, sequencing, and market decisions.
3. This README for repository governance only.

Do not resolve ambiguity by inventing a new architectural category or product capability. Record the issue for an explicit source-level decision.

## Repository document map

| Range | Authoritative concern |
|---|---|
| [00](00_VISION.md)–[04](04_PLATFORM.md) | Vision, product strategy, governing principles, architecture, and platform foundation |
| [05](05_DOMAIN_MODEL.md)–[11](11_PROVENANCE_AND_RECEIPTS.md) | Domain primitives, bounded contexts, provenance, and receipts |
| [12](12_APPLICATION_CAPABILITIES.md)–[18](18_SURFACES.md) | Application capabilities, engines, and renderer contracts |
| [19](19_CAPABILITY_SYSTEM.md)–[24](24_SECURITY_AND_PERMISSIONS.md) | Capabilities, modules, plugins, SDKs, providers, security, and permissions |
| [25](25_STORAGE_AND_DATA_OWNERSHIP.md)–[32](32_VERSIONING_MIGRATIONS_UPDATES.md) | Storage, events, synchronization, networking, configuration, diagnostics, errors, and evolution |
| [33](33_TESTING_STRATEGY.md)–[38](38_AI_ASSISTED_DEVELOPMENT.md) | Testing, builds, cloud boundaries, Android adapters, privacy controls, and AI-assisted development |
| [39](39_ARCHITECTURE_DECISIONS.md)–[40](40_GLOSSARY.md) | Approved architecture-decision index and canonical terminology |
| [41](41_IMPLEMENTATION_CONTRACT_PACKS.md) and [`contracts/`](contracts/) | Implementation-ready, self-contained contracts for every major architecture module |

## Assumptions

- Android and Samsung One UI are the initial runtime environment.
- IANA Mode and Samsung Mode are separate presentation systems over identical capabilities and domain state.
- Personal data is local-first and device-authoritative by default.
- AI providers are replaceable and never authoritative over domain state.
- Platform constraints, process death, permission revocation, offline operation, and partial failure are normal conditions.
- Boring, proven patterns are preferred over clever abstractions.

## Terminology

Normative terms use the following meanings:

- **MUST / MUST NOT:** mandatory for conformance.
- **SHOULD / SHOULD NOT:** expected unless a documented exception is approved.
- **MAY:** optional within stated boundaries.
- **Core:** stable primitives and invariants shared by product identity; not a universal module.
- **Bounded context:** a separately owned domain boundary.
- **Engine:** substantial domain coordination.
- **Capability:** a narrowly scoped callable operation.
- **Module:** a trusted ownership and build boundary.
- **Service:** a reusable technical function.
- **Adapter:** an implementation against an external platform or system.
- **Plugin:** a separately versioned, restricted extension.
- **Provider:** an interchangeable AI implementation.
- **Surface:** a presentation and input channel containing no domain rules.

Canonical architectural meanings are defined in [03_ARCHITECTURE.md](03_ARCHITECTURE.md).

## Repository rules

1. Define each specification once and reference it elsewhere.
2. Keep product rules out of platform adapters and surfaces.
3. Keep implementation details out of public contracts.
4. Never infer another module’s database shape or import its internal types.
5. Cross-module changes require a contract proposal, compatibility analysis, consumer inventory, and contract tests.
6. Architecture decisions must be traceable to the immutable sources.
7. Future documents may add detail but may not silently alter Phase 1 decisions.
8. AI assistants modify one bounded context at a time unless an approved cross-module change exists.

## Conformance

A change conforms when it preserves the dependency law, data ownership, capability security, local-first authority, surface neutrality, deterministic domain validation, and explicit contract boundaries specified by Phase 1. Automated architecture and contract tests must eventually enforce these rules; prose memory is insufficient.

## Rationale

A small ordered document set gives each concern one authoritative home, reduces contradictory restatement, and lets developers load only the context relevant to a change. Explicit precedence and conformance rules prevent future implementation documents from silently redesigning the approved product or architecture.

## References

- [00_VISION.md](00_VISION.md)
- [01_PRODUCT_STRATEGY.md](01_PRODUCT_STRATEGY.md)
- [02_PRINCIPLES.md](02_PRINCIPLES.md)
- [03_ARCHITECTURE.md](03_ARCHITECTURE.md)
- [04_PLATFORM.md](04_PLATFORM.md)

## Repository status and future extension

Phase 2 specification generation is complete in documents `05`–`40`. Phase 3 implementation contracts are complete in [41_IMPLEMENTATION_CONTRACT_PACKS.md](41_IMPLEMENTATION_CONTRACT_PACKS.md) and [`contracts/`](contracts/). Later phases may add technology-specific schemas, implementations, deployment topology, test projects, and operational runbooks beneath this foundation, but may not silently alter prior authority.
