# 03 — Architecture

## Purpose

Define IANA’s authoritative system structure, dependency law, domain ownership, engines, communication model, and architectural invariants.

## Scope

This document specifies the stable logical architecture. Android implementation, runtime operations, plugins, providers, storage mechanisms, networking, and delivery are detailed in [04_PLATFORM.md](04_PLATFORM.md).

## Assumptions

- IANA is local-first, privacy-sensitive, modular, and intended to evolve for at least a decade.
- Android processes and surfaces are disposable; authoritative operations may be durable.
- Modules are developed independently by human teams and AI assistants.
- Isolation, durability, and distribution justify complexity; novelty does not.

## Architectural style

IANA uses a **modular monolith on-device**, surrounded by isolated processes, sandboxed plugins, and a small number of independently deployable cloud services.

It combines:

- Hexagonal architecture at system boundaries.
- Domain-driven modularity.
- Event-driven coordination where asynchronous reaction is required.
- Capability-based security.
- Local-first storage.
- Contract-first module development.
- Unidirectional dependencies.
- Process isolation for untrusted or failure-prone workloads.

The default communication mechanism is a typed in-process API. Events, IPC, and networking are introduced only when isolation, durability, or distribution requires them.

## Highest-level hierarchy

```text
Product Surfaces
        ↓
Experience Controllers / Surface Engine
        ↓
Application Capabilities
        ↓
Domain Engines
        ↓
Domain Core and Bounded Contexts
        ↓
Platform Service Contracts
        ↓
Android Platform Adapters
        ↓
Android / One UI / Hardware
```

Controlled side systems connect only through declared boundaries:

```text
Plugins    ↔ Capability Broker ↔ Application Capabilities
Providers  ↔ Provider Router   ↔ Reasoning Engine
Cloud Sync ↔ Sync Gateway      ↔ Domain Change Interfaces
Telemetry  ↔ Redacted Observability Ports
```

## Dependency law

Every compile-time dependency points inward:

- Surfaces → Application Capabilities → Engines → Domain Core.
- Platform Adapters → Platform Service Contracts.
- Plugins → Public SDK Contracts.
- Providers → Provider SDK Contracts.

Runtime control MAY flow outward through injected ports; source dependencies do not. The Domain Core depends only on standard language facilities, immutable primitives, clock and identifier interfaces, and explicit policy interfaces. It MUST NOT depend on Android, databases, networking, AI providers, UI frameworks, plugins, telemetry vendors, or serialization frameworks.

No bounded context imports another context’s implementation or reads another context’s storage. Required immediate behavior uses public synchronous interfaces. Completed facts and asynchronous projections use events.

## Architectural vocabulary

- **Domain:** business concepts and invariants.
- **Engine:** substantial domain coordination.
- **Capability:** narrowly scoped callable operation.
- **Module:** trusted ownership and build boundary.
- **Service:** reusable technical function.
- **Adapter:** external-system implementation.
- **Plugin:** separately versioned, restricted extension.
- **Provider:** interchangeable AI implementation.

No new category may be introduced without a distinct ownership or security requirement.

## Platform Kernel

One kernel exists per process and establishes the trusted runtime. It owns process initialization, module registration, dependency composition, runtime mode selection, security principal creation, migration coordination, critical health checks, graceful degradation, application lifecycle, process identity, and kernel compatibility version.

It depends only on platform contracts, module manifests, security bootstrap, and configuration bootstrap. It cannot access product UI, Memory or Project internals, provider APIs, or plugin implementation details. It starts before all hosted modules and terminates after them. Restricted processes host kernels with reduced module sets.

## Domain Core

Domain Core defines canonical language, identifiers, immutable value objects, shared invariants, domain error taxonomy, state-transition primitives, event primitives, provenance requirements, and ownership boundaries. It owns semantics, never persistence representations.

Core MUST remain small. Personal Context Graph, Memory, Projects, Commitments, Attention Inbox, Provenance, and Actions remain separately owned bounded contexts or engines; they are not fields on a universal Core object.

