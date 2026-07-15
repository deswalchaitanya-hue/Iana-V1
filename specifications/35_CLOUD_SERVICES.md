# 35 — Cloud Services

## Purpose
Define the small independently deployable cloud boundary without moving undocumented authority off device.

## Services
- Sync relay: stores/transfers encrypted operation envelopes and attachments.
- Backup coordinator: manages encrypted backup sets, retention, restore manifests, and deletion.
- Provider gateway: optional credential isolation, policy enforcement, usage accounting, and provider transport.
- Heavy-compute host: optional compatible task execution for approved workloads.

## Authority boundary
The device remains authoritative for personal domain state by default. Cloud services do not interpret or mutate Projects, Memory, Commitments, Attention, or Actions except through explicit compatible capability/domain contracts. Relay storage is not a queryable context database.

## Trust and identity
Every request authenticates user/device/service, authorizes purpose/scope, enforces tenant partition, and records safe audit metadata. Service identities and keys are separate. Payload encryption is end-to-end where service function permits.

## Data handling
Services declare data classes, region, encryption, retention, backups, deletion, subprocessors, and observability. On-device-only data is rejected. Queues and retries cannot outlive source retention.

## Reliability
Operations are idempotent, deadline-bound, bounded in payload, and resilient to duplicate delivery. Regional/service failure degrades sync or remote compute only; local capture and domain state remain available.

## Heavy compute
Receives a bounded task envelope and returns normalized candidates plus provenance. It cannot execute Actions or write domain storage.

## Operations
Each service has SLOs, capacity limits, migration ownership, disaster recovery, key rotation, signed deployment, SBOM, audit, and incident containment.

## References
[27_SYNCHRONIZATION.md](27_SYNCHRONIZATION.md), [23_PROVIDER_SYSTEM.md](23_PROVIDER_SYSTEM.md), [24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md)

## Future extension
Concrete APIs and deployment topology belong to Phase 3 contract packs.
