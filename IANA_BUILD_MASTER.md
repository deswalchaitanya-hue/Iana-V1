# IANA Build Master

## Authority and use

This is the final implementation planning document for IANA. It operationalizes the immutable engineering repository and the authoritative Implementation Contract Packs without changing product scope, architecture, ownership, interfaces, security policy, or terminology. If this document conflicts with an earlier authoritative specification or contract pack, the earlier authority wins and implementation stops until the conflict is resolved through the repository's approved architecture-decision process.

The intended readers are human leads and multiple AI coding assistants working concurrently. Each implementation task MUST name one owning module, one contract version, its allowed dependencies, its acceptance tests, and the evidence required for review. No further specification document is required before implementation begins.

## Executive Summary

IANA will be built as a local-first, privacy-sensitive modular monolith on Android, with explicit isolation for plugins and failure-prone workloads and a small set of optional cloud services. The implementation sequence follows the dependency law inward to outward: pure Domain Core contracts first; Kernel contracts and infrastructure ports next; bounded-context persistence and provenance next; Application Capabilities next; Context, Reasoning, Action, Automation, and Surface engines next; Android adapters and renderers last. This order keeps every team compiling against stable inward contracts and prevents surfaces, providers, plugins, or infrastructure from becoming accidental authorities.

Development is contract-first and test-first. AI assistants work in module-scoped branches or worktrees, modify one ownership boundary at a time, and submit independently reviewable changes with contract, architecture, security, migration, and compatibility evidence. The first usable release is intentionally narrow: a secure local runtime that can capture and retrieve explicit Memory, manage Projects and Commitments, assemble bounded context, produce non-executing reasoning proposals, surface Attention, and execute only transparently planned, explicitly confirmed low-risk Actions.

## Development Philosophy

1. **Authority before convenience.** Canonical state changes only through owner capabilities. No foreign-table access, inferred schemas, shared business utility packages, or implementation imports across modules.
2. **Contracts before implementations.** Public schemas, ports, events, errors, fixtures, and conformance kits land before production adapters or module internals.
3. **Determinism around nondeterminism.** IDs, clocks, scheduling, fixtures, provider responses, state transitions, authorization, validation, and budgets are deterministic in tests. Model output remains an untrusted proposal.
4. **Local authority by default.** Personal state remains device-authoritative. Derived indexes, caches, projections, and cloud copies are explicitly rebuildable or subordinate.
5. **Deny by default.** Every boundary authenticates, authorizes, minimizes, redacts, budgets, and audits. UI checks never replace execution-time authorization.
6. **Failure is ordinary.** Offline operation, process death, revoked permission, stale surfaces, unavailable providers, replay, and partial external effects are designed and tested paths.
7. **Small, reversible increments.** A change has one owner and one purpose. Cross-module work begins with a compatibility proposal and consumer inventory, not opportunistic edits.
8. **Evidence over confidence.** Passing conformance suites, reproducible builds, migration tests, compatibility reports, and review records are proof; generated prose or model confidence is not.
9. **Bound everything.** Payloads, pages, traversals, retries, queues, concurrency, tool loops, cost, retention, and deadlines have validated limits and cancellation.
10. **Promote identical artifacts.** Environments differ through validated configuration, never rebuilds or hidden code paths.

## Repository Overview

The implementation repository SHOULD mirror ownership rather than feature screens:

```text
/contracts                 Versioned public schemas, errors, events, ports, fixtures
/domain-core               iana.domain implementation and pure tests
/kernel                    iana.kernel implementation and platform contracts
/modules/<owner>/contract  Owner-exported API/event schemas only
/modules/<owner>/impl      Private aggregates, repositories, migrations, outbox
/engines/<name>/contract   Engine API/event schemas
/engines/<name>/impl       Private policies and durable operation state
/application               iana.application orchestration
/platform/android          Android permission, storage, scheduler, process, IPC adapters
/providers                 Provider adapters behind Provider SDK contracts
/plugins                   First-party isolated plugin packages
/surfaces                   HUD, Samsung Mode, widgets, notifications, voice renderers
/cloud                     Optional relay, backup, gateway, and heavy-compute services
/sdk                       Capability, Surface, Provider, Integration, Testing SDKs
/test-kits                  Shared producer/consumer conformance suites and fixtures
/build-logic                Dependency, ownership, compatibility, and supply-chain gates
```

Visibility MUST make implementation packages private outside their owning module. Only contract packages cross ownership boundaries. Every owner has its own schema namespace, migration sequence, tests, manifest, event registrations, and compatibility range. Generated artifacts are reproducible from checked-in schemas and locked toolchains; generated output is not hand-edited.

The authoritative implementation units are:

