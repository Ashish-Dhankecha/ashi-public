# What Ashi Can Do Today

Architecture describes intent. This document describes reality.

Every entry is graded conservatively: **any doubt resolves downward**. A capability is not
promoted without evidence of the matching kind — that something *runs* is shown by the
running system, that it is *correct* by checking the world rather than the system's own
report.

---

## The status vocabulary

Five rungs, and they are not interchangeable:

```
DESIGNED  →  IMPLEMENTED  →  WIRED  →  ACTIVE  →  VERIFIED
```

| | |
|---|---|
| **DESIGNED** | Specified. No working code, or only a model of one |
| **IMPLEMENTED** | Working code with tests. Says nothing about whether anything calls it |
| **WIRED** | Connected into the running system — reachable in principle |
| **ACTIVE** | Actually runs in ordinary operation, and something consumes its output |
| **VERIFIED** | Checked against ground truth outside the system's own report: a stored record, a file on disk, an independent re-observation |

Four further labels appear where they are the honest answer: **PARTIALLY_ACTIVE** (runs, but
on narrower terms than designed), **DORMANT** (built and reachable, but the input that would
trigger it is never produced), **PLANNED**, and **UNMEASURED** — which means exactly what it
says, with no partial credit claimed for owning the tooling.

The distinction that matters most in practice is **implemented ≠ active**. A cognitive
architecture accumulates complete, well-tested subsystems that nothing calls; unit tests
cannot detect this, because they exercise components in precisely the isolation that hides
it. Several capabilities below carry that history, and the vocabulary exists to keep it
visible.

---

## Cognition

| Capability | Status |
|---|---|
| Causal event log — append-only, typed, tiered, traversable | **VERIFIED** |
| Cognitive cycle with its full ordered contributor set | **VERIFIED** |
| Contributor bidding, every cycle, before execution | **VERIFIED** |
| Attention: structural salience scoring | **ACTIVE** |
| Attention: budget-bounded selection that genuinely rejects | **ACTIVE** — calibrated to bind on complex turns, so simple turns are never starved |
| Attention: sole admission point into a prompt | **ACTIVE** |
| Per-contributor budget grants, cancellation on overrun, re-admission rounds | **DESIGNED** — one competition round per cycle today |
| Concurrent execution of independent stages | **PARTIALLY_ACTIVE** — a fixed order with a limited concurrent group |
| Executive control: cognitive mode, interrupts | **PARTIALLY_ACTIVE** — prediction-error interrupts live; voice barge-in designed |
| Hot-path / background-path separation | **VERIFIED** — background work cannot add turn latency. Causal ordering is guaranteed; a total order *between* independent background handlers is not |
| Model routing with per-model health and graceful degradation | **VERIFIED** |
| Log reconstruction, with corruption and gap diagnosis | **ACTIVE** |
| **Re-execution replay** — re-run a cycle and assert identical results | **DESIGNED** |
| **Counterfactual replay** — re-run history with a changed component | **DESIGNED** |

The last two are the largest single gap between what the architecture claims and what
exists. Replay underwrites determinism, debuggability, recovery, and the ability to tune the
architecture empirically rather than by argument. Reconstruction of what the log *contains*
works; nothing yet re-executes cognition from it.

## Memory and knowledge

| Capability | Status |
|---|---|
| Working, episodic, semantic, and procedural memory | **ACTIVE** — all four |
| Scoped hierarchical retrieval, short-circuiting when sufficient | **VERIFIED** |
| Decay, strength, consolidation, forgetting | **ACTIVE** |
| Reinforcement gated on real execution evidence | **VERIFIED** |
| Retrieval quality against a baseline | **UNMEASURED** |
| Entities, facts, relationships | **ACTIVE** |
| Beliefs with evidence, confidence, and a status lifecycle | **ACTIVE** |
| Contradiction detection and evidence-weighted revision | **ACTIVE** |
| Truth maintenance — retraction cascades to dependents | **ACTIVE** |
| Explicit-trigger-only assimilation | **VERIFIED** — Ashi does not silently ingest what it is told |
| Layered extraction with reliability-tiered sources | **ACTIVE** |
| Belief accuracy over time | **UNMEASURED** |
| Bounded graph queries at scale | **PARTIALLY_ACTIVE** — some queries are unbounded in graph size, a known scaling risk |

