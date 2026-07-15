# 04 — Platform

## Purpose

Specify the runtime platform that hosts IANA’s architecture: surfaces, Android adapters, capabilities, modules, plugins, providers, security, storage, events, synchronization, observability, testing, packaging, and evolution.

## Scope

This document governs technical platform behavior and boundaries. It does not redefine product scope ([01_PRODUCT_STRATEGY.md](01_PRODUCT_STRATEGY.md)) or domain ownership ([03_ARCHITECTURE.md](03_ARCHITECTURE.md)). Detailed schemas and contracts belong to Phase 2.

## Assumptions

- Android and One UI impose permission, process, scheduling, and background limits.
- Network absence, process death, stale widgets, revoked permissions, unavailable providers, and partial failure are expected.
- Personal state is device-authoritative by default.
- Plugins and providers may fail independently and receive only explicit authority.

## Surface platform

### HUD / IANA Mode

The HUD owns overlay rendering, semantic animation, focus behavior, and user input. It cannot reason, query storage, or execute Actions. It renders Surface Engine states such as listening, interpreting, awaiting confirmation, executing, succeeded, and failed. Its lifecycle follows explicit invocation and Android overlay availability.

### Samsung Mode

Samsung Mode owns conventional One UI-aligned screens, navigation, widgets, and system surfaces. It shares application capabilities, domain state, and presentation semantics with IANA Mode. Neither presentation package imports the other.

### Overlay adapter

The Android Overlay System handles permission, placement, insets, accessibility coexistence, process recovery, and lifecycle. It contains no HUD product rules.

### Widgets

Widgets consume compact read-only projections and submit predefined commands. They tolerate stale state and process termination and never host engines or query domain stores.

### Notifications

Ingestion and delivery are separate adapters. Ingested notifications become normalized source records. Delivered IANA notifications render Attention or Action state. This separation prevents feedback loops.

### Voice

Voice is a transport and renderer composed of speech recognition, turn management, speech synthesis, audio focus, and voice presentation. It submits ordinary capability commands and cannot bypass confirmation policy.

### Rendering pipeline

```text
Domain or Projection State → Surface Engine → Presentation Model
→ Surface Renderer → Android / One UI primitives
```

Presentation models express primary content, supporting evidence, available Actions, privacy, execution state, urgency, and density. Renderer-owned animation is triggered only by semantic transitions.

## Capability system

A capability is a narrowly scoped operation exposed by Core, Android, a provider, or a plugin. Every descriptor declares:

- Stable identifier and version.
- Input and output schemas.
- Required permissions and resource scope.
- Data sensitivity and risk level.
- Execution mode, timeout, and availability.
- Idempotency behavior.
- Undo or compensation support.

A module may expose multiple capabilities. A capability is not a module. The Action Engine discovers operations through the registry and executes them through authorized capability executors.

## Module system

Trusted first-party modules may run in the main process. Every module owns its public contracts, implementation package, data namespace, migrations, events, lifecycle, compatibility range, test contract, and security classification.

Modules MUST NOT read foreign storage, import foreign implementation packages, or use “common” packages for business behavior. Synchronous collaboration uses public interfaces; asynchronous reaction uses events. The kernel composes modules explicitly with constructor injection.

## Plugin system

Plugins default to out-of-process execution through Android bound services or equivalent isolation. A plugin receives capabilities through a broker; it never receives the dependency container, domain database, Context Graph, or unrestricted filesystem.

The runtime includes manifest handling, package verification, compatibility resolution, capability brokerage, permission grants, IPC, resource governance, lifecycle, health, and updates.

```text
Discovered → Verified → Installed → Disabled
→ Permissioned → Started → Healthy | Degraded | Quarantined
→ Updated | Removed
```

IPC uses versioned request/response messages with correlation IDs, deadlines, cancellation, payload limits, explicit errors, and schema versions. Internal domain objects never cross the boundary.

Plugins cannot read Core databases, call arbitrary providers, publish trusted domain events, render unrestricted overlays, escalate or inherit permissions, call plugins directly, exceed retention policy, or execute high-risk Actions without Core authorization.

Android process separation is not a complete hostile-code sandbox. Signed packages, IPC-only authority, payload limits, resource monitoring, revocation, marketplace review, and stronger remote isolation for highly untrusted code are mandatory controls.

## SDK surfaces

The platform maintains separate, intentionally narrow SDKs:

- Capability SDK for Actions, triggers, and context sources.
- Surface SDK for approved cards and presentation models.
- Provider SDK for inference adapters.
- Integration SDK for external services.
- Testing SDK for fixtures, fakes, and broker conformance.