| Layer | Units |
|---|---|
| Foundation | `iana.domain`, `iana.kernel` |
| Orchestration | `iana.application` |
| Bounded contexts | `iana.context`, `iana.memory`, `iana.projects`, `iana.commitments`, `iana.attention`, `iana.provenance` |
| Engines | `iana.engine.context`, `iana.engine.reasoning`, `iana.engine.action`, `iana.engine.automation`, `iana.engine.surface` |
| Platform boundaries | capability broker, storage/outbox, events, security, configuration, observability, synchronization, networking/background work, provider routing, plugin runtime, Android adapters |
| Product surfaces | HUD/IANA Mode, Samsung Mode, widgets, notifications, voice |

## Dependency Graph

Compile-time dependencies point inward. Runtime callbacks flow outward only through injected ports.

```text
Android/One UI Renderers ───────────────┐
Widgets / Notifications / Voice ────────┼──> Surface Engine ──> Application Capabilities
Plugins ─> Capability Broker ───────────┤                              │
Automation Engine ──> Action Engine ────┤                              ├──> Bounded-context ports
Reasoning Engine ──> Context Engine ────┘                              │
        │                         │                                     └──> Domain Core
        └──> Provider Router      └──> authorized query capabilities

Bounded contexts ──> Domain Core + Kernel-owned technical ports + Provenance port
Android/Cloud/Store adapters ──> inward-owned platform service contracts
Kernel ──> Domain shared envelopes + injected adapter ports
```

Allowed principal edges:

- `iana.kernel` depends only on shared Domain envelopes and injected technical ports.
- `iana.domain` depends only on standard language facilities and deterministic generated validators.
- `iana.application` depends on Domain, owner command/query ports, Kernel ports, policy, idempotency, unit-of-work, and Provenance.
- Each bounded context depends on Domain, Kernel technical ports, permission/encryption as required, and Provenance; never peer repositories.
- Context Engine depends on Application query capabilities for Context Graph, Memory, Projects, Commitments, and Attention.
- Reasoning Engine depends on Context Engine, Provider Router ports, validation, policy, and Provenance.
- Action Engine depends on Application capabilities, risk/confirmation policy, Provenance, and registered capability executors.
- Automation Engine depends on Kernel scheduling/events, Application queries, Action Engine, Attention submission, and Provenance.
- Surface Engine depends on Application capabilities and approved explanation capabilities; renderers depend on Surface Engine.
- Platform adapters implement inward-owned ports and never export Android, database, network, provider, or telemetry types inward.

CI MUST reject cycles, foreign implementation imports, foreign storage access, unregistered events, internal-type SDK leakage, and undeclared capability use.

## Module Build Order

| Order | Unit | Why now | May proceed in parallel with |
|---:|---|---|---|
| 1 | Contract toolchain and architecture gates | All teams need identical schema generation, fixtures, manifests, visibility rules, and compatibility checks. | Repository/build bootstrap only |
| 2 | Domain Core | Every module needs canonical IDs, metadata, results, transitions, redaction inputs, and errors. | Kernel port interface stubs after schemas freeze |
| 3 | Platform Kernel | Composition, lifecycle, capability registration, events, configuration, scheduling, health, cancellation, and correlation are shared runtime prerequisites. | Domain Core completion and technical adapter fakes |
| 4 | Provenance | All ingestion, inference, mutation, confirmation, and Action work requires immutable evidence and receipts. | Storage/outbox/security adapters |
| 5 | Context Graph, Memory, Projects, Commitments | These owners share only stable foundation contracts and can be implemented independently. | One assistant/team per module |
| 6 | Attention Inbox | It needs stable producer event contracts and scheduling behavior, but no producer repositories. | Late work in the four contexts |
| 7 | Application Capabilities | Owner ports must be stable before orchestration can be implemented and tested without schema invention. | Context conformance hardening |
| 8 | Context Engine | Requires authorized read capabilities and stable source DTOs. | Surface semantic schemas and provider fixture work |
| 9 | Reasoning Engine | Requires bounded Context Assemblies and a validated provider boundary. | Action Engine state-machine implementation against fixtures |
| 10 | Action Engine | Requires capability execution, confirmation receipts, risk policy, and durable operation infrastructure. | Reasoning integration and low-risk executor adapters |
| 11 | Automation Engine | Must reuse mature Action authorization and confirmation rather than creating another execution path. | Surface Engine |
| 12 | Surface Engine | Requires stable Application and engine DTOs to produce renderer-neutral models. | Automation and Android adapter work |
| 13 | Android adapters and renderers | Outermost code is built against stable Surface/Application/Kernel contracts. | One team per renderer/adapter |
| 14 | Plugins, provider adapters, sync/cloud services | External and isolated boundaries come after capability, security, IPC, and local authority are proven. | Controlled, contract-specific tracks |

## Parallel Development Strategy

### Work partitioning

