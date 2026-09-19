---
title: "Maturity Model"
order: 11
lang: en
---
# 11. Maturity Model

> **Part of the RENAR Standard v1.1** · [← Table of contents](README.md)

## 11.1 Maturity as a ladder, not a label

[Chapter 3](03-substrate-versioning.md) laid the foundation — a substrate capable of storing the history and provenance of requirements. But having a foundation says nothing about how maturely a team uses it. One project simply keeps its requirements in a substrate; another validates them against a schema, runs paired tests, and makes a second model challenge the first. These are different rungs of one ladder — and this chapter numbers them.

RENAR defines **five maturity levels** — `RENAR-1`..`RENAR-5`, a closed list. A level is a **measurable characteristic** of how formalized requirements management is in a project. It is important not to confuse it with a **conformance claim** (conformance): the level describes *where* a project stands, while conformance is the procedure by which it *proves* this. The mechanics of the claim are in [chapter 13](13-conformance.md).

Each next rung adds obligations on top of the previous one and closes its class of divergences; the chapter is best read in order: §11.4–§11.8 cover the rungs one at a time, §11.10–§11.11 answer "where to start" and "how to grow."

The chapter's boundary is strict. It governs **only** the level criteria and the transitions between them. The conformance claim procedures are [chapter 13](13-conformance.md); the metrics for measuring a level are [chapter 12](12-metrics.md); the substrate capabilities themselves are [chapter 3](03-substrate-versioning.md).

---

## 11.2 RENAR-M as a domain-specific dimension in the SENAR maturity model

### 11.2.1 The SENAR maturity model (general maturity)

SENAR defines a one-dimensional model of five levels of the team's and methodology's general maturity: `Ad-hoc → Supervised → Measurable → Managed → Optimizing`. This model assesses the **process maturity of the team as a whole**, independently of any specific process area.

### 11.2.2 RENAR-M as a separate dimension

RENAR-M is a **separate dimension** within the general SENAR maturity, specializing it for the "requirements engineering" process area. A project is characterized by a **pair**:

```text
project ──▶ (SENAR-N, RENAR-M)

where N ∈ {1, 2, 3, 4, 5} — general SENAR maturity
      M ∈ {1, 2, 3, 4, 5} — RENAR level of requirements management
```

The pairs `(SENAR-N, RENAR-M)` are normatively independent: a project at SENAR-4 (Managed) MAY be at RENAR-2 (Documented) — the team is mature in SENAR practices overall, yet **requirements management specifically** is weakly formalized. This is a permitted and observable scenario.

### 11.2.3 Alignment with the SENAR Reference

The SENAR Reference allows domain-specific maturity dimensions (authentication, security, observability, and other process areas). RENAR-M is one such dimension; it does not contradict SENAR and does not duplicate it.

In the industry it is typical for different process areas within one organization to have differing maturity. RENAR-M is the normative maturity dimension specifically for the "requirements engineering" area, with criteria drawn only from this chapter and adjacent sections of the standard.

### 11.2.4 Conformance as a pair