Every public API declares stability, semantic version, schema, permission semantics, error model, conformance tests, and deprecation policy. Internal repository and engine interfaces are never automatically public.

## Provider system

```text
Reasoning Task → Provider Router → Policy and Budget Evaluation
→ Normalized Provider Capability → Provider Adapter → Local or Remote Provider
```

Descriptors declare provider, model, capability matrix, routing policy, cost, privacy, health, credential reference, and usage accounting. Capabilities may include text, structured output, vision, embeddings, speech, tool use, and streaming.

Providers receive bounded task envelopes, not user accounts or databases. They cannot mutate Memory or execute Actions. Routing is stateless; health, usage, and budgets are durable. Circuit breakers isolate failure. Base task contracts permit declared capability-specific extensions.

## Security and permission platform

Security is deny-by-default, least-privilege, purpose-bound, short-lived, auditable, and non-transitive.

Authorization layers are distinct:

1. Android OS permission.
2. IANA feature permission.
3. Plugin capability grant.
4. Data-scope grant.
5. Action-specific authorization.
6. Provider disclosure consent.

An Android permission alone never authorizes product use.

Security components include identity and device trust, capability-token issuance, policy decision and enforcement points, key management, secret storage, classification, redaction, audit ledger, and app exclusions.

Sensitive operations use short-lived tokens containing subject, capability, resource scope, purpose, expiration, risk, and confirmation evidence. Hardware-backed Android Keystore is used where available. Device identity, database, synchronization, backup, and plugin-channel keys remain separate and rotatable.

## Storage

The device is authoritative for personal data by default.

- **Transactional domain store:** mature relational storage for canonical Memory, Project, Commitment, Action, and Attention state.
- **Graph projection:** rebuildable adjacency-oriented relationship indexes.
- **Search indexes:** rebuildable full-text, semantic, and metadata indexes.
- **Blob store:** encrypted artifacts separated from transactional records.
- **Event outbox:** atomic domain-event persistence with state changes.
- **Audit store:** append-only security and provenance with stricter retention.
- **Ephemeral cache:** rebuildable values only.

Each bounded context owns its schema. Cross-context reporting uses projections or APIs, never foreign-table joins. A universal data model is prohibited.

## Networking and background work

Only network adapters access HTTP clients. Domain modules depend on service ports. Transport provides authentication, certificate validation, signing, bounded retries, connectivity and bandwidth policy, payload encryption, tracing, and safe offline queues.

Mutations require idempotency keys. Retries apply only to safe or explicitly idempotent operations. Sensitive data is classified before transmission. Provider and synchronization traffic use separate clients and policies.

Android-supported scheduling replaces permanent background processes. Every job declares constraints, deadline, retry, idempotency, energy class, network requirement, privacy class, and user-visible status. Work is categorized as immediate foreground, deferrable durable, periodic maintenance, user-visible long-running, or event-triggered.

## Synchronization and cloud

Cross-device synchronization uses local-first replication with encrypted operation envelopes. It handles registration, change tracking, conflict detection, tombstones, attachments, key distribution, revocation, and checkpoints.

Merge policy is domain-owned:

- Immutable receipts merge.
- Set membership merges only where safe.
- Conflicting user text is preserved.
- Commitment status follows explicit transitions.
- Deletion uses signed tombstones.
- Security policy never merges permissively.

Universal last-write-wins is prohibited. The cloud acts as encrypted relay, backup coordinator, provider gateway where needed, and optional heavy-compute host; it does not become undocumented authority over personal state.

## Configuration, settings, and flags

- **Configuration:** operator-owned technical deployment values.
- **Settings:** validated user-owned preferences with sync and provenance policy.
- **Feature flags:** temporary release, experiment, emergency, or compatibility controls.

Flags declare owner, purpose, default, scope, expiration, removal issue, and offline behavior. They cannot permanently replace configuration or silently grant permissions or collect new data.

## Diagnostics and telemetry

Logs are structured, redacted, and carry stable event identifiers, severity, and correlation IDs. Raw prompts, authentication tokens, full notification content, unredacted artifacts, encryption keys, and plugin secrets are never logged by default.

User-exported local diagnostic bundles may include module versions, health, operation timelines, sanitized errors, queue depth, migration status, plugin compatibility, and device capabilities.

Product analytics, reliability, security audit, and billing usage remain separate, schema-controlled, purpose-limited pipelines. Observability failure never blocks product function.

## Errors and degradation

