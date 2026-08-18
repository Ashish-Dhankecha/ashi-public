# Research

Ashi is an engineering project with a research spine. This document sets out the questions
it is actually structured to explore, and is careful to distinguish what has been
demonstrated from what is hoped.

| Label | Means |
|---|---|
| **Established** | Built and demonstrated. A claim about this system, not about the world |
| **Engineering question** | A design bet with a clear success criterion, answerable in months |
| **Research question** | Open, answerable over years, and not yet answered |
| **Speculation** | Interesting, unresolved, not currently pursued |

---

## 1. Can a system accumulate judgment, rather than just text?

**Established.** State survives process death: identity, beliefs with their evidence, goals,
plans, commitments with deadlines, projects, episodes, relationship history, and the full
causal record. Retrieval is structural and scoped rather than a flat similarity search over
everything ever said. A commitment stated in a person's own words is recalled across a
restart and raised when its deadline passes.

**Research question.** Whether structured longitudinal state produces *better judgment* and
not merely better recall. The distinction is sharp: a system that remembers what you said is
not the same as a system that has learned how you tend to be wrong.

Answering it requires comparing decision quality against a memory-equipped but unstructured
baseline, over months. That comparison has not been run.

---

## 2. Does feedback topology change what a cognitive architecture can do?

The question is not rhetorical, and Ashi's own history is a clean negative result on one
side of it.

An earlier version of this architecture specified interacting cognitive systems and
simultaneously specified that information flows in one direction. Those are incompatible: a
system whose behaviour improves from experience requires an outcome to influence what is
recalled next time, and that is a cycle. The implementation followed the mechanism
specification faithfully — and the entire cognitive runtime, workspace and salience and
competition and executive control, was computed on every cycle and read by nothing.

Every individual decision to omit an adaptive loop had been defensible. Their sum was the
reason nothing emerged. The principle that replaced them:

> **Simplicity governs implementation, never topology. A simple feedback loop is correct. No
> feedback loop is not a simpler version — it is a different architecture.**

**Established.** The resolution: an immutable causal event log as the sole backward edge.
Forward flow within a cycle stays acyclic; feedback flows only through the log, into later
cycles. That one primitive supplies the feedback edge, reflection's substrate, the
reconstruction source for recovery, and a replayable causal record.

**Engineering question.** Whether the resulting behaviour differs *measurably* from the
feed-forward version. Answering it needs counterfactual replay — re-running real history
with one component changed — which is designed and not built. It is the highest-value
unbuilt thing in the system, and this is why.

---

## 3. Is bounded competitive attention a better answer to context than a bigger window?

**Established.** Subsystems bid; a budget policy selects a subset and genuinely rejects;
prompt content enters only through attention, so prompts are bounded by construction rather
than truncated after the fact.

One property is worth reporting as a finding rather than a decision. **Salience scores
structural shape — recency, urgency, explicit hints — and never content.** That discipline
was adopted for simplicity, and it made the mechanism modality-agnostic for free: an image,
an audio segment, and a line of text compete on identical terms with no new code. Constraints
occasionally pay compound interest.

**Engineering question.** Whether outcome-driven reinforcement of salience improves what gets
recalled over time. Currently inconclusive: the one experiment run against attention
prioritisation could not distinguish correct prioritisation from the competing item never
having been stored, and is treated as a failed experiment rather than a result.

---

## 4. Can a system change itself from experience without becoming something else?

**Established.** The mechanism, and it is enforced rather than conventional. Reflection
proposes and never writes. Learning verifies before integrating, and commits only through
each subsystem's own validated path. Every change is an event, is bounded, is revertible,
and is gated by risk. Identity has an immutable core that no automated process may modify,
with exactly one automated writer for everything outside it.

**One negative result is more instructive than the mechanism description.** An earlier
version of the reinforcement path was an unintended closed loop: a success signal emitted
every turn credited every recalled item, and retrieval ranking read that credit back.
Retrieval frequency was wearing an outcome label. A few dozen ordinary conversational turns
could pin an arbitrary memory at maximum strength, and the visible symptom was Ashi
confidently recommending a test fixture as its user's real ongoing work.

The finding generalises well beyond this system:

> **A self-reinforcing loop whose reward signal correlates with activity rather than outcome
> will converge on whatever is most active.**

The correction was to gate reinforcement on real execution evidence — deliberately not on a
success flag, so a decision to do nothing neither reinforces nor weakens.

**Research question.** Whether bounded self-modification produces net improvement over long
horizons. Unmeasured. The loop demonstrably runs and demonstrably writes; that it makes Ashi
better is not claimed anywhere in this documentation.

---

## 5. What is the minimum that is still the same individual?

**Established.** The tiering of state by reconstructibility forces the question instead of
deferring it, and the committed answer is **values, constraints, and personality** —
everything else, every memory and belief and skill, can in principle be relearned by the
same individual. Identity is code-independent versioned data with an enforced immutable core.

**Engineering question.** Whether that tiering is correct: whether a system restored from
convictions plus experience, with derived state recomputed, is recognisably the same
individual. The export-wipe-import drill that would settle it has not been run, and it is
the single experiment that would move the project's central claim from a design property to
a demonstrated one.

