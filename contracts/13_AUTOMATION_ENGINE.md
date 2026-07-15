# Automation Engine Contract Pack

**Module:** `iana.engine.automation` · **Contract:** `1.0` · **Authority:** [16](../16_AUTOMATION_ENGINE.md), [26](../26_EVENT_SYSTEM.md), [28](../28_NETWORKING_AND_BACKGROUND_WORK.md)

## Purpose and responsibilities
Own user-visible automation definitions, triggers, conditions, grants, limits, run state, pause/disable controls, and run receipts. React to time/events by requesting authorized actions; never acquire broader authority than the automation grant. It does not mutate domain storage, hide recurring behavior, or convert proposals into commitments.

## Public APIs, inputs, outputs, and schemas
`create(AutomationCommand)->AutomationReceipt`; `revise(AutomationId, Patch, expectedVersion)->Receipt`; `enable|pause|disable(AutomationId, expectedVersion)->Receipt`; `trigger(TriggerEnvelope)->RunReceipt`; `get(AutomationId)->AutomationView`; `listRuns(RunQuery)->RunPage`; `dryRun(AutomationDraft)->SimulationResult`.
Definitions include owner, purpose, versioned trigger/filter/condition, action-plan template or capability request, scoped grant, confirmation policy, rate/cost/time bounds, expiry, provenance. Trigger has event/schedule ID, occurrence time, dedup key, correlation. Results expose decisions, skipped reasons, planned/executed action refs, receipts. Triggering is idempotent.

## Owned data and invariants
Owns definitions/versions, schedules/subscriptions refs, scoped grants, run/dedup state, pause/error state, budgets, outbox. Every enabled automation is inspectable, revocable, bounded, attributed, and has finite authority. Definition changes create a new version; runs bind version and trigger. No execution without current grant and policy; repeated failures pause per policy.

## Events
Publishes `engine.automation.created|enabled|paused|disabled.v1`, `engine.automation.run-started|skipped|completed|failed.v1`. Consumes only declared versioned events and Kernel schedules; schema mismatch pauses safely. Payloads minimized to IDs/version/status/reason/receipt refs. At least once; trigger/event dedupe; ordering per automation where required.

## Dependencies, capabilities, and prohibitions
Requires Domain Core; Kernel scheduler/events/store/outbox/config/telemetry/clock; Application queries; Action Engine; permission/confirmation and Provenance; Attention submit for pauses/decisions. Exposes `engine.automation.manage.v1`, `.trigger.v1`, `.runs.read.v1`, `.dry-run.v1`. Forbidden: peer repositories, direct provider/domain writes, hidden background behavior, self-expanding grants, event wildcard by default, unbounded recursion/fan-out/retries.

## Security and configuration
Deny by default. Owner explicitly grants exact trigger data, action capabilities, targets, duration, and budget; reauthorize each run/step. Consequential actions retain Action confirmation unless an explicit policy permits narrowly scoped prior consent. Config: definition/run/queue/concurrency/rate/cost/time/retry/fan-out/recursion bounds, pause thresholds, retention; finite, validated, no secrets in definitions.

## Failures, observability, and performance
Errors: `INVALID_DEFINITION`, `GRANT_REQUIRED|EXPIRED`, `TRIGGER_UNSUPPORTED`, `DUPLICATE_TRIGGER`, `RATE_LIMITED`, `PAUSED`, `ACTION_FAILED`, `DEPENDENCY_UNAVAILABLE`. Skip/deny has explicit receipt; retry transient idempotent work with jitter; dead-letter and pause poison/repeated failure; no infinite catch-up. Log safe automation/run/trigger/action IDs, version, decision, duration—not payloads/secrets. Metrics: queue lag, run/skip/failure/pause, duplicate/rate limit, cost, action latency.

## Acceptance criteria and tests
Prove inspectability/revocation, exact grants, definition-version binding, event/schema validation, dedupe, no authority growth, rate/budget/recursion limits, pause/dead-letter, Action confirmation, crash replay, catch-up bounds, telemetry redaction, and no direct writes. Test trigger/condition/state unit rules; API/events; scheduler/action integration; grant expiry; event storm/poison; replay; migration; outage; concurrency/rate performance.

## Examples and extension points
Success: a daily trigger assembles a bounded due list and requests an approved Action plan. Skip: event fails declared filter and records reason. Expired grant pauses the run and submits attention. New trigger/condition adapters and policy-bound executors MAY be versioned; hidden automations, wildcard authority, or direct mutation is forbidden.