## Reasoning and inference

| Capability | Status |
|---|---|
| Chain of thought, self-consistency, tree of thoughts | **ACTIVE** |
| Strategy selection by request shape and budget | **ACTIVE** — correlation with answer quality **UNMEASURED** |
| Causal reasoning | **PARTIALLY_ACTIVE** — reachable only on a narrow path |
| Counterfactual evaluation | **IMPLEMENTED**, deliberately isolated to evaluation, never in the production loop |
| Reasoning never writes to memory or knowledge | **VERIFIED** — an invariant |
| Structured local inference, output-schema-constrained | **ACTIVE** |
| Local CPU-first model backend as offline fallback | **ACTIVE** |
| Natural language → structured intent → real action | **VERIFIED** end to end |
| Grammar-constrained decoding | **DESIGNED**, deliberately deferred |

**A known limit, stated because it shaped the architecture:** the local model is reliable at
identifying *which* action a request implies and unreliable at deciding that *no* action is
required. Quantisation and prompt design were both ruled out as causes; the failure is
structural to models of this size. The response was neither to trust it nor to discard it,
but to route its output through the permission boundary that already existed for unrelated
reasons — a capability limit absorbed by an existing safety property.

## Planning and action

| Capability | Status |
|---|---|
| Goals with decomposition, provenance, and lifecycle | **ACTIVE** |
| Plans as dependency graphs of steps | **ACTIVE** |
| Projects and episodes, with suspend / resume / retire | **VERIFIED** |
| Commitments — goals with a deadline, raised when due | **VERIFIED** across real restarts; a deadline is never invented |
| An empty plan is an explicit no-action outcome, never a success | **VERIFIED** |
| Goal achievement requires execution evidence | **VERIFIED** |
| Diagnose-and-repair replanning on genuine failure | **VERIFIED** |
| Repair convergence — forced strategy change instead of identical retry | **VERIFIED** |
| Goal formation from natural language | **PARTIALLY_ACTIVE** — a question can still occasionally be read as a goal |
| Permission boundary: allow / deny / require approval | **VERIFIED** — high risk never auto-allows |
| Asynchronous approval, resume from the paused step, approval expiry | **VERIFIED** |
| Policy layer that can only ever add friction | **PARTIALLY_ACTIVE** |
| **Real outward action, end to end** | **VERIFIED** against a real filesystem, read back independently |
| Multi-step execution with values propagating between steps | **VERIFIED** |
| Post-execution verification via a *different* capability | **PARTIALLY_ACTIVE** — one action class covered; every other reports itself unverified rather than passing silently |
| Execution sandboxing | **DESIGNED** — the most significant open safety item |
| Environment isolation across production, test, and read-only modes | **VERIFIED** |
| Capability self-model — honest refusal from the live registry | **VERIFIED** |
| External-world capabilities: mail, calendar, code hosting, HTTP | **IMPLEMENTED**, integration in progress |

## Reflection, learning, identity

| Capability | Status |
|---|---|
| Episode evaluation, prediction, prediction-error detection | **ACTIVE** |
| Reflection proposes and never writes | **VERIFIED** — an invariant |
| Learning verifies before integrating, and only through validated paths | **ACTIVE** |
| Bounded, revertible, risk-gated adaptation | **ACTIVE** |
| Failure lessons that transfer to different future objectives | **VERIFIED** across a real restart |
| **Learning produces measurable long-horizon improvement** | **UNMEASURED** — the loop runs and writes; that it makes Ashi better is not claimed |
| Self-model of tool reliability, consulted during selection | **ACTIVE** |
| Self-model of strategy reliability | **DORMANT** — reachable, but nothing produces its input |
| Identity as versioned, code-independent data | **VERIFIED** |
| Immutable identity core, enforced against every automated writer | **VERIFIED** |
| Exactly one automated identity writer | **VERIFIED** |
| Proactive sweep against persisted commitments | **ACTIVE** |
| Unprompted contact actually delivered | **ACTIVE** — delivered; not yet routine, and never acknowledged by a person |

