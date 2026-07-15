# Implementation Contract Packs

## Status and authority
This document indexes the authoritative implementation bridge for the architecture in [03_ARCHITECTURE.md](03_ARCHITECTURE.md). The packs refine, but never override, documents `00`–`40`. On conflict, the earlier governing specification wins; correction requires an ADR, never an implementation-local exception.

Normative terms **MUST**, **MUST NOT**, **SHOULD**, and **MAY** are binding in descending order. Examples are illustrative. Every boundary uses canonical IDs, versioned envelopes, UTC timestamps, correlation/causation IDs, actor identity, provenance, and typed results. Commands are idempotent where retries are possible; completed facts use a transactional outbox and at-least-once delivery. Consumers deduplicate by event ID.

## Manifest
| ID | Module | Pack | Owns |
|---|---|---|---|
| `iana.kernel` | Platform Kernel | [01](contracts/01_PLATFORM_KERNEL.md) | lifecycle, capability registry, event transport contracts |
| `iana.domain` | Domain Core | [02](contracts/02_DOMAIN_CORE.md) | shared value objects and invariant policies |
| `iana.application` | Application Capabilities | [03](contracts/03_APPLICATION_CAPABILITIES.md) | use-case orchestration and boundary envelopes |
| `iana.context` | Personal Context Graph | [04](contracts/04_PERSONAL_CONTEXT_GRAPH.md) | entities, relations, context snapshots |
| `iana.memory` | Memory | [05](contracts/05_MEMORY.md) | memory records and retrieval indexes |
| `iana.projects` | Projects | [06](contracts/06_PROJECTS.md) | projects, outcomes, project state |
| `iana.commitments` | Commitments | [07](contracts/07_COMMITMENTS.md) | commitments and lifecycle |
| `iana.attention` | Attention Inbox | [08](contracts/08_ATTENTION_INBOX.md) | attention items and triage state |
| `iana.provenance` | Provenance | [09](contracts/09_PROVENANCE.md) | receipts, evidence links, audit records |
| `iana.engine.context` | Context Engine | [10](contracts/10_CONTEXT_ENGINE.md) | bounded context assemblies |
| `iana.engine.reasoning` | Reasoning Engine | [11](contracts/11_REASONING_ENGINE.md) | proposals and explanations |
| `iana.engine.action` | Action Engine | [12](contracts/12_ACTION_ENGINE.md) | action plans, confirmations, execution receipts |
| `iana.engine.automation` | Automation Engine | [13](contracts/13_AUTOMATION_ENGINE.md) | automation definitions and runs |
| `iana.engine.surface` | Surface Engine | [14](contracts/14_SURFACE_ENGINE.md) | surface models and presentation policies |

## Dependency law
Dependencies flow `renderer/adapter → application → domain`; infrastructure implements inward-owned ports. Bounded contexts communicate through application capabilities, canonical references, and events, never foreign-table access. Engines may query capabilities and publish proposals; they MUST NOT mutate bounded-context storage directly. The kernel coordinates lifecycle but contains no product policy.

Allowed module edges:
- Kernel → Domain contracts only for shared envelopes; Kernel → adapter ports.
- Application → Domain and bounded-context ports.
- Each bounded context → Domain, Kernel ports, Provenance capability; no peer storage dependency.
- Context Engine → Context Graph, Memory, Projects, Commitments, Attention query capabilities.
- Reasoning Engine → Context Engine and query capabilities.
- Action Engine → Application command capabilities and Provenance.
- Automation Engine → Action Engine, Application, and event subscriptions.
- Surface Engine → Application query/command capabilities; renderers depend on Surface Engine.

This graph MUST remain acyclic. Constructor injection or equivalent explicit composition is required.

## Capability and event registry
Capability names are globally unique and versioned: `kernel.*`, `domain.*`, `application.*`, `context.*`, `memory.*`, `projects.*`, `commitments.*`, `attention.*`, `provenance.*`, and `engine.<name>.*`. Owners alone define semantics; delegates require explicit grants.

Events follow `<module>.<aggregate>.<past-tense-fact>.v1`. Minimum envelope: `eventId`, `eventType`, `schemaVersion`, `occurredAt`, `producer`, `aggregateId`, `aggregateVersion`, `actor`, `correlationId`, `causationId`, `provenanceRef`, and minimized `payload`. Event payloads MUST NOT contain secrets or unnecessary private content. Pack-specific tables register producers and consumers.

## Shared operational rules
- **Security:** deny by default; least privilege; explicit confirmation for destructive, external, irreversible, or materially consequential actions; secrets remain in approved secret stores.
- **Storage:** local-first authority unless a governing spec says otherwise; module-owned schema; encryption in transit and at rest; transactional data-plus-outbox writes.
- **Failures:** typed, stable error codes; bounded retries with jitter only for transient idempotent work; no retry for denial or validation; compensation where atomicity ends.
- **Observability:** structured logs and traces include operation, module, outcome, duration, correlation, and safe IDs; never secrets, raw sensitive content, credentials, or full prompts.
- **Performance:** every collection, traversal, batch, retry, and wait is bounded and configurable; cancellation and deadlines propagate; no pack invents universal latency numbers.
- **Evolution:** additive compatible changes within a major version; breaking changes require a new major contract, migration, deprecation window, and ADR.

## Completion gate
A module is implementation-ready only when its pack passes API/schema validation, ownership and dependency checks, authorization tests, event replay/deduplication tests, migration tests, failure/degradation tests, observability redaction tests, and configured performance-bound tests. No incomplete placeholder, implicit permission, foreign write, unbounded operation, or undocumented public behavior is permitted.