Boundary errors use a stable envelope with code, category, retryability, required user action, correlation ID, safe message, and internal cause reference. Internal modules retain domain-specific errors.

Categories include validation, permission, policy denial, unavailable capability, provider, network, conflict, timeout, storage, plugin, compatibility, and invariant violation. Failure degrades narrowly: provider failure cannot disable Memory; index failure cannot corrupt Projects.

## Versioning and migration

Version application release, kernel, module, plugin, provider, events, storage, synchronization, SDK, and capability schemas independently. Support declared compatible ranges and explicitly reject incompatibility.

Production schema migrations are forward-only, preflighted, transactional where possible, checkpointed when large, backed up before destructive transitions, and followed by background backfill for derived data. Rollback relies on application compatibility, not assumed database reversal.

Event evolution is additive where possible. Consumers ignore unknown optional fields. Semantic breaking changes create a new event type or major version.

## Testing

- **Domain tests:** pure deterministic invariants and transitions.
- **Contract tests:** shared conformance for modules, providers, plugins, storage, and Android adapters.
- **Integration tests:** real persistence and event boundaries.
- **Process tests:** IPC failure, death, limits, mismatch, and cancellation.
- **Security tests:** denial, scope, redaction, isolation, rotation, and audit completeness.
- **Migration tests:** every supported production schema snapshot.
- **AI tests:** recorded normalized responses for CI; live evaluation separately.
- **Device matrix:** Android and One UI versions, pressure, revocation, background limits, battery policy, overlay conflict, accessibility coexistence, and offline behavior.
- **Architecture tests:** forbidden imports, cycles, and cross-module storage access.

## Build, packaging, and updates

Packages are separated into Domain Core, application capabilities, platform contracts, Android adapters, surfaces, first-party plugins, SDKs, and test kits. Builds are reproducible with locked dependencies, provenance, signed artifacts, software bills of materials, static analysis, and API/schema compatibility checks. Cycles are forbidden.

Trusted modules ship in the main application unless isolation requires a service process. Large optional features MAY use safe dynamic delivery. Security-critical executable code is not fetched opaquely. Plugins are separately signed and manifest-verified.

Core uses signed store updates with staged rollout. Plugin updates validate publisher identity, signature continuity, compatibility, permission changes, and migrations. New permissions require renewed approval.

Signed emergency compatibility policy MAY disable a broken provider, quarantine a plugin, disable a dangerous capability, or reduce rollout. It cannot grant permissions or activate data collection.

## AI-assisted development

Every independently developed module has a Module Contract Pack as specified in [03_ARCHITECTURE.md](03_ARCHITECTURE.md). AI assistants modify one bounded context at a time. Cross-module changes require contract proposal, compatibility analysis, consumer inventory, contract tests, and independently reviewable implementation changes. Generated code never infers foreign schemas or imports internal types. Mechanical enforcement is required.

## Rationale

This platform uses established Android scheduling, relational transactions, explicit dependency composition, bounded IPC, signed packages, versioned contracts, deterministic state machines, and transactional outboxes because these patterns remain understandable and operable over time. Complexity is admitted only where trust, isolation, durability, or distribution requires it.

## References

- [README.md](README.md)
- [00_VISION.md](00_VISION.md)
- [01_PRODUCT_STRATEGY.md](01_PRODUCT_STRATEGY.md)
- [02_PRINCIPLES.md](02_PRINCIPLES.md)
- [03_ARCHITECTURE.md](03_ARCHITECTURE.md)

## Future extension notes

Future desktop, web, watch, wearable, DeX, and AR renderers consume presentation-neutral models. Future cloud workers host compatible capabilities or engines without moving undocumented authority from the device. Public SDK and marketplace work begins only after internal contracts and security controls stabilize.

---

# Phase 2 Generation Checklist

Generate the following next; do not combine authoritative definitions that have different ownership:

