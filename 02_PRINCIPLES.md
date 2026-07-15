# 02 — Governing Principles

## Purpose

Establish mandatory decision rules for product behavior, architecture, security, data, AI, interfaces, and long-term maintenance.

## Scope

These principles apply to all first-party modules, surfaces, adapters, providers, plugins, cloud services, SDKs, tests, documentation, and AI-assisted changes. More specific requirements are defined in [03_ARCHITECTURE.md](03_ARCHITECTURE.md) and [04_PLATFORM.md](04_PLATFORM.md).

## Assumptions

IANA is a decade-scale platform handling sensitive personal context under constrained mobile-runtime conditions. Its correctness and comprehensibility matter more than novelty or short-term implementation speed.

## Product principles

1. **Preserve the operating-layer identity.** IANA overlays Android and One UI without replacing them.
2. **Strengthen the core loop.** Features MUST contribute to Capture → Understand → Remember → Resurface → Act or remain outside Core.
3. **Context is more valuable than conversation.** Optimize for fewer repeated explanations, coherent Projects, and resolved Commitments—not longer chats.
4. **Attention, not engagement.** Surface bounded matters requiring judgment; do not create feeds, streaks, or attention-maximizing mechanics.
5. **Proactivity is earned.** Begin quiet. Increase proactivity only through demonstrated accuracy and explicit user control.
6. **Never pretend capability.** Clearly state whether IANA can understand, prepare, open, execute, compensate, or only suggest.
7. **Both modes remain first-class.** HUD/IANA Mode and Samsung Mode MUST use identical domain behavior and may differ only in presentation.
8. **Motion is semantic.** Visual effects MUST communicate listening, interpretation, privacy, confirmation, execution, success, failure, urgency, or hierarchy.

## Architecture principles

1. **Favor boring, proven patterns.** Prefer explicit code and mature technology that remains understandable after ten years over clever abstraction.
2. **Begin with a modular monolith.** Add processes, plugins, events, networking, or cloud services only for isolation, durability, or distribution.
3. **Dependencies point inward.** Surfaces → Capabilities → Engines → Domain Core. Runtime callbacks through ports do not reverse source dependencies.
4. **Keep boundaries explicit.** Modules own contracts and data. No implementation imports or foreign storage access.
5. **Use typed in-process calls by default.** Use events for completed facts and asynchronous reactions, not immediate validation, authorization, or disguised synchronous dependencies.
6. **Separate authority from projections.** Canonical relational state is authoritative; graph, search, summaries, and other projections are rebuildable.
7. **Do not create universal models.** Each bounded context owns semantics, storage namespace, transitions, and merge policy.
8. **Avoid abstraction without purpose.** Introduce interfaces for replaceable infrastructure, security boundaries, contracts, test control, or platform variability—not every class.
9. **No hidden dependencies.** Use constructor injection and explicit composition; prohibit service locators, field injection, reflection magic, and mutable global registries.
10. **Process death and partial failure are normal.** Persist long-running state, degrade narrowly, and never rely on permanent background execution.

## Domain principles

1. AI proposes candidates; deterministic domain code enforces invariants.
2. No bounded context mutates another directly.
3. Shared identifiers are stable Domain Core primitives, not imported aggregate implementations.
4. Provenance accompanies ingestion, inference, Memory mutation, and Action execution.
5. Commitments remain authoritative over derived deadline indexes and Attention candidates.
6. Attention consumes public events rather than foreign repositories.
7. Synchronization applies validated domain changes and never replaces local database files.
8. Every graph traversal is typed, user-partitioned, depth-bounded, and budgeted.

## Security and privacy principles

1. **Deny by default.** No Android permission alone authorizes product use.
2. **Least privilege and explicit purpose.** Authority is narrow, scoped, expiring, and non-transitive.
3. **Data minimization.** Collect and disclose only what the requested operation requires.
4. **User ownership.** Users can inspect, correct, export, expire, and delete personal context.
5. **Local-first authority.** The device owns authoritative personal data by default.
6. **Memory contracts are explicit.** Data may be session-only, Project-scoped, time-limited, permanent, or on-device-only.
7. **Provider disclosure is visible.** Sensitive content is classified before transmission; providers receive bounded task envelopes.
8. **Plugins are untrusted by default.** They receive granted capabilities, never the dependency container, domain database, unrestricted filesystem, or transitive authority.
9. **Observability is not a shadow datastore.** Raw prompts, tokens, notification bodies, personal artifacts, keys, and secrets are not logged by default.
10. **High-risk automation is prohibited.** Payments, legal acceptance, medical decisions, security changes, and destructive operations require future explicit policy and are not initially automated.

