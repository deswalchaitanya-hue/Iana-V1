# 39 — Architecture Decisions

## Purpose
Index approved decisions already present in the immutable Architecture Blueprint and Phase 1; it does not create new decisions.

## Decision index
| ID | Approved decision | Authority |
|---|---|---|
| ADR-001 | On-device modular monolith is the default | [03_ARCHITECTURE.md](03_ARCHITECTURE.md) |
| ADR-002 | Dependencies point inward; runtime callbacks use ports | [03_ARCHITECTURE.md](03_ARCHITECTURE.md) |
| ADR-003 | Typed in-process APIs precede events, IPC, and networking | [03_ARCHITECTURE.md](03_ARCHITECTURE.md) |
| ADR-004 | Canonical transactional state is relational and owner-partitioned | [04_PLATFORM.md](04_PLATFORM.md) |
| ADR-005 | Projections, graph indexes, search, and summaries are rebuildable | [03_ARCHITECTURE.md](03_ARCHITECTURE.md) |
| ADR-006 | State mutation and events use a transactional outbox | [04_PLATFORM.md](04_PLATFORM.md) |
| ADR-007 | Events deliver at least once with no global ordering | [03_ARCHITECTURE.md](03_ARCHITECTURE.md) |
| ADR-008 | Personal data is local-first and device-authoritative by default | [04_PLATFORM.md](04_PLATFORM.md) |
| ADR-009 | AI proposes; deterministic domain code validates | [02_PRINCIPLES.md](02_PRINCIPLES.md) |
| ADR-010 | Providers are replaceable adapters behind task contracts | [03_ARCHITECTURE.md](03_ARCHITECTURE.md) |
| ADR-011 | Actions use explicit risk, approval, idempotency, compensation, receipts | [03_ARCHITECTURE.md](03_ARCHITECTURE.md) |
| ADR-012 | Plugins default out of process and receive brokered capabilities only | [04_PLATFORM.md](04_PLATFORM.md) |
| ADR-013 | HUD and Samsung Mode share presentation-neutral semantics | [00_VISION.md](00_VISION.md) |
| ADR-014 | Android process death and partial failure are normal | [02_PRINCIPLES.md](02_PRINCIPLES.md) |
| ADR-015 | Synchronization applies domain changes, not database files | [04_PLATFORM.md](04_PLATFORM.md) |
| ADR-016 | Domain-owned merge policies replace universal last-write-wins | [04_PLATFORM.md](04_PLATFORM.md) |
| ADR-017 | Boring, proven patterns outrank clever abstraction | [02_PRINCIPLES.md](02_PRINCIPLES.md) |
| ADR-018 | Every independent module has a Module Contract Pack | [03_ARCHITECTURE.md](03_ARCHITECTURE.md) |

## Usage
Implementation records cite these IDs and the linked authority. A future ADR may clarify an approved decision but cannot redesign immutable sources. Ambiguity is recorded for explicit source-level decision.

## References
[README.md](README.md), [02_PRINCIPLES.md](02_PRINCIPLES.md), [03_ARCHITECTURE.md](03_ARCHITECTURE.md), [04_PLATFORM.md](04_PLATFORM.md)

## Future extension
Phase 3 may create individual ADR files only for decisions traceable to approved authority or later explicit approval.
