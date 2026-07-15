# Action Engine Contract Pack

**Module:** `iana.engine.action` · **Contract:** `1.0` · **Authority:** [15](../15_ACTION_ENGINE.md), [12](../12_APPLICATION_CAPABILITIES.md), [24](../24_SECURITY_AND_PERMISSIONS.md)

## Purpose and responsibilities
Convert an accepted proposal or explicit request into a validated action plan, classify risk, obtain scoped confirmation, execute authorized application/provider capabilities, compensate where possible, and produce receipts. It owns plans, steps, execution state, confirmations references, and compensation records—not domain aggregates or provider credentials.

## Public APIs, inputs, outputs, and schemas
`plan(ActionRequest)->ActionPlan`; `confirm(PlanId, PlanVersion, Confirmation)->ConfirmationReceipt`; `execute(PlanId, expectedVersion)->ExecutionReceipt`; `cancel(ExecutionId)->Outcome`; `status(ExecutionId)->ExecutionView`; `compensate(ExecutionId, reason)->CompensationReceipt`.
Inputs identify actor/grants, intent/proposal and provenance, ordered typed steps/capabilities, preconditions, consequences, idempotency, budgets, correlation, deadline. Plans show exact effects, data recipients, reversibility, required grants/confirmation, cost, compensation, expiry. Execution returns per-step status and evidence. Confirmation binds actor, immutable plan hash/version, scope, expiry, nonce; plan changes invalidate it.

## Owned data and invariants
Owns plans, plan versions/hashes, confirmation refs, execution/step state, idempotency, compensation records, outbox. Domain mutations occur only through Application capabilities. State: `draft→validated→awaiting-confirmation|ready→running→succeeded|partially-succeeded|failed|cancelled→compensating→compensated|compensation-failed`. Executed immutable steps are never silently rerun. Receipts persist before reporting success.

## Events
Publishes `engine.action.plan-created|confirmation-required|execution-started|step-completed|execution-completed|failed|compensated.v1`. Consumes accepted reasoning proposal, attention triage confirmation, capability result, and privacy/revocation events where policy allows. Payloads use IDs/status/capability/receipt refs, not secrets or action payloads. At least once, step idempotency, event dedupe.

## Dependencies, capabilities, and prohibitions
Requires Domain Core; Kernel state/outbox/events/config/telemetry/clock; Application capabilities; permission/risk/confirmation policy; Provenance; optional provider ports only through registered capabilities. Exposes `engine.action.plan.v1`, `.confirm.v1`, `.execute.v1`, `.status.v1`, `.compensate.v1`. Forbidden: context repositories, direct database writes, UI-trusted authorization, model-granted permission, hidden steps, confirmation reuse, unbounded plans/retries.

## Security and configuration
Deny by default and reauthorize each step at execution. External, destructive, irreversible, financial, privacy-sensitive, or materially consequential steps require explicit confirmation. Use least-privilege scoped credentials from secret ports; prevent confused deputy and SSRF via allowlists/policy. Config: step/parallelism/payload/cost/time/retry bounds, plan/confirmation expiry, risk policy versions, compensation attempts; finite, validated, no embedded secrets.

## Failures, observability, and performance
Errors: `INVALID_PLAN`, `PERMISSION_DENIED`, `CONFIRMATION_REQUIRED|EXPIRED|MISMATCH`, `PRECONDITION_FAILED`, `STEP_FAILED`, `PARTIAL_FAILURE`, `COMPENSATION_FAILED`, `DEADLINE_EXCEEDED`. Retry only transient idempotent steps; otherwise pause and request attention; compensate reverse order when declared. Log plan/execution/step IDs, capability, risk, status, duration—not arguments, credentials, or private results. Metrics: planning/execution latency, confirmation, retries, partial/compensation rates, external failures.

## Acceptance criteria and tests
Prove plan transparency, exact confirmation binding, execution-time authorization, immutable successful step replay, idempotency, preconditions, receipt durability, partial failure visibility, bounded compensation, cancellation, secret redaction, SSRF/confused-deputy defense, and no domain direct writes. Test state machine, API/events, capability/provider integration, stale/tampered confirmation, crash between effect/receipt, replay, outage, compensation, migration, bounds.

## Examples and extension points
Success: confirmed two-step project update executes through capabilities and returns linked receipts. Denial: altered recipient changes plan hash and invalidates confirmation. Replay skips receipted step. New action types, risk policies, and compensators MAY register behind versioned capabilities; invisible steps, blanket consent, or mutable execution history is forbidden.