- Assign one durable code owner and one AI workstream per module implementation package.
- A workstream receives only its Contract Pack, referenced authoritative documents, public dependency contracts, test kit, and task-specific acceptance criteria.
- Shared contract directories have serialized ownership. Implementation directories permit parallel work.
- No workstream edits another owner's migrations, fixtures, manifest, event schema, or implementation.
- Cross-module changes split into: contract proposal, owner change, generated consumer inventory, consumer PRs, and final compatibility gate.

### Branch and merge discipline

- Branch naming: `module/<module-id>/<change-id>` for implementation; `contract/<owner>/<version>` for serialized contract work.
- Rebase or merge from the integration branch before review; do not resolve semantic contract conflicts automatically.
- Generated files are produced by one pinned generator version and validated for clean regeneration.
- Contract changes merge before consumers, but remain backward compatible until every registered consumer is migrated.
- Migrations are append-only and numerically namespaced by owner to avoid file collisions.
- Event/capability registries are generated from owner manifests rather than manually maintained shared lists.

### Integration cadence

Each module maintains a green owner branch against contract fakes. The integration branch accepts only changes that pass owner tests plus repository-wide architecture and compatibility gates. Nightly integration runs real storage/outbox, event replay, security matrices, migrations, process-death scenarios, and recorded-provider suites. Device and live-provider suites run separately and never replace deterministic CI.

## Sprint-by-Sprint Roadmap

A sprint is a delivery checkpoint, not a fixed duration. A sprint closes only when its exit criteria are met.

### Sprint 0 — Repository and enforcement bootstrap

**Why this order:** Parallel AI development is unsafe until boundaries are mechanically visible and review evidence is standardized.

**Required inputs:** Immutable specifications, 14 Contract Packs, package graph, canonical module IDs, dependency law.

**Expected outputs:** Ownership-aligned repository skeleton; locked toolchain; contract generator; module manifest format; architecture rules; owner code map; PR template; change-evidence manifest; deterministic clocks/IDs; test-kit skeleton; secret/static/dependency scanners.

**Exit criteria:** Empty or skeletal modules compile independently; a deliberately forbidden import, cycle, foreign migration, unregistered event, and SDK leak each fail CI; generated contracts reproduce cleanly.

**Validation:** Clean checkout build; regeneration diff; negative architecture fixtures; SBOM generation; cache-independent reproducibility smoke test.

### Sprint 1 — Domain Core and shared contract primitives

**Why this order:** Every later API and event relies on canonical IDs, references, metadata, errors, classifications, and transition results.

**Required inputs:** Domain Core Contract Pack, domain model, error model, security classification rules.

**Expected outputs:** Pure value objects and validators; request/result/event envelopes; transition and redaction policy inputs; serialization fixtures; property-test generators.

**Exit criteria:** No I/O or outward dependencies; invalid values are rejected; supported versions round-trip; deterministic property tests pass; package visibility is enforced.

**Validation:** Unit/property tests, schema compatibility, mutation testing of invariants, dependency scan, maximum-input bounds.

### Sprint 2 — Kernel contracts and deterministic runtime

**Why this order:** Hosted modules need composition, lifecycle, capability discovery, event transport, scheduling, configuration, health, cancellation, and correlation before durable integration.

**Required inputs:** Domain primitives, Kernel Contract Pack, capability/event/security/configuration contracts.

**Expected outputs:** Explicit composition root; lifecycle state machine; capability registry; event and scheduler abstractions; configuration and secret-reference ports; health model; in-memory deterministic fakes.

**Exit criteria:** Dependency ordering and cycle rejection work; duplicate capability ownership fails; deadlines/cancellation propagate; deny-by-default resolution works; optional dependency degradation is explicit.

**Validation:** Kernel unit/contract suites, grant matrix, lifecycle race tests, poison-event fixture, structured-log redaction checks.

### Sprint 3 — Durable platform foundations and Provenance

**Why this order:** Canonical modules require reliable local transactions, outbox publication, migrations, encryption, receipts, and audit lineage before storing user data.

**Required inputs:** Kernel ports, storage/event/security/provenance contracts, Android storage constraints.

**Expected outputs:** Relational store adapter; module schema namespaces; transaction/outbox implementation; migration runner; encryption/key-reference adapter; append-only Provenance implementation; telemetry redaction; backup/restore test fixtures.

**Exit criteria:** Data and outbox commit atomically; crash replay deduplicates; migrations resume after interruption; Provenance verifies append-only receipts; keys/secrets never appear in logs or configuration values.

**Validation:** Real-store integration, fault injection between write and publish, replay, tamper detection, key rotation fixture, migration snapshots, diagnostic bundle review.

### Sprint 4 — Independent bounded contexts

**Why this order:** Context Graph, Memory, Projects, and Commitments are authoritative sources for later orchestration and engines, yet can be built concurrently behind stable foundation contracts.