## Continuity

| Capability | Status |
|---|---|
| Durable state surviving real process restarts | **VERIFIED** |
| Consistent backup covering both stores together | **ACTIVE** |
| State tiering by reconstructibility | **PARTIALLY_ACTIVE** — tiers are carried; tier-directed policy is designed |
| Portable, tool-independent, self-describing export archive | **DESIGNED** |
| Export → wipe → import into an empty database, verified | **DESIGNED** — the substrate-independence test |
| Degraded boot reporting its own condition per tier | **DESIGNED** |
| Snapshot of transient in-flight cognitive state | **DESIGNED** |
| Degradation ladder as an injected, asserted property | **UNMEASURED** |
| Model, embedding, and hardware migration rehearsals | **PLANNED** |

The pattern is consistent: **the parts of continuity exercised every day are built and
proven; the parts that only matter during a disaster are specified and untested.** That is
the ordinary failure mode of disaster recovery, and the correct response is to run the
drill rather than to describe it better.

## Perception and surfaces

| Capability | Status |
|---|---|
| Ambient world perception, entirely off the conversation path | **VERIFIED** — turn-path cost is a rounding error against response latency |
| Non-mutating observation of the environment | **VERIFIED** — asserted by benchmark, not assumed |
| Projection of perception into knowledge and memory | **ACTIVE** |
| Explicit focus override with a provenance surface | **ACTIVE** |
| Cognitive perception pipeline over a single observation | **PARTIALLY_ACTIVE** |
| Document indexing on explicit trigger | **ACTIVE** |
| Terminal surface | **VERIFIED** |
| Browser surface with canvas and contextual selection | **ACTIVE** |
| Artifact generation, revision, and export | **IMPLEMENTED** — never exercised by a person |
| Turn-based voice, terminal and browser | **IMPLEMENTED** — never spoken to by a person |
| Continuous duplex voice, barge-in, predicted turn-taking | **DESIGNED** |
| Image, screen, camera, and sensor modalities | **PLANNED** |
| Consent scoping for capture streams | **DESIGNED** — the model exists; the streams it would govern do not |
| Ambient desktop overlay and causal timeline surface | **DESIGNED** |
| Mobile capture surface | **DESIGNED** |
| Tutor | **DESIGNED** — deliberately last; it integrates everything else |

---

## Where Ashi is weakest

Ranked by the distance between architectural claim and evidence:

1. **Replay.** The architecture rests on it for determinism, debugging, recovery, and
   empirical self-tuning. Only reconstruction exists.
2. **Disaster recovery.** Specified thoroughly; the drill has never been run.
3. **Long-horizon behaviour.** Reflection and learning run and write. That they improve Ashi
   over months is unmeasured. No extended production soak has been run.
4. **Failure recovery.** Exercised once, inconclusively.
5. **Human use.** Voice has never been spoken to; the artifact workspace has never been
   clicked through by a person.
6. **Verification coverage.** One action class is independently verified after execution.
7. **Sandboxing.** Some execution paths run with host privileges.

## The behavioural bar

Ashi is periodically scored against an adversarial behavioural standard rather than a test
suite: does it hold something for you across time, contact you unprompted for a good reason,
and tell you the truth about what it did?

It currently satisfies the third robustly, the first well, and the second only once. The
standard's own rule holds the score below the midpoint until there is a substantial model of
the user and their world — and reaching the next rung requires reliably executing *and*
verifying real actions across a live session, which is precisely what items 6 and 1 above
are blocking.

The honest answer to *"is Ashi a persistent cognitive partner?"* is **partially**: it
genuinely holds things for you across restarts, which it could not do before; it has
contacted its user unprompted once; and until recently it could not act at all.