## Application Capabilities

Capabilities expose stable user-meaningful use cases such as capturing context, asking about a Project, confirming a Commitment, preparing an Action, and resolving an Attention item.

Each request-scoped, stateless coordinator owns authorization, input validation, transaction boundaries, engine orchestration, idempotency, audit initiation, and result construction. It may depend on domain engines, policy services, repositories through ports, and event transaction interfaces. It MUST NOT access views, Android components, provider SDKs, raw database clients, or plugin internals.

Long-running work returns a durable operation handle rather than blocking indefinitely. New surfaces reuse capabilities instead of reimplementing workflows.

## Bounded contexts

### Personal Context Graph

**Purpose:** Represent typed relationships among people, Projects, artifacts, decisions, Commitments, Actions, and sources.

**Owns:** Node and edge identities, relationship semantics, temporal metadata, confidence, source attribution, provenance references, traversal policies, and duplicate candidates.

**Rules:** It uses canonical entity references without importing aggregate implementations. Initial storage is adjacency-oriented and user-partitioned. Every traversal is typed, depth-bounded, and budgeted. The graph does not own Memory retention.

### Memory

**Purpose:** Retain user-owned knowledge under explicit scope, provenance, and retention.

**Owns:** Memory records, contracts, verification, confidence, sensitivity, source links, expiration, supersession, correction, redaction, deletion, and embedding references.

**Rules:** Canonical records are separate from rebuildable indexes. Memory depends on Context Graph contracts, provenance, encryption, repositories, and retrieval ports. It cannot call providers or execute Actions.

### Projects

**Purpose:** Provide durable contexts for ongoing user objectives.

**Owns:** Project identity, objective, status, explicit membership, artifact association, decisions, open questions, state snapshots, and Project policy.

**Rules:** Projects query public Memory and Commitment interfaces and abstract reasoning ports. Project state is incrementally materialized from authoritative events. Shared Projects require a future collaboration context rather than embedded personal-access complexity.

### Commitments

**Purpose:** Model obligations, expectations, deadlines, and follow-ups.

**Owns:** Owner, counterparty, evidence, due conditions, confidence, conflict, follow-up policy, status, and transitions.

**Lifecycle:** Proposed → Confirmed → Active → Completed, Dismissed, Expired, or Superseded.

**Rules:** Commitment records are authoritative. Deadline indexes and scheduled evaluations are projections. The context cannot notify or send messages directly.

### Attention Inbox

**Purpose:** Present durable matters requiring user judgment or action.

**Owns:** Attention items, deduplication, ranking metadata, suppression, explanation, delivery eligibility, resolution, invalidation, and expiration.

**Rules:** It consumes public events from Projects, Commitments, Actions, Memory, and Context; it does not query producer repositories. Ranking operates over bounded user-partitioned candidates. Delivery adapters remain outside the context.

### Provenance

**Purpose:** Explain source and transformation lineage.

**Owns:** Immutable source identities, evidence references, transformation chains, provider disclosures, data-flow receipts, and audit correlation.

**Rules:** Every ingestion, inference, Memory mutation, and Action creates provenance. Records are append-only, user/time partitioned, and may archive payloads while retaining hashes and essential metadata.

## Intelligence engines

### Context Engine

Builds an immutable, policy-compliant, budgeted snapshot from authorized sources, Memory, and current Project state. Snapshots are ephemeral and never authoritative. Collection is request-scoped or explicitly scheduled; uncontrolled continuous sensing is prohibited. Source collection is parallel, cancellable, deadline-bound, and sensitivity-filtered.

### Reasoning Engine

Executes provider-independent reasoning workflows: task classification, context assembly, routing request, structured validation, tool-loop policy, confidence, and fallback. It owns plans and normalized inference results but cannot mutate domain state, execute Actions, or call provider SDKs directly. Long operations persist operation state and enforce cost, token, latency, and tool limits.

### Action Engine

Plans and executes authorized Actions through capabilities. It owns plans, risk classification, preview, confirmation, capability resolution, execution, retry, compensation, receipts, and durable state machines.

