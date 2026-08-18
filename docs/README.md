# Ashi Documentation

The public architectural specification of Ashi — written for a reader who wants to
understand the system's structure and reasoning.

---

## Where to start

**For the argument:** [Vision](vision/README.md) → [Principles](vision/principles.md) →
[Cognitive OS](architecture/cognitive-os.md).

**For the mechanism:** [Architecture](architecture/README.md) →
[Cognitive cycle](architecture/cognitive-cycle.md) → [Subsystems](architecture/subsystems.md).

**For what actually works:** [Status](status/README.md), then [Roadmap](roadmap/README.md).

**For the pictures:** [Diagrams](../assets/diagrams/README.md).

---

## Contents

| | |
|---|---|
| [vision/](vision/README.md) | Why Ashi exists, the problem it addresses, what makes it structurally different |
| [vision/principles.md](vision/principles.md) | Design principles, and the invariants that make them testable |
| [architecture/](architecture/README.md) | How the system fits together: the event log, feedback, layering, degradation |
| [architecture/cognitive-os.md](architecture/cognitive-os.md) | What "Cognitive OS" means, and Ashi's placement against adjacent systems |
| [architecture/cognitive-cycle.md](architecture/cognitive-cycle.md) | The cycle as a mechanism, and how much of it is built |
| [architecture/subsystems.md](architecture/subsystems.md) | Every major subsystem in depth |
| [architecture/continuity.md](architecture/continuity.md) | Tiers, reconstruction, portability, degradation |
| [architecture/experience.md](architecture/experience.md) | Surfaces, multimodality, consent |
| [status/](status/README.md) | Capability-level truth: what Ashi can and cannot do today |
| [roadmap/](roadmap/README.md) | Direction, near-term and long |
| [research/](research/README.md) | Open questions, labelled by how open they are |

---

## A note on capability language

This documentation uses a deliberately strict vocabulary — **designed**, **implemented**,
**wired**, **active**, **verified** — and does not treat them as synonyms. The full
definitions are in [status/](status/README.md#the-status-vocabulary).

The reason is practical. In a system of this size, a subsystem can be complete, correct,
well-tested, connected, and still never run, because nothing produces the input that would
trigger it. Documentation that collapses those states reads as more capable than the system
is. Where a capability sits below the top rung here, it is because the evidence for the
higher rung does not exist yet.

---

## Public and private

This documentation explains what Ashi does, why it is designed that way, and how the
architecture works conceptually. It does not describe how the private implementation
realises it.

Concepts, principles, architecture, mechanisms, invariants, lifecycle and state models,
design rationale, capability status, and research direction are public. Source code and
layout, internal structure, wiring, storage schemas, configuration, credentials,
infrastructure, deployment, and operational procedure are not.

Where a mechanism can be explained conceptually, it is. Where naming something is
unavoidable, it is named as a concept rather than as an identifier.

---

## Keeping this accurate

The implementation is the authority on what exists; this documentation is the authority on
what the architecture is.

- When the architecture changes, the conceptual documents change — and superseded design is
  marked as superseded rather than deleted, because the reasons explain the shape of the
  system.
- When a capability advances a rung, [status/](status/README.md) changes; the
  [roadmap](roadmap/README.md) changes only if what comes next changes.
- Implementation changes that preserve the architectural contract require no change here.
  That is what makes this documentation maintainable rather than a second codebase.
- A capability is never promoted without evidence of the matching kind. A passing test shows
  that something is implemented, never that it runs in practice.
