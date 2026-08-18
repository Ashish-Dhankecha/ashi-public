# Design Principles and System Invariants

Two different kinds of rule govern Ashi, and conflating them is a mistake worth avoiding.

**Principles** are commitments about what the system is for. They are argued, occasionally
amended, and occasionally deleted.

**Invariants** are testable properties with a designated enforcement point. Violating one
is a breaking change requiring a recorded decision.

---

## Part 1 — Principles

### Retained

1. **Human first.** Ashi advises; the human decides. Every outward action is subject to a
   permission boundary, and the identity core is human-writable only.
2. **Identity is independent.** Identity is structured persistent data, never prompt text.
   It is readable and editable without running Ashi's code.
3. **LLM independence.** Models are replaceable computational engines. Nothing
   architecturally load-bearing may depend on a specific model's behaviour.
4. **Local first.** Personal cognition belongs to the person. The system runs on the
   owner's hardware and holds its own state.
5. **Vendor independence.** No single provider is load-bearing. Routing degrades across
   providers and models rather than failing.
6. **Explainability.** Every output traces to its inputs — structurally, through the
   causal event graph, not by asking a model to explain itself.
7. **Truth over agreement.** Ashi contradicts the user when evidence requires it.
8. **Explicit uncertainty.** Confidence is represented, never implied. Low confidence
   stays a checkable hypothesis rather than being silently resolved into false certainty.
9. **Provenance.** Every belief carries its evidence and its source reliability.
10. **Graceful degradation.** Reduced capability always beats failure.
11. **Privacy by default.** Nothing leaves the machine without explicit consent. Screen
    and camera perception default to off, with no configuration that enables them
    silently.

### Amended

12. **Simplicity before intelligence — but never instead of it.**

    Applied as originally written, this principle correctly justified excluding every
    individual adaptive loop, and produced a system in which nothing could emerge. The
    amendment: simplicity governs *implementation*, never *topology*. A simple feedback
    loop is correct; no feedback loop is a different architecture, not a simpler one.

### Deleted, and why the deletions matter

13. ~~**Information flows in one direction.**~~ Deleted. It forbade by name the cycles the
    vision requires. Replaced by invariant **I2**, which preserves everything the rule was
    actually protecting — debuggability, no hidden coupling — while permitting feedback
    through a single, explicit, inspectable channel.

14. ~~**Stateless where practical.**~~ Deleted. Correct for infrastructure, actively wrong
    for cognition: a cognitive system's state *is* its cognition. Replaced by: *state is
    explicit, typed, inspectable, and reconstructible.*

15. ~~**Evolution over perfection.**~~ Deleted. Not a principle; a mood. It provided cover
    for deferring structural decisions whose cost rises monotonically with time. Replaced
    by an explicit cost-of-delay analysis for every breaking change.

---

## Part 2 — System invariants

Each is a testable property with an enforcement point. They are numbered as they are in
the private architecture so that discussion can reference them stably.

| # | Invariant | Status |
|---|---|---|
| **I1** | Identity is data, never process state. It is readable without Ashi's code. | **ACTIVE** — file-backed, versioned, exportable |
| **I2** | Forward flow within a cognitive cycle is acyclic. Feedback flows only through the event log. | **ACTIVE** — no subsystem holds a reference to a later-stage subsystem |
| **I3** | Every cognitive act emits an event *before* its effects are observable. | **ACTIVE** — append precedes state mutation |
| **I4** | Derived state is always reconstructible from durable state plus the event log. | **PARTIALLY VERIFIED** — holds by construction; not exercised as a full wipe-and-rebuild drill |
| **I5** | No subsystem *triggers* another directly. Queries may be direct; triggering is via events. | **ACTIVE** — enforced by dependency rules and review |
| **I6** | Nothing enters a model prompt except through the attention context. | **ACTIVE** for retrieved and derived content — attention is the single admission point for it. Not type-enforced against a caller supplying content by another route; the boundary is structural, at one place, rather than impossible to circumvent |
| **I7** | Every cognitive stage has a defined degraded mode that cannot fail the cycle. | **ACTIVE** by construction — every optional dependency is guarded. **UNMEASURED** as a systematically injected fault ladder |
| **I8** | Given the same event log and seeds, replay is bit-identical. | **DESIGNED.** Log reconstruction exists and diagnoses gaps and corruption; nothing re-executes cognition from the log. See below |
| **I9** | No subsystem imports upward. Ports are defined by the consumer. | **ACTIVE** — the reason cognitive packages never depend on the model layer |

### Why I8 matters

Emergence and deterministic debugging look like they are in tension: a system whose
behaviour arises from interaction is a system whose behaviour is hard to predict. Event
sourcing is supposed to dissolve that tension — emergence comes from *interaction*,
determinism comes from *replayability*. An emergent behaviour observed once can then be
reproduced, stepped through, and attributed.

The architecture also specifies a research instrument on top of it: replay a real
historical cycle with a changed component — different salience weights, a different
reasoning strategy, a different model — and diff the outcomes against ground truth already
in the log. That would make the architecture itself empirically tunable.

**Neither is built.** What exists is faithful reconstruction of what the log contains,
including detection of truncation, corrupted records, and sequence gaps. Re-execution
replay and counterfactual divergence remain designed only — the single largest gap between
Ashi's architectural ambition and its current reality, and the reason this invariant is
listed with its true state rather than as satisfied.

---

## Part 3 — Architectural freeze discipline

The cognitive substrate is in maintenance mode. The distinction being enforced:

**Permitted without a recorded architectural decision:** bug fixes, measured performance
work, test coverage, documentation corrections, dependency updates.

**Requires a recorded decision:** a new package, a new stage in the cognitive cycle, a new
event category, a new persisted store or singleton, or any change to an invariant above.

**The evidence bar for proposing an architectural change** is a benchmark result, a filed
reproducible defect, or a demonstrated capability gap — never speculation.

The rule that does the most work in practice: *if implementation reveals a contradiction
with the documented architecture, record it as a new decision rather than quietly changing
the architecture to match the code.* An architecture that silently absorbs whatever the
code does is not an architecture.

---

## Part 4 — Two working rules

Neither is an invariant, but both shape design decisions often enough to belong here.

> **Do not act on a hypothesis without re-measuring it, and report what the measurement
> says even when it contradicts the plan.**

Plausible architectural hypotheses in this system have turned out to be backwards more than
once — a relevance threshold that needed raising rather than lowering; a timeout that was
correctly reporting an unsuitable component rather than being badly sized. Acting on the
plan instead of the measurement would have made both worse.

> **Bound anything derived and long-lived.**

Unbounded growth has appeared three separate times in three unrelated subsystems. It is the
default outcome of any accumulating structure that nobody was assigned to cap, and it is
cheap to prevent and expensive to discover.
