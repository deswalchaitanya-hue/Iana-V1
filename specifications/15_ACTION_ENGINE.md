# 15 — Action Engine

## Purpose
Plan and execute supported operations under deterministic risk, authorization, and receipt policy.

## Action plan
Contains Action ID, user, purpose, ordered steps, capability/version per step, typed inputs, dependencies, expected outputs, risk tier, required grants, preview, confirmation policy, idempotency keys, timeouts, retry policy, compensation steps, provenance, and version.

## Risk tiers
A: automatic and reversible. B: one-tap approval. C: explicit review. D: prohibited initially. Effective risk is the maximum of declared capability, data disclosure, executor, and plan-composition risk; it cannot be reduced by AI or plugins.

## State machine
`Drafted → Validated → Awaiting Approval → Authorized → Executing → Succeeded | Failed | Partially Completed | Compensated`.
Cancellation is allowed before irreversible execution and otherwise becomes a stop/compensate request. Every transition records actor, time, reason, and version.

## Preview and approval
Preview is renderer-neutral and includes effects, recipients/resources, disclosed data classes, irreversible steps, estimated cost, and undo availability. Approval binds user, plan version/hash, scope, risk, and expiration. Any material plan change invalidates approval.

## Execution
The registry resolves an authorized executor. Each step obtains a short-lived capability token, enforces timeout and idempotency, records result, and emits a receipt. Retries apply only to safe/idempotent steps.

## Compensation
Compensation is explicit best-effort business behavior, executed in reverse dependency order where valid. Failure produces partial-completion detail; it never claims atomic rollback.

## Receipts
Every attempt creates an immutable receipt per [11_PROVENANCE_AND_RECEIPTS.md](11_PROVENANCE_AND_RECEIPTS.md). Undo is itself an Action with policy and receipt.

## Invariants
No executor bypasses Core authorization. Multi-step plans do not use distributed transactions. Tier D cannot execute. Provider inference cannot approve or authorize.

## References
[19_CAPABILITY_SYSTEM.md](19_CAPABILITY_SYSTEM.md), [24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md), [31_ERROR_MODEL.md](31_ERROR_MODEL.md)

## Future extension
New risk policies require explicit product and security approval; plugins cannot define weaker tiers.
