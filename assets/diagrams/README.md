# Conceptual Diagrams

The complete diagram set for Ashi, in one place. Several are reproduced inline in the
documents where they carry an argument.

These are conceptual diagrams of how the system thinks — not of module structure, internal
wiring, or deployment. Status is annotated *inside* the diagrams where it matters, so that
a reader who sees only the picture does not come away believing that re-admission rounds,
duplex voice, or the mobile surface already exist.

---

## 1. System overview

```mermaid
graph TB
    subgraph Surfaces["Surfaces — thin clients: no cognition, no authoritative state"]
        CLI[Terminal]
        WEB[Browser workspace]
        VOICE[Voice]
        AMB[Ambient / mobile — DESIGNED]
    end

    subgraph Core["Cognitive core"]
        CYCLE[Cycle driver]
        ATT[Attention · bounded budget]
        WS[Workspace]
        EXEC[Executive control · mode · interrupts]
    end

    LOG[("Cognitive Event Log<br/>append-only · typed · tiered · causal")]

    subgraph Contribs["Cognitive contributors"]
        PERC[World & cognitive perception]
        INTR[Interpretation]
        MEM[Memory]
        KNO[Knowledge & belief]
        REA[Reasoning]
        PLAN[Planning]
        EXPR[Character · relationship · presence]
    end

    subgraph Bg["Background path — never on the turn"]
        REF[Reflection]
        LRN[Learning]
        MET[Metacognition]
        PRO[Proactive sweep]
    end

    subgraph Act["Action boundary"]
        PB{Permission boundary}
        PROV[Execution providers]
        VER[Post-execution verification]
    end

    ID[("Identity<br/>immutable core")]

    Surfaces -->|percepts| CYCLE
    CYCLE --> ATT --> WS --> Contribs
    Contribs --> LOG
    PLAN --> PB
    PB -->|allow| PROV --> VER --> LOG
    PB -->|requires approval| Surfaces
    LOG --> Bg
    Bg -.->|bounded, evidenced proposals| Contribs
    REF -.->|the only automated writer| ID
    ID -.-> Contribs
    LOG -.->|the only backward edge| CYCLE
```

---

## 2. The cognitive cycle

```mermaid
graph LR
    P["PERCEIVE<br/><i>any modality<br/>becomes a percept</i>"]
    A["ADMIT<br/><i>workspace +<br/>attention evaluates</i>"]
    C["COMPETE<br/><i>contributors bid;<br/>budget allocated</i>"]
    E["EXECUTE<br/><i>granted work runs;<br/>writes back</i>"]
    R["RESOLVE<br/><i>response from attention<br/>context alone; episode closed</i>"]

    P --> A --> C --> E --> R
    E -.->|"re-admission (DESIGNED)<br/>bounded rounds"| A
    R ==>|events| LOG[(Event log)]
    LOG -.->|"feedback into the<br/>NEXT cycle's bids"| C
```

**Read the dotted edges carefully.** Re-admission is designed and not built. The feedback
edge is real, but it is *between* cycles, never within one — that is what keeps the forward
path acyclic and debuggable.

---

## 3. The cognitive feedback loop

```mermaid
graph TD
    EXP["Experience<br/>a closed cycle"] --> LOG[(Event log)]
    LOG --> REF["Reflection<br/>evaluate · criticise · predict"]
    REF -->|"proposals — never writes"| LRN["Learning<br/>verify · integrate · adapt"]
    LRN -->|"bounded · revertible · risk-gated<br/>via each subsystem's OWN validated path"| CH["Changed parameters<br/>retrieval ranking · salience weights<br/>belief confidence · memory strength · identity"]
    CH --> FUT["Future cognition"]
    FUT --> EXP
    REF -.->|"prediction error<br/>= surprise"| INT["Interrupt<br/>mode shift"]
    INT --> FUT

    GATE{{"Reinforcement requires<br/>EXECUTION EVIDENCE<br/>— not a success flag"}}
    GATE -.-> LRN
```

The gate is the fix for a real defect: without it, retrieval frequency wore an outcome
label and the loop converged on whatever was most active rather than on what worked.

---

## 4. Memory lifecycle

