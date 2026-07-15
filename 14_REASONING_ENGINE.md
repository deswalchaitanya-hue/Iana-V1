# 14 — Reasoning Engine

## Purpose
Coordinate provider-independent reasoning while keeping AI non-authoritative.

## Task contract
A task declares task ID/version, purpose, input schema, snapshot requirements, output schema, allowed provider capabilities, tool policy, sensitivity, quality mode, token/cost/latency/tool limits, confidence policy, and fallback policy.

## Plan
A reasoning plan contains normalized steps, provider requests, allowed tools, budgets, checkpoints, validation stages, and termination conditions. Long plans persist operation state; short plans remain request-scoped.

## Execution
The engine assembles authorized context through the Context Engine, requests routing from the Provider System, submits bounded envelopes, validates structured output, applies policy and confidence thresholds, and returns normalized candidates with provenance.

## Tools
Tools are capabilities explicitly allowed by task policy. Read tools receive constrained tokens. Mutating and Action tools are prohibited inside reasoning. Tool loops have bounded count, payload, deadline, and repeated-call detection.

## Validation
Outputs undergo parse/schema validation, content and policy checks, reference verification, confidence evaluation, and safe normalization. Invalid output may retry only within declared budget. It never mutates domain state.

## Confidence
Confidence includes provider-reported signals only as evidence. Task-specific calibration and deterministic checks produce accept, request-confirmation, abstain, or fallback outcomes.

## Fallback
Fallback order is task-defined: alternate model/provider, reduced context, deterministic implementation, user clarification, or safe failure. Privacy constraints never relax during fallback.

## Cancellation and recovery
Cancellation propagates to context, provider, and tool calls. Persisted operations resume from safe checkpoints and never duplicate external mutations.

## Invariants
The engine does not call provider SDKs directly, execute Actions, or write Memory. Every provider disclosure and transformation receives provenance.

## References
[13_CONTEXT_ENGINE.md](13_CONTEXT_ENGINE.md), [23_PROVIDER_SYSTEM.md](23_PROVIDER_SYSTEM.md), [11_PROVENANCE_AND_RECEIPTS.md](11_PROVENANCE_AND_RECEIPTS.md)

## Future extension
Capability-specific task extensions are versioned without flattening providers to a lowest common denominator.
