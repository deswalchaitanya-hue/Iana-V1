# Reasoning Engine Contract Pack

**Module:** `iana.engine.reasoning` · **Contract:** `1.0` · **Authority:** [14](../14_REASONING_ENGINE.md), [13](../13_CONTEXT_ENGINE.md), [23](../23_PROVIDER_SYSTEM.md)

## Purpose and responsibilities
Transform an authorized Context Assembly and explicit objective into bounded analyses, alternatives, recommendations, uncertainty, and explanations. It owns reasoning runs, proposal artifacts, evaluation metadata, and policy—not domain truth or execution. Outputs are proposals until accepted through application/action capabilities.

## Public APIs, inputs, outputs, and schemas
`reason(ReasoningRequest)->ReasoningResult`; `compare(AlternativesRequest)->Comparison`; `explain(RunId)->ReasoningExplanation`; `cancel(RunId)->Outcome`; `evaluate(EvaluationRequest)->EvaluationResult`.
Request includes actor/grants, purpose/objective, assembly ref/version, policy/provider constraints, output schema, cost/token/time/tool-step budgets, correlation, deadline. Result includes run ID/version, structured conclusions/options, cited source refs, assumptions, uncertainty/confidence, safety flags, omissions, provider/model receipt refs, and status. Idempotency applies to externally retried runs; streaming chunks are provisional until final receipt.

## Owned data and invariants
Owns run metadata, policy/evaluation versions, structured proposal artifacts, citations, bounded provider receipts, and outbox. Context remains external. Every material claim maps to source or is labeled inference; uncertainty is explicit; user correction is not overridden; provider output is untrusted until schema/policy validation. Retention is purpose/classification bounded.

## Events
Publishes `engine.reasoning.run-started|completed|failed|cancelled.v1`, `engine.reasoning.proposal-created.v1`; minimized metadata only. Consumes no raw domain events; callers provide assembly refs, while policy/config changes may invalidate caches. At least once, run-version ordering, event-ID dedupe.

## Dependencies, capabilities, and prohibitions
Requires Domain Core; Kernel config/events/telemetry/clock; Context Engine; permission/Provenance; provider gateway ports and output validator. Exposes `engine.reasoning.reason.v1`, `.compare.v1`, `.explain.v1`, `.evaluate.v1`. Forbidden: domain repositories/writes, Action execution, hidden capabilities/tools, direct UI, provider authority, uncited factual promotion, unbounded loops/tokens/cost.

## Security and configuration
Deny by default by purpose/data/provider/tool scope. Minimize provider input, apply residency/privacy policy, never send secrets, and treat prompt/context as sensitive. Model/provider cannot grant permission. Config: allowed providers/models by policy, token/cost/tool-step/retry/time/concurrency bounds, output schemas, safety/evaluation policy versions, retention; finite and validated; credentials only via secret ports.

## Failures, observability, and performance
Errors: `INVALID_OBJECTIVE`, `PERMISSION_DENIED`, `CONTEXT_STALE`, `BUDGET_EXCEEDED`, `PROVIDER_UNAVAILABLE`, `OUTPUT_INVALID`, `SAFETY_REJECTED`, `DEADLINE_EXCEEDED`. Retry transient idempotent provider calls only within budget; invalid output may get bounded repair; fallback provider only if policy permits and is disclosed. Log run/provider policy IDs, counts, budgets, duration/status—not prompts/context/output/secrets. Metrics: latency, tokens/cost, invalid/repair/fallback/safety rates, citation coverage, cancellation.

## Acceptance criteria and tests
Prove structured schema validation, citation/source labeling, uncertainty, no mutations, permission/provider data minimization, bounded budgets/retries, cancellation, fallback disclosure, provider-output distrust, reproducible policy metadata, and redacted telemetry. Test policy/ranking logic; provider contracts; Context integration; prompt-injection/tool denial; malformed output; timeout/fallback; replay; performance/cost limits.

## Examples and extension points
Success: produce three project options citing assembly fragments and assumptions. Failure: malformed provider result fails validation after bounded repair. Denial: requested provider lacks data-residency approval. New provider adapters, reasoners, evaluators, and schemas MAY be added behind ports; autonomous execution, hidden context acquisition, or provider-specific domain semantics is forbidden.