The conformance manifest ([§13.4](13-conformance.md#13.4)) records **only** the RENAR-M (the `level` field). SENAR-N remains within the scope of SENAR conformance and is not duplicated in the RENAR manifest.

---

## 11.3 The closed list of levels RENAR-1..RENAR-5

The list of five levels is closed. Changing the list is possible only through the formal change procedure of the standard ([§13.9.3](13-conformance.md#13.9.3)).

| Level | Short name | Semantics (one line) |
|---|---|---|
| `RENAR-1` | Ad-hoc | A requirements substrate exists; artifacts are kept without a formal schema or lifecycle |
| `RENAR-2` | Documented | Artifacts have basic frontmatter and are stored structurally; the TZ is fixed |
| `RENAR-3` | Tracked | Full frontmatter schema; lifecycle statuses are used; delta-TZ workflow via ADAPT |
| `RENAR-4` | Verified | 100% of the artifacts of the approved set version have `verified-by`; pos/neg pairing; QG-2 is enforced; AI provenance |
| `RENAR-5` | Optimized | Adversarial review as a gate; multiple models for `priority: must`; knowledge graph; metrics |

Intermediate levels (`RENAR-3.5`) and project-local levels are prohibited ([§13.9.2](13-conformance.md#13.9.2)). Local tightening of criteria within a declared level is permitted via `declared-stricter` ([§13.4.2](13-conformance.md#13.4.2)).

Each level includes the normative criteria of all lower ones. `RENAR-M` implies satisfaction of the requirements of `RENAR-1`..`RENAR-(M-1)`.

---

## 11.4 RENAR-1: Ad-hoc

The first rung answers the question: **where do the requirements live?** The answer is "in the substrate, not in someone's head, an issue tracker, or a chat thread." There is no formal schema or lifecycle yet, but provenance is already recoverable.

### 11.4.1 Normative definition

RENAR-1 is the lowest conformant level. A project MUST satisfy: the substrate exists physically (V1–V6 [§13.3.2](13-conformance.md#13.3.2) — an absolute mandatory clause regardless of level); requirements are kept as artifacts inside the substrate (not in issue trackers or chat threads); each TZ has passed the adversarial review, its outcome is issued as an AR, and an ADAPT is created on a "findings present" verdict ([§13.3.3](13-conformance.md#13.3.3)); substrate-independent language is applied ([§2.5.4](02-methodology-positioning.md#2.5.4)).

> **RENAR-1 ≠ zero infrastructure.** "Ad-hoc" is about the absence of a formal schema / lifecycle, not of a substrate: V1–V6 are mandatory at any level. A flat file server, Notion, Google Docs, or Confluence without immutable history / atomic change / version pin **do not qualify** even for RENAR-1. The minimum is a distributed VCS or a document-oriented store with V1–V6 ([§3](03-substrate-versioning.md), [guide/03](../../guide/en/03-tool-guide-git.md)–[04](../../guide/en/04-document-store-substrate.md)).

### 11.4.2 What is NOT required at RENAR-1

Standardized frontmatter (a minimum of `id` + `title` is acceptable); lifecycle statuses; TC as separate artifacts (keeping them in code is acceptable); COVERAGE; substrate-native lifecycle hooks.

### 11.4.3 Observable signals

BR/SR/ADAPT artifact files in the substrate (not in a tracker/chat); on a "where does this requirement come from" query, the answer is given through the substrate; a manifest ([§13.4](13-conformance.md#13.4)) with `level: RENAR-1`.

### 11.4.4 QG application (enforcement)

QG-0/QG-1/QG-2 are normatively required ([§13.3.6](13-conformance.md#13.3.6)), but enforcement through hooks is **not mandatory** — a human checks manually as needed.

---

## 11.5 RENAR-2: Documented

RENAR-2 adds **structure** on top of existence: basic frontmatter, the TZ as an immutable artifact, a predictable location for artifacts. A requirement can be found, quoted, and referenced. There is no machine checking yet; discipline is held by people.

### 11.5.1 Normative definition

On top of RENAR-1: every BR/SR has frontmatter with the mandatory fields ([§6.5.2](06-requirements-hierarchy.md#6.5.2), [§6.6.2](06-requirements-hierarchy.md#6.6.2)) — at minimum, without strict schema validation; the TZ is an immutable artifact ([§7.4.2](07-adapt.md#7.4.2)); structural storage (logical folders / native collections); a delta-TZ is an explicit artifact, not a verbal one; the normative sections of artifacts observe the rules of the description language — operators, glossary discipline, prohibited constructions, atomicity ([§15.2](15-description-language.md#15.2)–[§15.5](15-description-language.md#15.5)).

### 11.5.2 What is NOT required at RENAR-2

CI validation of frontmatter against a schema; the states of TR / ADAPT / TC implementations — at the team's discretion; `tc-type` extensions; TC implementations — a norm without an implementation is permitted, the implementation is admitted to runs as it becomes ready ([§10.3.2](10-lifecycle-qg.md#10.3.2)). Pairs of TC norms per statement of the version are not waived: that is the mandatory clause [§13.3.5](13-conformance.md#13.3.5), in force at every level.

### 11.5.3 Observable signals

All BR/SR have valid (at-minimum) frontmatter; the TZ is one native artifact; the delta-TZ is an explicit change-set ([§7.6](07-adapt.md#7.6)); a manifest with `level: RENAR-2`.

### 11.5.4 QG application (enforcement)

QG-0 ([§10.3.1](10-lifecycle-qg.md#10.3.1)) is a procedural gate (an explicit approval with a V6 author + timestamp), without automatic blocking hooks.

---

## 11.6 RENAR-3: Tracked

At RENAR-3 the **machine** kicks in. Frontmatter is validated against a schema, lifecycle statuses work, and the delta-TZ goes through ADAPT. The substrate blocks a reference to a non-approved ADAPT and does not let an implementation reference a requirement without a version binding.

### 11.6.1 Normative definition

On top of RENAR-2: frontmatter is validated against the schema ([reference/02-schemas.md](../../reference/en/02-schemas.md)) by a native mechanism ([§10.11.1](10-lifecycle-qg.md#10.11.1)); description set versions are actually released, the states of TR / ADAPT / TC implementations are used ([chapter 10](10-lifecycle-qg.md)); TC implementations are admitted to runs (QG-1) for all norms of artifacts with `priority: must`; COVERAGE is auto-generated ([§9.15](09-test-cases.md#9.15)); the delta-TZ is a change-set with impact analysis ([§9.16](09-test-cases.md#9.16)); the reference-validation hook ([§10.11.1](10-lifecycle-qg.md#10.11.1)) blocks the creation of an artifact referencing an ADAPT below `approved`; the implementation substrate binds `automation.set-version` ([§9.3](09-test-cases.md#9.3), V5).

### 11.6.2 Observable signals

The frontmatter-validation hook returns a structured response on a schema violation; at least one approved set version has been released ([§10.5](10-lifecycle-qg.md#10.5)); TR, ADAPT and TC implementations are in the states of [§10.6](10-lifecycle-qg.md#10.6), [§10.8](10-lifecycle-qg.md#10.8), [§10.9](10-lifecycle-qg.md#10.9); COVERAGE is updated within a reasonable time; on a delta-TZ the architect automatically sees the affected SR/SPEC/TC; a manifest with `level: RENAR-3`.

### 11.6.3 QG application (enforcement)

QG-0 + QG-1 are enforced natively; QG-2 partially (the `verifies[]` check is mandatory, pos/neg pairing ([§9.7](09-test-cases.md#9.7)) is not required to be automatic).

---

## 11.7 RENAR-4: Verified

RENAR-4 is the threshold of **trust in tests**. Every TC norm of the version has an implementation that passed on its `set-version`; the verification record of the version is not created without green tests on that version, and a once-per-iteration spot-check catches tests faked to match the code.

### 11.7.1 Normative definition

On top of RENAR-3: 100 % of the TC norms of the approved set version have an implementation with the result `pass` on `set-version = N.M` ([§9.9](09-test-cases.md#9.9)) — the pairing of norms per statement is already given by [§13.3.5](13-conformance.md#13.3.5), the level adds the run; QG-2 ([§10.3.3](10-lifecycle-qg.md#10.3.3)) is **enforced** — recording the verification of a version is blocked unless all TCs have passed on its `set-version`; all TC are automated (`automation.status: automated`) or marked `manual-pending` with a deadline ([§9.5](09-test-cases.md#9.5)); for `tc-type: ux` — VLM judge isolation ([§9.13.4](09-test-cases.md#9.13.4)); for `tc-type: eval` the judge model differs from the implementation model (Decision #8); AI provenance in frontmatter — the `ai-provenance` block in the composition fixed by the canonical [§4.10.1](04-terms.md#4.10.1) (`generated-by`, `generated-at`, `prompt-template`, `context-tokens`, `output-tokens`, `human-edits`); this section does not redefine that composition; source citation in the artifact body (`[TZ-XXX §Y line Z]` or a `derived` marker); a continuous-reconciliation hook ([§2.4.2](02-methodology-positioning.md#2.4.2)) on a schedule (no less than once a week); a spot-check of 5 random passing TC once per iteration ([§9.14](09-test-cases.md#9.14)) in the audit-trail.

### 11.7.2 Observable signals

COVERAGE contains `verified-by-percent: 100%` for the approved version; an attempt to record the verification of a version without all TCs passed returns a blocking error; a sample of 5 random artifacts shows source citation on all normative statements; a manifest with `level: RENAR-4`.

### 11.7.3 QG application (enforcement)

QG-0/QG-1/QG-2 are enforced natively. QG-3 (Architecture, [§10.4.1](10-lifecycle-qg.md#10.4.1)) is declared if the project uses ADAPT with the Architect's signature and ACTZ with the dual signature (the default for regulated industries).

---

## 11.8 RENAR-5: Optimized

RENAR-5 closes the loop of **AI reliability**. A second model is set to challenge the first, critical requirements are generated by several models with mandatory analysis of divergences, and the hallucination rate is held below 1%. The standard becomes a system that checks itself.

### 11.8.1 Normative definition

On top of RENAR-4: **adversarial review** is a mandatory gate for `draft → approved` (the artifact passes review by a second AI model, recorded in the audit-trail); **multi-model agreement** for `priority: must` (the artifact is generated by ≥ 2 models; divergences are flagged `[multi-model-disagreement]` and MUST be analyzed); a **cost/latency budget** per artifact (`cost-budget` + `latency-budget`; exceeding it triggers automatic decomposition); a **knowledge graph** ([reference/05](../../reference/en/05-knowledge-graph-schema.md)) as the primary search for AI agents; **continuous evaluation** for all SPEC-AI ([§8.5.7](08-specifications.md#8.5.7)); the **Hallucination Rate** ([chapter 12](12-metrics.md)) is measured and < 1%; the **Multi-model Disagreement Rate** ([chapter 12](12-metrics.md)) is measured and its trends are tracked; feeding template improvements back into the `requirements-library` is standard practice.

### 11.8.2 Observable signals

Every promoted artifact has an adversarial-review record in the audit-trail; for `priority: must` BR — a `[multi-model-agreement]` or `[multi-model-disagreement]` marker; a knowledge-graph dashboard is available; a Hallucination Rate dashboard shows < 1% over a rolling window; a manifest with `level: RENAR-5`.

### 11.8.3 QG application (enforcement)

All mandatory gates (QG-0/1/2) are enforced natively. Adversarial review is enforced as part of QG-0 — the substrate blocks `draft → approved` without confirmation. QG-3 is declared. QG-4 is declared if the project applies post-release outcomes ([§10.4.2](10-lifecycle-qg.md#10.4.2)).

---

## 11.9 Comparative feature table

Everything spread across the five sections above, in one matrix. Cells: `❌` — not required; `partial` — required, but without substrate enforcement (the check is procedural); `✓` — normatively mandatory.

| Feature | RENAR-1 | RENAR-2 | RENAR-3 | RENAR-4 | RENAR-5 |
|---|---|---|---|---|---|
| Substrate with V1–V6 | ✓ | ✓ | ✓ | ✓ | ✓ |
| Adversarial review of the TZ with an issued AR; ADAPT per the verdict | ✓ | ✓ | ✓ | ✓ | ✓ |
| Frontmatter standardized | ❌ | partial | ✓ | ✓ | ✓ |
| Set versions released; states of TR / ADAPT / TC implementations kept | ❌ | partial | ✓ | ✓ | ✓ |
| Frontmatter schema validation (substrate hook) | ❌ | ❌ | ✓ | ✓ | ✓ |
| TC as a full-fledged artifact | ❌ | ❌ | partial | ✓ | ✓ |
| An implementation with `pass` on `set-version` for every TC norm of the version | ❌ | ❌ | ❌ | ✓ | ✓ |
| COVERAGE auto-generated | ❌ | ❌ | ✓ | ✓ | ✓ |
| Reference-validation hook | ❌ | ❌ | ✓ | ✓ | ✓ |
| `automation.set-version` binding (V5) | ❌ | ❌ | ✓ | ✓ | ✓ |
| QG-0 enforced natively by the substrate | ❌ | partial | ✓ | ✓ | ✓ |
| QG-2 enforced natively by the substrate | ❌ | ❌ | partial | ✓ | ✓ |
| AI provenance in frontmatter | ❌ | ❌ | ❌ | ✓ | ✓ |
| Source citation in artifact bodies | ❌ | ❌ | ❌ | ✓ | ✓ |
| Adversarial review as a gate | ❌ | ❌ | ❌ | ❌ | ✓ |
| Multi-model agreement for `priority: must` | ❌ | ❌ | ❌ | ❌ | ✓ |
| Cost and latency budget per artifact | ❌ | ❌ | ❌ | ❌ | ✓ |
| Knowledge graph as primary search | ❌ | ❌ | ❌ | ❌ | ✓ |
| Continuous reconciliation | ❌ | ❌ | ❌ | ✓ (basic) | ✓ (full) |
| Continuous evaluation (SPEC-AI) | ❌ | ❌ | ❌ | ❌ | ✓ |
| Hallucination Rate < 1% | n/a | n/a | n/a | n/a | measured and controlled |
| Multi-model Disagreement Rate trend | n/a | n/a | n/a | n/a | tracked |

---

## 11.10 Minimum entry level (entry-level)

`RENAR-1` is the normative **lower bound** of the conformance manifest. A project that does not satisfy the mandatory clauses ([§13.3](13-conformance.md#13.3)) — in particular, without a substrate with V1–V6, or without a recorded adversarial-review verdict (AR) for each TZ — has **no** RENAR-M level at all; a conformance manifest is not issued for such a project.

| Project type | Recommended target level |
|---|---|
| Short experiment (spike), < 1 sprint, no contract | RENAR-2 — sufficient for documentation |
| Internal automation, 1–3 months | RENAR-3 — tracked lifecycle |
| Client product under contract | RENAR-4 — verified, with pos/neg pairing |
| AI-critical component (depends on eval) | RENAR-5 — mandatory |
| Regulated industries (medicine, fintech, the public sector) | RENAR-4 minimum, RENAR-5 recommended |

The standard allows **different levels in different projects** of one organization — there is no requirement to unify the level across a portfolio.

**Negative scenario**: a substrate that lacks even one capability from V1–V6 ([§3.2](03-substrate-versioning.md#3.2)) cannot normatively implement any RENAR-M — not even `RENAR-1`. This is a structural constraint, not an operational one; the manifest of such a project is invalid ([§13.3.2](13-conformance.md#13.3.2)).

---

## 11.11 The path RENAR-1 → RENAR-5

What is added between adjacent levels. The numbering is for reference, not a prescribed order of work: the order of adoption is the organization's choice ([§1.2.1](01-scope.md#1.2.1)); the recommended one is [guide/02](../../guide/en/02-transition-guide.md). The times are an expected order of magnitude for a small project, not a normative guarantee.

### 11.11.1 RENAR-1 → RENAR-2

1. Create structural storage of artifacts in the substrate (logical folders or substrate-native collections).
2. Migrate existing requirements from the issue tracker, chat, or documents into substrate-native artifacts with minimal frontmatter (`id`, `title`).
3. Fix the TZ as an immutable artifact ([§7.4.2](07-adapt.md#7.4.2)).
4. Agree within the team: new requirements only through the substrate, not through the issue tracker.

Expected time: 1–2 weeks for a small project; 1–2 months for a project with a large volume of accumulated requirements.

### 11.11.2 RENAR-2 → RENAR-3

1. Bring the frontmatter of all artifacts into line with the schema (a substrate-native normalizer).
2. Enable the substrate hook for schema validation (block the change-set on a violation).
3. Release the first approved set version (QG-0, [§10.5.2](10-lifecycle-qg.md#10.5.2)); keep the states of TR / ADAPT / TC implementations.
4. Enable the reference-validation hook ([§10.11.1](10-lifecycle-qg.md#10.11.1)).
5. Generate the initial auto-generated COVERAGE artifact ([§9.15](09-test-cases.md#9.15)).
6. Admit to runs (QG-1) the TC implementations for the norms of artifacts with `priority: must`.
7. Introduce the `automation.set-version` binding from the implementation substrate.

Expected time: 2–4 weeks, including team training.

### 11.11.3 RENAR-3 → RENAR-4

1. An AI agent writes implementations for all TC norms of the version (the pairs of norms per statement are already in the version per [§13.3.5](13-conformance.md#13.3.5)).
2. Implementation of TC in code / SPEC-runners — in parallel with product work; spread over 1–2 quarters.
3. Enable the QG-2 substrate hook (the verification record of the version is blocked without `pass` of all implementations on its `set-version`).
4. Introduce the spot-check process ([§9.14](09-test-cases.md#9.14)).
5. Enable the continuous-reconciliation hook on a weekly schedule.
6. Introduce AI provenance in frontmatter (the substrate hook blocks when it is missing for AI-generated artifacts).
7. Introduce source citation in artifact bodies (the substrate hook blocks when a citation is missing for normative statements).

Expected time: 1–2 quarters.

### 11.11.4 RENAR-4 → RENAR-5

1. Connect adversarial review by a second model (different from the primary one; the judge-isolation principle [§9.13.4](09-test-cases.md#9.13.4)); enable it as QG-0 enforcement.
2. Enable generation by several models for artifacts with `priority: must`.
3. Deploy the knowledge graph ([reference/05](../../reference/en/05-knowledge-graph-schema.md)).
4. Set up the targeted Hallucination Rate and Multi-model Disagreement Rate metrics ([chapter 12](12-metrics.md)).
5. Introduce a cost and latency budget with automatic decomposition on overrun.
6. Establish the practice of feeding template improvements back into the `requirements-library`.
7. Continuous evaluation for all `SPEC-AI` artifacts ([§8.5.7](08-specifications.md#8.5.7)).

Expected time: 1–2 quarters after RENAR-4.

### 11.11.5 Reverse transitions (level downgrade)

A normative downgrade ([§13.8.2](13-conformance.md#13.8.2)) — issuing a new manifest version with a level lower than the current one — is permitted given a formal justification (for example, decommissioning an AI-critical component, simplifying the substrate). A downgrade MUST be accompanied by an audit-trail record; concealing a downgrade is a violation of a mandatory clause ([§13.3.1](13-conformance.md#13.3.1)).

---

## 11.12 Relationship to the SENAR ADR (Adversarial Detection Rate)

SENAR §9 defines ADR — Adversarial Detection Rate — as a general metric of a process's ability to detect errors before release to production. RENAR-M defines at which levels a normative adversarial-review infrastructure exists. The RENAR-specific subclass of ADR for the requirements zone is **ACR** (Adversarial Catch Rate, [§12.3.6](12-metrics.md#12.3.6)).

| RENAR-M | Normative adversarial-review infrastructure | ADR / ACR measurability |
|---|---|---|
| RENAR-1, RENAR-2 | Absent ([§11.4](#11.4), [§11.5](#11.5)) | n/a |
| RENAR-3 | Normatively absent; the team MAY introduce adversarial review as a local tightening (`declared-stricter`, [§13.4.2](13-conformance.md#13.4.2)) | optional |
| RENAR-4 | Pos/neg TC pairing ([§9.7](09-test-cases.md#9.7)) — a structural proxy for ADR; adversarial review of artifacts is not normatively required | optional (pos/neg coverage is measurable as a proxy) |
| RENAR-5 | Adversarial review as a mandatory gate ([§11.8.1](#11.8.1)) + multi-model agreement for `priority: must` | normatively measurable (ACR, [§12.3.6](12-metrics.md#12.3.6)) |

RENAR-M maturity is linked to the SENAR ADR metric **continuously**, without duplication: SENAR ADR sets the concept of the metric; the RENAR-M level defines which adversarial cycles are normative; ACR ([§12.3.6](12-metrics.md#12.3.6)) is the domain-specific metric for the requirements zone.

---

## 11.13 Relationship to other chapters

The maturity model is the map onto which the requirements of all the other chapters are placed: each chapter "turns on" at its own level. The table below shows exactly where.

| Chapter | Relationship |
|---|---|
| [02 Positioning in the methodology typology](02-methodology-positioning.md) | §2.3 Source-of-Truth inversion + §2.5 substrate-independent versioning — mandatory clauses regardless of level; §2.4 the four distinctions — observed at all levels, starting with RENAR-2 |
| [06 Requirements hierarchy](06-requirements-hierarchy.md) | artifact frontmatter and mandatory fields are checked at RENAR-3+; the BR/SR/TR hierarchy — at all levels |
| [07 ADAPT](07-adapt.md) | Reactive ADAPT — a mandatory clause regardless of level (§11.4.1): an adversarial review for each TZ, ADAPT per the verdict; the Architect's signature on an ADAPT (QG-3) — declared at RENAR-4+; the dual signature is on the ACTZ (§7.13) |
| [08 Specifications](08-specifications.md) | The closed list of 12 SPEC types — mandatory; full type-specific coverage at RENAR-4+; continuous evaluation of SPEC-AI at RENAR-5 |
| [09 Test cases](09-test-cases.md) | TC for `priority: must` at RENAR-3; pos/neg pairing at RENAR-4+; the spot-check process at RENAR-4+ |
| [10 Lifecycle and QG](10-lifecycle-qg.md) | QG-0/QG-1/QG-2 declared required at all levels; substrate enforcement is gradual — partial at RENAR-2, full at RENAR-4+; QG-3 declared at RENAR-4+ if ADAPT is used |
| [03 Substrate versioning](03-substrate-versioning.md) | V1–V6 — an absolute mandatory clause for any level; a substrate without V1–V6 has no RENAR-M level (§11.10) |
| [12 Metrics](12-metrics.md) | the Hallucination Rate / Multi-model Disagreement Rate / Defect Escape Rate metrics are measured at RENAR-4+; the precise definitions are in [chapter 12](12-metrics.md) |
| [13 Conformance](13-conformance.md) | §13.2 references this chapter for the criteria of each level; §13.5 self-assessment uses the checklists of §§11.4–12.8 (one section per level); §13.9 the closed-list policy applies to the closed list RENAR-1..5 (§11.3) |