**Required inputs:** Domain and Kernel contracts, Provenance capability, permission/encryption ports, each owner Contract Pack.

**Expected outputs:** Four owner implementations; private schemas and migrations; command/query ports; owner events/outboxes; deterministic fixtures and conformance suites; bounded indexes/projections.

**Exit criteria:** Ownership isolation, optimistic concurrency, idempotency, authorization/redaction, provenance, atomic outbox, migration restart, bounded queries, and owner-specific invariants pass independently.

**Validation:** Unit/property tests, API/event producer and consumer tests, real persistence, crash replay, stale-version conflicts, privacy deletion/export, maximum configured loads, repository scans proving no peer access.

### Sprint 5 — Attention Inbox and Application Capabilities

**Why this order:** Attention consumes stable facts from the initial contexts, while Application Capabilities provide the only stable orchestration boundary required by all engines and surfaces.

**Required inputs:** Stable owner ports/events, Kernel scheduling/events, permission and confirmation policies, Provenance.

**Expected outputs:** Attention lifecycle/dedup/ranking implementation; capability descriptors; authenticated command/query envelopes; idempotency store; authorization coordination; transaction and compensation receipts; minimized DTO mappings.

**Exit criteria:** Event storms deduplicate; consequential operations require scoped confirmation; queries are side-effect free; commands replay safely; unauthorized operations make no mutation or existence leak; no application code accesses repositories directly.

**Validation:** Capability conformance, producer grant matrix, duplicate/event-storm tests, compensation fault injection, stale confirmation tests, DTO redaction snapshots, architecture scans.

### Sprint 6 — Context Engine and local MVP read path

**Why this order:** Reasoning must receive a bounded, authorized, provenance-rich Context Assembly rather than query domain stores or assemble hidden prompts.

**Required inputs:** Application query capabilities, source DTOs, permission/redaction policies, Context Engine Contract Pack.

**Expected outputs:** Purpose-scoped assembly; deterministic ranking/truncation; source-version tracking; omissions and freshness; encrypted derived cache; invalidation; explainability receipts.

**Exit criteria:** Every fragment is authorized and attributable; budgets and deadlines are enforced; partial/stale results are explicit; caches are principal/purpose isolated; source modules are never mutated.

**Validation:** Multi-source integration, source timeout, permission revocation, deterministic ordering, invalidation storm, cache isolation, redacted telemetry, configured maximum budgets.

### Sprint 7 — Provider boundary and Reasoning Engine

**Why this order:** Provider nondeterminism is admitted only after context minimization, policy, budget, provenance, and deterministic validation exist.

**Required inputs:** Context Assemblies, Provider SDK/router contracts, recorded normalized fixtures, output schemas, privacy/residency and budget policy.

**Expected outputs:** Stateless provider routing; local/remote adapter contract implementations; structured-output validation and bounded repair; reasoning run state; citations, uncertainty, explanations; fallback disclosure; cancellation.

**Exit criteria:** No provider can mutate domain state or grant permission; every material claim is cited or labeled inference; malformed output fails safely; token/cost/tool/time limits hold; prompts and outputs are absent from default logs.

**Validation:** Recorded-provider CI, schema fuzzing, prompt-injection/tool-denial tests, timeout/cancellation/fallback tests, citation coverage checks, privacy data-flow review, separate live-model evaluation.

### Sprint 8 — Action Engine and constrained execution MVP

**Why this order:** Execution is the highest-risk path and depends on mature authorization, capabilities, receipts, durable operation state, and transparent presentation of consequences.

**Required inputs:** Application command capabilities, Provenance confirmations, risk policy, capability registry, low-risk executor adapters, Action Contract Pack.

**Expected outputs:** Action plan/version/hash; risk classification; confirmation binding; durable execution state machine; per-step idempotency; compensation; receipts; status and cancellation; Attention handoff for unresolved failures.

**Exit criteria:** No hidden step; plan edits invalidate confirmation; execution reauthorizes every step; crash replay never repeats receipted effects; partial completion is visible; only explicitly approved low-risk capabilities are enabled for MVP.

**Validation:** State-machine/property tests, tampered/stale confirmation, confused-deputy and SSRF tests, crash between external effect and receipt, retry/compensation faults, secret redaction, end-to-end confirmed Action on a controlled adapter.

### Sprint 9 — Surface Engine and Android MVP surfaces

**Why this order:** UI is intentionally downstream of stable capabilities and operation state, preventing business rules or authorization from leaking into Activities, overlays, widgets, or voice.

**Required inputs:** Stable Application/engine DTOs, Surface Contract Pack, renderer contracts, accessibility/privacy requirements, Android adapters.

