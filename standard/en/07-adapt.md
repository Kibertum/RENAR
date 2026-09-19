---
title: "ADAPT — two-way TZ adaptation"
order: 7
lang: en
---
# 07. ADAPT — two-way TZ adaptation

> **Part of the RENAR Standard v1.1** · [← Table of contents](README.md)

## 7.1 What ADAPT is and why it exists

The client sends a TZ in their own language — the language of business and of the contract. It is signed and no longer changes: it is a contract. But turning it directly into precise requirements almost never works. Somewhere the TZ is silent about something important ("data export" — in what format? for what retention period?), somewhere it contradicts itself, somewhere the same term means one thing to the client and another to the engineer. Editing the TZ is not allowed — it is a contract. Silently filling in the gaps on the client's behalf is also not allowed: that way someone else's guesses seep into the requirements, and at acceptance the "this is not what we ordered" surfaces.

ADAPT is the bridge across this gap. It has two sides: **forward interpretation** ("we understood §4.2 of the TZ as follows") and **backward findings** ("§4.3 does not set a deadline — please clarify"), which go to the client and come back as the client's decisions.

But the document the client reads and the document the engineer works from are **two different documents**. Decisions put to the client and signed are obligations, and they live in a separate artifact: **ACTZ**, the TZ Clarification Protocol ([§7.13](#7.13)). ADAPT remains an internal interpretation: only the Architect signs it, and the client never sees it. The boundary is drawn not by the content of a record but **by audience** — everything shown to the client and approved is an obligation; everything not shown is an interpretation. The intractable argument "is this an interpretation or already a change?" thereby disappears.

ADAPT need not precede all derivation. The typical occasion to create it is TZ import, but a question to the client rooted in the TZ may also surface **later** — during the BR → SR → SPEC decomposition, or while developing test cases. A **new** ADAPT then arises, bound to the stage where the finding was discovered, while previously derived artifacts remain valid. ADAPT is not always created: if the conversion of the relevant TZ fragment is unambiguous and there are no questions, it is not needed ([§7.4.1](#7.4.1)).

ADAPT is a consequence of [Statement 2 of §2.4](02-methodology-positioning.md#2.4) (RENAR is a waterfall-form ≠ classical waterfall, because ADAPT provides two-way adaptation instead of "throwing the specification over the wall").

## 7.2 The problem ADAPT regulates

Without a formalized intermediate artifact between the TZ and BR/SR, one of two negative outcomes arises:

| Outcome | What happens |
|---|---|
| **TZ drift** | The TZ is edited after signing → the contract is breached |
| **Hidden interpretation** | Engineering assumptions silently seep into BR/SR/SPEC → the trace chain and provenance break down |

ADAPT eliminates both outcomes: the TZ remains an immutable contractual document, **all** interpretations, clarifications, and backward findings are registered in ADAPT, which itself becomes immutable after the Architect's signature (§7.5), while the client's decisions are registered in ACTZ protocols signed by both parties ([§7.13](#7.13)).

---

## 7.3 The two-way adaptation cycle

```text
        ┌───────────────────────────────┐
        │   TZ (immutable, contract)    │
        └───────────────┬───────────────┘
                        │ source
                        ▼
┌──────────────────────────────────────────────┐
│              ADAPT-NNN                        │
│                                              │
│  Forward interpretation (engineer → client)  │
│  ─ by TZ section                             │
│  ─ term mapping                              │
│  ─ filled-in scenarios                       │
│  ─ scope clarification                       │
│                                              │
│  Backward findings (engineer → client)       │
│  ─ 7 categories of records                   │
│  ─ lifecycle: open → asked → answered →      │
│              resolved → frozen               │
│                                              │
│  Status: draft → review → answered →         │
│         approved → frozen                    │
│  Approval: Architect's signature (§7.5)      │
└────────────────────┬─────────────────────────┘
                     │ approved
                     ▼
        ┌───────────────────────────────┐
        │   BR / SR / SPEC              │
        │   reference ADAPT§            │
        │   through source.adapt        │
        └───────────────────────────────┘
```

The TZ is the primary source; ADAPT is the canonical source of interpretation; BR/SR/SPEC reference ADAPT, not the TZ directly.

The "TZ → ADAPT" input shown is the typical one (TZ import), but **not the only one**. The cycle trigger is stage-agnostic: if a question to the client rooted in the TZ surfaces later — at the BR → SR → SPEC decomposition stage or while developing TC — the cycle runs again and produces a **new** ADAPT-NNN, bound to the stage at which the finding was discovered ([§7.4.1.1](#7.4.1)). A single TZ may have several such ADAPTs (MVR-3, [§0.5](00-introduction.md#0.5)).

---

## 7.4 Normative requirements for ADAPT

### 7.4.1 Reactive obligatoriness

ADAPT is a **reactive artifact**: it is created if and only if converting the TZ → RENAR description produces a gap between the client's language and the requirements language ([§7.12](#7.12)). If the conversion is unambiguous, no ADAPT is created; BR / SR / SPEC are derived directly from the TZ through the mandatory `source.tz-section` field.

#### 7.4.1.1 Conditions for ADAPT obligatoriness

ADAPT is REQUIRED **if and only if** at least one of the conditions holds:

1. **A backward finding is discovered.** The adversarial reviewer ([§7.10.2](#7.10.2)) at any stage of the requirements lifecycle (TZ import or BR → SR → SPEC → TC decomposition) has identified ≥ 1 record under at least one of the 7 categories of §7.4.4 (`contradiction`, `gap`, `hidden-assumption`, `feasibility`, `regulatory`, `terminology`, `scope`) rooted in the language or intent of the TZ.
2. **Term mapping is required.** The TZ uses a term that has no unambiguous engineering interpretation (requires a "client → engineer" mapping).
3. **Scope clarification is required.** The scope from the TZ is ambiguous and requires fixing "in / out".

If none of the conditions holds (the adversarial reviewer returned a "no findings, no clarifications" verdict), no ADAPT **is created**. BR / SR / SPEC reference the TZ directly through `source.tz-section` ([§6.5.2](06-requirements-hierarchy.md#6.5.2), [§6.6.2](06-requirements-hierarchy.md#6.6.2), [§8.5](08-specifications.md#8.5)).

#### 7.4.1.2 The adversarial reviewer as a mandatory gate

The adversarial review of the TZ ([§7.10.2](#7.10.2)) is a **mandatory step** for every TZ at import, regardless of whether ADAPT is created or not. The verdict at import is one-time but **not final**: at the derivation stages (BR → SR → SPEC → TC) the adversarial reviewer issues the verdict again as decomposition uncovers new questions about the TZ. A "no findings" verdict at import does not block the appearance of ADAPT later, if a finding rooted in the TZ is discovered at the decomposition stage ([§7.7.3](#7.7.3)). The adversarial reviewer issues a formal verdict in one of two forms:

| Verdict | What is recorded | Consequence |
|---|---|---|
| **"findings present"** | A list of concrete findings across the 7 categories + an indication of TZ sections | ADAPT is REQUIRED; the lifecycle of §7.4.5 starts |
| **"no findings, no clarifications"** | Confirmation by the adversarial reviewer (a different model, §9.4) that the TZ converts to RENAR unambiguously | ADAPT MAY be omitted; the verdict remains a recorded piece of evidence |

In both outcomes the verdict MUST be recorded in an **adversarial-review record** (AR, [§7.4.6](#7.4.6)) — an evidence record, immutable once issued, carrying its own identifier, author and timestamp (V6). AR is the sole carrier of the verdict: on the "findings present" outcome it points to the ADAPT it produced; on the "no findings" outcome it **is itself** the evidence referenced by the `source.adversarial-review-ref` field ([§6.5.2](06-requirements-hierarchy.md#6.5.2)).

The hook ([§10.11.1](10-lifecycle-qg.md#10.11.1) `adapt-applicability validation`), when BR / SR / SPEC are created without `source.adapt`, checks for an AR with status `issued` and a valid verdict for the corresponding TZ. The absence of such a record is fatal: creation is blocked until the adversarial review is passed.

#### 7.4.1.3 Prohibition of silent skipping

Creating BR / SR / SPEC from a TZ **without an adversarial review** (skipping ADAPT without a verdict) is a violation of the standard. This preserves the Source-of-Truth inversion ([§2.3](02-methodology-positioning.md#2.3)): "no findings" is a **recorded assertion** by the adversarial reviewer, not a silent assumption by the Architect. The verdict MUST be issued as an AR ([§7.4.6](#7.4.6)) with author and timestamp (V6) and be available on an auditor's request ([§13.5](13-conformance.md#13.5)).

When a delta-TZ is present, the same rule applies: a delta-ADAPT is created reactively, upon findings during the adversarial review of the delta-TZ ([§7.6](#7.6)).

#### 7.4.1.4 Multiplicity of ADAPTs per single TZ

Since the trigger is stage-agnostic ([§7.4.1.1](#7.4.1)), a single unmodified TZ may have **zero or more** root ADAPTs (cardinality 0..N — a reformulation of MVR-3, [§0.5](00-introduction.md#0.5)):

- **zero** — adversarial review at all stages returned "no findings";
- **one** — a finding was discovered once (typically at import);
- **several** — findings rooted in the TZ arose at different derivation stages (import, then BR → SR decomposition, etc.).

Each ADAPT keeps a stable end-to-end `ADAPT-NNN` and records a `trigger-stage` field — the stage at which it was produced ([§7.8.1](#7.8)). Multiplicity is the **regular** case, not an exception, and it does not weaken provenance: each BR / SR / SPEC still references exactly one ADAPT (through `source.adapt`) or the TZ directly (through `source.tz-section`) from which it is derived.

### 7.4.2 Immutability of the TZ

The TZ is a contractual document. After a TZ is registered in the substrate, its content **is not edited**. If during work it turns out that the TZ has an error / gap / contradiction, this is registered as a backward finding in ADAPT (§7.4.4), the client gives an answer, and the answer becomes part of ADAPT. The TZ remains immutable.

With a large number of edits, or when the scope changes, the client signs a delta-TZ as a new immutable document (§7.6).

### 7.4.3 Forward adaptation of the TZ

For each TZ section, the forward-interpretation section of ADAPT MUST contain:

| Element | Obligatoriness | Purpose |
|---|---|---|
| Exact reference to TZ§N.N | REQUIRED | provenance |
| Quote from the TZ (or a paraphrase with an explicit marker) | REQUIRED | Context |
| Engineering interpretation of the section | REQUIRED | Translation of the client's language → the requirements language |
| Term mapping (client → engineer) | REQUIRED if applicable | Term disambiguation |
| Filled-in scenarios | REQUIRED if the TZ implies them | Explicit recording of implicit cases |
| Scope clarification (in / out) | REQUIRED | Scope |
| References from the forward interpretation to BR/SR/SPEC | auto-derived | Trace chain ([§7.7](#7.7)) |

### 7.4.4 Backward findings and their categories

The backward-findings section of ADAPT records discovered problems across seven normative categories. **The list of categories is closed at v1.0**; adding new categories is done through the formal change procedure of the standard (see [chapter 13](13-conformance.md)). The general closed-list policy and the master index — [§1.7.5](01-scope.md#1.7.5).

| ID | Category | What is recorded |
|---|---|---|
| `contradiction` | Contradiction | Internal contradictions in the TZ (§A vs §B) |
| `gap` | Gap | The TZ is silent about something without which implementation is impossible |
| `hidden-assumption` | Hidden assumption | An engineer's assumption that may be wrong |
| `feasibility` | Feasibility | A technically infeasible or disproportionately expensive requirement |
| `regulatory` | Regulatory | A requirement that touches legislation / compliance |
| `terminology` | Terminology | An unclear TZ term with several possible meanings |
| `scope` | Scope | An unclear scope |

Each backward-finding record has a **stable ID** (`B-NNN`), immutable after creation. The ID is not reused even for withdrawn records (audit log).

### 7.4.5 Lifecycle of a single backward-finding record

```text
open → asked-to-client → answered → resolved → frozen
              ↑                          │
              └────── revised ───────────┘  (if the answer needs clarification)
```

| Status | What it means |
|---|---|
| `open` | The engineer recorded it; not yet put to the client |
| `asked-to-client` | The question is put to the client as part of an ACTZ ([§7.13](#7.13)); signing is awaited; the date is recorded |
| `answered` | The client took a decision; the decision is recorded as a clause of a **signed** ACTZ |
| `resolved` | The engineer integrated the decision into the forward interpretation; the record carries `decided-in: ACTZ-NNN §M` |
| `revised` | The client's decision is vague; the question is put again in a new ACTZ. Transition back to `asked-to-client` |
| `frozen` | After ADAPT approval — changes are impossible |

**The link to an ACTZ is mandatory.** Every record that requires a client decision MUST carry the field `decided-in: ACTZ-NNN §M` — a reference to the clause of a **signed** ACTZ in which the decision is recorded ([§7.13](#7.13)). In the first-party confirmation kind ([§1.4.4](01-scope.md#1.4.4)) the ACTZ has no subject: `decided-in` points to the section of the revised concept signed by the responsible person (V6) — with the same obligation and the same prohibition on referencing unsigned text. A reference to an unsigned ACTZ, and equally a dangling reference to a non-existent ACTZ, is **fatal** ([§10.11.1](10-lifecycle-qg.md#10.11.1)).

ADAPT approval (§7.5) is prohibited while at least one backward-finding record is in status `open` / `asked-to-client` / `answered` / `revised`. All such records MUST be in `resolved` before approval — that is, each MUST carry a signed client decision.

The reverse direction MUST also be expressed: a decision taken by the client on their own initiative (an ACTZ with no parent finding, §7.13.2) MUST be **reflected in the interpretation** — ADAPT reacts to the ACTZ. A signed decision not reflected in any ADAPT is **fatal**: it is an obligation absent from the requirements.

### 7.4.6 AR — the adversarial-review record

**AR** (Adversarial-Review Record) is an evidence record, immutable once issued, that captures the fact and the outcome of the mandatory adversarial review ([§7.4.1.2](#7.4.1)).

AR belongs to the class of **evidence records**, not to the requirements graph: it has no parents and no children, it is not decomposed, it is not verified by test cases, and it is neither a requirement nor a specification. AR therefore **does not extend** any closed list of the standard ([§1.7.5](01-scope.md#1.7.5)): it gives a definite target to the already existing `source.adversarial-review-ref` field rather than introducing a new type of requirements artifact.

#### 7.4.6.1 Identity and multiplicity

The identifier is `AR-NNN`, sequential within the parent TZ and immutable once created (mirroring `ADAPT-NNN`, [§7.4.1.4](#7.4.1)). The year is carried by the parent TZ (`TZ-YYYY-NNN`); for a delta-TZ, numbering is scoped to that delta-TZ ([§7.11](#7.11)).

Cardinality is **zero or more** ARs per TZ, exactly as for ADAPT. The review at TZ import is always mandatory and yields the first AR; at derivation stages an AR is issued whenever a review is actually conducted. The unit of granularity is the **review event**: several artifacts derived from one review event without new questions reference **one** AR.

Completeness is ensured not by mandating "an AR at every node" but by the node-provenance invariant:

> Every BR / SR / SPEC references exactly one source of adaptation — `source.adapt` **or** `source.adversarial-review-ref`. The adversarial review is mandatory at TZ import; at derivation stages its outcome is recorded as an AR whenever the review is conducted.

#### 7.4.6.2 Mandatory fields

| Field | Obligation | Meaning |
|---|---|---|
| `id` | REQUIRED | `AR-NNN`; immutable |
| `tz-ref` | REQUIRED | The (delta-)TZ under review |
| `trigger-stage` | REQUIRED | Review stage: `import-tz` / `decompose-br` / `decompose-sr` / `spec` / `tc` (the list is aligned with ADAPT's `trigger-stage`, [§7.8.1](#7.8)) |
| `reviewer.vendor` + `reviewer.model` | REQUIRED | The adversarial reviewer's model; it MUST differ from the primary agent's model ([§7.10.2](#7.10.2)) |
| `primary.vendor` + `primary.model` | REQUIRED | The primary agent's model **at the moment the AR was issued** — the second operand of the §7.10.2 condition. Without it, independence is checkable only at issuance: later there is nothing to compare against, and when the primary model changes the review ceases to be adversarial without any signal. Format as for `ai-provenance.generated-by` ([§4.10.1](04-terms.md#4.10.1)), including the version |
| `verdict` | REQUIRED | `findings-present` or `no-findings` |
| `produces-adapt[]` | Conditional | REQUIRED and non-empty when `verdict: findings-present`; absent or empty when `no-findings` |
| `status` | REQUIRED | `draft` / `issued` / `superseded` |
| `superseded-by` | Conditional | REQUIRED when `status: superseded` |
| `signature.author` + `signature.timestamp` | REQUIRED for `issued` | Substrate capability V6 |

On the `findings-present` outcome the AR body carries only pointers to TZ sections and to the `B-NNN` records it produced; **the findings themselves live in ADAPT and are not duplicated in the AR**. On the `no-findings` outcome the body carries a short justification of the conversion's unambiguity.

#### 7.4.6.3 Lifecycle

```text
draft → issued → superseded
```

| Status | Meaning |
|---|---|
| `draft` | The review is conducted, the verdict is being drawn up; referencing this AR from `source.adversarial-review-ref` is **prohibited** |
| `issued` | The verdict is issued and signed by the reviewer (V6); the record is **immutable** |
| `superseded` | A later review at the same stage displaced the earlier verdict; a terminal state, the record is retained for audit (V1) |

An AR in status `issued` is not edited. Its only exit is `superseded`, when a repeat review at the same stage issues a new verdict: a new AR is then issued and the previous one receives `status: superseded` and `superseded-by`. The supersession model is the one already defined for ADAPT ([§7.6.4](#7.6.4)); no separate quality gate is introduced for AR ([§10.11.1](10-lifecycle-qg.md#10.11.1)).

A `source.adversarial-review-ref` pointing at a non-existent AR, or at an AR in status `draft` or `superseded`, is **fatal**: the gate blocks the change ([§10.11.1](10-lifecycle-qg.md#10.11.1)).

---

## 7.5 ADAPT approval — the Architect's signature

ADAPT is an **internal artifact of interpretation**. Its audience is the engineer, the AI agent, the adversarial reviewer, and the verifier; the client neither sees nor signs it. Obligations towards the client live in ACTZ ([§7.13](#7.13)).

ADAPT moves from status `answered` to `approved` after the **Architect's signature** (an atomic change unit, substrate capabilities V2 + V3 from [§3.3](03-substrate-versioning.md)):

| Signature | Who | What it confirms |
|---|---|---|
| Architect signature | The Architect on the implementer's side | All backward findings are handled (each is in status `resolved` and carries `decided-in` pointing at a clause of a **signed** ACTZ, §7.4.5); the forward interpretation is technically feasible and consistent with the decisions taken |

The substrate-native implementation of the signature is a combination of V3 (diff & review) + V6 (author + timestamp). The concrete mechanism (digital signature / approval process / dual review attestation) is chosen by the implementation and recorded in the conformance manifest ([§3.7](03-substrate-versioning.md#3.7)).

**Why there is no client signature here.** A client signature on ADAPT was a fiction: the client signed an engineering document they did not read and could not assess. The boundary is drawn **by audience**: everything shown to the client and approved is an obligation (ACTZ, dual signature); everything not shown is an interpretation (ADAPT, the Architect's signature). The content of a record is thereby not up for debate: there is no longer any need to argue whether something "is an interpretation or already a change" — it suffices to ask whether the decision was put to the client.

After approval, ADAPT is **immutable** on par with the TZ. Further changes are made only by adding a new artifact with a typed link, through one of three non-overlapping mechanisms: delta-ADAPT for a delta-TZ (§7.6.1), errata for an interpretation error (§7.6.3), or supersession of a previously correct decision (§7.6.4). The frozen ADAPT itself is not edited in any of them.

An erratum that touches no decision of a signed ACTZ (a fix to an interpretation error that does not affect the obligations towards the client) **does not concern** the client and is signed by the Architect alone ([§7.6.3](#7.6.3)).

---

## 7.6 Delta-TZ and delta-ADAPT

### 7.6.1 Preconditions of a delta-ADAPT

A delta-ADAPT follows the same reactive rule of §7.4.1 as the root ADAPT: it is created upon findings during the adversarial review of the delta-TZ. The standard does not set the order of work ([§1.2.1](01-scope.md#1.2.1)); the preconditions are normative:

- **The delta-TZ is registered** in the substrate as a new immutable document (`TZ-YYYY-NNN-delta-N`) and **signed by the client** (V6 author + timestamp) — without this, artifacts referencing the delta are not approved.
- **The adversarial review of the delta-TZ is performed** ([§7.10.2](#7.10.2)) and its verdict is recorded in the substrate with V6 author + timestamp — machine-accessible for audit (hook §10.11.1 reference-validation).
- **With the verdict "findings present"** a delta-ADAPT (`ADAPT-NNN-delta-N`) exists with the fields `parent-adapt: ADAPT-NNN` and `source-tz: TZ-YYYY-NNN-delta-N`: the forward interpretation covers only the sections of the delta-TZ, backward findings are recorded against the delta-TZ, approval is the architect's signature §7.5; the client's decisions on the findings — in new ACTZ with a dual signature. The affected BR / SR / SPEC reference the delta-ADAPT through `source.adapt`.
- **With the verdict "no findings, no clarifications"** no delta-ADAPT is created; the BR / SR / SPEC changed as a result of the delta-TZ reference `source.tz-section: TZ-YYYY-NNN-delta-N` directly.

### 7.6.1bis Example: a trivial delta-TZ

Delta-TZ: "rename the 'username' field on the registration form to 'email'".

1. The client signs `TZ-YYYY-NNN-delta-1`.
2. The adversarial reviewer checks: the term is unambiguous (`email` is a standard engineering term), the scope is clear (one field on one form), there are no questions for the client.
3. Verdict: "no findings, no clarifications"; recorded in the substrate.
4. The delta-ADAPT **is not created**.
5. SR-NN with the behavior "POST /auth/sign-up accepts email" receives an update: `source.tz-section: TZ-YYYY-NNN-delta-1 §1`. The SR `parent` BR-NN does not change.

### 7.6.2 Delta-ADAPT chain

```text
ADAPT-001 (from TZ-YYYY-NNN main)
  └─ ADAPT-001-delta-1 (from TZ-YYYY-NNN-delta-1)
        └─ ADAPT-001-delta-2 (from TZ-YYYY-NNN-delta-2)
              └─ ADAPT-001-delta-3 (from TZ-YYYY-NNN-delta-3)
```

The chain is strictly sequential: applying delta-ADAPTs MUST proceed in order (see the cross-substrate version pin V5 in [§3.3.5](03-substrate-versioning.md#3.3.5)). Renumbering or reordering delta-ADAPTs in the chain is prohibited.

### 7.6.3 Errata for an already approved ADAPT

If, a considerable time after approval, it is discovered that the forward interpretation of the TZ in ADAPT-NNN is wrong, or the resolution of a backward finding is recorded incorrectly — two permissible outcomes:

| Outcome | Artifact |
|---|---|
| The TZ contains an ambiguity discovered late | delta-ADAPT with a new backward-finding record and a client answer |
| Wrong interpretation (an engineer's error) | errata-ADAPT-NNN-M as a separate artifact. If the fix touches a decision of a signed ACTZ (changes an obligation), a **new ACTZ** with a dual signature is REQUIRED; if it does not, the Architect's signature suffices |

In both outcomes the **frozen ADAPT is not edited**. Only the addition of new artifacts with an explicit typed link.

### 7.6.4 Supersession of an approved ADAPT

Delta and errata do not cover one case: a previously accepted decision was **correct**, but requirements formed later **contradict** it under a new understanding. This is not an engineer's error (errata) and not a new contract from the client (delta) — it is the cancellation of a previously correct decision. For it, a third mechanism is introduced — **supersession** (`supersession`).

| Mechanism | When | Nature |
|---|---|---|
| **delta-ADAPT** ([§7.6.1](#7.6)) | a delta-TZ arrived from the client | new source: the contract changed |
| **errata-ADAPT** ([§7.6.3](#7.6)) | the former interpretation was **erroneous** | a correction of what was always wrong |
| **superseding ADAPT** (§7.6.4) | the former decision was **correct**, but requirements formed later contradict it | cancellation of a previously correct decision under a new understanding |

Supersession rules:

1. **Superseding artifact.** A new `ADAPT-NNN` is created with the frontmatter field `supersedes: ADAPT-MMM` and a mandatory `supersession-rationale` referencing the concrete contradicting requirement (`BR` / `SR` / `SPEC` ID) and its source. The superseded ADAPT receives an automatically derived back-reference `superseded-by: ADAPT-NNN` ([§7.8.1](#7.8)).
2. **Signature.** If the superseded decision had a **contractual outcome** (it was recorded as a clause of a signed ACTZ) — supersession REQUIRES a **new ACTZ with a dual signature**: a decision agreed with the client cannot be cancelled unilaterally, and the cancelling protocol sets `superseded-by` on the earlier one ([§7.13.4](#7.13)). If the fix touches no decision of a signed ACTZ, the Architect's signature suffices (by analogy with the errata rule, [§7.6.3](#7.6)). No separate QG is introduced — supersession passes through the same QG-3 ([§7.5](#7.5), [§10.8.5](10-lifecycle-qg.md#10.8)).
3. **State.** The superseded `ADAPT-MMM` moves to the dedicated terminal state **`superseded`** — separate from `frozen` and from `obsolete`, the state through which TR / SPEC / TC become outdated (ADAPT has no `obsolete` state, [§10.8.1](10-lifecycle-qg.md#10.8)). `superseded` is immutable and is **retained** for audit (immutable history, substrate capability V1); it is not deleted. The transition is regulated in [§10.8.5](10-lifecycle-qg.md#10.8).
4. **Redirection of derivatives.** All `BR` / `SR` / `SPEC` with `source.adapt: ADAPT-MMM` MUST be either redirected to the superseding ADAPT or re-derived. A dangling `source.adapt` reference to an ADAPT in status `superseded` is **fatal**: the gate `check-adapt-supersession.js` blocks it ([§10.11.1](10-lifecycle-qg.md#10.11.1)), by analogy with reference-validation.
5. **Additivity.** Like delta and errata — the superseded ADAPT **is not edited**; only a new artifact and the typed link `supersedes` / `superseded-by` are introduced.

Supersession is an extending capability: a single ADAPT without supersessions remains a valid special case, and migration of existing projects is not required.

---

## 7.7 Relationship of ADAPT to other artifacts

### 7.7.1 BR / SR / SPEC reference ADAPT through `source.adapt`

```yaml
# Frontmatter SR (example)
source:
  adapt: ADAPT-001
  adapt-section: "Forward §3"   # forward interpretation; canonical section identifier — Forward §*
  tz-section: "§3.4"            # for traceability; the primary source remains the TZ
```

The `source.adapt` field is conditional: present when the TZ → RENAR conversion required ADAPT; omitted when the adversarial reviewer returned a "no findings" verdict — then the `source.adversarial-review-ref` field is REQUIRED ([§7.4.1](#7.4.1)). The `source.tz-section` field is REQUIRED always (the dual trace chain).

### 7.7.2 The full trace chain

```text
TC-NN  →  verifies SR-12  →  derived from ADAPT-001 §4 (forward interpretation)
                                  │     │
                                  │     └─ resolves B-007 (was: contradiction,
                                  │            answered by client 2026-03-15)
                                  │
                                  └─ interprets TZ-YYYY-NNN §3.4
```

When verifying from a test case, one can reach: (a) the source TZ section, (b) the forward interpretation of that section, (c) the implementer's questions on backward findings, (d) the client's answers, (e) the derived BR / SR / SPEC.

### 7.7.3 TR does not reference ADAPT directly

A TR (task) references SR / SPEC, and those already reference ADAPT. The task implementer **does not access ADAPT directly** — all necessary interpretations are already contained in SR / SPEC. If the implementer discovers an ambiguity in an SR, the root determines the outcome ([§7.4.1.1](#7.4.1)):

- **root in decomposition** (not in the TZ — for example, an imprecise SR wording, a missed `constrained-by[]` link) — resolved by clarifying the SR / SPEC **without** ADAPT;
- **root in the language or intent of the TZ** — a backward finding is registered in ADAPT. If an ADAPT for this TZ already exists, a new record is added; if an ADAPT has not yet been created (the verdict at import was "no findings"), a **new** ADAPT is created at the current stage ([§7.4.1.1](#7.4.1)). This is the regular case, not an exceptional one.

---

## 7.8 ADAPT schema

### 7.8.1 frontmatter (mandatory fields)

```yaml
---
id: ADAPT-NNN                       # immutable; NNN sequential per project
title: "TZ adaptation <name>"
type: ADAPT
trigger-stage: import-tz            # stage that produced the ADAPT (§7.4.1.4):
                                    # import-tz | decompose-br | decompose-sr | spec | tc

source-tz:
  id: TZ-YYYY-NNN
  signed-date: "<ISO-date>"
  signed-by-client: "<name + role>"
  document-version-ref: "<substrate-native version identifier>"   # V5 pin (see §3.3.5)

parent-adapt:                       # for delta-ADAPT
  id: ADAPT-NNN
  delta-tz: TZ-YYYY-NNN-delta-N

supersedes: ADAPT-MMM               # only for a superseding ADAPT (§7.6.4); omitted otherwise
superseded-by: ADAPT-NNN            # auto-derived; set on the superseded ADAPT
supersession-rationale: >           # mandatory if supersedes: is present
  "<reference to the contradicting BR/SR/SPEC ID + its source; rationale for cancellation>"

status: draft | review | asked | answered | approved | frozen | superseded
created: "<ISO-date>"
last-updated: "<ISO-date>"

approval:                            # mandatory for approved
  # client-signature IS ABSENT: an ADAPT is an internal interpretation (§7.5).
  # The client's obligations are carried by an ACTZ (§7.13) with the dual signature.
  architect-signature:               # mandatory for approved
    signed-by: "<name>"
    role: architect
    signed-at: "<ISO-datetime>"

generates-requirements: []           # auto-derived; BR/SR from this ADAPT
generates-specs: []                  # auto-derived; SPEC-* from this ADAPT
open-questions-count: integer        # auto-derived; mandatory 0 for approved
resolved-questions-count: integer

ai-provenance:                       # mandatory if the ADAPT draft was AI-generated
  generated-by: "<vendor>-<model>-<version>@<date>"
  prompt-template: "<template-path>@<version>"
  context-tokens: integer
  output-tokens: integer
  human-edits: boolean               # mandatory true for approved — the Architect reviewed the text (§7.5)
---
```

Note: ADAPT exists in only one mode (the Architect's signature, the full lifecycle of §7.4.5). Reactivity ([§7.4.1](#7.4.1)) is expressed **at the level of creation**: if the adversarial reviewer returned a "no findings, no clarifications" verdict, ADAPT is not created at all, rather than being created in a simplified form.

### 7.8.2 Body structure (mandatory sections)

The mandatory sections of the ADAPT body:

1. **Summary** — 3–5 paragraphs for one-page reading by the Architect and the adversarial reviewer ([§7.5](#7.5)).
2. **Term mapping** — a "client → engineer" table.
3. **Forward interpretation (the Forward section)** — a section for each TZ section with the mandatory elements from §7.4.3.
4. **Backward findings** — all records with the lifecycle from §7.4.5.
5. **Backward-findings summary** — a statistical table by categories and statuses.
6. **Derived-artifacts table** — an auto-derived list of BR / SR / SPEC.
7. **ADAPT change history** — substrate-native, auto-generated.

Optional sections are at the implementation's discretion and are not regulated.

---

## 7.9 Quality Gates for ADAPT

ADAPT has a dedicated state machine. Details in [chapter 10 §10.8](10-lifecycle-qg.md#10.8). Brief summary:

| Gate | Precondition | Postcondition |
|---|---|---|
| QG-ADAPT-draft | ADAPT created; frontmatter present | The forward interpretation covers all TZ sections |
| QG-ADAPT-review | Forward interpretation filled in; initial backward findings in `open` | All backward findings in `open` or `asked-to-client` |
| QG-ADAPT-asked | All backward findings in `asked-to-client`; the questions have been put to the client as part of an ACTZ (`status: sent`) | Awaiting the protocol's signing |
| QG-ADAPT-answered | All backward findings in `answered`; resolution begun | Ready for finalization |
| QG-ADAPT-approve | All backward findings in `resolved` and carry a `decided-in` pointing to a signed ACTZ; the Architect's signature is ready | ADAPT immutable; generation of BR / SR / SPEC is permitted |
| QG-ADAPT-frozen | `approved` | Further changes — only through delta-ADAPT or errata |

The substrate hooks ([§3.3.3 V3](03-substrate-versioning.md#3.3.3), [§3.3.5 V5](03-substrate-versioning.md#3.3.5)) MUST:

- Block the transition to `approved` when `open-questions-count > 0`.
- Block the creation of BR / SR / SPEC with `source.adapt` on an ADAPT in a status below `approved`.
- Recompute `open-questions-count` / `resolved-questions-count` after each change.

---

## 7.10 ADAPT and AI generation

### 7.10.1 The AI agent creates a draft ADAPT

On a "findings present" verdict from the adversarial reviewer ([§7.4.1](#7.4.1)), the AI agent creates a **draft ADAPT**: the forward interpretation by section, an attempt to detect contradictions / gaps / terminology ambiguities, a first version of the term mapping. This draft is a starting point for the Architect, not the final artifact.

### 7.10.2 The adversarial reviewer

Application of the **adversarial review principle** (a separate AI agent-critic with a **different model**; the procedure — [guide/07 §3.5](../../guide/en/07-failure-modes.md)): it looks for what the primary agent missed — missing backward findings. If the adversarial reviewer finds at least three serious new findings, the primary draft is returned for rework.

### 7.10.3 The client does not communicate with the AI directly

Questions on backward findings are aggregated by the Architect into a human format **before being sent to the client**. The Architect MAY remove duplicates, rephrase into the client's language, merge related ones. The client sees a prepared list of questions, not the raw output of the AI agent. The client's answer (in any form: text, video call, letter) is transcribed by the Architect into ADAPT with an indication of the channel and authentication.

---

## 7.11 Storage scheme

ADAPT documents are stored in the `adapt/` subfolder of the requirements substrate:

```text
[project]/
  adapt/
    ADAPT-001-main.md
    ADAPT-001-delta-1.md
    ADAPT-001-delta-2.md
    ADAPT-002-main.md
    errata/
      errata-ADAPT-001-1.md
  ar/
    AR-001.md
    AR-002.md
  tz/
    TZ-YYYY-NNN.md
    TZ-YYYY-NNN-delta-1.md
```

Adversarial-review records ([§7.4.6](#7.4.6)) are stored in the `ar/` subfolder. They exist even when no ADAPT was created (the `no-findings` verdict) — hence the subfolder is independent of `adapt/`.

The concrete file structure on a concrete substrate is substrate-specific (see [guide/03](../../guide/en/03-tool-guide-git.md) for distributed VCS; [guide/04](../../guide/en/04-document-store-substrate.md) for a document-oriented store).

---

## 7.12 The relationship of the TZ and the RENAR description

### 7.12.1 Two languages with a variable distance

The TZ and the RENAR description are two different artifacts with different natures:

| Parameter | **TZ** | **RENAR description** |
|---|---|---|
| Target reader | The client (human) | The AI agent ([§0.2.1](00-introduction.md#0.2.1)) and the human verifier |
| Language | The client's language (business domain, contractual vocabulary) | The requirements language (canonical IDs, closed lists, formal frontmatter and graph links) |
| Completeness | Allows defaults, incompleteness, ambiguity of wording | Allows no defaults; completeness is the lower bound of machine-enforceability ([§0.3](00-introduction.md#0.3)) |
| Evolution | Incremental: the first TZ describes the system completely, the subsequent ones — only the delta ([§7.6](#7.6)) | Non-incremental: before changing the system, a new description set version `N.M+1` is approved ([§10.5](10-lifecycle-qg.md#10.5)), and only then does the AI agent make changes in the implementation |
| Status after signing | An immutable contractual document ([§7.4.2](#7.4.2)) | Evolves through an approved delta-ADAPT or (when no ADAPT is created, §7.4.1) through source.tz-section directly |

The distance between the two languages is **variable**. Sometimes TZ sections are worded unambiguously enough for the AI agent to convert them into BR/SR/SPEC without loss of meaning. Sometimes the reverse: the TZ contains a contractual phrase that has several engineering interpretations, or omits behavior without which implementation is impossible.

### 7.12.2 ADAPT — a reactive bridge between languages

ADAPT exists only when a gap arises between the languages. Its forward interpretation (forward, [§7.4.3](#7.4.3)) is a **translation** of concrete TZ sections from the client's language into the requirements language. The backward findings (backward, [§7.4.4](#7.4.4)) are a formalization of the fact that, during translation, gaps, ambiguities, or contradictions were discovered that require agreement with the client.

Three consequences follow from this nature:

1. **ADAPT is a reactive artifact by design.** The existence of a gap between the languages is a necessary condition for creation ([§7.4.1.1](#7.4.1)); a formal ADAPT without content loses its meaning for audit and devalues the other ADAPTs.
2. **The adversarial reviewer is the only one who declares the absence of a gap.** "ADAPT is not needed" is a recorded verdict by a different model ([§7.10.2](#7.10.2), [§7.4.1.3](#7.4.1)), not a silent assumption by the Architect.
3. **The form of ADAPT is the only one.** It is created in the full form (the Architect's signature §7.5, all body sections [§7.8.2](#7.8.2), the full lifecycle [§7.4.5](#7.4.5)); intermediate "light" forms do not exist.

### 7.12.3 Why the RENAR description is always complete

The RENAR description is non-incremental by design: the AI agent ([§0.2.1](00-introduction.md#0.2.1)) is unable to reliably "fill in" changes relying only on the delta. A full new version of the RENAR description is the only form that guarantees that:

- machine-enforceable invariants (completeness, graph consistency, lifecycle states) are applicable to the description **as a whole**, not to the diff;
- the adversarial reviewer works on the full artifact, not on the delta;
- the subsequent steps (decomposition, TC generation, implementation — `guide/00-quickstart §"The two regular scenarios"`) are performed on the up-to-date full picture of the system.

A delta-TZ is incremental at the **source** level (the contractual side); the full new version of the RENAR description — the next set version ([§10.5](10-lifecycle-qg.md#10.5)) — is assembled by the AI agent from the parent RENAR + either the delta-ADAPT (if it was created) or directly accounting for the delta-TZ through `source.tz-section`.

### 7.12.4 Relationship to the Source-of-Truth inversion

The TZ is a contractual artifact but **not** the Source of Truth about system behavior ([§2.3](02-methodology-positioning.md#2.3)). The Source of Truth is the RENAR description (BR / SR / SPEC / TR / TC). The TZ is the source from which the RENAR description is derived **either** through ADAPT (when there is a gap) **or** directly through `source.tz-section` (when there is no gap); code is a derived artifact of the implementation of the RENAR description. ADAPT and §7.12 fix the dual inversion: the contractual source (TZ) → the Source of Truth (RENAR description) → code. ADAPT is embedded in the chain reactively, when the bridge between the languages is needed.

---

## 7.13 ACTZ — the TZ Clarification Protocol

### 7.13.1 Purpose and the boundary with ADAPT

**ACTZ** (`ACTZ-NNN`, Agreed Clarification of TZ; in contractual usage — "**TZ Clarification Protocol No. N**") is an artifact of the **contractual circuit**: a batch of questions, proposals, and decisions put to the client and signed by both parties.

The boundary between ACTZ and ADAPT is drawn **by audience**, not by the content of a record:

> **Everything shown to the client and approved is an obligation. Everything not shown is an interpretation.**

| | **ADAPT** — interpretation | **ACTZ** — approval |
|---|---|---|
| Content | Translation of the TZ language into the engineering one, term mapping, filled-in scenarios, backward findings, forward interpretation | Questions, proposals, and decisions put to the client. Worded in the language of obligations ("the button is named X"), not of interpretation |
| Audience | Engineer, AI agent, adversarial reviewer, verifier | The client and the parties to the contract |
| Signature | The Architect's only ([§7.5](#7.5)) | **Dual**: client + implementer |
| Weight | Internal circuit | **Contractual** |

The criterion is binary and checkable: the fact "put to the client and signed" either holds or does not. The argument over the materiality of an edit ("is this a clarification or already a change?") disappears by construction.

**The form of a clause.** An ACTZ clause is an obligation to the client and is worded as an obligation, without interpretive hedges; a question requiring the client's decision is put as a question ([§15.6.4](15-description-language.md#15.6.4)).

### 7.13.2 Origin and multiplicity

An ACTZ arises in two ways, both regular:

1. **From a backward finding.** A finding that requires a client decision ([§7.4.4](#7.4.4)) is put into an ACTZ; once signed, the record receives `decided-in: ACTZ-NNN §M` ([§7.4.5](#7.4.5)).
2. **On the client's initiative, with no finding.** The client proposes a decision on their own ("let us rename the button"). Such an ACTZ references only the TZ; it has no parent finding. Once signed, the decision MUST be reflected in ADAPT — the interpretation reacts to the protocol.

Cardinality:

| Relation | Cardinality | Explanation |
|---|---|---|
| TZ : ACTZ | **1 : 0..N** | Zero protocols is lawful: a conversion with no questions and no client initiatives produces no documents |
| ADAPT : ACTZ | **1 : 1..N** | The findings of a single ADAPT are closed by several protocols across rounds of clarification |

**A late ACTZ is the regular case, not an exception.** A protocol may arise at **any stage in the life of the engagement**, including from triaging the customer's remarks after a demonstration. The rule is stage-agnostic — symmetrically to the reactive ADAPT ([§7.4.1](#7.4.1)).

### 7.13.3 Mandatory fields

| Field | Obligation | Meaning |
|---|---|---|
| `id` | REQUIRED | `ACTZ-NNN`; sequential within the parent TZ; immutable |
| `tz-ref` | REQUIRED | The TZ the protocol relates to |
| `resolves[]` | Conditional | The `B-NNN` records in ADAPT that the protocol closes; absent for an ACTZ raised on the client's initiative |
| `decisions[]` | REQUIRED | Decision clauses; each is worded in the language of obligations and carries a stable number `§M` |
| `annexes[]` | Conditional | New versions of TZ annexes approved by this protocol ([§7.13.5](#7.13.5)) |
| `status` | REQUIRED | `draft` → `sent` → `signed` → `superseded` |
| `client-signature` | REQUIRED for `signed` | The client or a client representative with authority (V6: author + timestamp) |
| `vendor-signature` | REQUIRED for `signed` | The implementer (V6) |
| `superseded-by` | Conditional | REQUIRED when `status: superseded` |

### 7.13.4 Lifecycle

```text
draft → sent → signed → superseded
```

| Status | What it means |
|---|---|
| `draft` | The protocol is being prepared; not yet put to the client. Referencing it from `decided-in` is **prohibited** |
| `sent` | Put to the client, signing is awaited |
| `signed` | Signed by both parties; **immutable**, carries contractual weight |
| `superseded` | A later signed ACTZ cancelled the decision; a terminal state, the record is retained for audit (V1) |

A signed ACTZ **is not edited**. A correction is made only by a new ACTZ cancelling the earlier decision (`superseded-by`); the supersession model is the one already defined for ADAPT ([§7.6.4](#7.6.4)).

What is put to the client is a **prepared list of decisions**, not the raw output of the AI agent ([§7.10.3](#7.10.3)): the Architect aggregates and rewords the questions.

### 7.13.5 TZ annexes

Annexes (mapping tables, reference lists, mockups) are an **integral part of the TZ**; they are not a separately addressable entity. A clarification to an annex is formalized as a **new version of the annex attached to an ACTZ** and approved by the same dual signature (`annexes[]`).

This closes the case where the client's decision is not a text but a new version of a table: the former version remains immutable (V1), and the new one enters the effective TZ through the protocol that signed it.

---

## 7.14 The effective TZ — the acceptance benchmark

The **effective TZ** is the benchmark against which the system is delivered and accepted:

> **Effective TZ = the initial TZ (annexes included) + all signed ACTZ protocols.**
> On a discrepancy between documents, the **later signed** document prevails.

Acceptance tests AT ([§9.19](09-test-cases.md#9.19)) are derived against the effective TZ; the acceptance report is built from it as well.

**ADAPT is not part of the acceptance benchmark.** The internal interpretation is taken out of the contractual circuit entirely — the client answers only for what they signed and understood. This is the very point of the split: acceptance becomes legally clean.

**QG-4** ([§10.4.2](10-lifecycle-qg.md#10.4.2)) is a separate **optional** gate on the business result (`achievement ≥ 80%`). It **does not replace** acceptance against the effective TZ and is not conflated with it: a business goal MAY be met while the contract is not conformed to, and vice versa.

---

## 7.15 Relationship to other chapters

| Chapter | Relationship |
|---|---|
| [02 Methodology positioning](02-methodology-positioning.md) | ADAPT is a consequence of Statement 2 (two-way adaptation instead of "throwing the specification over the wall") |
| [06 Requirements hierarchy](06-requirements-hierarchy.md) | BR / SR reference ADAPT through `source.adapt` |
| [08 Specifications](08-specifications.md) | SPEC-* also reference ADAPT through `source.adapt` |
| [09 Test cases](09-test-cases.md) | TC, through SR / SPEC, returns to ADAPT for the full trace chain |
| [10 Lifecycle and QG](10-lifecycle-qg.md) | the ADAPT state machine + QG-ADAPT-* gates |
| [03 Substrate versioning](03-substrate-versioning.md) | The Architect's signature on an ADAPT and the ACTZ dual signature (V6) + atomic approval (V2 + V3); immutability after approved (V1); delta-ADAPT through V4 |
| [13 Conformance](13-conformance.md) | The adversarial review of each TZ is a mandatory clause; ADAPT is created reactively ([§7.4.1](#7.4.1)) |
