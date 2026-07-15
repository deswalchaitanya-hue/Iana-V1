# 12 — Application Capabilities

## Purpose
Define stable user-meaningful use cases and coordinator conventions.

## Scope
Requests, results, authorization, transaction ownership, operations, and initial catalog.

## Contract convention
Every capability declares stable ID/version, request/result schemas, caller principal, purpose, authorization scope, sensitivity, risk, idempotency, timeout, consistency, emitted events, and errors. Requests contain correlation, idempotency key for mutations, locale/timezone where relevant, and cancellation. Results are completed values or durable `OperationHandle`s.

## Coordinator rules
Coordinators are stateless and request-scoped. They authorize, validate, establish transaction boundaries, call public engine/context ports, initiate audit, and construct results. They do not access UI, Android components, raw databases, provider SDKs, or plugin internals.

## Initial catalog
- `capture.submit`, `capture.status`, `capture.cancel`
- `memory.propose`, `memory.verify`, `memory.correct`, `memory.delete`, `memory.query`
- `project.create`, `project.assign`, `project.recordDecision`, `project.resolveQuestion`, `project.getState`
- `commitment.propose`, `commitment.confirm`, `commitment.changeDue`, `commitment.complete`, `commitment.dismiss`
- `attention.list`, `attention.resolve`, `attention.dismiss`, `attention.snooze`
- `context.ask`, `context.explain`
- `action.prepare`, `action.approve`, `action.execute`, `action.cancel`, `action.undo`, `action.status`
- `privacy.explain`, `privacy.export`, `privacy.delete`, `privacy.privateSession`

## Transactions and orchestration
One command transaction mutates one owning context and its outbox. Multi-context use cases use ordered calls and durable operation state; no distributed transaction is introduced. Read capabilities state freshness and consistency.

## Authorization
Authorization precedes sensitive retrieval and repeats before external execution. Grants are purpose-bound and resource-scoped. Confirmation evidence is passed by reference and independently verified.

## Idempotency
Mutating requests require keys. The same principal, capability, key, and equivalent input return the prior result; differing input is a conflict. Records expire only after external retry windows and operation retention permit.

## Errors
Public results use [31_ERROR_MODEL.md](31_ERROR_MODEL.md). Validation returns field-safe details; policy denial does not expose hidden data.

## Invariants
Surfaces and plugins use these capabilities rather than domain repositories. Long-running work never holds a UI lifecycle or database transaction open.

## References
[19_CAPABILITY_SYSTEM.md](19_CAPABILITY_SYSTEM.md), [24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md)

## Future extension
Catalog additions require ownership, contract tests, security review, and compatibility declaration.
