---
title: "Conformance"
order: 13
lang: en
---
# 13. Conformance

> **Part of the RENAR Standard v1.1** · [← Table of contents](README.md)

> **Dense chapter:** start with the [reference/08](../../reference/en/08-conformance-self-assessment.md) kit; the MVR↔§13.3 bijection is there too, in §1.

## 13.1 How to prove conformance

Saying "we do everything the RENAR way" is easy — proving it is harder. This chapter is about how a project makes a verifiable claim: "we implement RENAR at the `RENAR-3` level" — and backs it up so that an external assessor can re-check it. The claim is not a slogan but a signed manifest with references to evidence in the substrate: here is the level, here is the evidence for each mandatory clause, here is who confirmed it and when.

The chapter normalizes three questions. **Who** is entitled to claim — the project Architect (self-assessment) or an independent assessor (third party). **On what basis** — the closed list of RENAR-1..5 levels plus the universal clauses mandatory for any level. **How the claim lives over time** — scheduled re-assessment, and upon a violation, loss of conformance. If the claim ceases to be true, this is a recorded event, not a silent omission.

The levels themselves are given here as a short semantic summary: the full RENAR-1..5 criteria are in [chapter 11](11-maturity-model.md), and the substrate-native check mechanisms are in [chapter 3](03-substrate-versioning.md) and `guide/06-compliance.md`. Here — the rules of the claim itself.

---

## 13.2 The closed list of RENAR levels

A closed list of five maturity levels. The full criteria are in [chapter 11](11-maturity-model.md); below is the normative **semantic summary** for a conformance claim.

