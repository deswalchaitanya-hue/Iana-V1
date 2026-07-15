# 20 — Module System

## Purpose
Define trusted ownership/build boundaries and independently testable contracts.

## Manifest
A module declares ID/version, purpose, public contract packages, implementation packages, owned data namespace and migration line, consumed contracts, published/consumed events, exposed/required capabilities, lifecycle hooks, compatibility range, security classification, process eligibility, and forbidden dependencies.

## Lifecycle
`Discovered → Validated → Migrated → Initialized → Started → Healthy | Degraded → Stopped`. Startup follows a deterministic dependency DAG. Failure degrades dependents explicitly; cycles fail build or startup.

## Dependency rules
Modules import public contracts only. They cannot read foreign storage, import foreign implementation, use shared business-behavior packages, or locate dependencies dynamically. Constructor injection and explicit kernel composition are mandatory.

## Data ownership
One module owns each schema namespace, repository, migration sequence, and outbox. Cross-context reporting uses APIs or projections. Migration coordination orders owners but does not merge migration histories.

## Module Contract Pack template
1. Purpose and ownership
2. Public concepts and terminology
3. Commands, queries, and schemas
4. Aggregate invariants and transitions
5. Published and consumed events
6. Capability descriptors
7. Owned data and migration policy
8. Permissions and security classification
9. Errors and degradation
10. Lifecycle and health
11. Compatibility/deprecation
12. Examples and fixtures
13. Conformance tests
14. Forbidden dependencies
15. Architecture decision references

## Health
Health reports initialized, dependency availability, migration state, queue/outbox condition, and safe diagnostic codes without personal data.

## Invariants
Module internals never become SDK APIs by accident. Module boundaries are mechanically enforced. Common utility code contains only stable technical primitives.

## References
[03_ARCHITECTURE.md](03_ARCHITECTURE.md), [25_STORAGE_AND_DATA_OWNERSHIP.md](25_STORAGE_AND_DATA_OWNERSHIP.md), [38_AI_ASSISTED_DEVELOPMENT.md](38_AI_ASSISTED_DEVELOPMENT.md)

## Future extension
Process extraction retains the same contract pack and ownership; it is not a redesign opportunity.
