# 38 — AI-Assisted Development

## Purpose
Constrain AI-generated changes to explicit contracts and mechanically enforced boundaries.

## Bounded change workflow
1. Identify one owning bounded context/module.
2. Load its Module Contract Pack and referenced authoritative specifications.
3. State intended behavior, invariants, and files.
4. Modify implementation behind existing contracts.
5. Add/update deterministic tests and fixtures.
6. Run architecture, contract, security, and migration checks as applicable.
7. Produce a change summary, assumptions, and unresolved decisions.

## Cross-module proposal
Before changing a contract, provide purpose, owner, current/new schema, compatibility classification, security/data impact, migration/evolution plan, rollback, consumer inventory, and conformance-test updates. Each consumer implementation remains independently reviewable.

## Consumer inventory
Generated from manifests, event registry, package dependencies, capability registry, SDK usage, and schema references. Memory or text search alone is insufficient.

## Prohibitions
AI tools must not infer foreign database shape, import internal types, create shared business utility packages, add architectural categories, bypass permission/confirmation, fabricate source decisions, log sensitive content, or modify multiple contexts opportunistically.

## Mechanical enforcement
Build rules enforce dependency direction, implementation-package visibility, schema ownership, event registration, migration ownership, API compatibility, SDK boundaries, and required contract tests. Generated code receives no exemption.

## Review evidence
Changes include tests run, generated artifacts, compatibility report, security/data classification, migration impact, and architecture decision references. Nondeterministic model output is not proof.

## References
[20_MODULE_SYSTEM.md](20_MODULE_SYSTEM.md), [33_TESTING_STRATEGY.md](33_TESTING_STRATEGY.md), [39_ARCHITECTURE_DECISIONS.md](39_ARCHITECTURE_DECISIONS.md)

## Future extension
Phase 3 may define repository automation and templates for this workflow.