| Level | Brief semantics | Conformance status |
|---|---|---|
| `RENAR-1` | Ad-hoc (spontaneous level): requirement artifacts are kept in a substrate with V1–V6 ([§13.3.2](#1332-substrate-capabilities-v1v6)); no formal `frontmatter` schema, no lifecycle statuses | Minimum entry level (§13.2.1) |
| `RENAR-2` | Documented (fixed in artifacts): a requirements substrate exists; artifacts have a basic `frontmatter`; the TZ is recorded | Conformance declarable (§13.2.2) |
| `RENAR-3` | Tracked (lifecycle and link accounting maintained): full `frontmatter` schema; lifecycle statuses are used; delta-TZ workflow through ADAPT | Conformance declarable (§13.2.2) |
| `RENAR-4` | Verified: 100% of `approved` artifacts have `verified-by`; pos/neg pairing ([§9.7](09-test-cases.md#9.7)); QG-2 enforced; AI provenance | Conformance declarable (§13.2.2) |
| `RENAR-5` | Optimized: adversarial critic; multi-model generation; knowledge graph; Hallucination Rate measured | Conformance declarable (§13.2.2) |

### 13.2.1 RENAR-1 as the minimum entry level

`RENAR-1` is the normative entry level. A project with no requirements substrate (where requirements live only in ticket systems or private correspondence) MUST NOT claim `RENAR-1`; a conformance claim against the standard begins with the actual presence of a requirements substrate.

`RENAR-1` is recorded in the conformance manifest only if the project explicitly declares the start of its RENAR journey; for projects with no intent to adopt RENAR, a conformance manifest is not required.

### 13.2.2 Closedness of the list

New levels (`RENAR-6`, `RENAR-N+`) MUST NOT be created locally at the project level. Changing the list of levels is possible only through the formal change procedure of the standard (§13.9).

An implementation MAY tighten requirements relative to the level (declare-stricter — see [§10.10.2](10-lifecycle-qg.md#10.10.2)); this does not change the level and does not take the project outside the closed list.

---

## 13.3 Mandatory clauses, universal to all levels

Normative clauses mandatory for **any** conformance claim regardless of the declared level (including `RENAR-1`). Violating even one of them means the absence of conformance to the RENAR Standard as a whole. **Version-graduated clauses:** some clauses of this section are introduced with a transition period, and their modality depends on the version of the standard the conformance is claimed against; on a version where the clause is stated as a recommendation, failing it does not remove conformance. The current modality is recorded in the machine-readable index of normative points (`reference/normative-index.yaml`). On v1.1 there are no clauses in a transition period: [§13.3.8](#13.3.8), which was one on v1.0, has been moved to mandatory.

### 13.3.1 Source-of-Truth inversion

An implementation MUST observe the **Source-of-Truth inversion** ([§2.3](02-methodology-positioning.md#2.3)): the requirement-artifact hierarchy is the source of truth about system behavior; code is a derived implementation artifact. Equivalent violations: reverse-engineering behavior from code into an SR without a bug-fix justification ([§2.3.3 (1)](02-methodology-positioning.md#2.3.3)); silent adaptation of an SR to the observed behavior of code ([§2.3.3 (4)](02-methodology-positioning.md#2.3.3)).

### 13.3.2 Substrate capabilities V1–V6

The project substrate MUST satisfy capabilities V1–V6 ([chapter 3 §3.3](03-substrate-versioning.md#3.3)):

| Capability | Status for conformance |
|---|---|
| V1 — immutable history | **Mandatory absolutely**: without V1 an audit trail and V5 are impossible |
| V2 — atomic change unit | **Mandatory absolutely**: without V2 delta-ADAPT consistency is impossible |
| V3 — diff and review | **Mandatory absolutely**: without V3 there are no gates, no approval ([§10.11.2](10-lifecycle-qg.md#10.11.2)) |
| V4 — branching / change-set | **Mandatory absolutely**: without V4 WIP and the source of truth are inseparable ([§10.11.2](10-lifecycle-qg.md#10.11.2)) |
| V5 — cross-substrate version pin | **Mandatory absolutely**: without V5 pinning `set-version` — the description set version ([§10.5.4](10-lifecycle-qg.md#10.5.4)) — and QG-2 ([§10.3.3](10-lifecycle-qg.md#10.3.3)) are impossible |
| V6 — author and timestamp | **Mandatory absolutely**: without V6 the Architect's signature on an ADAPT, the dual signature of an ACTZ, and AI provenance are impossible ([§10.11.2](10-lifecycle-qg.md#10.11.2)) |

**Negative scenario:** a substrate in which at least one of the V1–V6 capabilities is missing normatively **cannot** implement any RENAR-N — including `RENAR-1`. This is a structural constraint ([§3.2](03-substrate-versioning.md#3.2)), not an operational one.

### 13.3.3 Reactive ADAPT

ADAPT is a reactive artifact ([§7.4.1](07-adapt.md#7.4.1)). Every TZ MUST pass an adversarial review ([§7.10.2](07-adapt.md#7.10.2)); its outcome MUST be issued as an AR — an adversarial-review record in status `issued` (V6 author + timestamp, [§7.4.6](07-adapt.md#7.4.6)). What follows depends on the verdict:

| Adversarial reviewer's verdict | Requirement for ADAPT | Requirement for `source` of BR/SR/SPEC |
|---|---|---|
| **"findings present"** — backward findings, term mapping clarification, or scope clarification were detected | ADAPT is REQUIRED, in status `approved`, with the Architect's signature ([§7.5](07-adapt.md#7.5)). Every finding carries a `decided-in` pointing to a clause of a **signed** ACTZ ([§7.13](07-adapt.md#7.13)). Lifecycle [§7.4.5](07-adapt.md#7.4.5). The AR carries a non-empty `produces-adapt[]`. | `source.adapt` mandatory; `source.tz-section` mandatory |
| **"no findings, no clarifications"** — the TZ → RENAR conversion is unambiguous | No ADAPT is created | `source.tz-section` mandatory; `source.adversarial-review-ref` mandatory — a reference to an AR with the `no-findings` verdict |

Delegation to the adversarial reviewer is the only permissible way to declare "no ADAPT is needed." Creating BR / SR / SPEC from a TZ without a recorded verdict is a violation of the standard; hooks ([§10.11.1](10-lifecycle-qg.md#10.11.1) `adapt-applicability validation`) MUST block such artifacts.

In the presence of a delta-TZ — the same rule ([§7.6](07-adapt.md#7.6)): a delta-ADAPT is created upon findings from the adversarial review of the delta-TZ.

**Multiplicity and stage independence.** The ADAPT trigger is stage-agnostic: the verdict is rendered not only at TZ import but also at the derivation stages (BR → SR → SPEC → TC, [§7.4.1.1](07-adapt.md#7.4.1)). A single TZ has **zero or more** root ADAPTs (cardinality 0..N, MVR-3, [§7.4.1.4](07-adapt.md#7.4.1)); each records a `trigger-stage`. Multiplicity is conformant to the standard and does not weaken provenance: each BR / SR / SPEC references exactly one ADAPT or a `source.tz-section`.

**Supersession (`supersession`).** An approved/frozen ADAPT MAY be superseded by a superseding ADAPT ([§7.6.4](07-adapt.md#7.6)). A conformant supersession MUST: (1) preserve the superseded ADAPT in the terminal `superseded` state (immutable, V1) — not delete it; (2) upon a contractual outcome (the decision was a clause of a signed ACTZ), be formalized by a **new ACTZ with the dual signature**, which stamps `superseded-by` on the former one; (3) redirect or re-derive all derivatives so that no dangling `source.adapt` pointing at a `superseded` one remains ([§6.10.3](06-requirements-hierarchy.md#6.10.3)). A separate QG is not required — QG-3 is used ([§10.8.5](10-lifecycle-qg.md#10.8)).

**Negative scenarios (non-conformant):**

- The manifest declares conformance, but BR/SR/SPEC are derived from a TZ without `source.tz-section` and without `source.adapt` — a violation of mandatory provenance.
- Creating BR/SR/SPEC with `source.adapt` omitted **without** the evidence `source.adversarial-review-ref` — a violation of §7.4.1.3 "no silent skip."
- `source.adversarial-review-ref` points at a non-existent AR, at an AR in status `draft` (the verdict is not issued), or at an AR in status `superseded` (the verdict was displaced) — invalid evidence ([§7.4.6](07-adapt.md#7.4.6)).
- An AR issued with a reviewer model equal to the primary agent's model — the review is not adversarial ([§7.10.2](07-adapt.md#7.10.2)).
- An AR with `verdict: findings-present` and an empty `produces-adapt[]` — the verdict claims findings, yet no ADAPT was produced.
- A finding in status `resolved` without a `decided-in` pointing to a signed ACTZ — the interpretation rests on a decision the client never took ([§7.4.5](07-adapt.md#7.4.5)).
- A signed ACTZ none of whose decisions is reflected in an ADAPT — an obligation exists outside the requirements.
- The client signature on an ADAPT declared mandatory — a redundant requirement; it is permissible only as `declared-stricter` ([§1.7.2](01-scope.md#1.7.2)), not as baseline conformance.
- **An AT generated by an agent that had access to the internal contour** (ADAPT / BR / SR / SPEC / TC / code) — the acceptance level collapses into the verification level, and an interpretation error receives a false confirmation of conformance ([§9.19.2](09-test-cases.md#9.19)).
- The product submitted for trials with AT derived from an **outdated** revision of the effective TZ ([§9.19.4](09-test-cases.md#9.19)).
- A TC counted as evidence **without a red history** and without a killed mutant ([§9.18.2](09-test-cases.md#9.18)).
- A requirement of the `implementation-originated` class describing **client-observable** behavior ([§6.13.2](06-requirements-hierarchy.md#6.13)) — an obligation toward the client cannot originate in the code.
- An ADAPT created under a "no findings" verdict (evidence exists) — a contradiction: the verdict claims that no ADAPT is needed, yet an ADAPT is present.
- An ADAPT in status `superseded` deleted from the substrate — a violation of immutable history (V1).
- A dangling `source.adapt` reference to a `superseded` ADAPT (the derivative was not redirected or re-derived) — an invalid trace chain.
- Supersession of a decision with a contractual outcome without a new signed ACTZ — a unilateral cancellation of what was agreed with the client, a violation of §7.6.4.

### 13.3.4 Closed list of 12 SPEC types

A SPEC type MUST belong to the closed list of twelve types ([§8.3](08-specifications.md#8.3)): `SPEC-ARCH`, `SPEC-API`, `SPEC-DATA`, `SPEC-INT`, `SPEC-PROC`, `SPEC-UI`, `SPEC-AI`, `SPEC-SEC`, `SPEC-OPS`, `SPEC-TEST`, `SPEC-DOC`, `SPEC-UC` (the last one added in v1.1 under the procedure of §13.9).

A project MUST NOT create new SPEC types locally. Changing the list is possible only through the formal change procedure of the standard ([§8.3.1](08-specifications.md#8.3.1), §13.9).

### 13.3.5 TC pos/neg pairing for normative statements

Every normative statement of a verifiable artifact of the description set (BR / SR / SPEC) that describes observable behaviour MUST be covered by a positive + negative pair of TC norms in the same set version ([§9.7](09-test-cases.md#9.7)). Single-TC coverage is permitted only in one case: the statement itself describes a negative invariant (for example, a `SPEC-SEC` STRIDE category).

QG-0 of the set ([§10.5.2](10-lifecycle-qg.md#10.5.2)) MUST block the approval of a version in which at least one such statement is not covered at all or is covered without a pair. The `coverage-presence` enforcement point ([§10.11.1](10-lifecycle-qg.md#10.11.1)) MUST check the **presence** of coverage, not only the validity of existing `verifies[]` references: a version in which no TC norm at all is written for a statement violates this clause just as a version with a single positive TC does.

**Negative scenario:** a project declares `RENAR-N` conformance having approved a set version in which an SR with observable behaviour has no TC norm at all — **non-conformant**: the absence of a TC norm is a violation on a par with an incomplete pair.

### 13.3.6 Closed list of Quality Gates

The closed list of Quality Gates ([§10.3](10-lifecycle-qg.md#10.3), [§10.4](10-lifecycle-qg.md#10.4)):

| Gate | Status in the conformance manifest |
|---|---|
| QG-0 — Approval | **Mandatory required** |
| QG-1 — Implementation | **Mandatory required** |
| QG-2 — Verification | **Mandatory required** |
| QG-3 — Architecture (opt.) | `declared` (supported) or `absent`; for projects with mandatory ADAPT — effectively always `declared`, since ADAPT approval operates with the Architect's signature at QG-3 ([§10.4.1](10-lifecycle-qg.md#10.4.1)) |
| QG-4 — Acceptance (opt.) | `declared` or `absent`; when `absent`, the version record carries no `accepted-outcomes[]` — the verification of the version remains the last evidence |

Creating new gate types locally is prohibited ([§10.10.2](10-lifecycle-qg.md#10.10.2)). Locally tightening the preconditions of a canonical gate is permitted (declare-stricter); locally weakening them is prohibited.

### 13.3.7 Closed lists of backward findings and SPEC decompositions

- The list of backward-findings categories in ADAPT is closed ([§7.4.4](07-adapt.md#7.4.4)) — 7 categories; adding new ones is the formal change procedure of the standard (§13.9).
- The closed list of SPEC types is additionally fixed by §13.3.4.
- The closed list of artifact lifecycle states ([chapter 10](10-lifecycle-qg.md)) — is not extended locally at the project level.

### 13.3.8 Cross-level subsystem traceability (`implements`-edge)

For the "subsystem as a standalone product" scenario ([§6.8.2](06-requirements-hierarchy.md#6.8.2)), an implementation MUST provide a machine-readable trace chain `BR (subsystem) → BR (system)` through the typed `implements[]` edge ([§6.5.2](06-requirements-hierarchy.md#6.5.2)):

| Condition | Requirement |
|---|---|
| `BR.level = subsystem` AND the parent system has ≥ 1 BR in an approved version of its set | `implements[]` MUST contain ≥ 1 reference to an applicable BR of the parent system. Its absence when the condition holds is non-conformance. |
| `BR.level = subsystem` AND the parent system is a container without BRs of its own | `implements[]` is omitted; the justification is recorded in the `Context` section with a reference to ADAPT§ ([§6.8.2](06-requirements-hierarchy.md#6.8.2)). |

The substrate MUST implement the `implements`-edge validation checkpoint from [§10.11.1](10-lifecycle-qg.md#10.11.1) (the target belongs to the approved set version of its system, no cycles, removed target → warning, `implements[]` not on `level: system`). This obligation applies to the substrate tooling and was already in force on v1.0, when the presence of `implements[]` was still recommended; the checkpoint MUST also verify the **presence** of the edge when the condition holds, not merely the validity of an existing one. Its violation is caught by the checkpoint on change, not through the loss-of-conformance triggers of [§13.8.1](#13.8.1) — those address violations of mandatory clauses, not a missing check at the substrate.

**Negative scenario:** a project declares conformance to `RENAR-N` with `BR.level = subsystem` and an approved parent BR in the same system, but without `implements[]` and without a justification in the Context — non-conformant from v1.1 onward; on v1.0 — predictably-non-conformant upon upgrade ([migration guide](../../guide/en/12-migration-v11.md)).

---

## 13.4 The conformance manifest

### 13.4.1 Location and format

The conformance manifest is stored at the root of the project's requirements substrate under the name `RENAR-CONFORMANCE.yaml`. The format is YAML 1.2; an alternative serialization (`.json`) is permitted as an **additional** artifact, not a replacement.

The manifest is **immutable** in the V1 sense: each conformance claim creates a new version of the manifest (`manifest-version` is incremented); previous versions are not deleted, they remain in the substrate as an audit trail. Replacing the manifest through `replaced-by` points to the new version.

### 13.4.2 Mandatory fields

```yaml
# RENAR Conformance Manifest schema (mandatory fields, v1.1)
renar-version: "1.1"           # the version of the RENAR Standard against which conformance is claimed
senar-version: "1.0"           # the version of SENAR the implementation relies on (mandatory, MVR-7)
manifest-version: 3            # incremented on each update; not re-used
manifest-id: "CFM-2026-001"    # stable substrate-native identifier (V1)

# Conformance level
level: "RENAR-3"               # from the closed list §13.2 (RENAR-1..RENAR-5)
level-target: "RENAR-4"        # optional: the next target level (for path planning)
set-version: "N.M"             # the approved description set version at assessment time ([§10.5.4](10-lifecycle-qg.md#10.5.4))

# Assessment metadata
assessment-mode: "self"        # self | third-party
confirmation: "second-party"   # second-party (a client exists, ACTZ) | first-party (a concept as the input, §1.4.4; ACTZ and AT have no subject)
assessment-date: "2026-05-13"  # ISO-8601 date of completion of the current assessment
assessor:
  id: "architect-andrey-y"     # V6 author identifier
  role: "architect"            # role from §13.5 (architect | authorized-role-holder | external-assessor)
  signature-ref: "<substrate-native pointer to the signature event>"
next-assessment-due: "2026-08-13"  # §13.7

# Mandatory clauses confirmation (§13.3)
mandatory-clauses-confirmed:
  sot-inversion: true                              # §13.3.1
  substrate-v1-v6: { v1: true, v2: true, v3: true, v4: true, v5: true, v6: true }   # §13.3.2
  adapt-per-tz: true                               # §13.3.3
  spec-types-closed-list: true                     # §13.3.4
  tc-pos-neg-pairing: true                         # §13.3.5
  quality-gates-closed-list: true                  # §13.3.6
  closed-lists-backward-findings: true             # §13.3.7
  implements-edge-subsystem: true                  # §13.3.8

# Quality gates declaration (§10.4.4)
quality-gates:
  qg-0: required               # required (mandatory)
  qg-1: required
  qg-2: required
  qg-3: declared               # required | declared | absent
  qg-4: absent

# Substrate capabilities declaration (§13.3.2)
substrate-capabilities:
  v1-immutable-history: declared
  v2-atomic-change-unit: declared
  v3-diff-review: declared
  v4-branching: declared
  v5-version-pin: declared
  v6-author-timestamp: declared
  substrate-id: "<substrate-native pointer to guide/03..06>"   # cross-ref to guide

# Spec types support (§13.3.4)
spec-types-supported: ["SPEC-ARCH", "SPEC-API", "SPEC-DATA", "SPEC-INT",
                       "SPEC-PROC", "SPEC-UI", "SPEC-AI", "SPEC-SEC", "SPEC-OPS",
                       "SPEC-TEST", "SPEC-DOC", "SPEC-UC"]
# All 12 types are mandatory as the minimum-supported; a declaration "type X is not used in the project" is permitted,
# but it CANNOT be a declaration "type X is not supported substrate-natively."

# Optional fields
declared-stricter:             # §13.2.2, §10.10.2 — local tightening
  - clause: "QG-2"
    override: "required-negative-tc-per-clause"
    rationale: "regulated industry, double safety margin"
  - clause: "tc-pos-neg-pairing"
    override: "100% (no single-TC exception for security)"
    rationale: "mandatory requirement of the internal ISMS"

exceptions: []                 # declared exceptions relative to the base level (with justification in the audit trail)

# External conformance records per §14.4 (optional; for the value field see the `claim` key below — substrate §14.6)
external-claims:
  - standard: "ISO/IEC 5338:2023"
    clause-ref: "§2.4.x"
    claim: "partial"           # full | partial | aligned
  - standard: "NIST AI RMF 1.0"
    clause-ref: "§2.4.x"
    claim: "aligned"

replaced-by: null              # substrate-native pointer to the next version of the manifest, if released
replaces: "CFM-2026-001@v2"    # substrate-native pointer to the previous version of the manifest
```

### 13.4.3 Field semantics

- `set-version` — the description set version ([§10.5.4](10-lifecycle-qg.md#10.5.4)) assessed at the declaration; the declaration refers to it, not to individual artifacts.
- `renar-version` — the version of the standard; conformance is claimed against a specific point of the standard; upon the release of a minor version of the standard (§13.9), a re-assessment is REQUIRED ([§13.7](#137-re-assessment-cadence)) with an update of `renar-version`.
- `senar-version` — the version of SENAR the implementation relies on; mandatory per MVR-7 ([§0.5](00-introduction.md#0.5)); its absence from the manifest is non-conformance ([§13.8](#138-loss-of-conformance)).
- `manifest-id` — V1 immutable identifier; not re-used even after `replaced-by`.
- `level` — the closed list §13.2; a violation of the mandatory clauses (§13.3) means conformance is absent regardless of `level`.
- `level-target` — an optional declaration of the development path; it is not a normative obligation.
- `assessment-mode` — `self` (§13.5) or `third-party` (§13.6); `third-party` MUST contain a formal reference to the external assessment act.
- `confirmation` — `second-party` (the contract-oriented kind §1.4.1: a client, ACTZ, AT) or `first-party` (the kind of [§1.4.4](01-scope.md#1.4.4): a concept as the input, the ACTZ and the AT release gate have no subject, adversarial review without exception, ACR §12.3.6 mandatory). The value MUST match the actual kind: `second-party` with one person in both ACTZ signatures is a violation of §5.5.3.
- `assessor` — V6 author + role; for `third-party` — an external participant with explicit identification.
- `next-assessment-due` — `assessment-date + cadence` (§13.7); an overrun is a trigger for loss of conformance (§13.8).
- `quality-gates` — MUST contain all five gate-ids; the status of `qg-0..qg-2` ∈ {required}; `qg-3, qg-4` ∈ {required, declared, absent}.
- `mandatory-clauses-confirmed` — each field set to `true` is mandatory for any conformance claim; `false` makes the manifest invalid.
- `declared-stricter` — a list of local tightenings; MUST contain `clause`, `override`, `rationale`.
- `exceptions` — a list of declared exceptions relative to the base level; each exception REQUIRES a justification in the audit trail and MUST NOT touch the mandatory clauses.
- `external-claims` — an optional list of conformance records against external normative references ([§14.4](14-normative-refs.md#14.4), [§14.6](14-normative-refs.md#14.6)); each record contains `standard`, `clause-ref`, and the field **`claim`** (value: full \| partial \| aligned) — a technical schema key, with no term substitution in the manifest.

---

## 13.5 Self-assessment procedure

### 13.5.1 Actor

Self-assessment is conducted by the project **Architect** or by an **authorized role holder** ([chapter 5](05-roles.md)) explicitly declared responsible for conformance.

### 13.5.2 Methodology

Steps: (1) the §13.3 checklist — the actor checks each mandatory clause; the result goes into `mandatory-clauses-confirmed`; at least one `false` → the assessment is not completed, and a remediation plan for the violations is formed; (2) the [chapter 11](11-maturity-model.md) §§11.4-11.8 checklist for the declared `level` — each level criterion = a separate check with evidence in the substrate; (3) filling in the manifest (all mandatory fields §13.4.2; `level` not higher than the passed checklist); (4) signing and publishing — the actor signs the manifest with the native mechanism (V6 author + timestamp); the manifest becomes part of the source of truth through V3 review (for self-assessment, self-approval is permitted, the evidence MUST be recorded).

### 13.5.3 Evidence

Each mandatory-clause check MUST be accompanied by references to evidence in `audit-trail/CFM-<id>/<clause>.md` (V1 — a stable identifier pointing to specific artifacts, runs, events). Evidence is retained indefinitely (V1 + [§10.13.3](10-lifecycle-qg.md#10.13.3) retention).

### 13.5.4 Cadence

The first claim (kickoff); the cadence is §13.7; the trigger is §13.8 (loss of conformance); the release of a minor version of the standard (§13.9).

---

## 13.6 Third-party assessment (optional)

An optional path to confirming conformance by an external assessor. It applies in regulatory contexts (medicine, fintech, the public sector, AI-critical systems) or under contractual obligations toward the client.

### 13.6.1 Actor

The external assessor is an independent participant with read-only access to the substrate for the assessment period. The formal qualification is substrate-specific and is recorded in `assessor.signature-ref` (an external auditor of a certified organization).

### 13.6.2 Methodology

The same as §13.5.2 (the §13.3 checklist + the [chapter 11](11-maturity-model.md) checklist), plus: an audit trail — all of the assessor's actions (read-events) are recorded natively; independent verification — the assessor checks the evidence independently without relying on the self-assessment results; the outcome — a signed manifest (the assessor signs with the native V6 mechanism) **or** a denial with a justification and a list of specific violations.

### 13.6.3 Additional manifest fields

When `assessment-mode: third-party`, the following are mandatory:

```yaml
third-party:
  assessor-organisation: "<name>"
  assessor-qualification-ref: "<pointer to the qualification document>"
  audit-report-ref: "<pointer to the full audit report>"
  audit-log-ref: "<pointer to the read-events log>"
```

### 13.6.4 Relationship between self / third-party

Third-party **does not cancel** the self-assessment cadence (§13.7); the paths are compatible. A project MAY run self-assessment quarterly and third-party annually; the manifest is versioned separately for each path with different `manifest-id`s.

---

## 13.7 Re-assessment cadence

### 13.7.1 Default

Self-assessment is **quarterly** (every 3 months from `assessment-date`); third-party is **annual**. It is declared in the manifest's `next-assessment-due` field.

### 13.7.2 Override

The cadence MAY be overridden in `declared-stricter`:

```yaml
declared-stricter:
  - clause: "re-assessment-cadence"
    override: "monthly"
    rationale: "AI-critical project, eval dependency"
```

Weakening the cadence (`override: "annual"` for self under the default `quarterly`) is **prohibited** — a violation of [§13.3.1](#1331-source-of-truth-inversion) Source-of-Truth inversion (a hidden weakening = concealing a loss-of-conformance event).

### 13.7.3 Immediate re-assessment triggers

The release of a minor version of the RENAR Standard (`renar-version` shifts); a violation of a mandatory clause (§13.3, the loss-of-conformance trigger §13.8); a substantial change of substrate (substrate replacement — V1-V6 is re-assessed); a substantial change of scope (a new artifact class, a new SPEC type).

---

## 13.8 Loss of conformance

### 13.8.1 Triggers

Conformance is considered **lost** upon the occurrence of any of:

| Trigger | Description |
|---|---|
| A mandatory clause is violated | Any of §13.3.1–§13.3.8 ceased to hold (for example, a BR appeared without `source.adapt` and without `source.adversarial-review-ref` — a silent skip of the adversarial review; the substrate lost V3 after migration) |
| Substrate capability degradation | V1, V2, V3, V4, V5, or V6 is no longer provided by the substrate (for example, migration to a substrate without diff & review) |
| The manifest expired | `next-assessment-due` passed without releasing a new version of the manifest |
| Level criteria violated | The criteria of the declared level (see [chapter 11](11-maturity-model.md)) ceased to hold without a formal downgrade |
| Metric threshold exceeded | A critical metric of [chapter 12](12-metrics.md) exceeded the **calibrated** normative threshold for the level (for example, Hallucination Rate > 5% on RENAR-4 — [§12.3.3](12-metrics.md#12.3.3)). Exceeding a threshold that [§12.4](12-metrics.md#12.4) marks as provisional and that has not yet undergone calibration is **not by itself a trigger**: conformance is lost either against a calibrated threshold or through any other trigger in this list |
| External denial | An external assessor (third-party assessor) returned a denial with justification |

### 13.8.2 Procedure

Steps: (1) **recording the loss-event** in the audit trail (`audit-trail/CFM-<id>/loss-events/<timestamp>.md`); (2) **a formal downgrade or a declaration of unknown-state** — a downgrade (a new version of the manifest with a `level` below the current one, if the mandatory clauses hold) or unknown-state (an explicit declaration in public communications; the manifest is marked `replaced-by: "<unknown-state>"` with a sentinel value, distinct from the default `null`; re-assessment is REQUIRED for recovery); (3) **a recovery plan** — `recovery/CFM-<id>-<timestamp>.md` specifying the deadline and the mandatory clauses / level criteria; (4) **re-assessment** after the recovery plan — self-assessment §13.5 with an updated manifest is REQUIRED; for third-party — a repeated external assessment.

### 13.8.3 Public communications

Loss of conformance, if the level is claimed publicly, MUST be recorded publicly within a reasonable time (the notification cadence — `guide/`). Concealing the fact of loss is a violation of §13.3.1 (the Source-of-Truth audit trail).

---

## 13.9 The closed-list policy for RENAR-N levels

### 13.9.1 Normative rule

The closed list of levels RENAR-1..RENAR-5 (§13.2) is **not extended** locally at the project level. Any project claiming conformance MUST specify `level` ∈ {RENAR-1, RENAR-2, RENAR-3, RENAR-4, RENAR-5}. This policy is a specialization of the [§1.7](01-scope.md#1.7) closed-list policy for maturity levels; the master index is [§1.7.5](01-scope.md#1.7.5).

### 13.9.2 What is prohibited

| Action | Prohibited? | Why |
|---|---|---|
| Locally creating a level at the project level (`RENAR-6`, `RENAR-PRO`) | Prohibited | Violates the closed list; conformance is non-portable |
| Locally overriding criteria (weakening RENAR-4 without a formal downgrade) | Prohibited | Violates the contract of the standard |
| Locally tightening criteria (declare-stricter) | Permitted | See §13.4.2 `declared-stricter` |
| Claiming a level higher than the one actually achieved | Prohibited | Violates §13.3.1 Source-of-Truth inversion |
| A conformance claim without a manifest | Prohibited | §13.4 mandatory |
| Intermediate levels such as `RENAR-3.5` | Prohibited | The list is discrete |

### 13.9.3 The path to adding a new level

Only through the formal change procedure of the standard: a research draft (justification, a typology of criteria, a comparison with existing RENAR-N) → public review → a minor- or major-version bump of the standard → migration guidance (for existing conformant projects: changes to the self-assessment checklist, new manifest fields).

Project-local extensions remain outside conformance — permitted as internal practices (`declared-stricter`), but they do **not** affect the formal `level` in the manifest.

---

### 13.9.4 A degeneracy measurer for a new mandatory control

**When a new mandatory control is introduced, the standard-change proposal MUST state which measurer determines the degeneracy of that control. A new metric is REQUIRED only when none of the measurers in force ([§12.3bis](12-metrics.md#12.3)) applies to it.**

A control executed formally yields a degenerate value — zero findings, zero objections, a hundred per cent agreement — and by every other sign looks healthy. The requirement addresses the **author of a change proposal**, not projects in operation: it binds future amendments of the standard and has no retroactive force.

The second half of the rule matters no less than the first: it acts as a procedural filter against growth of the closed list of metrics ([§12.3](12-metrics.md#12.3)). Before proposing a twelfth metric, the author MUST show that none of the eleven in force fits the new control.

The clause does not extend retroactively to controls introduced before it: some of them have no measurer, and that gap is recorded explicitly in [§12.3bis](12-metrics.md#12.3).

---

## 13.10 Relationship to other chapters

| Chapter | Relationship |
|---|---|
| [02 Positioning in the typology of methodologies](02-methodology-positioning.md) | [§2.3](02-methodology-positioning.md#2.3) Source-of-Truth inversion — the mandatory clause §13.3.1; [§2.7](02-methodology-positioning.md#2.7) the conformance consequence explicitly refers back to this chapter |
| [06 Requirements hierarchy](06-requirements-hierarchy.md) | the `frontmatter` of artifacts (BR / SR / TR) — the RENAR-2/-3 level criteria are checked per [§6.5](06-requirements-hierarchy.md#6.5)–[§6.7](06-requirements-hierarchy.md#6.7) ([chapter 11](11-maturity-model.md)) |
| [07 ADAPT](07-adapt.md) | the mandatory clause §13.3.3 (reactive ADAPT: an adversarial review for each TZ, ADAPT per the verdict); [§7.4.4](07-adapt.md#7.4.4) the closed list of backward findings — §13.3.7 |
| [08 Specifications](08-specifications.md) | the mandatory clause §13.3.4 (the closed list of 12 SPEC types); [§8.3.1](08-specifications.md#8.3.1) the closed-list policy — §13.9 |
| [09 Test cases](09-test-cases.md) | the mandatory clause §13.3.5 (pos/neg pairing); [§9.10](09-test-cases.md#9.10) QG-2 — §13.3.6 |
| [10 Lifecycle and QG](10-lifecycle-qg.md) | [§10.3](10-lifecycle-qg.md#10.3) the canonical gates, [§10.4.4](10-lifecycle-qg.md#10.4.4) the conformance-manifest fragment — expanded here into the full manifest schema; [§10.10](10-lifecycle-qg.md#10.10) the closed-list policy for gates parallels §13.9 for levels |
| [03 Substrate versioning](03-substrate-versioning.md) | the mandatory clause §13.3.2 (V1–V6 declared); [§3.4](03-substrate-versioning.md#3.4) the mapping table — non-normative, informational |
| [11 Maturity model](11-maturity-model.md) | detailed criteria for levels RENAR-1..5 — here §13.2 contains a semantic summary; the full per-level checklist is in [§§11.4–11.8](11-maturity-model.md) (one section per level) |
| [12 Metrics](12-metrics.md) | the metrics on which self-assessment is built (`approved-without-verified`, `pos-neg-pairing-percent`, and others) — specified here as input data for §13.5 |

