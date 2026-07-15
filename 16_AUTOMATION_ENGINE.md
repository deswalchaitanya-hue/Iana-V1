# 16 — Automation Engine

## Purpose
Evaluate explicit deterministic rules and submit approved Action plans without bypassing policy.

## Scope
Delayed capability; contracts exist now to preserve architecture.

## Rule model
A rule contains identity, owner, enabled state, version, typed trigger, deterministic conditions, proposed Action template, schedule policy, rate limits, confirmation policy reference, validity window, provenance, and last simulation result.

Triggers are registered facts or schedules. Conditions operate only on declared typed inputs; arbitrary code and provider inference are not conditions. Natural-language authoring may propose a rule but deterministic compilation and user review are required.

## Evaluation
On a trigger, the engine loads the exact rule version, validates freshness and grants, evaluates conditions deterministically, checks rate and duplicate windows, materializes a plan proposal, and submits it to the Action Engine.

## Simulation
Simulation uses recorded or user-supplied inputs and returns matched conditions, unavailable data, resulting plan preview, required approvals, and no side effects. It is mandatory before activation or material edits.

## Scheduling
Schedules use Android-supported durable work. Missed windows follow explicit skip, run-late, or ask policy. Timezone and daylight-saving behavior are explicit.

## Run history
Records rule version, trigger reference, evaluation trace, decision, plan/Action reference, timing, errors, and correlation. Sensitive values are redacted.

## Invariants
Automation cannot execute integrations, mutate domain aggregates, weaken risk, inherit transitive permission, or silently broaden scope. Disabled or expired rules do nothing.

## References
[15_ACTION_ENGINE.md](15_ACTION_ENGINE.md), [28_NETWORKING_AND_BACKGROUND_WORK.md](28_NETWORKING_AND_BACKGROUND_WORK.md), [29_CONFIGURATION_SETTINGS_FLAGS.md](29_CONFIGURATION_SETTINGS_FLAGS.md)

## Future extension
Implementation and user-facing routines remain Version 2/3 work under Product Strategy sequencing.