```mermaid
graph LR
    IN["Experience"] --> ENC["Encode"]
    ENC --> ACT["Active record"]
    ACT --> RET["Retrieved"]
    RET -->|"evidenced use only"| REI["Reinforce strength"]
    REI --> RANK["Feeds retrieval ranking"]
    RANK --> RET
    ACT -->|"time without use"| DEC["Decay"]
    DEC --> CAND["Forgetting candidate"]
    CAND --> FGT["Forgotten"]
    ACT --> CLU["Redundant cluster"]
    CLU --> CON["Consolidate / merge"]
    CON --> ACT
```

### Retrieval order

```mermaid
graph TD
    T1["1 · Identity"] --> T2["2 · Pinned context"] --> T3["3 · Active goal"]
    T3 --> T4["4 · Current episode"] --> T5["5 · Recent episodes"] --> T6["6 · Project memory"]
    T6 --> T7["7 · Long-term memory"] --> T8["8 · Semantic search"] --> T9["9 · Knowledge graph"]
    N1["Tiers 1–3: near-zero cost, ALWAYS run"] -.-> T1
    N2["Tiers 4–9: searched, and short-circuit<br/>as soon as retrieval is sufficient"] -.-> T4
```

---

## 5. Knowledge and belief model

```mermaid
graph TD
    OBS["Observation / statement"] --> EXT["Layered extraction<br/>cheap deterministic parse, then<br/>model-based at a LOWER reliability tier"]
    EXT --> ASM["Assimilation path<br/>explicit trigger only"]
    ASM --> ENT["Entity"]
    ASM --> FACT["Fact<br/>subject → predicate → object"]
    FACT -->|"both ends resolve"| REL["Relationship"]
    FACT --> CD{"Contradiction<br/>detector"}
    CD -->|"no conflict"| STORE[("Knowledge graph")]
    CD -->|"genuine conflict"| BEL["Belief<br/>a HELD POSITION, not a triple"]
    BEL --> EVD["Evidence<br/>source · reliability tier · time"]
    EVD --> CONF["Confidence<br/>supporting vs. contradicting references"]
    BEL --> LIFE["Status lifecycle<br/>proposed → active → superseded → retracted"]
    LIFE --> REV["Belief revision"]
    REV --> TMS["Truth maintenance"]
    TMS -->|"cascade"| DEP["Dependent beliefs"]
    DEP --> REV
```

**A belief is formed only on a genuine conflict.** Forming one per fact duplicated the
entire graph — a real defect, fixed.

---

## 6. Attention competition

```mermaid
graph TB
    subgraph Inputs
        WSI["Workspace items"]
        BIDS["Contributor bids<br/>urgency · expected value · estimated cost"]
    end
    SAL["Salience scoring<br/>STRUCTURAL ONLY:<br/>recency · urgency · explicit hint<br/>· goal relevance · reinforcement"]
    WSI --> SAL
    BUD["Per-cycle budget"] --> POL
    DEP["Stage dependencies"] --> POL
    SAL --> POL{"Attention policy"}
    BIDS --> POL
    POL -->|"selected"| RUN["Runs this cycle"]
    POL -->|"rejected, WITH REASON"| REJ[(Recorded in the log)]
    RUN --> AC["Attention context"]
    AC --> PR["The only route into a prompt"]
```

Salience never looks at content. That constraint — adopted for simplicity — is why the
mechanism is modality-agnostic for free: an image and a line of text compete identically.

---

## 7. Reflection → learning loop

```mermaid
sequenceDiagram
    participant C as Cognitive cycle
    participant L as Event log
    participant R as Reflection
    participant N as Learning
    participant S as Owning subsystem
    participant I as Identity

    C->>L: events (append precedes effect)
    Note over C,L: the cycle ends here — nothing below adds turn latency
    L->>R: closed episode
    R->>R: evaluate · criticise · predict
    R--)C: interrupt, on prediction error
    R->>N: proposals (never a write)
    N->>N: verify against existing beliefs FIRST
    N->>S: bounded · revertible · risk-gated change
    S->>S: commit via its OWN validated path
    S->>L: change event
    R->>I: identity update, only with a justifying insight
    Note over I: immutable core is refused, always
```

---

## 8. Identity and continuity