**Expected outputs:** Renderer-neutral semantic models; stale-revision protection; loading/empty/error/redacted states; Samsung Mode MVP; minimal HUD invocation; notification delivery for Attention; Android permissions/lifecycle/process recovery adapters.

**Exit criteria:** Controls map to declared capabilities; unauthorized fields are absent; stale interactions cannot execute; process death restores durable operations; accessibility semantics and private-state concealment pass; renderers contain no domain logic.

**Validation:** Model snapshots/contracts, accessibility automation, localization, permission revocation, process death, stale widget/surface, offline behavior, overlay conflict, device matrix smoke tests.

### Sprint 10 — Automation, hardening, and Version 0.1

**Why this order:** Automation reuses Action safety and is introduced only after users can inspect, confirm, cancel, and understand manual execution.

**Required inputs:** Stable events/scheduler, Action Engine, Attention, scoped grants, run budgets, deterministic trigger fixtures.

**Expected outputs:** Inspectable automation definitions; deterministic triggers/conditions; dry run; grant expiry; pause/dead-letter; bounded catch-up; initial diagnostics; release candidate artifacts.

**Exit criteria:** Automations cannot expand authority or bypass Action confirmation; replay and event storms are bounded; repeated failures pause visibly; all Version 0.1 quality gates and human checkpoints pass.

**Validation:** Trigger/schema mismatch, grant expiry, recursion/rate limits, poison event, crash replay, battery/background constraints, full regression, reproducible signed build comparison.

### Sprint 11 — Version 0.5 integration breadth

**Why this order:** Provider/plugin/sync breadth follows proof of local authority and capability security, reducing the blast radius of external boundaries.

**Required inputs:** Stable public SDKs, broker/IPC contracts, sync merge policies, signing and compatibility infrastructure, Version 0.1 telemetry and defect evidence.

**Expected outputs:** Additional Android surfaces; approved provider adapters; first-party isolated plugin; encrypted cross-device sync/backup pilot; richer Actions and Automations; migration/backfill operations; expanded diagnostics.

**Exit criteria:** Plugin isolation, payload/resource limits, quarantine, signature continuity, domain-owned sync conflict handling, tombstones, key revocation, and compatibility ranges pass controlled rollout gates.

**Validation:** IPC death/mismatch/cancellation, malicious plugin fixtures, sync partitions/conflicts, key/device revocation, migration from every 0.1 schema, provider circuit breaker, expanded device matrix.

### Sprint 12 — Version 1.0 readiness and general availability

**Why this order:** General availability requires demonstrated upgrade safety, operational recovery, privacy controls, and stable contracts—not merely feature completion.

**Required inputs:** Version 0.5 field evidence, supported-device policy, finalized public compatibility ranges, release/security/privacy approvals.

**Expected outputs:** Stable 1.0 APIs and schemas; complete export/deletion/private-session controls; signed artifacts and SBOM; operational runbooks; staged rollout controls; support diagnostics; deprecation and recovery plans.

**Exit criteria:** No open release-blocking security, data-loss, migration, Action-safety, accessibility, or architecture issue; supported upgrade paths pass; rollback uses compatible binaries; on-call and incident exercises succeed; human release board approves.

**Validation:** Full quality matrix, destructive migration backup/restore, disaster and key-recovery drills, privacy audit, penetration testing, battery/performance baselines, limited-production soak, reproducible artifact verification.

## MVP Definition

The MVP is the smallest end-to-end proof of IANA's category and architecture, not a mock or UI-only prototype. It includes:

- Device-authoritative encrypted local data and owner-scoped migrations.
- Explicit user capture, correction, search, explanation, retention, and forgetting of Memory.
- Project creation/update and Commitment creation/transition with provenance.
- Personal Context Graph references and bounded traversal sufficient to connect MVP records.
- Attention Inbox for confirmations, due matters, failures, and corrections.
- Application Capabilities as the only surface/engine mutation path.
- Context Engine assemblies with budgets, citations, freshness, omissions, and redaction.
- Reasoning Engine producing structured, cited proposals with uncertainty; no autonomous mutation.
- Action Engine supporting a small allowlisted set of low-risk, reversible or locally contained capabilities with exact confirmation and receipts.
- Surface Engine plus one complete conventional Samsung Mode flow; minimal HUD and notification paths may invoke the same capabilities.
- Offline behavior, process-death recovery, export/delete controls, diagnostics, and deterministic CI.

MVP excludes a public plugin marketplace, unrestricted third-party plugins, broad autonomous Actions, permissive background sensing, undocumented cloud authority, universal sync conflict resolution, and large surface breadth.

## Version 0.1

Version 0.1 is an internal/controlled-test implementation of the MVP. It proves architecture, local durability, privacy controls, bounded context assembly, recorded-provider reasoning, confirmed low-risk Actions, one complete Android flow, and deterministic recovery. Contracts may evolve additively within declared pre-1.0 compatibility policy, but ownership and dependency law are already mandatory. Release gates emphasize data integrity, migration, denial behavior, Action replay, process death, and log redaction.

