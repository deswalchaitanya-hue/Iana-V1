# 17 — Surface Engine

## Purpose
Convert capability and projection state into renderer-neutral presentation while managing independent sessions.

## Surface session
Contains session ID, user, invoking surface type, purpose, privacy context, density, locale, device capability snapshot, current semantic state, operation references, created/last-active/expiry times, and version. It is ephemeral and not tied to Activity lifecycle.

## Semantic states
Idle, listening, capturing, interpreting, presenting, awaiting-confirmation, executing, succeeded, failed, unavailable, and private. Transitions are deterministic and drive allowed semantic motion.

## Presentation model
Contains stable model/version, primary content, supporting evidence and provenance links, available capability intents, privacy/disclosure indicators, urgency, density, execution state, safe error, and accessibility semantics. It contains no Android view types.

## Intent flow
Renderers submit typed intents with session/version. The engine validates stale intents, invokes application capabilities, and emits immutable state. Renderers never invoke domain repositories or executors.

## Streaming
Streams are ordered within a session, cancellable, backpressure-aware, and replaceable by final state. Partial model content is visibly provisional and cannot present an Action as approved or completed.

## Privacy adaptation
Lock screen, screen sharing, background, and restricted app state may redact content, reduce Actions, or terminate a session. Privacy can only become stricter without fresh authorization.

## Continuity
A session may transfer to another surface through a signed handoff containing purpose, operation references, and safe presentation state. Domain and operation truth is reloaded through capabilities. Mutable renderer state is not shared.

## Invariants
HUD and Samsung Mode use identical semantics. The engine cannot access concrete views or storage. Surface loss never cancels durable operations unless explicit policy says so.

## References
[18_SURFACES.md](18_SURFACES.md), [12_APPLICATION_CAPABILITIES.md](12_APPLICATION_CAPABILITIES.md), [37_PRIVACY_PRODUCT_CONTROLS.md](37_PRIVACY_PRODUCT_CONTROLS.md)

## Future extension
Desktop, web, watch, and DeX renderers consume compatible presentation models.
