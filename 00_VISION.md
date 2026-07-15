# 00 — Vision

## Purpose

Define the enduring product identity and boundaries that all IANA features, interfaces, and technical decisions must preserve.

## Scope

This document states what IANA is, whom it serves, the user promise, the core product loop, and what the product will not become. Product sequencing and commercial strategy are defined in [01_PRODUCT_STRATEGY.md](01_PRODUCT_STRATEGY.md); technical realization is defined in [03_ARCHITECTURE.md](03_ARCHITECTURE.md).

## Assumptions

- Users encounter important context across fragmented applications and routinely lose its meaning, relationships, or required follow-up.
- Platform assistants and model providers can supply strong inference but do not provide a neutral, user-owned continuity layer.
- IANA can create durable value without replacing Android, One UI, or existing applications.
- Trust, provenance, and user control are prerequisites for useful memory and action.

## Product category

IANA is a **personal context and action system presented as an AI Operating Layer**. It overlays rather than replaces Android and One UI. It is not an operating system, launcher, model company, universal app controller, or generic chatbot.

## Product promise

> IANA remembers what matters across your apps and turns it into the next action at the right moment, under your control.

The emotional promise is: **the user does not have to keep everything in their head.**

The functional promise is: **anything important can be captured once and reliably found, understood, connected, and acted on later.**

## Core loop

All identity-defining work strengthens one loop:

**Capture → Understand → Remember → Resurface → Act**

1. **Capture:** Accept an explicitly supplied screenshot, link, selection, file, image, notification, note, or voice input through a supported surface.
2. **Understand:** Normalize the source, establish provenance, identify relationships, and produce structured candidates.
3. **Remember:** Retain verified knowledge under an explicit scope and retention contract.
4. **Resurface:** Present relevant Project state, Commitment, or Attention item when it becomes actionable.
5. **Act:** Prepare or execute a supported action under deterministic policy, confirmation, and receipt rules.

No AI inference is authoritative by itself. Deterministic application code validates every state mutation and execution plan.

## Identity-defining concepts

### Personal Context Graph

Represents typed, temporal, provenance-backed relationships among people, Projects, artifacts, decisions, Commitments, Actions, and sources. It is the relational structure of context, not a substitute for bounded domain ownership.

### Memory

Stores user-owned retained knowledge with source, confidence, verification, sensitivity, scope, expiration, correction, and deletion semantics. Memory is inspectable and portable across providers.

### Projects

Provide durable contexts for ongoing objectives. A Project connects artifacts, people, decisions, open questions, Commitments, and current state. It answers, “Where does this stand?” rather than preserving only a chat transcript.

### Commitments

Represent obligations, expectations, deadlines, and follow-ups with an owner, counterparty, evidence, due condition, confidence, status, and next action.

### Attention Inbox

Contains only matters requiring judgment or action: expected replies, approaching deadlines, at-risk Commitments, decisions, relevant changes, failed Actions, and ambiguities requiring confirmation. It is not another notification stream.

### Trusted Actions

IANA proposes, prepares, or executes supported operations with explicit capability disclosure, risk classification, preview, authorization, status, failure explanation, receipt, and undo or compensation where possible.

### HUD and Samsung Mode

The functional, futuristic HUD remains a first-class presentation layer. Samsung Mode provides conventional One UI-aligned presentation. They switch without changing intelligence, state, policy, or capability behavior. Visual effects communicate semantic state, hierarchy, privacy, or execution; they never exist as empty spectacle.

## Product character

IANA is calm, precise, discreet, accountable, capable, and non-theatrical. It behaves like a trusted chief of staff: quiet until useful, honest about limitations, and explicit about actions.

## Non-goals

IANA MUST NOT become:

- A replacement operating system or launcher.
- A universal controller that claims arbitrary reliable access to every app.
- A model provider or provider-administration product.
- A surveillance layer using hidden continuous screen or microphone capture.
- A notification replacement or engagement feed.
- A cinematic interface whose effects are detached from function.
- An autonomous authority for payments, legal acceptance, medical decisions, security settings, or destructive operations.
- A collection of commodity AI features without connection to the core loop.

Emotion inference, wellness inference, productivity streaks, engagement gamification, silent message sending, and arbitrary autonomous Accessibility control are excluded.

## Long-term advantage

The durable asset is a user-corrected, provider-independent Personal Context Graph containing relationships among people, Projects, artifacts, decisions, Commitments, preferences, Actions, and outcomes. Its defensibility comes from accumulated context, corrections, reliable action history, transparent provenance, and user ownership—not from any individual model or commodity feature.

## Rationale

Platform vendors will retain advantages in hardware, invocation, system APIs, distribution, and first-party integrations. Model companies will retain advantages in general intelligence. IANA therefore competes on continuity: preserving meaning across app boundaries and ensuring important context becomes coherent Project state and completed Commitments.

## References

- [01_PRODUCT_STRATEGY.md](01_PRODUCT_STRATEGY.md) — target users, scope, sequencing, and metrics.
- [02_PRINCIPLES.md](02_PRINCIPLES.md) — mandatory behavioral and engineering rules.
- [03_ARCHITECTURE.md](03_ARCHITECTURE.md) — domain and system structure.
- [04_PLATFORM.md](04_PLATFORM.md) — Android, surfaces, capabilities, plugins, providers, and operations.

## Future extension notes

Future surfaces, devices, and plugins may express this vision only through existing application capabilities and presentation-neutral semantics. They may extend reach; they may not redefine product state or bypass the core loop, provenance, permission, or action policies.
