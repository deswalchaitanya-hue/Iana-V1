# 27 — Synchronization

## Purpose
Define local-first replication without replacing device authority or domain merge rules.

## Device trust
A registered device has device ID, user binding, public keys, trust status, capabilities, registration/last-seen times, and revocation epoch. New devices require authenticated approval and key distribution.

## Operation envelope
Encrypted/signed envelope contains operation ID, domain/aggregate identity, source device, sequence/checkpoint, base version/vector where required, operation type/version, encrypted payload reference, tombstone state, created time, and integrity metadata. It never contains a database file.

## Flow
Local validated changes enter an owned change log. Sync packages bounded envelopes and attachments, uploads to relay, downloads unseen envelopes, verifies device/signature/schema, decrypts, invokes the owning domain merge interface, records result/conflict, and advances checkpoints only after durable application.

## Checkpoints
Checkpoints are per user/device/domain stream; global ordering is prohibited. Gaps pause affected partitions and trigger bounded recovery.

## Domain merge policy
- Immutable receipts union by identity.
- Safe set membership uses owner-defined add/remove semantics.
- Conflicting user text preserves both variants.
- Commitment status uses explicit transition and evidence rules.
- Project aggregates preserve conflicting decisions/questions for resolution.
- Memory contradictions form conflict sets.
- Deletion uses signed tombstones.
- Security policy never merges permissively.

## Attachments
Blobs synchronize separately using encrypted chunking, integrity hashes, resumable transfer, quotas, and retention. Records may arrive before blobs and expose unavailable state, never corrupt content.

## Revocation and deletion
Revoked devices cannot upload or decrypt new material. Key rotation and signed tombstones propagate. Relay retention and backups honor deletion policy.

## Invariants
Sync applies domain operations through public interfaces, not table replacement. Unknown/incompatible operations quarantine. Last-write-wins is not universal.

## References
[24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md), [25_STORAGE_AND_DATA_OWNERSHIP.md](25_STORAGE_AND_DATA_OWNERSHIP.md), [35_CLOUD_SERVICES.md](35_CLOUD_SERVICES.md)

## Future extension
Cross-device web access implements a registered-device or separately approved client trust profile.
