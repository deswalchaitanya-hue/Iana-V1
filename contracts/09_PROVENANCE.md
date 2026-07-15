# Provenance Contract Pack

**Module:** `iana.provenance` · **Contract:** `1.0` · **Authority:** [11](../11_PROVENANCE_AND_RECEIPTS.md), [24](../24_SECURITY_AND_PERMISSIONS.md), [30](../30_OBSERVABILITY_AND_DIAGNOSTICS.md)

## Purpose and responsibilities
Own immutable evidence references, lineage, operation receipts, consent/confirmation receipts, correction/revocation links, and auditable explanations of who/what/when/why. It is not general logging, raw payload replication, authorization by itself, or a license to retain sensitive content indefinitely.

## Public APIs and schemas
`recordEvidence(EvidenceCommand)->EvidenceRef`; `recordReceipt(ReceiptCommand)->ReceiptRef`; `link(LineageCommand)->LineageReceipt`; `correct(TargetRef, CorrectionCommand)->ReceiptRef`; `revoke(TargetRef, RevocationCommand)->ReceiptRef`; `get(Ref, Projection)->ProvenanceView`; `trace(LineageQuery)->LineagePage`; `verify(Ref)->VerificationResult`.
Inputs include actor/system identity, source type/reference, event/action/capability IDs, timestamps, schema version, cryptographic digest where applicable, classification, retention, correlation/causation, and minimized metadata. Outputs are immutable refs, verification state, authorized lineage, redactions, cursor. Recording is idempotent; immutable entries are corrected/revoked by append-only links.

## Owned data and invariants
Owns evidence metadata, receipts, lineage edges, confirmation/consent records, corrections/revocations, verification metadata, indexes, outbox. No mutation or deletion except privacy-governed cryptographic erasure/tombstone while preserving lawful structural audit. Every receipt identifies issuer, subject/action, time, scope, version, and outcome. Raw source content is external unless explicitly required/minimized. Partition and retention follow subject/tenant/legal policy; atomic record+outbox; migrations append-compatible.

## Events
Publishes `provenance.evidence-recorded.v1`, `provenance.receipt-recorded.v1`, `provenance.reference-corrected|revoked.v1`, `provenance.verification-failed.v1`. Payloads contain refs/status/digests/classification, not source content. Consumes completed operation/security/privacy events only when registered to create receipts. At least once; append idempotency and event-ID dedupe.

## Dependencies, capabilities, and prohibitions
Requires Domain Core; Kernel append store/outbox/events/config/telemetry/clock/crypto; identity and permission ports. Exposes `provenance.evidence.record.v1`, `provenance.receipt.record.v1`, `provenance.read.v1`, `provenance.verify.v1`. Forbidden: peer repositories, authorization overrides, mutable history, direct provider/UI/model access, secrets/raw prompt/content capture by default, unbounded lineage traversal.

## Security and configuration
Deny by default with subject, purpose, receipt class, and field scopes. Writers are authenticated capability owners; issuer identity cannot be caller-forged. Confirmation receipts bind actor, exact scope, expiry, and nonce. Encrypt/sign or hash according to policy; redaction must preserve verifiability where possible. Config: lineage depth/node/page bounds, digest/signature algorithms and rotation policy, retention classes, metadata bytes, verification deadlines; validated, versioned, no key material outside secret ports.

## Failures, observability, and performance
Errors: `INVALID_EVIDENCE`, `ISSUER_DENIED`, `SCOPE_MISMATCH`, `NOT_FOUND`, `PERMISSION_DENIED`, `VERIFICATION_FAILED`, `RETENTION_CONFLICT`, `DEPENDENCY_UNAVAILABLE`. Never rewrite on failure; retry transient idempotent append/sign operations; report uncertain verification explicitly. Log refs/algorithms/status/counts/duration, never evidence content or keys. Metrics: append/verify latency, failures, lineage truncation, correction/revocation, outbox lag. Traversals and crypto work are bounded/cancellable.

## Acceptance criteria and tests
Prove append-only behavior, issuer authenticity, scoped confirmation anti-replay, digest verification, correction/revocation lineage, minimized storage/output, authorization/redaction, atomic outbox, duplicate append safety, algorithm rotation/migration, privacy erasure policy, and bounded trace. Test schemas/events, tampering, replay, key outage/rotation, migration, permissions/existence leaks, crash recovery, and performance limits.

## Examples and extension points
Success: record an action receipt linked to user confirmation and provider result digest. Denial: a plugin cannot mint a user confirmation receipt. Tampering makes `verify` return `VERIFICATION_FAILED` without exposing content. New evidence/receipt classes, digest/signature adapters, and export projections MAY be versioned; mutable audit, bearer confirmation, or raw-content hoarding is forbidden.