```mermaid
graph TD
    subgraph T0["Tier 0 — Self · loss = death · never reconstructible"]
        V["Values"]
        CN["Constraints"]
        PS["Personality"]
    end
    subgraph T1["Tier 1 — Convictions · loss = amnesia, still Ashi"]
        B["Beliefs"]
        F["Core semantic facts"]
        G["Active goals"]
    end
    subgraph T2["Tier 2 — Experience · loss = lost history, intact self"]
        EP["Episodic memory"]
        EL["Event log"]
        KG["Knowledge graph"]
    end
    subgraph T3["Tier 3 — Derived · loss = nothing · NEVER backed up"]
        EM["Embeddings"]
        ST["Strength / decay"]
        IX["Indices / projections"]
    end
    T2 -->|"always rebuildable"| T3
    T2 -->|"replay through validated paths"| T1
    HUMAN(["The human — the ONLY writer of the immutable core"]) --> T0
    REFL(["Reflection — the only automated writer, everything else"]) --> T0
    NOTE["Tier 0 is a SOURCE, never a derivative:<br/>nothing can rebuild it"] -.-> T0
```

**Backup frequency is inverse to reconstructibility, not proportional to size.**

---

## 9. Planning → permission → execution

```mermaid
graph TD
    G["Goal<br/>provenance · level · status · deadline"] --> P["Plan — a dependency GRAPH of steps"]
    P --> S["Step"]
    S -->|"purely cognitive"| CG["Completes via reasoning / memory / knowledge"]
    S -->|"outward action needed"| AP["Action proposal<br/>type · parameters · risk · rationale<br/>full provenance back to the goal"]
    AP --> PB{"Permission boundary<br/>classify risk ONLY"}
    PB -->|"DENY"| REC["Recorded; never executed"]
    PB -->|"REQUIRES_APPROVAL<br/>HIGH risk always lands here"| PA["Pending approval — expires"]
    PA -->|"human approves"| TM
    PA -->|"human rejects"| REC
    PA -->|"TTL elapses"| EX["Expired — still rejectable,<br/>so it can never become unresolvable"]
    PB -->|"ALLOW"| TM["Tool manager"] --> PV["Provider"] --> W[("The world")]
    W --> AR["Action result"]
    AR --> VF{"Verification<br/>re-observe via a DIFFERENT capability"}
    VF -->|"CONTRADICTED"| LOSS["Success flag revoked —<br/>propagates to the goal AND to what the user is told"]
    VF -->|"UNVERIFIED"| HON["Names itself unverified.<br/>Never a silent pass"]
    AR --> FAIL{"Failed?"}
    FAIL -->|"yes"| RV["Diagnose & repair the plan"]
    RV -->|"same failure again"| STRAT["FORCE a strategy change —<br/>never an identical retry"]
    STRAT --> TERM["Bounded exhaustion →<br/>terminal state reconciled across goal and plans"]
    RV --> P
    AR --> LOG[(Event log)]
```

Two rules encoded here that were learned expensively: **an empty plan is a failure, not a
success**, and **a provider's self-report is not evidence.**

---

## 10. One cognition, many surfaces

```mermaid
graph TB
    CORE["<b>Ashi</b> — one long-running local process<br/>cycle · event log · stores · identity<br/><i>the only place cognition happens</i>"]

    subgraph Now["ACTIVE"]
        T["Terminal"]
        BW["Browser workspace + canvas"]
        BV["Browser voice — never spoken to by a human"]
    end
    subgraph Wired["WIRED, unexercised"]
        TV["Terminal voice"]
    end
    subgraph Later["DESIGNED / PLANNED"]
        OV["Desktop overlay"]
        TL["Timeline — the causal log, navigable"]
        MB["Mobile capture — no cognition, no durable state"]
        DX["Continuous duplex voice + barge-in"]
        VIS["Image · screen · camera percepts"]
    end

    T <--> CORE
    BW <--> CORE
    BV <--> CORE
    TV <--> CORE
    OV -.-> CORE
    TL -.-> CORE
    MB -.-> CORE
    DX -.-> CORE
    VIS -.-> CORE
```

**A surface is a view, and a view is not part of who Ashi is.** This is why switching from
voice to text mid-thought continues the same conversation, and why identity survives
replacing every surface.

---

## Keeping them consistent

A diagram that contradicts the prose is worse than no diagram. When a mechanism changes,
the prose changes first, then the diagram — and the status labels inside diagrams 2 and 10
are checked against [what Ashi can do today](../../docs/status/README.md).
