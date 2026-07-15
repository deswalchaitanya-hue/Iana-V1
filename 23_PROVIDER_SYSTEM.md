# 23 — Provider System

## Purpose
Route bounded reasoning tasks to replaceable local or remote AI providers.

## Descriptors
Provider descriptor: identity/version, adapter version, credential reference, supported regions/privacy modes, health contract, and compatibility. Model descriptor: model ID/version, capability matrix, context/output limits, modalities, structured-output/tool/stream support, cost schedule, latency class, data-handling claims, and extensions.

## Capability matrix
Base capabilities include text, structured output, vision, embeddings, speech, tool use, and streaming. Provider-specific extensions are declared and task-gated; unsupported extensions never silently degrade.

## Routing request
Contains task contract, required capabilities, sensitivity, on-device requirement, privacy/quality/cost mode, region, budgets, deadline, fallback permission, and health requirements. It contains no domain database handles or unrestricted user context.

## Routing
Router filters policy and compatibility, excludes unhealthy circuits, estimates budget, chooses deterministically from eligible routes under configured policy, and returns route plus rationale. Routing itself is stateless; health, usage, and budgets are durable.

## Privacy
Adapters receive bounded task envelopes after classification and redaction. Credentials remain in secret storage. Provider claims are recorded but do not replace policy. On-device-only content cannot reach remote adapters.

## Budget and ledger
Reservations prevent known budget overrun. Ledger records provider/model, task class, token/compute units, estimated/final cost, latency, outcome, and correlation without raw content. Reconciliation handles provider reporting lag.

## Health
Circuit breakers track bounded failure windows by provider/capability. States are healthy, degraded, open, half-open, and disabled. Failure degrades only affected routes.

## Adapter contract
Adapters enforce deadline/cancellation, normalize errors and usage, support streaming backpressure, and expose no provider SDK to the Reasoning Engine.

## Invariants
Providers cannot mutate domain state, execute Actions, retain unrestricted context, or become product authority. Fallback never weakens privacy.

## References
[14_REASONING_ENGINE.md](14_REASONING_ENGINE.md), [24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md), [30_OBSERVABILITY_AND_DIAGNOSTICS.md](30_OBSERVABILITY_AND_DIAGNOSTICS.md)

## Future extension
Advanced user controls may expose privacy, quality, and cost modes—not infrastructure administration by default.