- [x] `05_DOMAIN_MODEL.md` — canonical primitives, identities, value objects, aggregate boundaries, and invariants.
- [x] `06_CONTEXT_GRAPH.md` — node/edge descriptors, traversal budgets, temporal semantics, and graph contracts.
- [x] `07_MEMORY.md` — Memory records, contracts, verification, contradiction, retention, and deletion.
- [x] `08_PROJECTS.md` — Project lifecycle, membership, state materialization, decisions, and open questions.
- [x] `09_COMMITMENTS.md` — lifecycle, evidence, due conditions, conflicts, and follow-up policy.
- [x] `10_ATTENTION_INBOX.md` — candidate intake, ranking, suppression, explanation, and resolution.
- [x] `11_PROVENANCE_AND_RECEIPTS.md` — source identity, lineage, provider disclosure, Action and data-flow receipts.
- [x] `12_APPLICATION_CAPABILITIES.md` — use-case catalog, request/result conventions, authorization, transactions, and idempotency.
- [x] `13_CONTEXT_ENGINE.md` — source contracts, snapshot schema, freshness, budgets, sensitivity, and cancellation.
- [x] `14_REASONING_ENGINE.md` — task contracts, plans, validation, routing, tools, confidence, and fallback.
- [x] `15_ACTION_ENGINE.md` — Action plans, risk tiers, state machine, approval, execution, compensation, and receipts.
- [x] `16_AUTOMATION_ENGINE.md` — rule model, deterministic evaluation, simulation, scheduling, and run history.
- [x] `17_SURFACE_ENGINE.md` — surface sessions, presentation models, streaming, privacy, and continuity.
- [x] `18_SURFACES.md` — HUD, Samsung Mode, overlay, widgets, notifications, and voice renderer contracts.
- [x] `19_CAPABILITY_SYSTEM.md` — descriptors, registry, broker, tokens, executors, and conformance.
- [x] `20_MODULE_SYSTEM.md` — manifests, ownership, lifecycle, dependency rules, and Module Contract Pack template.
- [x] `21_PLUGIN_SYSTEM.md` — manifest, verification, IPC, permissions, lifecycle, health, quarantine, and updates.
- [x] `22_SDK_AND_DEVELOPER_API.md` — SDK partitions, stability, versioning, permissions, errors, and testing.
- [x] `23_PROVIDER_SYSTEM.md` — provider/model descriptors, capability matrix, routing, budget, privacy, health, and ledger.
- [x] `24_SECURITY_AND_PERMISSIONS.md` — principals, grants, policy decisions, tokens, exclusions, redaction, keys, and audit.
- [x] `25_STORAGE_AND_DATA_OWNERSHIP.md` — schemas by owner, relational authority, blobs, projections, indexes, cache, and outbox.
- [x] `26_EVENT_SYSTEM.md` — domain/integration events, registry, delivery, ordering, replay, dead letters, and evolution.
- [x] `27_SYNCHRONIZATION.md` — envelopes, device trust, checkpoints, tombstones, attachments, and domain merge policies.
- [x] `28_NETWORKING_AND_BACKGROUND_WORK.md` — clients, classification, retries, queues, jobs, constraints, and energy policy.
- [x] `29_CONFIGURATION_SETTINGS_FLAGS.md` — ownership, validation, sync, signed compatibility policy, and flag lifecycle.
- [x] `30_OBSERVABILITY_AND_DIAGNOSTICS.md` — logs, redaction, metrics, audit separation, diagnostics, and retention.
- [x] `31_ERROR_MODEL.md` — domain errors, boundary envelope, categories, retryability, correlation, and degradation.
- [x] `32_VERSIONING_MIGRATIONS_UPDATES.md` — compatibility ranges, schema/event evolution, rollout, rollback, and emergency controls.
- [x] `33_TESTING_STRATEGY.md` — test layers, conformance kits, provider fixtures, security, migrations, devices, and architecture tests.
- [x] `34_BUILD_PACKAGING_RELEASES.md` — package graph, reproducibility, signing, SBOM, dynamic delivery, and staged rollout.
- [x] `35_CLOUD_SERVICES.md` — sync relay, backup coordination, provider gateway, heavy compute, and authority boundaries.
- [x] `36_ANDROID_ADAPTERS.md` — permissions, lifecycle, schedulers, intents, overlays, notifications, process recovery, and device matrix.
- [x] `37_PRIVACY_PRODUCT_CONTROLS.md` — ledger, app exclusions, Memory controls, private sessions, export, and deletion UX contracts.
- [x] `38_AI_ASSISTED_DEVELOPMENT.md` — bounded change workflow, contract proposals, consumer inventory, and mechanical enforcement.
- [x] `39_ARCHITECTURE_DECISIONS.md` — decision-record index grounded only in approved architecture decisions.
- [x] `40_GLOSSARY.md` — canonical terms referencing, not duplicating, authoritative specifications.

# Phase 3 Implementation Contract Packs

The implementation bridge is complete in [41_IMPLEMENTATION_CONTRACT_PACKS.md](41_IMPLEMENTATION_CONTRACT_PACKS.md) and its 14 self-contained module packs under [`contracts/`](contracts/). Implementers MUST use those packs without treating them as permission to override this platform contract or the Architecture Blueprint.
