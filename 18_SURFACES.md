# 18 — Surfaces

## Purpose
Define renderer contracts for HUD, Samsung Mode, overlay, widgets, notifications, and voice.

## Common renderer contract
A renderer declares type/version, supported presentation-model versions, density and interaction capabilities, privacy environment, lifecycle callbacks, accessibility support, and degraded behavior. It renders state and submits intents only.

## HUD / IANA Mode
Owns overlay composition, semantic depth, focus, input, and state-driven animation. Motion may express listening, interpretation, privacy, confirmation, execution, success, failure, urgency, or hierarchy. Decorative spectacle is prohibited. Overlay unavailability falls back to supported surfaces.

## Samsung Mode
Owns conventional One UI-aligned screens, navigation, and system presentation. It does not import HUD implementation. Switching modes retains capability, policy, operation, and domain behavior.

## Android overlay
The platform adapter owns permission, window placement, insets, coexistence, lifecycle, and recovery. The renderer owns no permission logic and assumes revocation at any time.

## Widgets
Widgets consume compact stale-tolerant projections and submit predefined commands. Initial widgets are quick capture, Attention Inbox, current Project, and Ask IANA. Sensitive content is hidden by default on locked surfaces.

## Notifications
Delivery renders Attention or Action state with stable deduplication and channels. Ingestion is a separate Android adapter and can never ingest IANA-delivered notifications as external source content.

## Voice
Voice composes recognition, turn transport, synthesis, and audio focus. It carries ordinary intents and presentation models. Spoken confirmation must meet the same evidence policy and cannot bypass review.

## Failure behavior
Every surface handles stale version, revoked permission, process death, unavailable capability, private-session state, and offline operation. Loss of one surface degrades only that surface.

## Conformance
Golden semantic-state fixtures must render equivalent meaning and available Actions across HUD and Samsung Mode. Accessibility labels and privacy indicators are mandatory.

## References
[17_SURFACE_ENGINE.md](17_SURFACE_ENGINE.md), [36_ANDROID_ADAPTERS.md](36_ANDROID_ADAPTERS.md), [33_TESTING_STRATEGY.md](33_TESTING_STRATEGY.md)

## Future extension
New renderers may change presentation, never domain or confirmation semantics.
