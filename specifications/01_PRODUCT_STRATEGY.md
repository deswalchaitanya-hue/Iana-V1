# 01 — Product Strategy

## Purpose

Define how IANA establishes a defensible product around user-owned context, coherent Projects, Commitments, and trusted Actions without competing directly with platform owners or model providers.

## Scope

This document governs target users, strategic differentiation, feature scope, sequencing, business model, and success measures. Product identity is defined in [00_VISION.md](00_VISION.md). Architecture and implementation boundaries are defined in [03_ARCHITECTURE.md](03_ARCHITECTURE.md) and take authority for technical decisions.

## Assumptions

- Android and Samsung One UI are the initial market and runtime.
- Google and Samsung retain privileged system access that IANA cannot and should not imitate through fragile workarounds.
- Leading AI models are replaceable suppliers, not the product moat.
- Immediate utility is required before long-term Memory compounds.
- Users will grant sensitive permissions only after receiving clear value and control.

## Strategic position

IANA serves people whose responsibilities are fragmented across messaging, browsing, documents, calendars, notifications, and applications. It preserves meaning across those boundaries and turns context into coherent Project state and accountable next Actions.

IANA does not compete to be a better Gemini, Galaxy AI, ChatGPT, Claude, Perplexity, Copilot, or operating system. It may use compatible providers and integrations while retaining user continuity independently of them.

## Initial target users

Launch with users who manage many parallel Commitments:

- Founders, freelancers, consultants, recruiters, and project leads.
- Graduate students and active job seekers.
- Caregivers and people coordinating complex moves, travel, purchases, or personal administration.

The initial wedge is:

> Share anything important to IANA. It remembers where it belongs, what it means, and what needs to happen next.

## Differentiation and moat

### Durable differentiators

1. **User-owned Personal Context Graph:** structured, provider-independent relationships rather than isolated chat history.
2. **Living Projects:** current state assembled from artifacts, decisions, people, open questions, and Commitments.
3. **Commitment tracking:** obligations and expectations retain evidence, ownership, due conditions, and resolution.
4. **Attention Inbox:** bounded matters requiring judgment instead of another feed.
5. **Correction loop:** user corrections improve classification, relevance, tone, timing, and confirmation behavior.
6. **Context and Action receipts:** sources, transformations, providers, disclosures, execution, and outcomes remain inspectable.
7. **Portable trust:** users can inspect, correct, export, expire, and delete retained context.
8. **Action reliability history:** supported capabilities improve from observed outcomes without making AI authoritative.

### Commodity capabilities

General chat, web search, summarization, translation, rewriting, image identification, voice conversation, document Q&A, event extraction, notification summaries, model selection, and basic reminders are commodities. They MAY exist only when they advance the core loop defined in [00_VISION.md](00_VISION.md).

Example: summarizing a document is commodity. Connecting it to a Project, comparing it with prior evidence, recording a decision, extracting a Commitment, and resurfacing its deadline is identity-defining.

## Core product scope

The product MUST provide:

- Explicit capture of supported source types.
- Structured Memory with provenance and retention contracts.
- Personal Context Graph relationships.
- Projects and incrementally materialized Project state.
- Commitment proposal, confirmation, lifecycle, and resolution.
- Attention Inbox intake, ranking, explanation, and resolution.
- Contextual questions against bounded, authorized context.
- Safe preparation or execution of supported Actions.
- Capability disclosure, risk classification, confirmation, and receipts.
- Privacy ledger, app exclusions, provider disclosure, export, and deletion.
- HUD and Samsung Mode as independent surfaces over common semantics.
- Lightweight Android invocation through supported mechanisms.

## Action policy

Supported execution follows this order:

1. Official APIs and intents.
2. Android App Actions and shortcuts.
3. Deep links.
4. User-approved integrations.
5. Share targets.
6. Explicitly installed, permissioned Accessibility action plugins where policy permits.

Risk tiers are:

- **A — automatic and reversible:** organize capture, update derived Project state, suggest follow-up.
- **B — one-tap approval:** create reminder, prepare or create calendar event, save a file, generate reply.
- **C — explicit review:** send a message, modify an appointment, or disclose personal information.
- **D — prohibited from automation initially:** payments, account changes, legal acceptance, medical decisions, security settings, and destructive operations.

The Action Engine lifecycle and capability security are authoritative in [03_ARCHITECTURE.md](03_ARCHITECTURE.md).

## Feature disposition

### Core

Capture, Context Graph, Memory, provenance, Projects, Project state, Commitments, Attention Inbox, contextual understanding, contextual Ask, safe Action preparation, Action receipts, permission controls, provider abstraction, privacy ledger, export/deletion, progressive onboarding, app exclusions, widgets, lightweight invocation, HUD, Samsung Mode, and capability disclosure.

### Plugins or specialized packages