## AI and provider principles

1. Providers are replaceable adapters behind task contracts.
2. Provider capability extensions are permitted; a lowest-common-denominator interface is not.
3. The Reasoning Engine cannot mutate domain state or execute Actions.
4. Structured outputs require schema validation, policy checks, and confidence handling.
5. Every operation enforces token, cost, latency, tool, cancellation, and fallback limits.
6. Deterministic CI uses normalized recorded responses; live-model evaluations are separate.
7. Model selection is an implementation concern by default, with user-facing privacy, cost, and quality modes where product strategy requires them.

## Action principles

1. Capability discovery is explicit and versioned.
2. Every Action has a risk level, input/output schema, permission requirements, timeout, idempotency behavior, availability, and undo support declaration.
3. Meaningful Actions provide preview and confirmation according to risk.
4. Every execution uses an idempotency key and produces a receipt.
5. Multi-step Actions use explicit plans and compensation records, not distributed transactions.
6. Plugins and automations cannot bypass Core authorization or confirmation policy.
7. Failures are specific, visible, and narrowly degraded.

## Interface principles

1. Surfaces render state and collect intent; they contain no domain rules.
2. Surface sessions are independent and not tied to an Activity lifecycle.
3. Presentation models are semantic and renderer-neutral.
4. Surface state is immutable, ephemeral, and separate from domain state.
5. Widgets and notifications tolerate stale projections and process termination.
6. Voice is a transport and cannot bypass confirmation.
7. Accessibility is developed as focused, tested capability—not broad marketing claims.

## Data and event principles

1. Use a mature relational database for canonical transactional state.
2. Store encrypted artifacts separately from relational records.
3. Commit state and domain events atomically through a transactional outbox.
4. Delivery is at least once; consumers are idempotent; global ordering is prohibited.
5. Domain and integration events are distinct contracts.
6. Derived indexes and caches are disposable and rebuildable.
7. Deletion uses policy-governed removal and signed tombstones where synchronization applies.
8. Conflict resolution is domain-specific; universal last-write-wins is prohibited.

## Delivery and maintenance principles

1. Contracts are versioned independently and changed additively where possible.
2. Production migrations move forward with preflight checks, checkpoints, backups where destructive, and compatibility-based rollback.
3. Every module provides conformance tests and a Module Contract Pack.
4. Dependency, API, schema, security, migration, and device-matrix rules are automated.
5. Builds are reproducible, dependencies locked and traceable, artifacts signed, and software bills of materials produced.
6. Feature flags are temporary, owned, expiring controls—not permanent configuration.
7. AI coding assistants modify one bounded context at a time. Cross-module work follows approved contract change procedure.

## Decision test

Before accepting a design, ask:

1. Does it preserve product identity and the core loop?
2. Is there an existing category or contract that owns it?
3. Can a simpler typed in-process implementation satisfy the requirement?
4. Is authority explicit, minimal, revocable, and auditable?
5. Is authoritative state clearly owned?
6. Can derived state be rebuilt?
7. Does behavior survive offline operation, process death, retries, and duplication?
8. Can the boundary be tested deterministically?
9. Is the result understandable to a maintainer in ten years?

A “no” requires redesign within the immutable architecture, not invention of a new architectural category.

## Rationale

These principles prevent the known failure modes identified by the Architecture Blueprint: cyclic dependencies, hidden event coupling, excessive abstraction, a god Core, globally ordered event bottlenecks, unbounded graph traversal, weak plugin isolation, Android lifecycle assumptions, authoritative AI output, generic synchronization, nondeterministic testing, conflicting migrations, divergent surfaces, and privacy leakage through observability.

## References

- [00_VISION.md](00_VISION.md)
- [01_PRODUCT_STRATEGY.md](01_PRODUCT_STRATEGY.md)
- [03_ARCHITECTURE.md](03_ARCHITECTURE.md)
- [04_PLATFORM.md](04_PLATFORM.md)

## Future extension notes

New principles require explicit approval against the immutable Product Strategy and Architecture Blueprint. A future document may add operational checklists, but MUST reference rather than restate these rules.
