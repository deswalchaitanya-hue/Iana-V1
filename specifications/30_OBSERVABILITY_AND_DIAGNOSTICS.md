# 30 — Observability and Diagnostics

## Purpose
Provide operability without creating a shadow personal-data store.

## Pipelines
Product analytics, reliability telemetry, security audit, provider/billing usage, and user diagnostics are separate schemas, stores, purposes, access policies, and retention schedules.

## Logs
Structured records contain stable event ID, time, severity, module/process, operation/correlation, safe dimensions, outcome, and redacted error reference. Raw prompts, tokens, notification bodies, artifacts, keys, credentials, plugin secrets, and user-authored text are excluded by default.

## Redaction
Schema fields carry logging classification. Unknown objects are not serialized. Redaction occurs before transport and records redaction version. Hashing personal values is not automatically anonymous and requires policy.

## Metrics
Use bounded-cardinality dimensions. Reliability measures latency, errors, queues, provider health, job completion, migration state, and resource pressure. Product measures follow approved Strategy metrics and never optimize engagement.

## Tracing
Correlation spans cross declared boundaries using opaque IDs. Trace baggage contains no personal data or authority token. Sampling is sensitivity-aware.

## Audit separation
Security/provenance audit follows [11_PROVENANCE_AND_RECEIPTS.md](11_PROVENANCE_AND_RECEIPTS.md) and cannot be disabled by ordinary telemetry settings where required for safety. Access is stricter and purpose logged.

## Diagnostic bundles
User-initiated export may include versions, compatibility, device capabilities, module/plugin/provider health, sanitized errors, operation timelines, queue depth, and migration status. Preview lists included classes and supports cancellation.

## Retention and failure
Each schema has minimum necessary retention and deletion policy. Observability outage never blocks product function; buffers are bounded and discard safely.

## References
[11_PROVENANCE_AND_RECEIPTS.md](11_PROVENANCE_AND_RECEIPTS.md), [37_PRIVACY_PRODUCT_CONTROLS.md](37_PRIVACY_PRODUCT_CONTROLS.md)

## Future extension
Remote support access requires explicit time-bound user authorization and audit.
