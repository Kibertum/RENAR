---
title: "Comparison with SAFe"
description: "Mapping RENAR ↔ SAFe 6.0: WSJF, PI Planning, ART coordination, RACI lifecycle, Built-in Quality."
order: 5
lang: en
version: "1.0"
---

# 05. Comparison with SAFe

> Mapping RENAR (the standard for **requirements**) onto SAFe 6.0 (the standard for **scaled agile coordination**). The documents are compatible but have a different scope: SAFe regulates *how* teams coordinate work at scale; RENAR regulates *what* a requirement is and *how* it is verified. This chapter is for teams that already run on SAFe (an enterprise multi-team ART is the typical case) and want to keep their SAFe ceremonies while adding RENAR artifacts as the primary Source of Truth for requirements.
>
> **Prerequisites:** familiarity with [RENAR Core](../../core/en/renar-core.md) — the conceptual overview written for a human reader and basic SAFe 6.0 terminology (Epic / Capability / Feature / Story, WSJF, PI, ART).

---

## 1. Scope: what RENAR regulates, what SAFe regulates

RENAR and SAFe overlap at the level of *work artifacts* (Feature, Story) but solve different problems.

| Aspect | SAFe 6.0 | RENAR |
|---|---|---|
| Standard type | Scaled Agile coordination framework | Requirements-engineering standard |
| Primary artifact | Epic / Capability / Feature / Story | TZ → ADAPT → BR → SR → SPEC → TC |
| What it regulates | Cadence, roles, ceremonies, flow | Schema, lifecycle, verifiability, drift control |
| Artifact substrate | Choice of task-management tool (Jira / Rally / ADO / other) | Substrate-agnostic; normatively — the **V1–V6** capabilities ([glossary §2.7](../../reference/en/01-glossary.md#2.7)) |
| Quality checkpoints | Definition of Done at the Feature level | `QG-0` (readiness to start) + `QG-2` (verified) |
| AI-nativeness | Does not regulate how to work with AI | AI as a full participant (adversarial expertise, evaluating scenarios, `judge` models) |

**Key principle:** SAFe and RENAR are compatible. SAFe says "how to coordinate teams on an ART", RENAR says "what must be true about each requirement for it to count as `verified`". A Feature in SAFe ≡ an SR in RENAR — this is **one and the same artifact**, described from different angles.

---

## 2. Master mapping table

| SAFe artifact | RENAR artifact | Where it lives | Owner | Acceptance criteria |
|---|---|---|---|---|
| Strategic Theme | (outside RENAR scope — corporate strategy) | strategic docs | Executive | business level |
| Portfolio Epic | A group of BR for one system | `<system>.req/br/` aggregated | Lean Portfolio Mgmt | All BR of the group belong to a verified set version |
| Capability | Subsystem BR (if the subsystem has its own stakeholder) | `<subsystem>.req/br/` | Solution Architect | All subsystem BR belong to a verified set version |
| Program Epic | Optionally — a large initiative inside the ART, aggregated SR | `<system>.req/sr/` aggregated | Product Manager | The target outcome metric achieved |
| Feature | SR (or a linked group of SR) | `<subsystem>.req/sr/` | Product Owner | TC from `verified-by` green with `last-run.set-version` = the current set version (QG-2) |
| Story | TR (Task Requirement) | `<subsystem>.req/tr/` or task tracker (Jira, Linear, GitLab Issues) | Team | All AC met, evidence recorded, code merged |
| Enabler Epic | SPEC-AI / SPEC-ARCH / SPEC-OPS | `<system>.req/specs/` | Architect | Eval metrics within thresholds; baseline recorded |
| Spike | A research TR + a Decision in the decision log | `<subsystem>.req/tr/` + decision log | Team | Decision recorded and linked to the originating SR |
| Defect | TR with `parent: SR-NN` (the violated SR) | task tracker + auto-derived `children[]` in the SR | Team | Negative TC passes, regression test added |

Forbidden mappings: INT-SR is a deprecated term ([§4.14.1 Terms](../../standard/en/04-terms.md#4.14.1)), replaced by `SR` with `constrained-by: [SPEC-INT-N]`. See §6 below.

---

## 3. WSJF prioritization for RENAR

SAFe uses **WSJF (Weighted Shortest Job First)** as its prioritization framework. RENAR adapts it for the BR / SR level.

### 3.1 Formula

```text
WSJF = (User-Business Value + Time Criticality + Risk Reduction & Opportunity Enablement) / Job Size
```

Each component is rated on a 1-20 scale (modified Fibonacci); WSJF is a relative metric and is only meaningful when comparing BR/SR within a single backlog.

### 3.2 BR frontmatter extension

```yaml
---
id: BR-12
title: "Reduce employee registration time to < 2 minutes"
priority: must
prioritization:
  framework: WSJF
  components:
    user-business-value: 10
    time-criticality: 8
    risk-reduction-opportunity: 6
    job-size: 5
  wsjf-score: 4.8                # auto-calculated: (10+8+6)/5
  prioritized-at: 2026-05-03
  prioritized-by: "@product-owner"
---
```

The `prioritization` field is OPTIONAL in Core RENAR and REQUIRED on an ART that applies SAFe.

### 3.3 When it applies

- **Mandatory** for BR with `priority: must` in projects coordinated through an ART.
- **Recommended** for all BR in a backlog that goes through PI Planning.
- **Not applied** for SR — an SR inherits the priority of its parent BR. Reshuffling SR within a single BR is the Product Owner's decomposition task, not WSJF.

### 3.4 Alternatives

If a team does not use SAFe / WSJF, RENAR permits other frameworks:

- **MoSCoW** (Must / Should / Could / Won't) — the simplest one, for small projects. Already present as the `priority` enum in RENAR Core.
- **RICE** (Reach × Impact × Confidence / Effort) — for product-driven teams (typically B2C).

RENAR regulates **the field and its schema**; the choice of framework is up to the project. See also [reference/02-schemas.md](../../reference/en/02-schemas.md) for the permitted values of `prioritization.framework`.

---

## 4. PI Planning integration

### 4.1 What a PI is

A **Program Increment (PI)** is a fixed time box (usually 8-12 weeks) in SAFe during which an ART commits to a set of Features from the shared backlog. PI Planning is a two- or three-day event before each PI, where teams jointly fix their commitment.

### 4.2 Where RENAR artifacts enter the PI flow

```text
[Before PI Planning]              [PI Planning event]                [Iteration 1..N]                 [System Demo / I&A]
       │                                  │                                  │                                │
       ▼                                  ▼                                  ▼                                ▼
 Backlog: SR with status      Teams pull SR                 SR → TR in the tracker        SR with status
 approved, WSJF-               (= Features) into the next    Story implementation          verified
 prioritized                   PI                            TC run on every
       │                       SR → TR decomposition         merge
       │                       Identify cross-team           QG-2 check
       │                       dependencies via              on SR completion
       │                       SPEC-INT
       │                                                                                  RENAR metrics feed
       └──────────────────────────────────────────────────────────────────────────────►  into SAFe Inspect & Adapt
```

### 4.3 RENAR artifacts in each ceremony

| SAFe ceremony | RENAR input | RENAR output |
|---|---|---|
| Backlog refinement | BR / SR in the draft or in the approved set version | A refined ADAPT, an updated WSJF |
| PI Planning | WSJF-sorted SR backlog, ADAPT docs | Commitment to SR in the PI; SPEC-INT for cross-team |
| Iteration Planning | SR + decomposed TR | TR in `approved` |
| System Demo | SR of a verified set version | Evidence from the verification entry and the TC last-run |
| Inspect & Adapt | RDLT, Coverage Velocity, Hallucination Rate metrics | Backlog / process adjustments |

---

## 5. ART coordination and roles

### 5.1 SAFe roles ↔ RENAR responsibilities

| SAFe role | RENAR responsibility |
|---|---|
| **RTE (Release Train Engineer)** | Coordinates cross-team dependencies via SPEC-INT; owner of the PI Objectives ↔ RENAR metrics mapping |
| **Product Manager** | Owner of portfolio Epics = groups of BR at the system level |
| **Product Owner** | Owner of Features = SR at the team level; accountable for QG-0 (version approval) and QG-2 (version verification) |
| **System Architect** | Owner of SPEC-* (especially SPEC-ARCH, SPEC-INT); consulted during BR → SR decomposition |
| **Tech Lead** | Accountable for QG-2 at the SR level; owns the team's TR backlog |
| **Business Owner** | Approver of BR / ADAPT from the business side |
| **Solution Architect** | Owner of BR at the subsystem level (when the subsystem has its own stakeholder) |
| **Scrum Master** | Owner of ceremonies; does not own RENAR artifacts directly |

### 5.2 Cross-team coordination

In a typical SAFe organization with several teams on a single ART:

- **Each team** owns its SR / TR in `<subsystem>.req/`.
- **The RTE / Solution Architect** own the SPEC-INT in `<system>.req/specs/int/` — the shared integration contracts between subsystems.
- **Changing a SPEC-INT** requires cross-team agreement (QG-0 from every affected team).

**Rule:** ART-level roles (the RTE) **do not edit** SR in subsystems directly. Coordination flows through SPEC-INT, which is explicitly `constrained-by` for every affected SR.

---

## 6. Cross-team dependencies via SPEC-INT

When a Feature in one subsystem blocks / depends on another:

### 6.1 An SR with a dependency

```yaml
# In <subsystem-a>.req/sr/SR-05.md
---
id: SR-05
parent: BR-12
title: "User registration via corporate email"
constrained-by:
  - id: SPEC-INT-01
    ref: "specs/int/SPEC-INT-01-auth-handshake.md"
verified-by:
  - TC-23
  - TC-24
---
```

### 6.2 SPEC-INT as a contract

```yaml
# In <system>.req/specs/int/SPEC-INT-01-auth-handshake.md
---
id: SPEC-INT-01
type: SPEC-INT
title: "Auth handshake between Subsystem A and Subsystem B"
version: 1.2
participants:
  - subsystem: "subsystem-a"
    role: "client"
  - subsystem: "subsystem-b"
    role: "provider"
verified-by:
  - TC-INT-01    # contract test
---
```

### 6.3 Breaking changes in SPEC-INT

Any change to `SPEC-INT.version` with breaking semantics ([§4.11 Drift classes](../../standard/en/04-terms.md#4.11)) requires:

1. An ADAPT-level discussion (why we are changing it).
2. Agreement from every `participant` (QG-0 from each team).
3. A migration plan for existing implementors (how the old SR will stay valid or be adapted).

The RTE **MUST** check SPEC-INT consistency across subsystems regularly (usually once per sprint) — this is part of Inspect & Adapt and feeds into the conformance self-assessment ([§13 Conformance](../../standard/en/13-conformance.md)).

---

## 7. Definition of Done at each level of the hierarchy

DoD is the set of **formal** conditions that are checked automatically (substrate hooks). Each level of the hierarchy has its own DoD.

| Level | RENAR artifact | DoD criterion |
|---|---|---|
| Strategic Theme | — | (outside RENAR scope) |
| Portfolio Epic | A group of BR | All BR of the group — in a verified set version, KPI impact confirmed via an outcome metric |
| Capability | Subsystem BR | All BR — in a verified version; outcome metric measured |
| Feature | SR | QG-2 passed: every TC from `verified-by` has `last-run.result = pass` with `last-run.set-version` = the set version |
| Story | TR | All AC met, evidence recorded, code merged, automated test exists |
| Enabler | SPEC-AI / SPEC-ARCH / SPEC-OPS | Eval runs cleared the thresholds (for SPEC-AI); baseline recorded (for all) |

**Key point:** at the Feature level the DoD in SAFe **coincides** with QG-2 in RENAR. There are not two different "definitions of done" — it is one and the same gate, described from different angles.

---

## 8. Built-in Quality ↔ RENAR mechanisms

The SAFe Built-in Quality principle: quality is built into the process, not ad-hoc. RENAR implements Built-in Quality through normative mechanisms:

| SAFe Built-in Quality practice | RENAR mechanism |
|---|---|
| Continuous Integration | Substrate hooks + CI on every change in requirements ([§13 Conformance](../../standard/en/13-conformance.md)); reconciliation hook (drift detection, [§4.11](../../standard/en/04-terms.md#4.11)) |
| Test-First | TC norms are created **before** implementation and approved in the same set version as the SR; QG-0 of the set requires a pair of TC norms for every statement |
| Refactoring | The continuous reconciliation hook ([§2.4.2](../../standard/en/02-methodology-positioning.md#2.4.2)); detects drift between the requirements and the code |
| Pairing / Mobbing | AI generator + AI critic ([§5.2 Roles](../../standard/en/05-roles.md#5.2)) — pair generation/review as a normative role |
| Definition of Done | QG-2 as a formal gate, checked automatically ([§10 Lifecycle and QG](../../standard/en/10-lifecycle-qg.md)) |
| Version Control | The **V1** capability (immutable history) without tying to a class of storage environment |
| Automation | The V2-V6 capabilities — all verifications are automatable |

RENAR **does not prescribe** the instrument (Jenkins / GitLab CI / GitHub Actions / Tekton) — only **what** must be checked (the capability), not **how**.

---

## 9. RACI of an artifact lifecycle (with SAFe roles)

The full lifecycle of a RENAR artifact with responsibility distributed across SAFe roles.

| Activity | Responsible | Accountable | Consulted | Informed |
|---|---|---|---|---|
| TZ import | AI agent | System Architect | Business Owner | Team, RTE |
| TZ → ADAPT decomposition | AI generator | System Architect | Stakeholder, AI critic | RTE, Team |
| Decomposition → BR | AI generator | Product Owner | Business Owner, AI critic | RTE, Team |
| WSJF prioritization | Product Manager | Product Owner | RTE, Stakeholder | Team |
| BR → SR decomposition | AI generator | System Architect | AI critic | Team, RTE |
| SR → SPEC-* decomposition | System Architect | System Architect | AI critic, Tech Lead | Team |
| TC generation | AI agent | Test Architect | — | Team |
| QG-0 approval | System Architect | Tech Lead | AI critic | Business Owner, RTE |
| SR selection for the PI | Product Owner | RTE | Team (capacity) | Stakeholder |
| SR → TR decomposition | Team | Product Owner | Tech Lead | RTE |
| TR implementation | Developer | Tech Lead | — | Team |
| TC run (automated) | Substrate hook | — | — | Team |
| QG-2 (version verified) | System Architect | Tech Lead | — | Business Owner, RTE |
| System Demo | Team + RTE | Product Owner | Stakeholder | RTE, Executive |
| delta-TZ approval | System Architect + Stakeholder | Product Owner | AI impact analysis, RTE | Team |
| Spot-check of 5 TC (audit) | System Architect | — | — | RTE |
| Reconciliation MR | AI reconciler agent | System Architect | — | Team |

---

## 10. PI Objectives ↔ RENAR metrics

PI Objectives are SMART outcomes for the quarter. RENAR metrics ([§12 Metrics](../../standard/en/12-metrics.md), [reference/02-schemas.md](../../reference/en/02-schemas.md)) feed into PI Objectives:

| PI Objective (example) | RENAR metric |
|---|---|
| "Reduce time from TZ signing to first commit to < 2 days" | **RDLT** (Requirement Decomposition Lead Time) |
| "Reach Coverage Velocity ≥ 60% per sprint" | **Coverage Velocity** (% of SR with `status: verified` per sprint) |
| "Lower the Hallucination Rate in new requirements to < 2%" | **Hallucination Rate** (% of AI-generated statements rejected at review) |
| "0 disputed requirements at acceptance in this PI" | **Dispute Rate at Acceptance** |
| "Drift detection — all SR consistent with code within 24 hours of merge" | **Reconciliation Findings per Week** ([§12.3.10](../../standard/en/12-metrics.md#12.3.10)) |

This turns RENAR from a "standard for documents" into a **measurable contribution** to ART success. The metrics feed automatically into Inspect & Adapt; the RTE uses them in the retrospective at the close of the PI.

---

## 11. What to keep from SAFe, what to replace

For teams migrating to RENAR from an already-running SAFe process:

### 11.1 Keep as is

- **Cadence** (PI, Iterations) — RENAR does not regulate time; use your own rhythm.
- **Ceremonies** (PI Planning, System Demo, I&A, Daily Standup) — RENAR artifacts fit transparently into these events.
- **WSJF** — keep it for prioritizing BR / SR; it fits into `prioritization.framework: WSJF`.
- **ART, RTE, Scrum Master** — the structure and roles are preserved.
- **PI Objectives** — keep them; the mapping onto RENAR metrics (§10) makes them measurable.

### 11.2 Replace with the RENAR equivalent

- **Feature description in Jira / Rally / ADO** → an SR with full frontmatter in `<subsystem>.req/sr/`. The tracker record becomes a **mirror** of the RENAR artifact, not the primary source.
- **Acceptance criteria in a Feature** → `verified-by: [TC-NN]` with automatically checked TC.
- **Definition of Done on a Feature** → QG-2 (no two different DoDs — it is one gate).
- **Integration agreements between teams** → SPEC-INT (replaces the INT-SR from older SAFe implementations).
- **Architecture Decision Records (ADR)** → SPEC-ARCH / SPEC-AI / SPEC-OPS (one of the 12 SPEC types, [§4.4](../../standard/en/04-terms.md#4.4)).

### 11.3 Add (new in RENAR)

- **ADAPT** — the bridge artifact between the TZ and the requirements hierarchy. SAFe has no direct analog; it is created **reactively** ([standard/07 §7.4.1](../../standard/en/07-adapt.md#7.4.1)) — only when the adversarial reviewer returns a "findings present" verdict. On a "no findings" verdict no ADAPT is created, and BR / SR / SPEC are derived from the TZ directly via `source.tz-section` + `source.adversarial-review-ref`. What is mandatory is the adversarial review itself, not the ADAPT.
- **The AI critic role** — adversarial review of AI generation. Not regulated in SAFe; in RENAR Core it is mandatory for all AI-generated artifacts.
- **Reconciliation (drift detection)** — continuous reconciliation of requirements against the implementation (relies on V5 — end-to-end version pinning). In SAFe it is a manual reconciliation; in RENAR it is a normative mechanism.

---

## 12. Negative: what this chapter does not claim

- **RENAR is not a replacement for SAFe.** RENAR regulates *requirements*, SAFe regulates *work coordination*. A team can run on RENAR without SAFe (a small project, a single team) or with SAFe (an enterprise multi-team ART scope).
- **RENAR is not a planning standard.** Cadence, capacity planning, velocity tracking — all outside the scope. RENAR says "what must be true about a requirement", not "when the team must finish it".
- **RENAR does not forbid other frameworks.** WSJF, MoSCoW, RICE — all compatible. RENAR fixes **the field** `prioritization.framework`, not the choice of framework.
- **RENAR does not regulate the Jira / Rally / ADO workflow.** A tracker-level implementation is a substrate detail; the normative source is `<subsystem>.req/`.
- **RENAR does not prescribe the PI as mandatory.** A team can run on continuous flow without a PI; in that case sections 4 and 10 of this chapter become informative.

---

## 13. Resolved decisions for v1.0

- **`priority: must` does NOT require a WSJF score, even in SAFe projects.** Per §12 of this chapter: RENAR fixes `prioritization.framework`, it does not prescribe the choice of framework. `priority: must` is a RENAR MoSCoW marker ([reference/02-schemas](../../reference/en/02-schemas.md)), independent of WSJF. WSJF is an optimization for SAFe teams, not a normative RENAR requirement.
- **Feature/Capability/Story mapping is substrate-specific.** RENAR regulates the closed list of requirement types (BR/SR/TR + 11 SPEC) and does not introduce SAFe Feature levels. Projects that apply SAFe fix the mapping in a substrate-native `safe-mapping/` manifest (see [reference/02-schemas](../../reference/en/02-schemas.md) — informative extension).
- **PI Objectives are informative, outside RENAR scope.** The PI is a SAFe coordination artifact; RENAR does not regulate it. If a team wants traceability, an informative `cross-link.pi-objective-id` field in the BR frontmatter is recommended — it is not conformance-gating, but it eases RTE-side queries.
- **Inspect & Adapt: metrics are automated substrate-natively.** Per [§10.13](../../standard/en/10-lifecycle-qg.md#10.13) Logging + [§12](../../standard/en/12-metrics.md) — the substrate MUST surface COVERAGE / audit-trail substrate-natively. The RTE uses the same data through the query API; manual extraction is an anti-pattern (drift).

### 13.1 Deferred to v1.1 (phase 8 backlog)

- **An AI heuristic for estimating the WSJF Job Size field** (a 1–20 scale by the number of AC, `TC` complexity, and code surface area). Not regulated in v1.0; specific to the SAFe–RENAR binding. The extended informative mapping — [reference/11](../../reference/en/11-external-standards-mapping.md).

---

## 14. Relationship to other chapters

- [00-quickstart](00-quickstart.md) — the basic RENAR cycle without the SAFe overlay.
- [01-walkthrough](01-walkthrough.md) — a full example on a small-scale project (without PI Planning).
- [reference/01-glossary](../../reference/en/01-glossary.md) — the exact semantics of BR / SR / SPEC / TR / TC.
- [reference/02-schemas](../../reference/en/02-schemas.md) — frontmatter schemas, including `prioritization`.
- [standard/04-terms](../../standard/en/04-terms.md) — normative definitions; the mapping onto SAFe is fixed in [§4.13](../../standard/en/04-terms.md#4.13).
- [standard/08-specifications](../../standard/en/08-specifications.md) — the closed list of 12 SPEC types, including SPEC-INT.
