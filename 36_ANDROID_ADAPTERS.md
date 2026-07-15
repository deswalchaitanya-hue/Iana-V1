# 36 — Android Adapters

## Purpose
Define Android/One UI boundary behavior for permissions, lifecycle, scheduling, intents, overlays, notifications, and recovery.

## Adapter rule
Adapters implement platform service contracts and contain Android mechanics only. They do not own product policy, domain rules, provider routing, or presentation semantics.

## Permission adapter
Reports OS grant state, requests through system UI, observes revocation, and maps capability availability. OS grant never becomes IANA authorization.

## Lifecycle and process recovery
Components assume process death at any boundary. Durable operation IDs, saved projection checkpoints, idempotent initialization, and kernel-managed composition restore behavior. Activities and services do not own domain operations.

## Scheduling
WorkManager or supported platform schedulers implement job descriptors. Foreground services are used only for Android-allowed user-visible long work with correct notification. Exact alarms require explicit product need and permission.

## Intents and invocation
Adapters cover share target, selected content where supported, Quick Settings tile, assistant role where available, widgets, deep links, app shortcuts, and explicit capture. Inputs are normalized and provenance recorded.

## Overlay
Handles draw-over permission, windows, insets, orientation, focus, lock state, accessibility coexistence, OEM restrictions, and teardown. Permission loss closes overlay safely and offers Samsung Mode fallback.

## Notifications
Ingestion listener and IANA delivery are separate components and identities. App exclusions and sensitivity apply before persistence. Delivery uses channels, deduplication, privacy, actions, and user settings.

## Device capability matrix
Record Android API, One UI/OEM, available roles/APIs, background/battery restrictions, screen/lock capability, accessibility coexistence, speech support, and known compatibility policy. Capability discovery reports facts, not promises.

## Testing
Matrix covers supported Android and One UI versions, memory pressure, reboot, upgrade, revocation, offline, battery saver, thermal pressure, overlay conflict, accessibility services, and notification loops.

## References
[18_SURFACES.md](18_SURFACES.md), [28_NETWORKING_AND_BACKGROUND_WORK.md](28_NETWORKING_AND_BACKGROUND_WORK.md), [24_SECURITY_AND_PERMISSIONS.md](24_SECURITY_AND_PERMISSIONS.md)

## Future extension
Galaxy Edge Panel, DeX, watch, and OEM-specific adapters remain plugins/specialized packages.
