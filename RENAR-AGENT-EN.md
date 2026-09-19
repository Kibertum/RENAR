# RENAR — Operational Standard for the AI Agent

**Version 1.0** | Authors: Vadim Soglaev, Andrey Yumashev | [renar.tech](https://renar.tech) | CC BY-SA 4.0

> **What this file is.** This is a **self-sufficient** working edition of the RENAR standard for an AI agent. It collects everything the agent needs to do requirements engineering by RENAR at 100% — without consulting any other document. Download this file, place it beside the agent, and say: "study this standard and work by it."
>
> Chapters the agent does not need at work (history, maturity metrics, comparisons with other methodologies, substrate details) are omitted; the needed ones are reworked and compressed to an operational minimum. For a formal dispute over the exact wording of "MUST / SHOULD / MAY", the primary source is the full normative corpus at [renar.tech](https://renar.tech); but for everyday work this file is enough.

---

## 0. How to use this file

1. Read sections 1–2: the principle and the artifact map. This is the coordinate system.
2. Before approving, closing a version or presenting, check the preconditions of artifacts (section 4); the order of work is set by the organization's production model, not by this file.
3. Do not break the hard rules (section 12) or the closed lists (section 11) — they are not subject to local extension.
4. Take the recording forms (frontmatter) from section 13 — they are normative.

You are an **executing agent**: you produce and maintain artifacts, but you do not own them and you do not sign. The owner and signatory is a human (section 10).

---

## 1. The core principle: the source of truth is the requirements, not the code

RENAR inverts the usual order: **the source of truth (SoT) about system behavior is the requirements hierarchy**, while code is a derived implementation artifact. Requirements define behavior → the agent produces the implementation from the requirements → tests verify the implementation against the requirements. Not the other way around.

It is **forbidden** to reconstruct the meaning of a requirement from finished code and retroactively fit `SR`/`SPEC` to the implementation (source-of-truth inversion). The exception is a justified bug-fix, where the code is repaired to match the requirement, not the requirement to match the code.

From this follows a **double inversion**: the contractual source (TZ) → SoT (the RENAR description of `BR`/`SR`/`SPEC`/`TR`/`TC`) → code.

---

## 2. Artifacts and hierarchy

```text
                      internal circuit (interpretation)
client TZ ──► ADAPT ──► BR ──► SR ──► SPEC ──► TC ──► QG ──► release
(immutable) (as needed)  └──────► TR ─────┘   (tests)
     │          ▲
     │          │ decided-in
     ▼          │
   ACTZ ────────┘        contractual circuit (commitments)
(protocol, 2 signatures)
     │
     ▼
Effective TZ = TZ + all signed ACTZ ──► AT (acceptance against the contract)
```

| Artifact | What it is | Key point |
|---|---|---|
| **TZ** | The client's contractual input in business language | **Immutable** after registration |
| **ADAPT** | The **internal** interpretation of the TZ (forward interpretation + backward findings) | Created **reactively** (section 5), 0..N per TZ; signed by the **architect only** |
| **ACTZ** | The TZ clarification protocol: decisions put to the client | **Dual** signature, contractual weight (section 5A); 0..N per TZ |
| **Effective TZ** | The initial TZ (with annexes) + all signed ACTZ | The benchmark for delivery and acceptance (section 5A.4) |
| **BR** | Business Requirement: who, what, **why** (business goal) | Has a lifecycle and provenance |
| **SR** | System Requirement: what the system does | `parent` — exactly one BR |
| **TR** | Task Requirement: an implementation task (Goal + acceptance criteria) | Lives in the tracker, not a separate file; references SR/SPEC |
| **SPEC** | Specification — an axis parallel to the requirements | **12 types, closed list** (section 7) |
| **TC** | Test Case as a standalone artifact | A pos/neg pair is mandatory (section 8) |
| **AT** | The acceptance test **against the contract**: derived only from the effective TZ | Isolated generator agent (section 8A) |
| **AR** | The adversarial-review record (evidence) | Not a node of the requirements graph (section 5.2) |

**Two circuits and the boundary between them.** Everything shown to the client and approved is a **commitment** (ACTZ). Everything not shown is **interpretation** (ADAPT). The boundary is drawn **by audience**, not by the content of an entry; the criterion is binary and checkable: the fact "put to the client and signed" either exists or it does not.

**Provenance is mandatory.** Every `BR`/`SR`/`SPEC` has a `source`: either through ADAPT (`source.adapt` + `source.adapt-section`) or directly (`source.tz-section` + `source.adversarial-review-ref`). The `source.tz-section` field is **always** mandatory. An artifact without a source is forbidden.

**A RENAR description is always complete, never incremental.** Before changing the system, a new complete version of the RENAR description is created, and only then does the agent change the implementation. Only the TZ is incremental (delta-TZ); the full picture is reassembled by the agent in whole.

---

## 3. Minimum Viable RENAR (MVR) — seven mandatory statements

An implementation that breaks even one of them **does not conform** to RENAR. The list is closed.

1. **MVR-1 — SoT inversion.** Requirements are the source of truth; reverse-engineering behavior from code into SR without a bug-fix is forbidden (section 1).
2. **MVR-2 — substrate V1–V6.** The substrate MUST provide: immutable history (V1), atomic change unit (V2), diff & review (V3), branching/change-set (V4), cross-substrate version pin (V5), author + timestamp (V6).
3. **MVR-3 — reactive stage-independent ADAPT (0..N per TZ).** ADAPT is created if and only if converting the TZ → requirements at any stage produces a gap between the client's language and the language of the requirements (section 5). When a gap exists, the ADAPT MUST reach `approved` with the **architect's signature**; the client's decisions are recorded in **signed ACTZ** (section 5A), and the benchmark for delivery and acceptance is the **effective TZ** = the initial TZ + all signed ACTZ.
4. **MVR-4 — 12 SPEC types, closed list** (section 7).
5. **MVR-5 — pos/neg TC pairing** for every normative statement (section 8).
6. **MVR-6 — closed list of Quality Gates** QG-0..QG-2 as `required`; QG-3/QG-4 — `declared` or `absent` (section 9).
7. **MVR-7 — conformance manifest** with simultaneous `renar-version` + `senar-version` + `level` + confirmation of the mandatory clauses (section 14).

---

## 4. Preconditions of artifacts

The standard **does not set the order of work** — that is set by your organization's production model (simplified or fuller, with artifacts and rules of its own — permitted as `declared-stricter`, §1.7.2). What is normative are the **preconditions**: what MUST exist, and in what state, before an artifact is approved, a version is closed or the product is presented. Check the preconditions rather than reproduce steps.

### 4.1 What MUST exist before what

| Artifact / event | Preconditions | Who approves |
|---|---|---|
| TZ (`TZ-YYYY-NNN`) | registered as an immutable document; the client's signature (author + timestamp) | the client |
| Adversarial review of the TZ (AR) | the TZ is registered; the reviewer is a separate agent on a **different model**; the verdict "findings present" / "no findings, no clarifications" is recorded; the reviewer's silence is not a verdict | the Architect records the verdict |
| ADAPT | the verdict "findings present" (otherwise no ADAPT is created); forward interpretation + findings (section 5) | **the Architect's signature** |
| ACTZ | a finding that needs the client's word; draft decisions in the language of obligations (section 5A) | **dual signature**: client + vendor; the finding gets `decided-in` |
| BR / SR / SPEC (a set version) | a `source` on each: a section of an approved ADAPT or `source.tz-section` with the verdict "no findings"; SR → SPEC via `constrained-by[]`; structural completeness (section 7); a pair of TC norms per normative statement (`coverage-presence`) | QG-0 — the Architect approves the set version |
| TR (task) | the parent SR is in an approved version; Goal + acceptance criteria; `implements-spec[]` when implementing a SPEC | the task's QG-0 |
| TC implementation | the TC norm is in an approved version; `automation.set-version` matches; the author is **not** the agent writing the code (section 8.2); a red history | QG-1 — admission to runs |
| Verification of a version on a product version | all TCs of the version green at `last-run.set-version = N.M`; ≥ 1 negative; spot check | QG-2 |
| AT | the effective TZ (TZ + signed ACTZ); an isolated agent on a different model without access to internal artifacts (section 8A) | the release gate: all AT `passing` — the Architect admits the product to trials |
| Acceptance | all AT `passing`; with QG-4 declared — the measured business outcome | the Stakeholder |

The adversarial review is **stage-independent**: a question rooted in the TZ that surfaces at decomposition or later produces a new ADAPT at the stage where it surfaced (section 5); ACTZ likewise — a late protocol is no worse than an early one.

### 4.2 Delta-TZ (incremental change) — preconditions

- The delta-TZ (`TZ-YYYY-NNN-delta-N`) is registered as a new immutable document and signed by the client.
- The adversarial review of the delta-TZ is performed, the verdict is recorded.
- "Findings present" → a delta-ADAPT (`ADAPT-NNN-delta-N`, `parent-adapt`) signed by the architect; the client's decisions — in new ACTZ with a dual signature. "No findings" → no delta-ADAPT **is created**; the affected artifacts reference `source.tz-section: TZ-…-delta-N` directly.
- The affected area of the description — as a new set version; TC implementations are re-created against the new norm.
- The `AT` are regenerated from the new revision of the effective TZ (section 8A.3).

**Example of a trivial delta** (rename the field `username` → `email` on a form): the term is unambiguous, the scope is clear, there are no questions for the client → verdict "no findings" → no delta-ADAPT is created; the affected `SR` gets `source.tz-section: TZ-...-delta-1 §1`, the `parent` BR and `constrained-by[]` do not change; the `TC` are regenerated.

### 4.3 Points of human approval (delegation to the agent)

The agent performs most steps itself, but **does not approve** — delegation has fixed points where a human is required:

| Step | What the agent does | What the human approves |
|---|---|---|
| Adversarial review | A critic agent (a different model) issues the verdict | The Architect records the verdict as evidence |
| ADAPT | The agent prepares a draft (forward interpretation + findings) | **The Architect's signature** (QG-3). There is **no** client signature here |
| ACTZ | The agent prepares the draft decisions; the Architect puts them to the client | **Dual signature:** client + vendor |
| QG-0 Approval | The agent creates `BR`/`SR`/`SPEC` with provenance | The Architect approves the transition to `approved` |
| `implementation-originated` | The agent marks the internal technical detail it added | A human supervisor explicitly approves the addition (section 6.3) |
| Release acceptance gate | The agent presents all `AT` in `passing` | The Architect admits the product to the trials |
| QG-4 Acceptance | The agent presents the result | The Stakeholder accepts the business outcome |

The agent never signs and never declares itself the owner of an artifact (section 10). If human approval is not obtained, the artifact stays in `draft` and transitions are blocked by the substrate.

---

## 5. ADAPT in full

### 5.1 Why

A TZ is written in business language and signed as a contract — it cannot be edited. Turning it into precise requirements directly often does not work out: the TZ is silent about something important, contradicts itself, or uses a term with a double meaning. Silently filling in for the client is also not allowed — that is the leakage of someone else's guesswork into the requirements. ADAPT is the bridge across this gap: a **forward interpretation** ("we understood section §4.2 of the TZ this way") and **backward findings** ("§4.3 sets no deadline — please clarify").

**ADAPT is an internal artifact.** Its audience is the engineer, the AI agent, the adversarial reviewer, and the verifier. The client does not see an ADAPT and **does not sign** it. Questions go to the client and come back as decisions **not through ADAPT but through ACTZ** (section 5A) — a separate document of the contractual circuit.

### 5.2 When to create one and when not (reactivity)

An ADAPT is created **if and only if** at least one holds:

- a **backward finding** in one of the 7 categories is discovered (section 5.3);
- a **term** needs clarification (no unambiguous engineering reading);
- the **scope** of work needs clarification.

If the review verdict is "no findings, no clarifications", no ADAPT is created: `BR`/`SR`/`SPEC` reference the TZ directly via `source.tz-section`, and the verdict is recorded as evidence (`source.adversarial-review-ref`).

**AR — the adversarial-review record.** In **both** outcomes the review result is issued as an AR (`AR-NNN`) — an evidence record, immutable once issued. Mandatory fields: `tz-ref`, `trigger-stage`, `reviewer` (the model MUST differ from yours), `primary` (your model at the moment of issuance — the second operand of the condition; without it independence cannot be re-checked later), `verdict` (`findings-present` | `no-findings`), `produces-adapt[]` (non-empty when `findings-present`), `status` (`draft` -> `issued` -> `superseded`), signature. `source.adversarial-review-ref` MAY reference **only** an AR in status `issued` with the `no-findings` verdict. AR is not a requirement and not a graph node: it is not decomposed and not covered by TC.

### 5.3 The seven backward-finding categories (closed list)

| ID | Category | What is recorded |
|---|---|---|
| `contradiction` | Contradiction | Internal contradictions of the TZ (§A vs §B) |
| `gap` | Gap | The TZ is silent about something without which implementation is impossible |
| `hidden-assumption` | Hidden assumption | An engineer's assumption that may be wrong |
| `feasibility` | Feasibility | Technically infeasible or disproportionately expensive |
| `regulatory` | Regulatory | Touches legislation / compliance |
| `terminology` | Terminology | An unclear term with several meanings |
| `scope` | Scope | An unclear boundary of work |

Each entry has a stable `B-NNN`, immutable after creation.

### 5.4 Stage independence and multiplicity (0..N)

The ADAPT trigger is **not tied to TZ import**. A question for the client rooted in the TZ may surface later — during `BR → SR → SPEC` decomposition or `TC` development. Then a **new** ADAPT is created at that stage (the `trigger-stage` field). A single TZ has **zero or more** root ADAPTs — this is the normal case.

If the uncertainty is rooted **in the decomposition** (and not in the TZ), it is resolved by clarifying `SR`/`SPEC` **without** an ADAPT. An ADAPT arises only when the root is in the language or intent of the TZ.

### 5.5 Entry lifecycle and approval

Every backward finding goes through: `open → asked-to-client → answered → resolved → frozen` (with a possible return `revised → asked-to-client`). The meaning of the statuses is tied to ACTZ:

| Finding status | What it means |
|---|---|
| `open` | Recorded; not put to the client |
| `asked-to-client` | Put to the client **as part of an ACTZ** (`status: sent`); awaiting signature |
| `answered` | The client made a decision; it is recorded as a clause of a **signed** ACTZ (`status: signed`) |
| `resolved` | The decision is integrated into the forward interpretation; the entry carries `decided-in: ACTZ-NNN §M` |
| `revised` | The decision is vague; the repeat question goes out as a **new** ACTZ; return to `asked-to-client` |
| `frozen` | After ADAPT approval — no changes are possible |

**`decided-in` is mandatory.** Every finding that needs a client decision MUST carry `decided-in: ACTZ-NNN §M` — a reference to a clause of a **signed** ACTZ. A reference to an unsigned ACTZ (`draft`/`sent`), as well as a dangling reference, is **fatal**. Approval of an ADAPT (QG-3) is forbidden while any entry is in `open`/`asked-to-client`/`answered`/`revised` — all MUST be `resolved`, that is, each MUST have a signed client decision.

**The reverse direction is mandatory too.** A decision taken by the client on their own initiative (an ACTZ with no parent finding) MUST be **reflected in the ADAPT**. A signed ACTZ not reflected in any ADAPT is **fatal**: it is a commitment absent from the requirements.

**The ADAPT signature is the architect's only.** An ADAPT moves `answered → approved` on the Architect's signature: all findings are worked through (each `resolved` and carrying `decided-in` to a signed ACTZ), and the forward interpretation is technically feasible. **There is no client signature on an ADAPT** — client commitments are carried by ACTZ. After approval an ADAPT is **immutable**.

### 5.6 Changes to an approved ADAPT — three mechanisms

A frozen ADAPT is not edited. Changes happen only by adding a new artifact via one of three paths:

| Mechanism | When | Signature |
|---|---|---|
| **delta-ADAPT** | a delta-TZ arrived (the contract changed) | the Architect signs the ADAPT; the client's decisions go into new ACTZ (dual signature) |
| **errata-ADAPT** | the prior interpretation was **wrong** | the Architect. If the correction touches a decision from a signed ACTZ, a **new ACTZ** with a dual signature is mandatory |
| **superseding ADAPT** | the prior decision was **correct** but later refuted by new requirements | the Architect. If the superseded decision had a contractual outcome (a clause of a signed ACTZ), the cancellation is formalized by a **new ACTZ** with a dual signature |

**Supersession.** A new `ADAPT-NNN` with the field `supersedes: ADAPT-MMM` and a mandatory `supersession-rationale` (a reference to the conflicting `BR`/`SR`/`SPEC` and its source). The superseded ADAPT moves to the terminal state **`superseded`** (distinct from `obsolete`), stays immutable, and is kept for audit; it gets `superseded-by: ADAPT-NNN`. All derivatives with `source.adapt: ADAPT-MMM` MUST be repointed to the superseding ADAPT or re-derived — a **dangling reference to a `superseded` is forbidden**. There is no separate gate — it goes through the same QG-3.

### 5.7 AI and adversarial review

The agent creates an ADAPT **draft** automatically (forward interpretation by section, an attempt to find contradictions/gaps/unclear terms, an initial term mapping). This is a starting point, not the final. A separate critic agent on a **different model** looks for what was missed. The client **does not talk to the AI directly**: the Architect aggregates and rephrases the questions; the client is presented with a prepared list of decisions (an ACTZ), not with the raw output of an agent.

---

## 5A. ACTZ — the TZ clarification protocol

### 5A.1 What it is and how it differs from ADAPT

**ACTZ** (`ACTZ-NNN`, Agreed Clarification of TZ; in contract practice — the "**TZ Clarification Protocol No. N**") is an artifact of the **contractual circuit**: a batch of questions, proposals, and decisions put to the client and signed by **both** parties.

| | **ADAPT** — interpretation | **ACTZ** — commitment |
|---|---|---|
| Content | Translation of the TZ into engineering language, term mapping, completed scenarios, backward findings | Questions, proposals, and decisions for the client. The language of commitments ("the button is called X"), not of interpretation |
| Audience | The engineer, the AI agent, the reviewer, the verifier | The client and the parties to the contract |
| Signature | **The Architect only** | **Dual**: client + vendor |
| Weight | The internal circuit | **Contractual** |

The criterion is binary: "put to the client and signed" either holds or it does not. The argument over the materiality of a change ("is this a clarification or already a change?") disappears by construction — do not reason about it; look for a signed ACTZ.

### 5A.2 Origin and multiplicity

Two normal sources:

1. **From a backward finding.** A finding that needs a client decision is put into an ACTZ; after signing it gets `decided-in: ACTZ-NNN §M`.
2. **On the client's initiative, with no finding.** The client proposes a decision themselves. Such an ACTZ references only the TZ; it has no parent finding. After signing, the decision **MUST** be reflected in the ADAPT.

Cardinality: **TZ : ACTZ = 1 : 0..N** (zero protocols is lawful — a conversion with no questions produces no documents); **ADAPT : ACTZ = 1 : 1..N** (the findings of one ADAPT are closed by several protocols across rounds).

ACTZ is **stage-independent**: a late protocol — from working through remarks after a demonstration — is the normal case, not an exception.

### 5A.3 Lifecycle

```text
draft → sent → signed → superseded
```

| Status | What it means |
|---|---|
| `draft` | Being prepared; not put to the client. Referencing it from `decided-in` is **forbidden** |
| `sent` | Put to the client, awaiting signature |
| `signed` | Signed by both parties; **immutable**, carries contractual weight |
| `superseded` | A later signed ACTZ cancelled the decision; a terminal state, kept for audit |

A signed ACTZ is not edited. A correction happens only through a new ACTZ that cancels the prior decision (`superseded-by`).

**Annexes to the TZ** (mapping tables, reference lists, mock-ups) are an integral part of the TZ and are not separately addressable entities. Clarifying an annex is formalized as a **new version of the annex**, attached to an ACTZ (`annexes[]`) and approved by the same dual signature.

### 5A.4 The effective TZ — the benchmark for delivery and acceptance

> **Effective TZ = the initial TZ (with annexes) + all signed ACTZ.**
> On divergence, the **later signed** document prevails.

The acceptance tests `AT` (section 8A) are derived against the effective TZ, and the acceptance report is built from it.

**ADAPT is not part of the acceptance benchmark.** The client answers only for what they signed and understood; the internal interpretation is taken out of the contractual circuit entirely.

---

## 6. Requirements hierarchy and provenance

- **BR (Business Requirement)** — captures the business goal of a group of related SR: who, what, **why**. When an SR changes, the link to the business need is visible.
- **SR (System Requirement)** — what the system does; `parent` — exactly one BR (multiple parents are forbidden).
- **TR (Task Requirement)** — an implementation task in the tracker: Goal + acceptance criteria. References `SR`/`SPEC`, **not** ADAPT directly — all interpretations are already in SR/SPEC.

### 6.1 System levels and references to higher requirements

Every artifact carries a `scope` with a level. There are three levels:

| Level | What it is | Who can be at the level |
|---|---|---|
| `system` | The information system as a whole | `BR`, `SR`, `TR` |
| `subsystem` | A technical division: a component, service, team | `BR` (if the subsystem is a standalone product), `SR`, `TR` |
| `module` | A part of a subsystem fit for one task | `SR`, `TR` (but **not** `BR` — a business goal is not formulated at the module level) |

The base decomposition `BR → SR → TR` works within a single level. For composite systems the levels link **upward**:

- **A subsystem as a technical division** (not a standalone product): has no `BR` of its own — it inherits the system's business goal; the subsystem's `SR` has `parent` = the system's `BR`.
- **A subsystem as a standalone product** (its own business owner): has its own root `BR`. That `BR` declares `implements[]` — a reference to the parent system's `BR` that it elaborates. The presence of `implements[]` is **mandatory** when the condition holds: `BR.level = subsystem` AND the parent system has ≥ 1 `BR` in status `approved`+. Omitting the edge is admissible **only** when the parent system is a container without BRs of its own; the justification is then recorded in the “Context” section. The `implements`-edge validation control point is required of the substrate and checks the **presence** of the edge when the condition holds, not merely the validity of an existing one. Without it the link between the subsystem tree and the system tree is lost, and the traceability of "why this subsystem exists" becomes irrecoverable.

The obligation to reference upward lies on the subsystem's `BR` (via `implements[]`), not on the parent: the system does not know in advance which subsystems will implement it — they declare their affiliation. The `implemented-by[]` field on the parent `BR` is assembled by the substrate automatically from the back-references.

**implements-edge (subsystem → system).** `implements[]` is an array of `id + scope.system` pointing at the parent system's `BR`. It is a typed cross-level edge (**not** a parent): cardinality 0..N, acyclic, target in status `approved`+. One subsystem `BR` may elaborate several system `BR`. The ban on multiple `parent`s does **not** apply to this edge — it is of a separate type.

### 6.1a Reusable component (§6.14)

A component (a library, a platform) is **a system in its own tree** with its own BR and set, not a fourth level. The client side: the component's Product Owner (path B) **or** assumptions `assumptions[]` (`A-NN`) declared in the root BR, which every consumer confirms by name on connection (path C; the component's conformance is conditional, the consumer claims it). SR / SPEC statements of a component carry `applies-to: self | consumer`; a `consumer` obligation is covered at every consumer by a static TC with a red history on a deliberately violating configuration. A consumer connects a component through the `uses[]` edge in its root BR: `component`, `set-version` (the component's set version, mandatory), `assumptions-confirmed[]`. Rules: read-only foreign tree; acyclicity; `constrained-by[]` stays local; the transitive closure of obligations; **stop** on an incompatibility of the `consumer` statements of two components: do not approve the version until a person has decided; the decision — by changing the connection or by a backward finding; the removal of a component artifact — a warning, not a cascade.

### 6.2 Artifact storage layout (informative, substrate-dependent)

> This section is **informative**: RENAR does not normalize the file layout — the substrate may be a file system, a database, a tracker, or a combination. Below is an **example** layout on a file-based substrate; an organization is free to arrange storage differently, as long as the `parent` / `source` / `implements[]` / `constrained-by[]` links and the machine-readability of the graph are preserved.

A convenient reference point for a file-based substrate is a layout by systems and levels, with TC next to the verified artifact:

```text
<requirements-root>/
├── tz/                          # immutable TZ and delta-TZ
│   ├── TZ-2026-001.md
│   └── TZ-2026-001-delta-1.md
├── adapt/                       # ADAPT (0..N per TZ), immutable after frozen
│   └── ADAPT-001.md
├── <system>/
│   ├── br/                      # BR at the system level
│   │   └── BR-01.md
│   ├── sr/
│   │   └── SR-01.md
│   ├── spec/                    # SPEC of all 12 types
│   │   ├── SPEC-API-01.md
│   │   └── SPEC-DATA-01.md
│   ├── tc/                      # TC next to its scope; pos/neg pairs together
│   │   ├── TC-01.md
│   │   └── TC-01-neg.md
│   └── <subsystem>/             # a standalone product — its own tree
│       ├── br/                  # subsystem BR with implements[] to system BR
│       │   └── BR-01.md
│       ├── sr/
│       └── tc/
└── RENAR-CONFORMANCE.yaml       # conformance manifest (section 14)
```

There is no TR here: a TR lives in the task tracker, not as a separate file (section 2). File names = the artifact `id` — this gives a stable reference that survives a title rename.

**Trace chain** (read-side) has two valid variants: through ADAPT (`source.adapt`) or directly from the TZ (`source.tz-section` + `source.adversarial-review-ref`). Both are machine-readable. A dangling reference to a `superseded` ADAPT makes the chain invalid.

### 6.3 A requirement originated by the implementation (`source: implementation-originated`)

While implementing a task you routinely add **internal technical details** that are not in the requirements: a defensive input check, an edge-case handler, logging. Demanding an ACTZ with a client signature for every such detail is an absurd ceremony — and it is exactly what pushes people toward silently fitting the requirements after the fact. Hence a **narrow** lawful class: `source: implementation-originated`.

| What | Where |
|---|---|
| **Behavior observable by the client** (a screen, a field, a message, a deadline, a format, the scope of delivery) | **Only** through the contractual circuit: ADAPT + ACTZ with signatures. There are no relaxations |
| **An internal technical detail with no client-observable behavior** | `source: implementation-originated` is allowed |

The boundary is **observability by the client**, not the size of the change. Doubt is resolved in favor of the contractual circuit.

The mandatory harness for such a requirement (all five items):

1. **Provenance**: a reference to the implementation change unit that produced it, and a justification of why it is needed.
2. **Human approval**: an explicit approval by a supervisor. The agent cannot legalize its own addition.
3. **A TC before the merge**: the covering test exists and passes before the change is included.
4. **A killed mutant**: such a TC cannot have a red history (the test is written after the code and is born green) — its bite is proved by a **mutation check** (`mutation-check.mutants-killed ≥ 1`). Without a killed mutant the TC is not evidence (section 8.2).
5. **A counter in the drift metrics**: a rising share of `implementation-originated` is a signal that the requirements process is leaking, and it MUST be visible.

What remains forbidden: silently fitting an existing `SR` to the behavior of the code (a breach of MVR-1) and using the class for client-observable behavior (non-conformance: a commitment to the client cannot arise from code).

---

## 7. Specifications (SPEC) — twelve types, closed list

A SPEC type MUST belong to the list of twelve. Creating new types locally is forbidden.

| Type | Purpose |
|---|---|
| `SPEC-ARCH` | Architecture: components, boundaries, decisions |
| `SPEC-API` | Interface contracts (endpoints, methods, formats) |
| `SPEC-DATA` | Data models, schemas, migrations, classification |
| `SPEC-INT` | Integrations with external systems |
| `SPEC-PROC` | Processes, workflow, orchestration |
| `SPEC-UI` | User interface, screens, behavior |
| `SPEC-AI` | AI components: model, risk class, judge isolation |
| `SPEC-SEC` | Security: threats, controls, requirements |
| `SPEC-OPS` | Operations: deployment, monitoring, SLO |
| `SPEC-TEST` | Test environments and data: topology, emulators and sandboxes of external systems, run configuration, datasets, anonymization |
| `SPEC-DOC` | Deliverable project documentation: composition, structure of each document, verifiable requirements for it |
| `SPEC-UC` | Use case: an end-to-end path through several SR / SPEC; `role: human` (through the interface, `ux` TC coverage) or `agent` (through the API, `system` / `contract`); every step with a `ref` to the statement it touches (v1.1, §8.5.12) |

**Coverage completeness (§8.4.1).** The mandatory body of any of the twelve types MUST describe its subject exhaustively. Restricting coverage by a subjective selection criterion is prohibited — regardless of vocabulary. Do not write "key screens", "critical components", "to a sufficient extent": whatever did not enter the SPEC as insignificant has no source of truth, and guessing at what is undescribed is prohibited (§2.3.3). The same words are legitimate where they name a property of the subject (the severity class of alerts in SPEC-OPS).

**Mandatory body sections of any SPEC (§8.4.1).** Six, regardless of type:

1. **Purpose** — 1–3 paragraphs.
2. **Scope** — what is in, what is out. This is the section that draws the coverage boundary: the "completeness" of the paragraph above means completeness **inside the declared Scope**, not a description of the whole system in one SPEC.
3. **Type-specific sections** — the table below.
4. **Link to requirements** — which SR / BR reference it.
5. **Link to other SPEC** — `depends-on[]`.
6. **Verification** — which TC verify this SPEC.

**Mandatory body per type (§8.5).** Omitting any item is an unfilled mandatory body:

A set of the `system` level contains non-empty `SPEC-ARCH` and `SPEC-SEC` regardless of the TZ (§8.3): the silence of the TZ about security is a gap the description closes, not inherits; their absence — the version is not approved (§10.7.3).

| Type | Mandatory body |
|---|---|
| `SPEC-ARCH` | system context (C4 L1), containers (C4 L2), components (C4 L3) for **every** L2 container, quality attributes (latency / throughput / availability), ADR log, the list of the system's screens (code / name / source from the description; a system without an interface records that explicitly) |
| `SPEC-API` | endpoints / operations with payload / response / errors, versioning rules (breaking vs non-breaking), error model, authn/authz reference to SPEC-SEC, rate limits, 2–3 example requests per endpoint |
| `SPEC-DATA` | domain entities, ERD, entity fields (type / constraints / indices / defaults), relationships (FK / cardinality / cascade), PII and sensitive-data classification + encryption at-rest + retention, migration approach, index strategy |
| `SPEC-INT` | integrated systems, exchange contract, failure modes + retry strategy, idempotency + dedup, security between systems, observability (correlation IDs) |
| `SPEC-PROC` | process diagram, states and transitions, participants and their roles, happy path, alternative scenarios and exceptions, timeouts and compensation (for saga), SLA |
| `SPEC-UI` | overall interface structure, **all screens in the declared Scope** under codes from the SPEC-ARCH list (`screens[]`), user journeys without technical details covering **every** user action available through the interface, cross-cutting elements (access rights / notifications / error / empty states), tone and style, accessibility, i18n |
| `SPEC-AI` | AI component architecture (pipeline / orchestration / fallback), model card (capabilities / limits / known failure modes), context strategy, eval strategy with judge ≠ production isolation, cost management, hallucination mitigation, adversarial aspects |
| `SPEC-SEC` | auth model (authn flow / authz rules), data classification with protection, threat model (STRIDE table with a mitigation for **each** threat), secrets management, audit (what is logged / retention / access), encryption (at-rest / in-transit / key management), compliance mapping with references to specific clauses |
| `SPEC-OPS` | environments, deployment process (CI/CD pipeline / gating / rollout strategy), SLO (availability / latency / error budget), observability, alerting (critical alerts / escalation), runbook, capacity planning, disaster recovery |
| `SPEC-TEST` | bench topology; the list of external systems and the way **each** is represented (real / sandbox / emulator — with rationale); datasets on both sides of **every** integration; reconciliation rules (**where the expected result lives**, if it is not in our system); run configuration; the data policy (volume, anonymization, retention) |
| `SPEC-DOC` | the composition of the delivery (which documents and for which audience); the structure of each document; the requirements on it (completeness across the roles and scenarios declared in the requirements; version currency; language and format); the readiness criteria |
| `SPEC-UC` | the goal of the scenario and its executor (role, persona); preconditions; the main path — numbered steps, each with a `ref` to the SR / SPEC statement it touches (§8.5.12.1); alternative and negative branches with the same reference; postconditions; covering TCs. Frontmatter: `role`, `persona`, `covers[]` (≥ 2), `entry`, `steps[]` (`n`, `action`, `ref` mandatory, `expects`) |

**Step reference to a statement (§8.5.12.1).** Every scenario step — in SPEC-UC, in a SPEC-UI user journey and in the happy path / alternative scenarios of SPEC-PROC — MUST carry a `ref` to the SR / SPEC statement it touches; a step without a reference violates structural completeness, the set version is not approved.

**The screen list and completeness across the system (§8.5.6.1).** A screen is a state of the interface in which the user performs an action or observes a result, addressable independently of the technique (a route, a page, a modal window, a wizard step). SPEC-ARCH keeps the screen list from the description (the SRs and the SPEC-ARCH itself), not from the implementation: a screen in the code without an entry in the list is a source-of-truth drift and a backward finding. Every screen of the list is covered by at least one SPEC-UI of the same set version (`screens[]`); an uncovered screen or a code outside the list — the set version is not approved (§10.7.2).

A SPEC is an axis parallel to the requirements. The link to a requirement is a typed `constrained-by[]` edge (an SR is constrained by specifications). The `depends-on` graph between SPECs MUST be acyclic (a DAG).

---

## 8. Test cases (TC)

A TC is a standalone artifact, not a line in code: the **norm** (frontmatter + body) is an item of the description set with no status of its own, the **implementation** (the code at `automation.location`) has its own cycle (not admitted → admitted → `pass` / `fail`; stale). For every normative statement of a verifiable artifact that describes observable behaviour, a positive + negative pair of TC norms **MUST** exist in the same set version — a version without the pair is not approved (QG-0 of the set, the `coverage-presence` enforcement point). The exception is a statement that itself describes a negative invariant.

- `verifies[]` points at the verified artifacts of the same set version; the reverse link `verified-by` is symmetric.
- `automation.set-version` — the set version the implementation was written against; `last-run.set-version` — the one the run was performed on. If the norm is changed in a later version, the implementation is stale and is re-created against the new norm (staleness detection — by `set-version`).
- An automated TC (`automation.status: automated`) MUST have a non-empty `automation.location` and a **mandatory** `automation.kind` (`dynamic` | `static`; `static` — when the runner is a static analyzer).
- For `SPEC-AI`: the judge model MUST differ in vendor from the production model (evaluation isolation, P7).
- Optional task binding: `verifies-tr` + `verifies-claims[]` — the test narrows to the scope of a TR (a subset of the parent SR's statements). Coverage is still counted from the artifact's statements, not from tasks.

TC implementation states: not admitted → admitted (QG-1) → `pass` / `fail`; stale when `automation.set-version` ≠ the version in which the norm was changed. Only the runner-actor writes `last-run`.

### 8.1 Six TC types (closed list)

```text
tc-type ∈ { business, ux, system, contract, eval, security }
```

| Type | What it checks | Applies to |
|---|---|---|
| `business` | Whether the business goal is achieved | BR |
| `ux` | Whether the UX matches the stated experience | SPEC-UI |
| `system` | Whether the system behaves as described | SR, SPEC-PROC, SPEC-ARCH |
| `contract` | Whether the interface contract holds | SPEC-API, SPEC-INT, SPEC-DATA |
| `eval` | Whether AI-component quality is reached | SPEC-AI |
| `security` | Whether the security invariants hold | SPEC-SEC |

**The `acceptance` type no longer exists** — it was renamed to `business`. There are two acceptances, on opposite sides of the circuit split: a `business` TC checks the business goal of a BR (the internal circuit), while `AT` checks conformance to the contract (section 8A). One name for two subjects was a direct source of confusion.

**Type of the covering TC for the interface (§9.8, mandatory).** Every SPEC-UI statement describing an action available through the interface, and every SPEC-UC step with `role: human`, is covered by at least one TC `tc-type: ux`; a `system` TC bypassing the interface **does not** close such a statement. A SPEC-UC step with `role: agent` is covered by `system` / `contract` by the channel of the step. Mandatory TC kinds per SPEC type (§9.8): SPEC-UC — `ux` per step + journey E2E with `role: human`; `system` / `contract` per step with `role: agent`.

### 8.2 Authorship isolation (P8) and red history (P9)

**P8 — isolation of test authorship.** A test created while writing the code will be fitted **to the code**, not to the requirement: it honestly checks what was written, not what was required. Isolation is mandatory on three axes:

| Axis | Requirement |
|---|---|
| **Time** | The TC norm is approved in a set version and the implementation is admitted (QG-1) **before** the implementation of the behaviour it checks begins. A TR does not enter work until the implementations of its linked TCs are admitted |
| **Author** | The agent writing the test **is not** the agent writing the implementation (different sessions or models). The TC's provenance is cross-checked against the provenance of the implementation change unit |
| **Change** | Editing the `## Pass criterion` / `## Fail criterion` of a frozen test happens only through `[test-spec-change]` with an engineer's approval |

**P9 — red history.**

> **A test never observed red is not evidence.**

- The **fixing run** happens **before** the implementation: the test MUST be **red** — the behavior under check does not exist yet. The "red → green" transition is recorded by the substrate and is the condition for counting the TC at QG-2.
- A green result on the fixing run is a **signal to investigate**, not a success: either the test checks nothing, or the functionality already exists. Both cases require an engineer's analysis.
- **Inheritance**: for tasks that do not change observable behavior (refactoring, a migration with no contract change) the red history is **inherited** from the TC of the same statement (`red-history.inherited-from`).
- **Exception — `implementation-originated`** (section 6.3): such a test is born green and cannot have a red history. Compensation is mandatory: a **killed mutant** (`mutation-check.mutants-killed ≥ 1`).

**Why P9 if P8 exists.** Isolation guarantees that the test was written before the code and not by whoever writes the code. But it does not guarantee that the test **checks anything**: an empty test, honestly written by an isolated agent, passes all three axes of P8 and is green from birth. Only the red history closes that hole.

**The machine signal of a weakened norm.** If a previously green system **fails after a test is edited**, the norm has changed in fact, however the edit was classified. The trigger is blocking: the edit must be carried out as a change of the norm (`[test-spec-change]` + an engineer's approval) or reverted.

---

## 8B. MW — manual walkthrough record (§9.20)

Automated checking is blind to undescribed behaviour; a person's manual walkthrough of a scenario (SPEC-UC / SPEC-UI user journey) is the second channel for detecting a divergence. The outcome is a record `MW-NN`, the evidence class (not a graph node, extends no closed list). Rules:

1. An MW **does not replace** any TC, **is not part** of the QG-2 evidence base and is not grounds for a verification entry of a version. Complementary manual checking is permitted, substitutive — prohibited (§14.7).
2. Every finding carries `route`: behaviour not observable by the client → a candidate `implementation-originated` (section 6.3); observable → the contractual contour (ADAPT / ACTZ). A finding without a route is a violation.
3. The walkthrough is performed by a person other than the implementation author (P8).
4. A permanently manual run of `ux` TCs is not provided for: perceptual assessment by a person — as an MW record, the TC stays automated.
5. Records without a single finding in a row are a sign of a formal walkthrough (accounted in §12.3.11).

## 8A. AT — the acceptance test of the contractual circuit

### 8A.1 Why

TC traceability is closed through the **interpretation**: `TC → SR → ADAPT → TZ`. Hence a class of defects that the TC level cannot catch **in principle**: if the interpretation of the TZ is wrong, all TC may be `passing` — the system perfectly matches a **wrong** interpretation — and still fail acceptance at the client.

**AT** (`AT-NN`) is derived **exclusively** from the **effective TZ** (section 5A.4) and checks conformance to the **contract**, not to the interpretation.

### 8A.2 Isolation as the generation mechanism

An AT is created by an **isolated agent**: the input is **only the effective TZ**. Access to ADAPT, BR / SR / SPEC, TC, and the code is **forbidden**. The model of the AT generator agent MUST differ from the model of the primary agent.

Isolation here is not hygiene but the **essence of the mechanism**: an AT is a simulation of the client's view. An agent that has seen the ADAPT or the SR will reproduce the **same** interpretation — and the AT will start confirming it instead of the contract. The acceptance level collapses into the verification level, and the error of interpretation receives a **false confirmation of conformance**. A breach of isolation is **fatal**.

### 8A.3 Regeneration before the trials

> **AT are regenerated before every trial run** from the **current** revision of the effective TZ.

Every signed ACTZ produces a new revision of the effective TZ. AT derived at planning time go stale by the time of the trials — without regeneration, at the end of a long engagement the system is checked against a year-old contract. A stale trial program is **not admitted** to the trials: the gate compares each AT's `tz-version` with the current revision and blocks a divergence. Regeneration is cheap — the same isolated agent performs it.

### 8A.4 Failure routing (diagnostics)

| AT | TC | Diagnosis | What gets fixed |
|---|---|---|---|
| ✗ | ✓ | **Error of interpretation**: the system matches the ADAPT but not the contract | The ADAPT / ACTZ, then the derived requirements |
| ✗ | ✗ | An implementation defect | The code |
| ✓ | ✗ | The requirement is stricter than the contract, or the TC is wrong | Investigate: an excess requirement or a defective TC |
| ✓ | ✓ | Normal | — |

The first row is the reason AT exists: this defect is not detected by any TC.

### 8A.5 Mandatory fields and body

- `verifies[]` — **only** `TZ §N` and `ACTZ-NNN §M`. A reference to an internal artifact (BR / SR / SPEC / TC) is **fatal**.
- `tz-version` — the revision of the effective TZ the AT was derived from.
- `generator` — the provenance of the isolated agent: vendor, model, confirmation that there was no access to the internal circuit.
- `negative` — pos/neg pairing as for TC; `status` and `automation` (including `automation.kind`) as for TC; `last-run` is written by the runner.
- `environment-ref` — the environment and dataset of the acceptance trials. The field is filled in **not by the generator**: the isolated agent does not see internal artifacts. The architect or the runner attaches the environment **after** generation — the trials stay reproducible and isolation is not broken.
- **`tz_text`** — a verbatim quote of the checked clause of the effective TZ next to the steps — a **mandatory** body section of an AT. The quote ends the argument at acceptance: what is presented is the contract clause itself, not a paraphrase.

**The acceptance report** `ACCEPTANCE.md` is auto-generated: AT coverage across the sections of the effective TZ and the clauses of the signed ACTZ. The product is not presented for delivery until all AT are `passing` (section 9).

---

## 8C. Description language (§15)

The normative sections of artifacts — "Need", "Success criteria", "Constraints" of a BR; "Requirement", "Behaviour", "Constraints" of an SR; the type-specific sections of a SPEC; the Pass / Fail criteria, preconditions and steps of a TC; the clauses of an ACTZ; the forward interpretation of an ADAPT — are written in a controlled dialect. Mandatory from `RENAR-2`; the forms are a recommendation.

**Operators — a closed list, exactly one per statement, synonyms prohibited:** `MUST` · `MUST NOT` · `SHOULD` (a deviation is justified in the same artifact) · `SHOULD NOT` · `MAY`. "Shall", "needs to", "is required to", "it is desirable", "can" are not used in a normative section; the operators are written in upper case per RFC 8174.

**Glossary discipline:** one term — one definition (the ADAPT term mapping or the first use) — one form along the chain BR → SR → SPEC → TC; a synonym of one concept is a `terminology` finding; a unit of measure with every number, identical throughout the artifact; an abbreviation is expanded at first use.

**Prohibited constructions (reformulate before the version is approved):** (1) an evaluative word without a criterion — "fast", "convenient", "sufficient"; (2) an escape clause — "where possible", "as needed", "where applicable"; (3) "/" as "and / or"; (4) an absolute without a tolerance — "always", "never", "100 %"; (5) "all / any / both" instead of "each"; (6) a pronoun without an antecedent in the same sentence; (7) a negation without a positive measurable criterion — "MUST NOT hang"; (8) "and / or / then / unless" gluing two conditions or actions; (9) passive voice without a subject; (10) a subordinate clause attached to the wrong word.

**Atomicity:** one sentence — one verifiable thought; the criterion — exactly one pair of TCs (positive + negative) can be written from the statement, otherwise split it; it SHOULD fit into 25 words.

**Forms by type:** SRs and SPEC statements about behaviour — the six forms of §6.6.3.1 (ubiquitous; WHILE …; WHEN …; IF …, THEN …; WHERE …; WHILE …, WHEN …); the "Constraints" of a BR — business rules: `‹Subject› MUST ‹action›` · `‹Subject› MUST NOT ‹action›` · `‹Subject› MAY ‹action› only if ‹condition›` ("only" is mandatory) · `IF ‹condition›, THEN ‹subject› MUST ‹action›`; TC — Given → preconditions, When → steps (for `system` / `contract` — one action), Then → Pass criterion, Fail is not a negation of Pass; ACTZ — the form of an obligation without interpretive hedges; ADAPT — "is interpreted as …" is permitted.

**Checking:** part of the adversarial review and a precondition of the approval of the set version; a language violation is a remark on the wording, not a backward finding (except a terminological conflict). The "before / after" example — §15.9.

---

## 9. Lifecycle and quality gates (QG)

The description moves **as a set**, not artifact by artifact. The description set — all BR, SR, SPEC and TC norms; it has one draft and approved versions `N.M`: `draft → approved N.M → superseded`. A minor version — on **every** approved change, immediately; a major one — the presented version, by the Architect's decision after a full audit. History is linear (one draft, one predecessor per version). Approval releases a **version record** (`set-version`, `major`, `approved-by`, `supersedes`, `resolves-to`, `changes`, `audit`; `verification` and `accepted-outcomes` are appended by the runner and the stakeholder).
- `BR` / `SR` / `SPEC` / TC norm: **no status or version of their own**. The state is derived: draft (only in the set draft) / approved (belongs to version `N.M`) / removed (absent from the current version; `replaced-by` — in the version record). "Verified" is a property of the pair (set version, product version), recorded by a verification entry in the version record, not by a status.
- `TR`: `draft → approved → done`; `obsolete` is an alternative terminal (the parent SR removed or changed in a new version).
- TC implementation: not admitted → admitted (QG-1) → `pass` / `fail`; stale when `automation.set-version` ≠ the version in which the norm was changed.
ADAPT states: `draft → review → asked → answered → approved → frozen`, plus the terminal `superseded` on supersession. The `client-ready` state is **withdrawn**: putting questions to the client is the subject of an ACTZ, not a state of an ADAPT.
ACTZ states: `draft → sent → signed → superseded`.

| Gate | What it checks | Who runs it | Level |
|---|---|---|---|
| **QG-0** Approval | Set: the frontmatter of all artifacts is valid, the tree is complete, sources resolved, adversarial review on changed artifacts, a pair of TC norms for every statement, the Architect's signature → version record `N.M`. Task: goal + AC. ADAPT — section 5.5 | Architect / authorized | required |
| **QG-1** Implementation | The TC implementation is admitted to runs: `automation.set-version` = the approved version of the norm, `automation.status` + `location` valid, dry-run passed, the fixing red run recorded (P8/P9) | Engineer + runner | required |
| **QG-2** Verification | Set version × product version: all TCs of the version `pass` with `last-run.set-version = N.M`, both TCs of every pair, every TC has a red history or a killed mutant (P9) → verification entry in the version record. Task → `done` | Automated runner | required |
| **QG-3** Architecture | **The Architect's signature** on the ADAPT; every finding `resolved` and carrying `decided-in` to a signed ACTZ; supersession too | Architect | declared / absent |
| **QG-4** Acceptance | The business outcome of a BR of the presented version is accepted (`achievement ≥ 80%`) → `accepted-outcomes[]` in the version record; task acceptance — by a person other than the executor | Stakeholder | declared / absent |
| **Release acceptance gate (AT)** | All `AT` are `passing`; each AT's `tz-version` = the current revision of the effective TZ; the generator's provenance confirms isolation | Isolated agent + runner | **mandatory** |

QG-0..QG-2 are mandatory (`required`). QG-3/QG-4 are `declared` or `absent`. Creating new gate types locally is forbidden.

**The release acceptance gate is not merged with QG-4:** QG-4 is optional and measures the business outcome, while the release gate measures conformance to the contract. The business goal may be reached while the contract is breached, and vice versa.

**Substrate-independent enforcement.** The substrate MUST automatically block transitions when preconditions are unmet: promote-transition — approval of a set version, task and ADAPT transitions, admission of a TC implementation (V3/V4), approve-transition (V6), reference-validation — references resolve within the set version (V1/V5), `coverage-presence` — a pair of TC norms for every statement at version approval, TC implementation staleness by `set-version` (V5), `implements`-edge validation, `adapt-applicability` validation, `adapt-supersession` validation (a dangling `source.adapt` to a `superseded` — fatal), `decided-in` pointing at an unsigned or non-existent ACTZ — fatal, a signed ACTZ not reflected in any ADAPT — fatal, a breach of AT generator isolation — fatal.

---

## 10. Roles and signatures

**The agent (AI) is a first-class executor**: the primary generator of `BR`/`SR`/`SPEC`/`TC`/draft-ADAPT drafts and the adversarial critic. But the agent is **not the owner** of an artifact and **does not sign**.

**The human** is the owner, verifier, and approver. By RACI: `R` (Responsible) = AI, `A` (Accountable) = the **Architect** (the role name for Architect / Tech Lead on the executor side). An attempt to declare the AI agent Accountable is non-conformant.

**Combining roles (§5.5.5) and first-party confirmation (§1.4.4).** One person MAY combine roles, up to all of them — adversarial checking by people is then absent, an accepted trade-off the manifest names honestly (`confirmation: first-party`). Two restrictions are not lifted: the ACTZ dual signature is not imitated by one person (without a second party the ACTZ has no subject — decisions on backward findings are recorded by a revised concept signed by the responsible person, `decided-in` points to it); execution and acceptance of one task are not combined (the implementation author neither writes its TC nor accepts their own task). In the first-party kind: the input is a frozen concept instead of a TZ (`source.tz-section` to its section), the AT release gate has no subject — presentation is by the verification entry of the set version and by MW manual walkthrough records, adversarial review without exception, ACR (§12.3.6) mandatory.

The signature map is closed:

| Artifact | Who signs |
|---|---|
| **TZ** and delta-TZ | The client |
| **ACTZ** | **Dual**: client + vendor (`client-signature` + `vendor-signature`; the same person on both sides is a violation) |
| **ADAPT** | **The Architect only** |
| **AR** | The adversarial reviewer |
| `implementation-originated` | A human supervisor |

The client signs **only the TZ and the ACTZ**. There is **no** client signature on an ADAPT: the client would be signing an engineering document they never read and cannot assess.

AI provenance is recorded in `ai-provenance` (model, prompt template, tokens, the fact of a human edit). Different agent roles MUST be different agents: the primary agent ≠ the adversarial reviewer; the test author ≠ the implementation author (P8); the AT generator ≠ the primary agent (a different model, with no access to the internal circuit).

---

## 11. Closed lists (cannot be extended locally)

- **12 SPEC types:** `ARCH / API / DATA / INT / PROC / UI / AI / SEC / OPS / TEST / DOC / UC`.
- **6 TC types:** `business / ux / system / contract / eval / security` (there is **no** `acceptance` type).
- **7 finding categories:** `contradiction / gap / hidden-assumption / feasibility / regulatory / terminology / scope`.
- **9 normative TC principles:** P1 standalone artifact, P2 document ≠ implementation, P3 AI-generated, P4 AI-executed, P5 pos/neg pairing, P6 `last-run` bot-managed, P7 judge ≠ production, **P8 authorship isolation**, **P9 red history**.
- **Quality Gates:** `QG-0 / QG-1 / QG-2 / QG-3 / QG-4`.
- **V1–V6** substrate capabilities: immutable history, atomic change unit, diff & review, branching, version pin, author + timestamp.
- **ADAPT states:** `draft / review / asked / answered / approved / frozen / superseded` (an ADAPT has no `obsolete` state).
- **ACTZ states:** `draft / sent / signed / superseded`.
- **3 requirement-axis types:** `BR / SR / TR`. There is no fourth type; an artifact that does not fit the three is not a requirement.
- **3 decomposition levels:** `system / subsystem / module`. There is no fourth level.
- **Description set states:** `draft / approved N.M / superseded` (+ the `major` flag); BR / SR / SPEC / TC norm have **no** states of their own. **TR states:** `draft / approved / done / obsolete`. **TC implementation states:** not admitted / admitted / `pass` / `fail` / stale.
- **5 modal operators of the description language:** `MUST / MUST NOT / SHOULD / SHOULD NOT / MAY` — exactly one per statement, synonyms prohibited (§15.2).

> The full corpus has **nineteen** closed lists; the ones listed here are those an agent can violate while producing artifacts. The rest — scopes of applicability, roles, metrics, maturity levels, drift classes and the register of forbidden terms — are not extended by an agent, but apply equally under formal assessment; the full corpus is the source.

Extending any list is possible only by a formal amendment to the standard (research draft → discussion → minor-version bump → migration guide).

---

## 12. Hard rules (cannot be broken)

- **A TZ, a signed ACTZ, and an approved ADAPT are immutable** — only the addition of new artifacts with an explicit typed link.
- **Do not reconstruct `SR`/`SPEC` from code** without a bug-fix justification (source-of-truth inversion). The narrow exception is `implementation-originated` for an internal technical detail, with the harness from section 6.3.
- **Do not fit tests** to the implementation; a change to verified behavior is tagged `[test-spec-change]`.
- **Do not let the test be written by the same agent that writes the implementation** (P8), and do not count a test that has never been red (P9) — the only exception is `implementation-originated` with a killed mutant.
- **Provenance is mandatory** for every `BR`/`SR`/`SPEC` (`source.tz-section` always; `source.adapt` or `source.adversarial-review-ref`).
- **A TC pair** (positive + negative) for every normative statement.
- **Adversarial review of the TZ is always mandatory**; "no ADAPT needed" is a recorded verdict of a different model, not a silent assumption.
- **Do not have the client sign an ADAPT.** What is put to the client is an ACTZ; an ADAPT is signed by the Architect only.
- **Every finding that needs the client's word carries `decided-in` to a clause of a SIGNED ACTZ.** A reference to a `draft`/`sent` ACTZ is fatal. A signed ACTZ not reflected in any ADAPT is fatal.
- **An AT is derived only from the effective TZ** by an isolated agent on a different model; the generator's access to ADAPT / BR / SR / SPEC / TC / code is forbidden; an AT's `verifies[]` references only `TZ §N` and `ACTZ-NNN §M`.
- **AT are regenerated before every trial run** from the current revision of the effective TZ; the product is not presented for delivery until all AT are `passing`.
- **Do not invent closed lists** (section 11).
- **A dangling reference to a `superseded` ADAPT** is forbidden — repoint or re-derive the derivatives.

---

## 13. Minimal recording forms (frontmatter)

### TZ
```yaml
id: TZ-YYYY-NNN
type: TZ
status: registered            # immutable after registration
signed-by-client: "<name + role>"
signed-date: "<ISO-date>"
document-version-ref: "<substrate version identifier>"
```

### ADAPT
```yaml
id: ADAPT-NNN
type: ADAPT
trigger-stage: import-tz       # import-tz | decompose-br | decompose-sr | spec | tc
source-tz: { id: TZ-YYYY-NNN, signed-date: "<ISO>", signed-by-client: "<name+role>" }
parent-adapt: { id: ADAPT-NNN, delta-tz: TZ-YYYY-NNN-delta-N }   # for a delta-ADAPT
supersedes: ADAPT-MMM          # only for a superseding ADAPT
superseded-by: ADAPT-NNN       # auto-derived on the superseded one
supersession-rationale: "<conflicting BR/SR/SPEC + source>"   # mandatory if supersedes
status: draft | review | asked | answered | approved | frozen | superseded
created: "<ISO-date>"
last-updated: "<ISO-date>"
approval:
  architect-signature: { signed-by: "<name>", role: architect, signed-at: "<ISO>" }   # the ADAPT's only signature
open-questions-count: 0        # MUST be 0 for approved
resolved-questions-count: 0    # number of findings in resolved
```
Every backward finding in the ADAPT body:
```yaml
- id: B-NNN
  category: gap                # one of the 7 closed categories
  status: open | asked-to-client | answered | resolved | revised | frozen
  decided-in: "ACTZ-NNN §M"    # mandatory for resolved; the ACTZ MUST be signed
```

### AR (adversarial-review record)
```yaml
id: AR-NNN                    # immutable; sequential within the parent TZ
tz-ref: TZ-YYYY-NNN           # the (delta-)TZ under review
trigger-stage: import-tz      # import-tz | decompose-br | decompose-sr | spec | tc
reviewer:                     # the adversarial reviewer
  vendor: "<provider>"
  model: "<model>"            # MUST differ from the primary agent's model
primary:                      # YOUR model at the moment of issue — the second operand
  vendor: "<provider>"        # without it independence cannot be re-checked later
  model: "<model>"
verdict: no-findings          # findings-present | no-findings
produces-adapt: []            # non-empty on findings-present; empty or absent on no-findings
status: issued                # draft → issued → superseded
superseded-by: null           # mandatory when status: superseded
signature:                    # mandatory for issued (substrate capability V6)
  author: "<who issued it>"
  timestamp: "<UTC ISO-8601>"
```
`source.adversarial-review-ref` may reference **only** an AR in status `issued` with verdict `no-findings`.

### `ai-provenance` (in any AI-generated artifact)
```yaml
ai-provenance:
  generated-by: "<vendor>-<model>-<version>@<date>"   # model identifier
  generated-at: "<UTC ISO-8601>"                      # generation time
  prompt-template: "<path>@<version>"                 # pointer to the prompt template
  context-tokens: 0                                   # input context size
  output-tokens: 0                                    # output size; feeds the §12.3 metrics
  human-edits: false                                  # were there manual edits after generation
  generation-time-ms: 0                               # optional; recommended at RENAR-5
```
The first six fields are **mandatory**. The block MUST NOT be abbreviated: `output-tokens` feeds the metrics, and `prompt-template` with `generated-at` are the only things that make a generation reproducible.

### ACTZ (the TZ clarification protocol)
```yaml
id: ACTZ-NNN
type: ACTZ
contract-name: "TZ Clarification Protocol No. N"
tz-ref: TZ-YYYY-NNN
resolves: [B-001, B-004]       # the ADAPT entries it closes; absent for a client-initiated ACTZ
decisions:                     # mandatory; the language of commitments, a stable clause number §M
  - { section: "§1", text: "<the client's decision in the language of commitments>" }
annexes: ["<new version of a TZ annex>"]   # if the decision is a new version of an annex
status: draft | sent | signed | superseded
client-signature: { signed-by: "<name>", role: "<role>", organization: "<org>", signed-at: "<ISO>" }   # mandatory for signed
vendor-signature: { signed-by: "<name>", role: "<role>", signed-at: "<ISO>" }                          # mandatory for signed
superseded-by: ACTZ-MMM        # mandatory when status: superseded
```

### BR
```yaml
id: BR-NN
title: "<short, descriptive>"
type: BR
slug: "<kebab-case>"           # auto-derived
owner: "<role / responsible person>"
# no status or version: a BR is approved as part of a set version (section 9)
level: system | subsystem
scope: { system: "<system>", subsystem: "<subsystem>" }   # subsystem: null when level=system
implements: [{ id: BR-MM, scope.system: "<system>" }]   # for a subsystem (0..N)
source:
  adapt: ADAPT-NNN             # if an ADAPT exists
  tz-section: "§N.N"           # always mandatory
  adversarial-review-ref: AR-NNN        # if source.adapt is omitted -> AR (issued, no-findings)
```
Body (mandatory sections):
```markdown
## Need
Who (role), what (action), why (business goal) — one sentence.
## Success criteria
Measurable outcomes, 3–7 items; each independently verifiable.
## Context
Where the requirement came from (reference to an ADAPT section if present); what alternatives.
## Constraints
Optional: business constraints (budget, deadlines, regulation). No technical ones.
```

### SR
```yaml
id: SR-NN
title: "<short, descriptive>"
type: SR
slug: "<kebab-case>"           # auto-derived
owner: "<role / responsible person>"
# no status or version: an SR is approved as part of a set version (section 9)
level: system | subsystem | module
scope: { system: "<system>", subsystem: "<subsystem>", module: "<module>" }   # null where not applicable
parent: BR-NN                  # exactly one
source:
  adapt: ADAPT-NNN
  adapt-section: "Forward §3"
  tz-section: "§3.4"           # always mandatory
constrained-by: [SPEC-API-02, SPEC-UI-04]
verified-by: [TC-NN, TC-NN-neg]
```
The provenance variant for an internal technical detail (section 6.3) — **only** when `observable-by-client: false`:
```yaml
source:
  tz-section: "§N.N"                   # always mandatory, here too
  implementation-originated:
    change-unit-ref: "<the implementation change unit that produced the requirement>"
    rationale: "<why the functionality was deemed necessary>"
    human-approval: { approved-by: "<human supervisor>", role: "<role>", approved-at: "<ISO>" }
    observable-by-client: false        # true → non-conformance
    covering-tc: TC-NN                 # exists and passes before the merge
    mutation-check: { mutants-killed: 1, report-ref: "<mutation check report>" }   # ≥ 1
```
Body (mandatory sections):
```markdown
## Requirement
One sentence in one of the five controlled forms (SHOULD, §6.6.3.1): "The system MUST ‹response›" or with a condition marker as the FIRST word — WHILE ‹state›, WHEN ‹trigger›, IF ‹condition› THEN, WHERE ‹feature is present›. The marker is recognized by position; EN renders it uppercase per its own convention. One sentence — one statement.
## Behavior
Detailed observable behavior; functional scenarios.
## Constraints
If applicable: non-functional (performance, security). Full ones — in SPEC.
## Link to SPEC
If constrained-by[] is present: which aspects of behavior are governed by which SPEC.
```

### SPEC
```yaml
id: SPEC-API-NN
type: SPEC-API                 # one of the 12 closed types
# no status or version: a SPEC is approved as part of a set version (section 9)
source: { adapt: ADAPT-NNN, tz-section: "§N.N" }
depends-on: [SPEC-DATA-NN]     # DAG, no cycles
```

### SPEC-UC (use case)

```yaml
id: SPEC-UC-NN
type: SPEC-UC
spec-type: SPEC-UC
role: human | agent             # human — through the interface (ux TC); agent — through the API (system / contract)
persona: "<ADAPT§persona>"       # for human
covers: [SR-NN, SPEC-UI-NN]     # ≥ 2 artifacts of the same set version
entry: SPEC-UI-NN | SPEC-API-NN
steps:
  - { n: 1, action: "<what the executor does>", ref: SR-NN#3, expects: "<observable result>" }   # ref — the statement address <id>#n, MANDATORY
source: { adapt: ADAPT-NNN, tz-section: "§N.N" }
```

## Goal and executor
## Preconditions
## Main path
## Alternative and negative branches
## Postconditions
## Covering TCs

### MW (manual walkthrough record)

```yaml
id: MW-NN
type: MW
scenario: SPEC-UC-NN | SPEC-UI-NN
set-version: "N.M"
product-version: "<pointer>"
walked-by: "<actor>"             # ≠ the implementation author (P8)
walked-at: "<ISO-8601>"
findings:
  - { step: 3, observed: "<what was seen>", expected-ref: SR-NN, route: implementation-originated | contractual }
evidence-refs: []                # the format is not governed (§1.3 item 5)
```

### TR (in the tracker, not a file)
```yaml
id: TR-NNN
title: "<short, descriptive>"
type: TR
slug: "<kebab-case>"
level: system | subsystem | module
scope: { system: "<system>", subsystem: "<subsystem>", module: "<module>" }   # null where not applicable
status: draft                  # draft → approved → done; obsolete is the alternative terminal
owner: "<role / assignee>"
goal: "<what to do>"
acceptance-criteria: ["<criterion 1>", "<criterion 2>"]
parent: { id: SR-NN }          # the single parent; the field is parent, NOT parent-sr
implements-spec: SPEC-API-NN
```
Body (mandatory sections; the names `Goal`/`Acceptance Criteria`/`Scope` are canonical):
```markdown
## Goal
One paragraph; the outcome the TR makes observable.
## Acceptance Criteria
A numbered list of falsifiable criteria; covers positive and negative scenarios.
## Scope
What is in and what is **not** in the TR.
## References
If applicable: to the SPEC in implements-spec[] and the sections of the parent SR.
```

### TC
```yaml
id: TC-NN
type: TC
tc-type: business             # business | ux | system | contract | eval | security  (acceptance WITHDRAWN)
negative: false               # the paired TC-NN-neg is mandatory (negative: true)
verifies: [{ id: SR-NN, statement: 5 }]   # statement — the statement address <id>#n (§15.1.1), mandatory with >1 statement; the version — once, automation.set-version
verifies-tr: TR-NN            # optional: narrowing to the scope of a task
verifies-claims: ["§2.1"]     # optional: a subset of the parent SR's statements
# a TC norm has no status; implementation states — automation.* / last-run.* (section 8)
red-history:                  # mandatory for the TC to count as evidence (P9)
  fixing-run: { date: "<ISO>", result: fail }      # the fixing run BEFORE implementation — MUST be red
  green-transition: "<ISO>"                         # written by the runner
  inherited-from: TC-NN                             # conditional: refactoring with no behavior change
  not-applicable-reason: implementation-originated  # conditional: the class of section 6.3
mutation-check: { mutants-killed: 1 }               # mandatory if not-applicable-reason is set (≥ 1)
environment-ref: SPEC-TEST-NN  # mandatory if automation.kind: dynamic — the environment and dataset the TC is valid against; not applicable to static
automation:
  status: automated           # automated | manual-pending
  set-version: "N.M"          # the set version the implementation was written against (QG-1)
  kind: dynamic               # MANDATORY: dynamic | static
  location: "<path/identifier>"                     # mandatory if automated
judge: { vendor: "<provider>", model: "<model>" }   # mandatory for ux | eval; vendor ≠ production (P7)
baseline:                       # mandatory for ux | eval — without it the TC is non-conformant
  artifact: "<baseline>"        # ux: baseline render; eval: versionable dataset
  perceptual-diff-threshold: 0.0   # ux only: perceptual divergence threshold
  metric-thresholds: {}         # eval only: metric thresholds
# Automatic update of baseline.artifact is FORBIDDEN: only via approval tagged [baseline-update]
ai-provenance: { generated-by: "<vendor>-<model>-<version>@<date>", human-edits: false }   # test author ≠ implementation author (P8)
```
Body (mandatory sections; `## Pass criterion` and `## Fail criterion` are fixed names):
```markdown
## Context
Which clause of the verified artifact the TC references; a quote or paraphrase.
## Preconditions
The system and data state for the run; the seed mechanism.
## Steps
Runner actions. For tc-type: ux — intentions, not selectors.
## Pass criterion
Binary, observable, reproducible.
## Fail criterion
Observable signs of a violation (not the negation of Pass): leaks, side effects, races.
## Postconditions
The expected state after the run; cleanup.
## Out of scope
What is deliberately not checked, naming the paired TC.
```
Additional **mandatory** body sections by TC type:
- `ux`: **Scenario** ("<actor> wants <result> after <condition>" — an intent, not selectors); **Perceptual criterion** (what the judge must see); **Paired negative** (empty state / error / lack of permissions).
- `eval`: **dataset provenance** (how it was assembled, what labelling); **metric cluster** (one eval-TC = one semantically coherent group of metrics; different families — different TCs); **regression rule** (crossing a threshold or a regression ≥ N% against the baseline).
- `contract`: **machine-readable contract** (a reference to OpenAPI / GraphQL SDL / Protobuf / JSON Schema from the SPEC); **side** — producer or consumer; **mocked counterparty**. For `SPEC-INT` a mocked contract is **not sufficient**: an additional integration TC (`tc-type: contract`, `level: subsystem | system`) against a real or sandbox counterparty is mandatory.
- `security`: **threat-model attributes** (STRIDE category or equivalent); **subject under test** (authn / authz / data classification / secrets / audit / encryption); **negative scenarios** (bypass, unauthorized access, leakage); **expected system behavior on a violation**. A security TC normatively contains **only negative scenarios**; the positive "grant correct access to the correct actor" is covered by `tc-type: system` with coverage scope SPEC-SEC.

### AT (the acceptance test against the contract)
```yaml
id: AT-NN
type: AT
verifies:                     # the contractual circuit ONLY; a reference to BR/SR/SPEC/TC is fatal
  - "TZ §3.4"
  - "ACTZ-001 §2"
tz-version: "<revision of the effective TZ>"    # MUST match the current revision
generator:                    # the isolated agent
  vendor: "<provider>"
  model: "<model>"            # MUST differ from the primary agent's model
  internal-access: false      # no access to ADAPT / BR / SR / SPEC / TC / code; true → fatal
negative: false               # pos/neg pairing as for TC
status: draft                 # draft → ready → passing | failing | obsolete
environment-ref: SPEC-TEST-NN            # mandatory: the environment and dataset of the acceptance trials; filled in NOT by the generator but by the architect or runner AFTER generation — isolation is not broken
automation: { status: automated, kind: dynamic, location: "<path/identifier>" }
```
The AT body is as for a TC, plus a **mandatory** section with a verbatim quote of the contract clause:
```markdown
## tz_text
A verbatim quote of the checked clause of the effective TZ (not a paraphrase).
```

---

## 14. Conformance minimum

A project declares conformance through a **manifest** (immutable, V1) with mandatory fields:

```yaml
renar-version: "1.1"                 # RENAR version conformance is claimed against
senar-version: "1.0"                 # mandatory, MVR-7
manifest-version: 3                  # incremented on every update; never re-used
manifest-id: "CFM-2026-001"          # stable substrate identifier (V1)
level: "RENAR-1"                     # RENAR-1 (ad-hoc) … RENAR-5 (optimizing)
set-version: "N.M"                   # approved description set version at assessment time (§10.5.4)
assessment-mode: "self"              # self | third-party
confirmation: "second-party"         # second-party (a client, ACTZ) | first-party (a concept as the input, §1.4.4)
assessment-date: "<ISO-8601>"
assessor:
  id: "<V6 author identifier>"
  role: "architect"                  # architect | authorized-role-holder | external-assessor
  signature-ref: "<pointer to the signature event>"
next-assessment-due: "<ISO-8601>"    # elapsed without a new manifest version → conformance lost
mandatory-clauses-confirmed:         # THIS field name, not mandatory-clauses
  sot-inversion: true                              # §13.3.1
  substrate-v1-v6: { v1: true, v2: true, v3: true, v4: true, v5: true, v6: true }   # §13.3.2
  adapt-per-tz: true                               # §13.3.3
  spec-types-closed-list: true                     # §13.3.4
  tc-pos-neg-pairing: true                         # §13.3.5
  quality-gates-closed-list: true                  # §13.3.6
  closed-lists-backward-findings: true             # §13.3.7
  implements-edge-subsystem: true                  # §13.3.8
quality-gates:                       # QG-0..QG-2 MUST be required
  qg-0: required
  qg-1: required
  qg-2: required
  qg-3: declared                     # required | declared | absent
  qg-4: absent
substrate-capabilities:              # substrate capability declaration
  v1-immutable-history: declared
  v2-atomic-change-unit: declared
  v3-diff-review: declared
  v4-branching: declared
  v5-version-pin: declared
  v6-author-timestamp: declared
  substrate-id: "<pointer to guide/03..06>"
spec-types-supported: ["SPEC-ARCH", "SPEC-API", "SPEC-DATA", "SPEC-INT", "SPEC-PROC",
                       "SPEC-UI", "SPEC-AI", "SPEC-SEC", "SPEC-OPS", "SPEC-TEST", "SPEC-DOC", "SPEC-UC"]
# All 12 types are mandatory as minimum-supported. A declaration "type not used in this project"
# is permitted; "type not supported by the substrate" is not.
```

An implementation MAY **tighten** the requirements (`declared-stricter`: QG-3/QG-4 as `required`, and so on), but MUST NOT **weaken** them: it cannot declare ADAPT optional, allow a single TC for a normative statement, or omit `senar-version`/`level`. The levels `RENAR-1..RENAR-5` reflect process maturity, not the volume of documentation.

---

## 15. Glossary of key terms

- **TZ** — the technical assignment: the client's contractual input, immutable.
- **ADAPT** — the **internal** interpretation of the TZ (forward interpretation + backward findings); reactive, 0..N per TZ; signed by the architect only.
- **ACTZ** — the TZ clarification protocol ("TZ Clarification Protocol No. N"): the decisions put to the client; **dual** signature, contractual weight; `draft → sent → signed → superseded`.
- **Effective TZ** — the initial TZ + all signed ACTZ; the benchmark for delivery and acceptance.
- **`decided-in`** — a finding's reference to a clause of a **signed** ACTZ recording the client's decision.
- **AT** — the acceptance test against the contract: derived by an isolated agent from the effective TZ only; regenerated before every trial run.
- **P8 (authorship isolation)** — the test is frozen before implementation; the test author ≠ the implementation author; criteria are edited via `[test-spec-change]`.
- **P9 (red history)** — a test never observed red is not evidence.
- **`implementation-originated`** — the narrow provenance class for an internal technical detail; a killed mutant is mandatory.
- **Forward interpretation (Forward)** — the translation of a TZ section into the language of requirements.
- **Backward finding** — a recorded question/problem in one of the 7 categories.
- **Adversarial review** — a review by a separate agent on a different model; issues a verdict; recorded as an AR.
- **Provenance / source** — the machine-readable origin of an artifact.
- **implements-edge** — a typed edge "subsystem BR implements system BR".
- **Supersession** — the cancellation of a previously correct but refuted decision; the state `superseded`.
- **QG (Quality Gate)** — a lifecycle-transition gate.
- **SoT inversion** — requirements (not code) are the source of truth about behavior.
- **MVR** — Minimum Viable RENAR: the seven mandatory statements.
- **Architect** — the role name for Architect / Tech Lead; the owner and signatory on the executor side (including the sole signatory of an ADAPT).

---

*RENAR 1.0 — the operational edition for the agent. Full normative corpus: [renar.tech](https://renar.tech). © 2026 Vadim Soglaev, Andrey Yumashev. CC BY-SA 4.0.*