```text
Drafted → Validated → Awaiting Approval → Authorized
        → Executing → Succeeded | Failed | Partially Completed | Compensated
```

Every execution has an idempotency key. Integration-specific executors are isolated according to risk. Multi-step Actions use explicit plans and compensation records, not distributed transactions.

### Automation Engine

Evaluates deterministic triggers and conditions, simulates runs, schedules work, enforces rate limits, records history, and submits approved Action plans. It cannot execute integrations or mutate domain entities. Automation never bypasses Action risk, authorization, or confirmation policy.

### Surface Engine

Converts application state into renderer-neutral presentation models and manages independent surface sessions. It owns density and privacy adaptation, available Actions, streaming coordination, and surface continuity. It cannot access concrete views or repositories.

Intents flow inward; immutable view-state streams flow outward. Sessions are bound to a surface invocation, not an Activity. HUD, Samsung Mode, widgets, notifications, voice, and future renderers share semantics without mutable state.

## State model

- **Domain state:** persistent, authoritative, modified only through capabilities.
- **Projection state:** derived, query-optimized, rebuildable.
- **Surface state:** ephemeral, immutable, scoped to one surface session.
- **Operation state:** durable for long-running reasoning, Actions, and synchronization.
- **Device state:** observed through adapters and never authoritative product state.

```text
User Intent → Surface Controller → Capability Command → Domain Transaction
→ Domain Event → Projection Update → Surface State → Render
```

## Event model

Domain events are trusted facts inside Core. Integration events are versioned, sanitized contracts safe across process, plugin, or network boundaries. They are never interchangeable.

Delivery is at least once with idempotent consumers, per-aggregate ordering where required, no global ordering, dead-letter handling, and replay only where justified. State mutation and outbox publication commit atomically.

Events MUST NOT perform immediate validation, authorization, request/response queries, hide direct dependencies, or carry large binary payloads. An event-consumer registry and contract tests make asynchronous coupling explicit.

## Module contract

Every module declares public contracts, implementation boundary, owned data, consumed contracts, published events, required capabilities, lifecycle hooks, compatibility range, test contract, security classification, and forbidden dependencies.

A Module Contract Pack additionally records purpose, concepts, invariants, errors, examples, compatibility, conformance tests, and architecture decision references. Modules build independently against contracts and own their storage namespace and migration sequence.

## Architectural risk controls

- Cycles: shared identifiers, public reads, events for reactions, and no cross-context mutation.
- Hidden event coupling: registry, versioning, contract tests, and synchronous calls for immediate requirements.
- Excess abstraction: fixed vocabulary and interfaces only at justified boundaries.
- God Core: separate bounded contexts and narrow shared primitives.
- Event bottleneck: partitioned streams and no global ordering.
- Graph bottleneck: bounded traversal, typed edges, user partitioning, and query indexes.
- Android lifecycle: durable operation state and disposable surfaces.
- AI nondeterminism: proposals plus deterministic validation.
- Provider flattening: base contracts with declared capability-specific extensions.
- Sync corruption: domain-owned merge policies.
- Surface divergence: common capabilities and presentation semantics.

## Rationale

This architecture maximizes maintainability through explicit contracts, relational authority, deterministic state machines, constructor injection, transactional outboxes, local-first ownership, and narrow process boundaries. It deliberately avoids speculative microservices, universal data models, unrestricted event buses, shared mutable state, reflection-heavy frameworks, and cross-module database access.

## References

- [00_VISION.md](00_VISION.md)
- [01_PRODUCT_STRATEGY.md](01_PRODUCT_STRATEGY.md)
- [02_PRINCIPLES.md](02_PRINCIPLES.md)
- [04_PLATFORM.md](04_PLATFORM.md)

## Future extension notes

Desktop and server runtimes may implement compatible kernels, adapters, and renderers. New bounded contexts require distinct ownership and invariants. New engines require substantial coordination responsibility. Neither extension may weaken the dependency law or local authority.
