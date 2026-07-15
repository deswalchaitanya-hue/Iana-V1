# 24 — Security and Permissions

## Purpose
Define deny-by-default principals, grants, policy, keys, exclusions, redaction, and audit.

## Principals
User, device, trusted module, Android adapter, plugin, provider adapter, cloud service, and operator are distinct principals. Identity is authenticated at each process/network boundary; identity alone grants no authority.

## Authorization layers
OS permission, IANA feature permission, plugin capability grant, data-scope grant, Action authorization, and provider disclosure consent remain separate. Effective authority is their intersection.

## Grants
A grant binds subject, capability, resource selector, purpose, sensitivity ceiling, constraints, issuer, issued/expiry time, revocation state, and evidence. Grants are explicit, narrow, expiring, non-transitive, and revocable.

## Policy decision
Inputs include principal, capability, resource, purpose, context, data classification, risk, grants, exclusions, confirmation, device trust, and time. Output is permit, deny, or require-action with narrowed scope, obligations, reason code, and policy version. Enforcement occurs at capability, data, provider, and executor boundaries.

## Tokens
Tokens follow [19_CAPABILITY_SYSTEM.md](19_CAPABILITY_SYSTEM.md), are audience-bound, short-lived, replay-resistant, and never logged. Revocation terminates future use; long operations reauthorize at irreversible boundaries.

## App exclusions
Exclusion may deny capture, notification ingestion, accessibility observation, provider disclosure, Memory, or all processing for a package/source. The strictest matching rule wins. Banking and other sensitive apps may be pre-suggested but user policy is explicit.

## Classification and redaction
Classification occurs before persistence and transmission. Redaction is schema-aware and records what classes were removed. Unknown sensitive payloads default to restricted handling.

## Keys and secrets
Use hardware-backed Android Keystore where available. Device identity, database, blob, sync, backup, and plugin-channel keys are separate and rotatable. Provider credentials are references to secret storage, never domain records. Recovery and rotation produce audit events.

## Audit
Security decisions, grant changes, exclusions, key operations, disclosures, denials, and high-risk attempts enter append-only audit storage with redacted metadata.

## Invariants
Permission cannot be inferred from successful prior use. Plugins cannot delegate. Policy failure denies. Tier D automation is prohibited.

## References
[11_PROVENANCE_AND_RECEIPTS.md](11_PROVENANCE_AND_RECEIPTS.md), [37_PRIVACY_PRODUCT_CONTROLS.md](37_PRIVACY_PRODUCT_CONTROLS.md)

## Future extension
Collaboration requires separate principals and grants; it must not broaden personal-device authority implicitly.