Galaxy-specific surfaces, Edge Panel, keyboard, call intelligence, camera lens, gallery indexing, browser capture, advanced scheduling, travel, SmartThings, Home Assistant, Tasker, routines, accessibility modules, local model runtimes, specialized voice/TTS, external storage, messaging integrations, desktop capture, wearables, and DeX.

Plugins remain constrained by [04_PLATFORM.md](04_PLATFORM.md) and do not imply near-term marketplace availability.

### Delayed

Full-duplex voice, broad ambient context, advanced automation, native desktop applications, shared Projects, public SDK, marketplace, call screening, broad gallery intelligence, and continuous proactive sensing.

### Excluded

Emotion inference, wellness inference, fake holographic effects, decorative spectacle, AI wallpaper features, launcher replacement, arbitrary autonomous Accessibility control, autonomous high-risk Actions, silent sending, hidden continuous capture, multi-user voice recognition, premature AR product work, engagement gamification, and personality marketplaces.

## Release sequencing

### Version 1 — Prove the loop

- Screenshot, share, selected-content, link, image, note, and document capture.
- Structured Memory and provenance.
- Projects and Project state.
- Commitments and Attention Inbox.
- Contextual Ask.
- Reply drafting, reminder creation, and calendar-event preparation.
- Privacy ledger, export, deletion, app exclusions, and provider disclosure.
- HUD, Samsung Mode, Quick Settings tile, share target, and focused widgets.

Success means users capture information and later return because IANA correctly connected, resurfaced, or advanced it.

### Version 2 — Improve action and reach

- Supported first-party integrations.
- Notification catch-up and selected-text actions.
- Browser capture and secure web access.
- Push-to-talk voice.
- Expanded deterministic Action execution.
- Explicit calendar- or location-based resurfacing.
- Advanced provider controls and private partner SDK.
- Device-specific plugins.

Success means IANA reliably progresses meaningful work rather than merely storing it.

### Version 3 — Build the ecosystem selectively

- Public SDK after contracts stabilize.
- Automation plugins, desktop applications, and shared Projects.
- Team or household permissions as a separate collaboration context.
- Specialized accessibility, scheduling, smart-home, and wearable integrations.
- Marketplace only after developer demand and a valuable installed base exist.

Success means external developers create safe value against stable contracts.

## Business model

### Free

Limited captures and processing, basic Projects and Actions, one default provider path, and complete baseline privacy controls.

### Pro

Higher processing limits, advanced providers, expanded Projects and Commitment workflows, cross-device access, advanced Actions, encrypted backup, and local-model options where supported.

### Future

Paid specialist plugins, family or team plans, and enterprise connectors only after consumer product-market fit. Advertising, sale of user data, and engagement optimization are prohibited business models.

## Metrics

### North-star metric

**Weekly resolved Commitments that originated from context captured through IANA.**

### Supporting measures

- Captures connected to an existing Project.
- User-confirmed Commitment candidates.
- Attention items resolved.
- Suggestions accepted and dismissed.
- Incorrect Memories corrected.
- Actions completed successfully.
- Weekly Project revisits.
- Time from capture to useful resurfacing.
- Permission retention and revocation.
- Four-week and twelve-week retention.
- Provenance inspection rate.
- Proactivity dismissal or disablement.
- Cost per resolved Commitment.

Message count, screen time, and daily prompts MUST NOT be primary success measures.

## Competitive rationale

- **Samsung and Google** dominate hardware and Android integration; IANA remains complementary and wins on neutral continuity.
- **Apple** demonstrates integrated privacy; IANA makes privacy observable through contracts and receipts on Android.
- **ChatGPT and Claude** lead general intelligence; IANA uses capable providers and differentiates through persistent structured context.
- **Perplexity** leads research; IANA connects research to decisions and Actions rather than becoming a search engine.
- **Nothing Essential Space** validates capture but IANA extends capture into Project state, Commitments, and Actions.
- **Microsoft Copilot** dominates Microsoft work context; IANA begins with individuals whose context crosses personal and professional ecosystems.

## Strategic risks

The strategy acknowledges platform dependency, permission friction, Play policy, reliability expectations, cold start, replication by incumbents, provider dependency, inference cost, security concentration, false positives, intrusive proactivity, integration scope pressure, willingness to pay, synchronization complexity, and brand confusion with the Internet Assigned Numbers Authority. These risks are controlled through narrow scope, explicit capability disclosure, local-first authority, deterministic validation, progressive permissions, bounded proactivity, and proven architecture.

## References

- [00_VISION.md](00_VISION.md)
- [02_PRINCIPLES.md](02_PRINCIPLES.md)
- [03_ARCHITECTURE.md](03_ARCHITECTURE.md)
- [04_PLATFORM.md](04_PLATFORM.md)

## Future extension notes

Release sequencing expresses product order, not authorization to bypass architecture. Later versions require explicit contracts, compatibility policies, data ownership, permission semantics, and conformance tests before implementation.