---

## 6. Does evidence-weighted belief revision beat last-write-wins over long horizons?

**Established.** Beliefs carry evidence with source reliability; confidence accumulates from
supporting and contradicting references rather than applying a flat penalty; contradictions
are detected; and retraction genuinely cascades to dependent beliefs. This is the component
with the most real internal dynamics in the system.

**Research question.** Whether it yields measurably better accuracy over months. Unmeasured.

**Open sub-question.** How domain-specific staleness should drive revalidation. Some beliefs
about a person hold for decades and some are stale within a week, and nothing currently
distinguishes them.

---

## 7. Can small local models handle bounded structured decisions?

This is the most thoroughly measured question in the project, and the answer is a qualified
and interesting *no*.

**Established.** Several sub-2B candidate models were evaluated identically — same prompt,
same output schema, same settings, no per-model tuning — against a task set grounded in the
system's real action vocabulary. After a production prompt and schema pass, the strongest
candidate reached high schema validity, a large improvement in parameter extraction accuracy,
fully deterministic decoding, and roughly halved latency.

**And it remained unreliable at one specific thing: deciding that no action is required.**

The investigation matters more than the number:

- **Quantisation ruled out.** No-action accuracy was identical across quantisation levels
  while every other metric moved with them — proving the comparison was sensitive, and that
  it detected nothing here.
- **Prompt design ruled out.** Categories named explicitly in the prompt, one of them
  demonstrated by its own in-context example, failed at the same rate as categories never
  mentioned at all.
- **The failure is structural, not semantic.** Near-total failure on well-formed
  conversational input of every kind; no failure at all on degenerate input.

A second finding came out of the same work, and is arguably more useful: **most of what
looked like a model-capability question turned out to be a measurement question.** Scoring
errors — counting timeouts as valid output, crediting failed calls as correct abstention,
grading one field independently of whether the step it belonged to was right — each flattered
the results, and every correction moved the numbers *worse*. The largest single correction
turned an apparent settled strength into a real hidden weakness.

**The architectural conclusion is the interesting part.** The right response to a model that
is good at *which action* and bad at *whether* was neither to trust it nor to discard it, but
to route its output through a boundary that asks before acting — a boundary the architecture
already required for unrelated reasons. A capability limit was absorbed by an existing safety
property rather than by a new mechanism, which is what a layered architecture is supposed to
produce and rarely does on the first try.

**Engineering question.** Whether grammar-constrained decoding closes the remaining
structural drift. Designed, deliberately not built — the evidence pointed at prompt and
schema work first.

---

## 8. What distinguishes a tool from a partner?

The working answer, and each element has a mechanism:

1. **It holds something for you across time.** Commitments persist with deadlines and are
   raised when due — *established*, and verified across real restarts.
2. **It contacts you unprompted, for a good reason.** A sweep against persisted commitments
   fires on its own schedule — *established once*, not yet routine.
3. **It tells you the truth about what it did.** *Established, and hardest-won.* Three
   mechanisms enforce it: silence about a failed action is treated as a failure mode rather
   than as neutral; an empty plan is an explicit no-action outcome rather than a vacuous
   success; and a claimed success is re-observed through a *different* capability than the
   one that claimed it.

**Research question.** Whether those three are sufficient. Probably not: a genuinely useful
partner also needs a substantial model of its user and their world, and an unprompted contact
that is delivered but never acknowledged has not closed its loop.

**Speculation.** Whether "partner" is the right frame at all, or whether the useful properties
are fully captured by "an instrument with durable state and honest reporting." This
documentation does not resolve that.

---

## 9. Is composition the actual contribution?

Stated so it can be attacked: most of the individual mechanisms here exist elsewhere. The bet
is that **the composition is the hard part.**

- A memory hierarchy without an attention budget grows unbounded.
- An attention budget without a causal log cannot be tuned against outcomes.
- A causal log without reflection is an archive nobody reads.
- Reflection without a bounded learning path is drift.
- A learning path without a protected identity core is drift with no floor.
- Execution without a permission boundary is an incident.
- A permission boundary without verification afterwards trusts a self-report.

Each mechanism makes the next one safe. That is a systems claim, testable over years rather
than turns, and it is the research question the whole project is.

The honest current answer to *"is Ashi a persistent cognitive partner?"* is **partially**: it
genuinely holds things for its user across restarts, which it could not do before; it has
made contact unprompted once; and until recently it could not act on the world at all.

---

## What is deliberately not claimed

- Nothing about consciousness, sentience, or understanding. Structure is borrowed from
  cognitive science; the phenomena those words describe in humans are not.
- Nothing about AGI, in either direction.
- No claim to outperform a well-prompted frontier model on single-turn tasks. It very likely
  does not, and is not built for that.
- No measured long-horizon improvement. The loops run; that they improve anything over months
  is unmeasured, and saying so is accuracy rather than modesty.
- No novel learning algorithm, model architecture, or training method. None is present. The
  contribution, if there is one, is systems architecture.