## Version 0.5

Version 0.5 is a limited-production beta. It expands provider choices, surfaces, low- and medium-risk Actions where confirmation and compensation are proven, visible Automations, controlled sync/backup, and a first-party isolated plugin. Public SDKs remain limited to approved partners or internal consumers. Upgrade paths from every supported 0.1 schema, field diagnostics, battery behavior, plugin quarantine, sync conflicts, and key revocation become release gates.

## Version 1.0

Version 1.0 is the first general-availability contract baseline. It stabilizes supported application, event, capability, module, SDK, provider, storage, and synchronization versions; documents compatibility and deprecation windows; and provides complete privacy, recovery, diagnostics, and staged-release operations. The release requires sustained controlled-production evidence that no external provider, plugin, cloud service, renderer, or automation can bypass local authority, scoped permissions, confirmation, or provenance.

## High-Risk Components

| Component | Primary risks | Required controls |
|---|---|---|
| Action Engine and executors | Irreversible effects, confused deputy, replay, partial completion, secret exposure | Exact plan-hash confirmation, execution-time authorization, idempotency, receipts, compensation, SSRF allowlists, crash tests |
| Security/capability broker | Authority escalation, transitive grants, existence leaks | Deny-by-default matrices, short-lived scoped tokens, non-transitivity, purpose binding, immutable audit |
| Storage, migrations, deletion | Data loss, corruption, foreign ownership, incomplete erasure | Owner namespaces, atomic outbox, backups, snapshot migration suite, interruption/resume, privacy verification |
| Synchronization/key management | Conflict corruption, permissive merges, stale devices, key compromise | Domain-owned merge, signed tombstones, device revocation, separate rotatable keys, partition tests |
| Plugin runtime and IPC | Untrusted code, resource abuse, permission escalation, schema mismatch | Process isolation, signed manifests, broker-only authority, payload/deadline limits, quarantine, compatibility checks |
| Reasoning/provider boundary | Sensitive disclosure, hallucination, prompt injection, cost loops | Context minimization, policy routing, structured validation, citations, bounded tools/tokens/cost, provider distrust |
| Automation Engine | Hidden recurrence, authority growth, event storms, infinite catch-up | Inspectability, scoped expiring grants, reauthorization, rate/recursion bounds, pause/dead-letter, Action confirmation |
| Android overlay/voice/notifications | Permission misuse, privacy exposure, lifecycle failure, feedback loops | Separate adapters, concealed defaults, accessibility, process recovery, ingestion/delivery separation |
| Provenance and confirmations | Forged approval, mutable history, sensitive retention | Append-only lineage, issuer authentication, scoped nonce/expiry, minimized content, tamper verification |

High-risk changes require a human security/privacy reviewer and owner reviewer even when all automated gates pass.

## Low-Risk Components

Low-risk does not mean unreviewed. Relatively low-risk units include pure Domain value objects, deterministic schema validators, generated DTOs, test fixtures containing synthetic data, renderer styling behind unchanged semantic models, rebuildable indexes behind stable ports, and local diagnostic formatting after redaction. They remain subject to ownership, compatibility, accessibility, reproducibility, and architecture tests; their review may use lighter human sampling when no security, data, contract, migration, or dependency change exists.

## Critical Milestones

1. **Mechanical architecture gate:** forbidden edges and ownership violations fail before product code begins.
2. **Foundation freeze:** Domain and Kernel 1.0 implementation contracts have passing conformance kits.
3. **Durability proof:** atomic owner write/outbox, crash replay, migration resume, and backup/restore pass.
4. **Authority proof:** all bounded contexts pass permission, provenance, idempotency, and foreign-access tests.
5. **Read-path proof:** authorized Context Assembly is bounded, explainable, redacted, and deterministic.
6. **Intelligence proof:** Reasoning produces validated cited proposals without mutation or hidden context acquisition.
7. **Execution proof:** confirmed Action survives crash/replay and produces durable receipts without repeated effects.
8. **Surface proof:** process death, stale revision, offline state, accessibility, and privacy work on target devices.
9. **Automation proof:** recurrence remains visible, scoped, revocable, bounded, and subordinate to Action safety.
10. **Release proof:** reproducible signed artifacts promote through controlled rollout with migration, privacy, and recovery evidence.

## Testing Strategy

### Required layers

