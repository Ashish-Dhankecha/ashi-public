# Vision — Why Ashi Exists

---

## 1. The problem

Contemporary AI assistants are optimised to be useful to everyone. The consequence is
structural, not incidental: a system that must serve any user cannot hold a model of a
particular one. It resets, it re-derives, it re-asks. Its competence is real and its
memory is a feature bolted to a stateless call.

This is fine for most uses. It is fatal for the use Ashi targets: a cognitive partner
that accumulates genuine judgment about one person over years — their goals, their open
commitments, their projects, what they already know, what they have already decided and
why, how they think, and where they are reliably wrong.

Building that requires architectural commitments that a general assistant framework
cannot make:

- **Persistent memory with structured, scoped retrieval** — not a similarity search over
  everything ever said.
- **A belief system that evolves under evidence** — with provenance, confidence,
  contradiction detection, and revision that cascades to dependent beliefs.
- **Goals, plans, and commitments that survive session boundaries** and process restarts,
  and that are answerable later.
- **An identity layer** stable enough to be trusted and adaptive enough to stay accurate,
  which is protected data rather than a system prompt.
- **A bounded cognitive budget**, because a system that does everything on every turn has
  no way to prioritise and no way to be fast.

None of these is exotic. What is unusual is treating them as *subsystems with contracts*
rather than as features, and wiring feedback between them.

---

## 2. The two claims

### 2.1 A cognitive partner is defined by what survives

The design target is an individual whose identity, convictions, and experience survive:

- model replacement
- provider replacement
- database replacement
- operating system replacement
- hardware replacement
- restart, migration, and partial data loss

…while evolving continuously without becoming a different individual.

The second half is the hard half. Anything can be made stable by freezing it. Anything can
be made adaptive by letting it drift. The engineering problem is a system that changes how
it thinks and what it knows, but not what it is for — which is why identity has an
immutable core that no automated process may modify, and exactly one automated writer for
everything else.

### 2.2 Intelligence worth building requires cycles in the component graph

Ashi's first architecture promised intelligence emerging from interacting cognitive
systems, and specified a unidirectional pipeline of stateless components with an explicit
rule that *information flows in one direction*.

Those are formally incompatible. Emergence requires cycles in the component interaction
graph. The rule forbade cycles by name. The implementation followed the mechanism
specification faithfully and correctly — which is why the entire cognitive runtime
(workspace, salience, competition, executive control) was computed on every cycle and read
by nothing.

The lesson generalised into a principle the project now holds:

> **Simplicity governs implementation, never topology.** A simple version of a feedback
> loop is correct. No feedback loop is not a simple version — it is a different
> architecture.

Every individual decision to omit an adaptive loop had been locally defensible. Salience
weights fixed at construction. Competition reduced to a sort. A scheduler explicitly
refusing to prioritise. Memory strength computed and never read. Each exclusion was right
in isolation. Their sum was the reason nothing emerged. This is the most transferable
finding the project has produced.

---

## 3. What makes Ashi structurally different

Not "it has memory". Four structural differences, each with a mechanism:

### 3.1 An event log that is a causal graph, not a timeline

Every cognitive act emits an immutable event before its effects are observable, and every
event carries the events that caused it. This is not logging. It is:

- **the feedback edge** — the only backward path in the system, which is what makes
  outcome-influences-future-cognition possible without reintroducing hidden coupling;
- **the substrate for reflection** — you cannot evaluate past behaviour you did not record
  causally;
- **the reconstruction source** — derived state is rebuildable, so partial data loss
  degrades rather than kills;
- **explainability delivered mechanically** — "why did you say that?" is answered by
  walking the causal graph backwards to the percepts, not by asking a model to narrate
  itself.

### 3.2 Attention as admission control, not decoration

Nothing retrieved or derived enters a model prompt except by winning attention. Content
must be in the workspace and must be selected. Three consequences follow immediately:
salience weights become a dial with real behavioural effect; context overflow is bounded
by construction rather than by truncation after the fact; and continuous ambient
perception becomes tractable, because unsolicited percepts without admission control are
simply unbounded context growth.

### 3.3 Subsystems compete for a scarce budget

Subsystems do not get called in order. They bid — cheaply, synchronously, declaring intent
without doing work — and an attention policy allocates a bounded per-cycle budget to a
dependency-satisfiable subset. A trivial exchange produces low bids and a cheap cycle
without a hardcoded special case for greetings.

Competition is only meaningful when there is something to compete for. Supplying a real
budget is what converts a sorted list into a selection.

### 3.4 A boundary between deciding and acting

Planning produces action proposals. It does not execute them. A permission boundary
classifies risk and returns allow, deny, or requires-approval; high risk never
auto-allows. Post-execution, a claimed success is re-observed through a *different*
capability than the one that made the claim — because a provider's self-report is not
evidence.

---

## 4. Honesty as an architectural property

A cognitive partner that reports optimistically is worse than one that reports nothing,
because its user calibrates on it. Three rules are therefore enforced structurally rather
than left to good behaviour.

**Silence is not truth.** A system that says nothing after failing to act is not being
honest by omission — it lets an earlier turn's claim stand unchallenged. Every no-action
outcome states itself explicitly, and an action turn that produced nothing says so.

**A vacuous success is a failure.** A plan with no steps trivially satisfies "all steps
completed". Ashi treats an empty plan as an explicit no-action outcome, and a goal cannot
be marked achieved without evidence that something actually ran.

**A self-report is not evidence.** When an action claims success, the claim is re-checked
by observing the world through a *different* capability than the one that acted. A
contradicted result loses its success, and that propagates to what the user is told. Where
no independent check exists for a class of action, the result names itself unverified
rather than passing quietly.

The same discipline governs this documentation. Capability claims are graded by the
evidence available for them, and the vocabulary
([designed, implemented, wired, active, verified](../status/README.md#the-status-vocabulary))
exists so that a gap between what is built and what is running stays visible instead of
being smoothed into a single word.

---

## 5. Non-goals

- **Not general-purpose.** One user, by design.
- **Not cloud-native.** Local-first; cloud models are replaceable executors.
- **Not autonomous.** Every outward action passes a permission boundary. The human decides.
- **Not a claim about machine consciousness or AGI.** Structure is borrowed from cognitive
  science; no claim is made about the phenomena those terms describe in humans.
- **Not maximally capable per turn.** Given a choice between a better single answer and a
  better system a year from now, the architecture chooses the second.
