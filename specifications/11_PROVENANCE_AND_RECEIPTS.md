# 11 — Provenance and Receipts

## Purpose
Make source, transformation, disclosure, mutation, and Action outcomes inspectable.

## Scope
Immutable source identity, lineage, provider disclosure, data-flow receipts, and Action receipts.

## Source identity
A source record contains `SourceId`, user, source kind, originating app/service/device, acquisition method, observed time, content hash, optional encrypted artifact reference, sensitivity, retention reference, and trust classification. Source identity survives payload archival.

## Lineage
A transformation record contains input provenance references, operation type and version, actor principal, provider/model disclosure where applicable, policy decision, normalized output hash, time, correlation, and confidence. Chains are directed and append-only. Corrections append; they do not rewrite history.

## Provider disclosure
Records provider, model or capability identifier, purpose, data classes disclosed, redactions, region where known, retention mode, consent or policy basis, request time, outcome, cost class, and correlation. Secrets and raw prompts are excluded.

## Data-flow receipt
Records movement across trust boundaries: source, destination class, purpose, fields/data classes, authorization, encryption state, start/result times, and failure. It supports the Privacy Ledger.

## Action receipt
Contains Action and plan identifiers, capability/version, executor principal, approved preview hash, confirmation evidence, idempotency key hash, started/completed times, outcome, safe result summary, external reference, compensation/undo status, and provenance. Receipts are immutable even when an Action is compensated.

## Integrity and retention
Records are append-only, user/time partitioned, hash-linked within practical partitions, encrypted, and covered by key rotation. Payloads may be archived or deleted under policy while essential hashes and metadata remain. Audit integrity does not justify retaining personal bodies indefinitely.

## Query contract
Queries require user, purpose, subject/correlation scope, time range, sensitivity ceiling, and result budget. Results distinguish unavailable payload, redacted payload, and verified absence.

## Invariants
- Every ingestion, inference, Memory mutation, and Action has provenance.
- A receipt describes what occurred; it does not establish domain truth by itself.
- Provider and plugin claims are labeled by trust source.
- Correlation identifiers contain no personal data.
- Receipt deletion and export follow explicit privacy policy.

## Assumptions
Audit storage has stricter access and retention than product analytics.

## Rationale
Receipts turn trust into verifiable product behavior while avoiding a shadow datastore.

## References
[24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md), [30_OBSERVABILITY_AND_DIAGNOSTICS.md](30_OBSERVABILITY_AND_DIAGNOSTICS.md), [37_PRIVACY_PRODUCT_CONTROLS.md](37_PRIVACY_PRODUCT_CONTROLS.md)

## Future extension
Cryptographic export proofs may be added without changing domain ownership.