- **Domain:** pure unit, property, state-transition, serialization, and invariant tests with controlled clocks/IDs.
- **Capability:** authentication, authorization, confirmation, idempotency, transaction, compensation, result/error, and side-effect-free query tests.
- **Contract:** producer and consumer conformance for every public API, event, port, provider, plugin, storage adapter, Android adapter, and SDK.
- **Integration:** real relational/blob/outbox/event boundaries with fault injection and restart.
- **Process:** IPC death, cancellation, version mismatch, payload/resource limits, and recovery.
- **Security:** denial, scope, replay, confused deputy, SSRF, redaction, isolation, key rotation, and audit completeness.
- **Migration:** every supported production snapshot, interrupted migration, resume, backfill, application compatibility, backup/restore.
- **AI:** normalized recorded responses in blocking CI; separate live-model evaluations for quality and drift.
- **Device:** supported Android/One UI versions, memory/process pressure, permission revocation, background/battery policy, offline state, overlay/accessibility conflict.
- **Architecture:** cycles, forbidden imports, foreign storage, undeclared events/capabilities, SDK leaks, manifest/ownership mismatch.

Every public contract ships fixtures plus producer and consumer tests for schema, errors, versioning, cancellation, timeout, permission, idempotency, and degradation. Production personal data is prohibited in tests. Flaky tests require an owner, issue, expiry, and explicit release-risk disposition; random tests persist their seed.

### Validation pyramid and gates

Fast pure and architecture tests run per change. Module contract/integration/security tests run per module PR. Repository-wide compatibility, migration, replay, and recorded-provider suites gate integration. Process and device suites gate release candidates according to risk. Live-provider quality, battery endurance, penetration, disaster recovery, and staged-production soak are separate evidence streams and cannot compensate for deterministic failures.

## CI/CD Strategy

### Pull-request pipeline

1. Validate change scope and owner manifest.
2. Regenerate schemas/contracts and require a clean diff.
3. Compile affected module and public consumers.
4. Run formatting, static analysis, secret/license/vulnerability/policy scans.
5. Run unit/property and owner conformance suites.
6. Run architecture and visibility rules repository-wide.
7. Run API/event/schema compatibility and consumer inventory.
8. Run applicable security, migration, integration, and replay suites.
9. Produce a machine-readable evidence manifest and SBOM delta.

### Integration and release pipelines

Nightly builds execute the full real-store/outbox/event/provider-fixture suite and reproducibility comparison. Release candidates add process tests, supported migration snapshots, device matrix, signing, provenance attestation, complete SBOM, and privacy/security checks. The same signed artifact progresses through internal, controlled test, limited production, expanded production, and general availability. Configuration is validated per environment; artifacts are never rebuilt for promotion.

Rollout health includes crash/error, migration, security denial anomalies, provider/plugin health, queue/outbox lag, battery, and Action outcomes. Signed emergency compatibility policy may disable a broken provider, quarantine a plugin, disable a dangerous capability, or reduce rollout; it may never grant permission or enable data collection.

## Code Review Strategy

Every change requires:

- One owning-module reviewer.
- A generated dependency/consumer/compatibility report.
- Tests run and artifacts produced.
- Security and data classification.
- Migration, event, capability, and configuration impact.
- Assumptions and unresolved decisions.
- Architecture references for non-obvious choices.

Contract, security, permissions, migrations, sync, plugins, provider disclosure, Action execution, or privacy changes require a designated human specialist. AI-generated code receives the same review standards as human code. Reviewers reject duplicated contract types, foreign schema knowledge, ambient globals, unbounded work, sensitive logging, speculative abstractions, disabled tests, silent degradation, or implementation behavior not represented in contracts.

A cross-module change cannot be approved as one opaque diff. It is reviewed as an owner contract change followed by independently reviewable consumer adaptations. Generated code and migrations are reviewed at their source schema/input and verified by deterministic regeneration.

## AI Coding Workflow

For every AI task:

1. Select exactly one owner and load its Contract Pack, referenced authorities, public dependency contracts, and conformance kit.
2. State objective, allowed files, forbidden files, invariants, inputs/outputs, security classification, and acceptance criteria.
3. Inspect existing owner patterns and consumer inventory; do not infer foreign implementation or database shape.
4. Implement behind existing contracts with constructor-injected ports and bounded behavior.
5. Add deterministic tests, synthetic fixtures, negative authorization cases, and failure/replay cases.
6. Run owner tests plus architecture, compatibility, security, and migration gates as applicable.
7. Return a change manifest: files, behavior, tests, compatibility, security/data impact, migration impact, generated artifacts, assumptions, and unresolved decisions.
8. Stop and request human/owner direction if a contract is insufficient or contradictory. Do not redesign locally.

AI assistants MUST NOT modify multiple contexts opportunistically, create a shared business utility package, import internal types, access foreign stores, invent events/capabilities, weaken confirmation, log sensitive content, fabricate decisions, or use nondeterministic model output as proof. Prompts SHOULD include exact file allowlists and a maximum change scope. Separate assistants MAY implement code, generate adversarial tests, and review architecture, but the reviewing assistant must not be treated as a substitute for required human checkpoints.

