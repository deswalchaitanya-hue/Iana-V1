# 37 — Privacy Product Controls

## Purpose
Define user-visible contracts for transparency, exclusions, Memory control, private sessions, export, and deletion.

## Privacy Ledger
Shows time, source, purpose, data classes, provider/plugin/destination, redaction, authorization basis, outcome, and receipt link. It supports filtering by app, Project, provider, capability, and time. Safe summaries never conceal material disclosure.

## App exclusions
Users can prohibit capture, notification ingestion, accessibility observation, provider disclosure, Memory, or all processing by app/source. Effective state and limitations are shown immediately. Revocation cancels eligible queued work.

## Memory controls
Each Memory displays assertion, source, confidence, verification, scope, sensitivity, retention, consumers, expiration, and correction/deletion Actions. Users choose session-only, Project-scoped, until-date, permanent, or on-device-only contracts.

## Private session
A private session disables retention and ordinary analytics, restricts provider use according to selected mode, expires automatically, and shows persistent semantic indication across surfaces. It does not falsely claim device/OS invisibility. Required security receipts remain minimized.

## Provider controls
Before material disclosure, surfaces identify destination class, purpose, data classes, and privacy mode. Users can select never-cloud-screen-content and other approved policies. Fallback cannot weaken choices.

## Export
Export is user-authenticated, scoped, previewable, cancellable, encrypted, and receipted. It includes canonical records, relationships, provenance, settings, and artifacts as selected, with machine-readable schemas and human index.

## Deletion
Users may delete item, Project association, Memory, source artifact, provider history where controllable, or account data. UI explains independently owned references, propagation, backup/tombstone windows, and irreversible effects. Progress and completion receipt are visible.

## UX invariants
Controls use plain language, no dark patterns, equal prominence for denial, and no permission bundling without purpose. “Why am I seeing this?” is available on Memory, Attention, Project State, and Actions.

## References
[07_MEMORY.md](07_MEMORY.md), [11_PROVENANCE_AND_RECEIPTS.md](11_PROVENANCE_AND_RECEIPTS.md), [24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md)

## Future extension
Cross-device privacy views must preserve these contracts.