## Human Review Checkpoints

| Checkpoint | Required human decision |
|---|---|
| Repository bootstrap | Approve package boundaries, ownership, toolchain, and enforcement before module work |
| Domain/Kernel freeze | Approve shared primitives and lifecycle/capability/event semantics |
| First durable schema | Approve ownership, encryption, migration, backup, outbox, and deletion behavior |
| Each public contract change | Approve compatibility classification, consumer inventory, migration, and deprecation |
| First provider data flow | Privacy/security approval of minimization, residency, credentials, retention, and disclosure |
| First executable Action | Security/product approval of risk policy, confirmation UX, idempotency, receipts, compensation |
| First Automation | Product/security approval of scope, visibility, prior consent, rate and pause policy |
| First plugin or sync pilot | Security/privacy approval of isolation, signing, grants, merge, keys, revocation |
| Each release stage | Release board approval based on quality gates and observed health |
| Version 1.0 | Architecture, security, privacy, accessibility, operations, and product sign-off |

Humans also adjudicate any architectural ambiguity, security exception request, destructive migration, data-retention change, new permission, breaking public contract, or release-blocker waiver. No AI assistant may self-approve these decisions.

## Definition of Done

A change is done only when:

- One owner and contract version are identified.
- Behavior matches the Contract Pack and authoritative specifications.
- Public behavior, errors, events, capabilities, configuration, and migrations are versioned and registered.
- Inputs, outputs, collections, retries, concurrency, deadlines, retention, and resource use are bounded.
- Authentication, authorization, confirmation, minimization, redaction, provenance, and audit are implemented at the correct boundary.
- Owner unit/property tests and relevant contract, integration, security, migration, replay, process, AI, device, and architecture tests pass.
- No foreign implementation dependency, foreign storage access, cycle, hidden event coupling, sensitive log, embedded secret, or ambient authority exists.
- Failure, cancellation, retry, compensation, degradation, offline, and process-death behavior is explicit where applicable.
- Telemetry is structured, redacted, purpose-limited, and non-blocking.
- Compatibility and consumer inventory reports are clean or have approved migration plans.
- Generated outputs reproduce cleanly; dependencies are locked; scans and SBOM pass.
- Documentation comments needed to use the implemented public contract are present without rewriting authority.
- Required human reviewers approve and the evidence manifest is attached.

A module is done only when its complete Contract Pack acceptance criteria and conformance suites pass against production-like adapters, migration restart and replay are proven, and its forbidden dependencies are mechanically absent.

## Release Strategy

Releases progress through internal, controlled test, limited production, expanded production, and general availability. Each stage has explicit population, duration/evidence target, health thresholds, and rollback authority. Rollout is segmented by compatibility and device capability; no stage silently broadens permissions or data collection.

Database rollback is not assumed. Forward-only migrations use preflight, backup, transactional changes where possible, checkpoints, resumable backfills, and application compatibility with old/new schemas during rollout. Binary rollback is allowed only while schema/event compatibility remains proven. Plugin and SDK artifacts retain separate signatures, compatibility ranges, and release channels.

Release artifacts include source revision, build identity, publisher, signature, provenance attestation, dependency inventory, SBOM, schema/API compatibility report, migration matrix, test evidence, configuration schema, and known limitations. Release blocking categories are security/permission bypass, data loss/corruption, unbounded high-impact work, Action replay/confirmation failure, migration failure, privacy/export/deletion failure, unsupported accessibility regression, architecture boundary violation, and irreproducible artifact.

## Post-Release Evolution

After 1.0, IANA evolves through additive contracts, owner-controlled migrations, explicit deprecation windows, and measured rollout. New surfaces consume existing capabilities and presentation semantics. New providers implement Provider SDK contracts and disclose capability/privacy/cost differences. New plugins remain isolated and brokered. New bounded contexts require distinct ownership and invariants; new engines require substantial coordination responsibility and an approved architecture decision.

Operational evidence drives priorities: reliability, battery, migration outcomes, denial anomalies, Action/Automation outcomes, provider/plugin health, accessibility, and user-controlled privacy behavior. Rebuildable indexes and projections may evolve independently; canonical authority does not move to cloud, providers, surfaces, or plugins without an explicit approved architectural change. Feature flags expire and are removed; emergency controls remain deny-only.

Every supported release maintains upgrade tests from declared schema snapshots, compatibility ranges, key/signature continuity, export/delete behavior, and recovery drills. Contract removals require consumer inventory, deprecation telemetry that does not expose personal data, migration completion, and human approval. The enduring rule is unchanged: extend through declared ports and contracts, preserve local authority and provenance, and never trade architectural clarity or user control for short-term implementation speed.
