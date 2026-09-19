---
title: "Introduction"
order: 0
lang: en
---
# 00. Introduction

> **Part of the RENAR Standard v1.1** · [← Table of contents](README.md)

> **For newcomers.** This chapter is normative and dense (RFC-2119, closed lists, mandatory clauses). If you are a human meeting RENAR for the first time — start with the conceptual overview [`core/renar-core.md`](../../core/en/renar-core.md) (≤ 10 min), then [`guide/00-quickstart`](../../guide/en/00-quickstart.md) (≈ 30 min), then come back here. If you are an AI agent — go straight to the normative chapters.

## 0.1 Why this chapter

The client wrote in the TZ: "the user exports a report." The engineer read it as a PDF export, the specification named CSV, and the test ends up checking Excel. There is no ill intent — the requirement simply lives in five places at once (the client's contractual TZ, the engineering decomposition, the specifications, the test cases, the code), and at each seam the meaning shifts a little. When artifacts are written by a mix of humans and AI agents, there are more seams and the shift is faster. So the bottleneck of development is no longer code speed but **requirements correctness**. RENAR exists so that meaning does not leak at these seams: it sets explicit contracts between artifacts.

This chapter answers four questions: **what** RENAR is (§0.2), **why** it is needed (§0.3), **how** it relates to SENAR (§0.4), and **what the minimum** is below which conformance cannot be claimed — the Minimum Viable RENAR (MVR; §0.5). Closed lists and the language of the corpus — §0.6, §0.7.

This chapter introduces **no** new normative requirements. Each of the seven MVR statements is a reference to a §1–§13 chapter where it becomes a precise norm; here it is given as a coherent whole.

> **Modal-verb convention.** Normative force is expressed with UPPERCASE RFC-2119 keywords: `MUST` / `SHALL` / `REQUIRED` — the mandatory level; `SHOULD` / `RECOMMENDED` — the recommended level; `MAY` / `OPTIONAL` — the permitted level; `MUST NOT` / `SHALL NOT` — prohibition. Conformance follows RFC 2119 + ISO/IEC/IEEE 29148:2018 §5.2.1. This convention applies in all normative chapters. (The RU corpus uses lowercase modals as its canonical form under the RFC 8174 carve-out; the EN corpus uses UPPERCASE — see the [EN Style Guide §2](../../reference/en/06-en-style-guide.md).)

---

## 0.2 What RENAR is

**RENAR** (*Requirements Engineering & Normative Adaptive Regulation*) is a normative requirements-management standard for development with AI agents. The standard governs:

- The **data model** of requirement artifacts (BR / SR / TR), ADAPT, ACTZ, the twelve SPEC types, TC, and AT.
- The **lifecycle** of artifacts through a closed list of Quality Gates (QG-0 / QG-1 / QG-2 mandatory; QG-3 / QG-4 optional).
- The **substrate capabilities** V1–V6.
- **Conformance** — the RENAR-1..RENAR-5 levels, mandatory clauses, the manifest, and assessment procedures.

The standard does not govern the order of work: it sets preconditions (what MUST exist before approval, a transition, a presentation) and the rules linking artifacts; the sequence of actions is the subject of the organization's production model ([§1.2.1](01-scope.md#1.2.1)).

RENAR is a **specialization of SENAR** ([§1.6](01-scope.md#1.6)) in the requirements-engineering domain. A RENAR-conformant implementation is **always** SENAR-compatible; the converse does not hold ([§1.6.2](01-scope.md#1.6.2)).

RENAR is not tied to a specific substrate (VCS, document store, wiki): the normative chapters use substrate-independent language and govern the **capabilities** V1–V6 ([§3.1](03-substrate-versioning.md#3.1)). RENAR is a standard, **not** an implementation.

### 0.2.1 The AI agent as a regular implementer

RENAR artifacts are **routinely** created and maintained by an AI agent under an engineer's assignment. The human acts as reviewer and approver. This is a normative positioning, not a methodological recommendation.

Consequences:

1. **Completeness** — the completeness requirements for artifacts: the AI agent executes "code in natural language," and without completeness, provenance breaks down ([§0.3](#03-why-renar-exists)).
2. **frontmatter density** — the dozens of fields are a consequence of the machine nature of the primary reader. In a multilingual UI, fields MAY be displayed human-readably ([§4.13.3](04-terms.md#4.13.3)).
3. **Compensating mechanisms** — the ACTZ dual signature ([§7.13](07-adapt.md#7.13)), the Architect's signature on an ADAPT ([§7.5](07-adapt.md#7.5)), test-authorship isolation and the red history ([§9.18](09-test-cases.md#9.18)), the engineer's spot-check ([§9.14](09-test-cases.md)), adversarial review ([§7.10.2](07-adapt.md#7.10.2)), and the Quality Gates ([§10.3](10-lifecycle-qg.md#10.3)) ensure that the human remains the source of decisions on contractual outcomes.

What §0.2.1 does **not** assert: RENAR does not require an AI agent for conformance — the standard is implementable by hand, but the process overhead makes that impractical ([§1.4.1](01-scope.md#1.4.1), indicator 5). The AI agent does not replace human approval — all normative signatures are performed by a human. The AI agent acts as responsible (R in RACI [§5.6](05-roles.md#5.6)), not accountable (A).

---

## 0.3 Why RENAR exists

In development with AI agents, requirements live simultaneously in several artifacts created and maintained by a mix of human and AI-agent authors. Without normative contracts between them, **requirements drift** arises — a divergence between what is recorded, what is verified, and what is implemented.

RENAR closes **eight normative drift classes** (the full list with enforcement points — [§4.11](04-terms.md#4.11); the conceptual description — [core/renar-core.md](../../core/en/renar-core.md)). All eight are **structural**: they arise from the very fact that an artifact is jointly owned by several authors, not from lapses in discipline. They can be closed only normatively — by fixing the contract of links, field authorship, and transition preconditions ([§13.3](13-conformance.md#13.3), mandatory clauses).

The contract is machine-enforceable only on the condition that artifacts are complete ([§0.2.1](#021-the-ai-agent-as-a-regular-implementer)). An artifact with defaults is a contract with holes through which drift passes regardless of any hooks: a hook sees only what is recorded. The same applies to canonical names and closed lists ([§4.2](04-terms.md#4.2)): a hook compares strings, it does not interpret synonyms.

---

## 0.4 Relationship to SENAR

RENAR governs **only** those aspects of requirements engineering that SENAR leaves to the domain standard's discretion: the artifact data model, the lifecycle of requirement artifacts, and the conformance manifest for requirements engineering. RENAR does **not** duplicate and does **not** override SENAR constructs (the 5 values, 14 rules, common Quality Gates, 5 base roles).

The project conformance manifest ([§13.4](13-conformance.md#13.4)) MUST declare both `senar-version` and `renar-version`. A claim of RENAR conformance without a compatible SENAR version is non-conformant ([§13.8](13-conformance.md#13.8)).

If a RENAR normative statement turns out to be incompatible with a SENAR norm, that is a bug in the RENAR Standard. The resolution is through the formal change procedure of the SENAR Standard, with a corresponding alignment in RENAR ([§13.9](13-conformance.md#13.9)), not through a project-local override.

| Aspect | SENAR (base) | RENAR (requirements-engineering specialization) |
|---|---|---|
| 5 values + 14 rules + 5 roles | governs | inherits |
| Common Quality Gates | general framework | closed list of canonical QG-0..QG-4 ([§10.3](10-lifecycle-qg.md#10.3)) |
| Artifact data model | — (out of scope) | BR / SR / TR / ADAPT / ACTZ / 12 SPEC types / TC / AT ([§6](06-requirements-hierarchy.md)–[§9](09-test-cases.md)) |
| Requirement-artifact lifecycle | — | canonical state machines ([§10](10-lifecycle-qg.md)) |
| Substrate capabilities | — | V1–V6 ([§3](03-substrate-versioning.md)) |
| Conformance manifest | generic SENAR | RENAR-CONFORMANCE.yaml + mandatory clauses ([§13](13-conformance.md)) |

**RENAR's original contribution** (not inherited from SENAR): the requirements data model; ADAPT with two-way adaptation and the Architect's signature; ACTZ with the dual signature and the effective TZ as the acceptance baseline; substrate-independent V1–V6; the canonical lifecycle and QG for the requirements axis; pos/neg pairing and judge ≠ production as blocking clauses; the RENAR-1..RENAR-5 conformance levels.

---

## 0.5 Minimum Viable RENAR (MVR)

**Minimum Viable RENAR** is a closed list of seven normative statements, mandatory for any RENAR-conformant implementation regardless of the declared level (including `RENAR-1`). The MVR is a **subset** of the mandatory clauses of [§13.3](13-conformance.md#13.3), not a full match: MVR-7 maps onto [§13.4](13-conformance.md#13.4), while §13.3.7 and §13.3.8 belong to no MVR.

| # | Statement[¹] | Normative source |
|---|---|---|
| MVR-1 | **Source-of-Truth inversion**: the requirement-artifact hierarchy MUST be the source of truth about system behavior; code is a derived artifact. Reverse-engineering behavior from code into an SR without a bug-fix justification is prohibited. | [§2.3](02-methodology-positioning.md#2.3), [§13.3.1](13-conformance.md#13.3.1) |
| MVR-2 | **V1–V6 capabilities**: the project substrate MUST satisfy all six capabilities — immutable history (V1), atomic change unit (V2), diff & review (V3), branching / change-set (V4), cross-substrate version pin (V5), author + timestamp (V6). | [§3.3](03-substrate-versioning.md#3.3), [§13.3.2](13-conformance.md#13.3.2) |
| MVR-3 | **Reactive, stage-agnostic ADAPT (0..N per TZ)**: ADAPT is a reactive artifact, created if and only if converting a TZ → RENAR description **at any derivation stage** produces a gap between the client's language and the requirements language. A single TZ has **zero or more** ADAPTs; each is bound to its trigger (TZ import or a specific decomposition stage) through `trigger-stage`, and multiplicity is the regular case. When a gap is present, the ADAPT is REQUIRED to be in status `approved` with the Architect's signature; the client's decisions are recorded in signed ACTZ (§7.13), and the acceptance baseline is the effective TZ = the initial TZ + all signed ACTZ (§7.14). When no gap is present (the adversarial reviewer returned a "no findings" verdict), no ADAPT is created; BR/SR/SPEC reference the TZ directly through the mandatory `source.tz-section` + `source.adversarial-review-ref` → AR ([§7.4.6](07-adapt.md#7.4.6)). In either outcome the review result is issued as an AR. A delta-TZ follows the same rule. | [§7](07-adapt.md), [§13.3.3](13-conformance.md#13.3.3) |
| MVR-4 | **Closed list of 12 SPEC types**: a SPEC type MUST belong to the closed list `SPEC-ARCH` / `API` / `DATA` / `INT` / `PROC` / `UI` / `AI` / `SEC` / `OPS` / `TEST` / `DOC` / `UC`. | [§8.3](08-specifications.md#8.3), [§13.3.4](13-conformance.md#13.3.4) |
| MVR-5 | **TC pos/neg pairing**: every normative statement of a set version that describes observable behaviour MUST be covered by a positive + negative pair of TC norms of the same version — a precondition of version approval. The exception is when the statement itself describes a negative invariant. | [§9.7](09-test-cases.md#9.7), [§13.3.5](13-conformance.md#13.3.5) |
| MVR-6 | **Closed list of Quality Gates**: an implementation MUST support QG-0 (Approval), QG-1 (Implementation), QG-2 (Verification) as `required`. QG-3 / QG-4 are `declared` or `absent`. Creating new gate types locally is prohibited. | [§10.3](10-lifecycle-qg.md#10.3), [§13.3.6](13-conformance.md#13.3.6) |
| MVR-7 | **Conformance manifest**: a project MUST contain a manifest with both `renar-version` + `senar-version` + `level` (RENAR-1..RENAR-5) + confirmation of the §13.3 mandatory clauses. The manifest is immutable (V1). | [§13.4](13-conformance.md#13.4) |

[¹] Each row states a mandatory-level requirement (RFC-2119 `MUST` / `SHALL`) as interpreted by ISO/IEC/IEEE 29148:2018 §5.2.1.

An implementation satisfying all seven MVR conforms to at least the `RENAR-1` level ([§13.2.1](13-conformance.md#13.2.1)). An implementation that violates even one MVR **has no** RENAR conformance regardless of the declared level ([§13.8](13-conformance.md#13.8)).

---

## 0.6 Closed-list policy

The list of seven MVR is closed at v1.0. A project MUST NOT extend the MVR locally or shorten the list.

An implementation MAY **tighten** requirements beyond the MVR through the `declared-stricter` marker ([§10.10.2](10-lifecycle-qg.md#10.10.2), [§13.4](13-conformance.md#13.4)): require QG-3/QG-4 as `required`, require adversarial review of TC on all SPEC types, declare `RENAR-3+` as the minimum.

An implementation MUST NOT declare-weaker: issue a RENAR conformance claim without supporting one of MVR-1..MVR-7; declare ADAPT optional; allow single-TC coverage for a normative statement without the negative-invariant exception; omit `senar-version` or `level` in the manifest.

A change to the MVR is made only through the formal change procedure of the RENAR Standard ([§13.9](13-conformance.md#13.9)): research draft → public review → minor-version bump → migration guidance.

---

## 0.7 Corpus language

The RENAR normative corpus is bilingual: an RU edition (`standard/`, primary) and an EN edition (`standard/en/`, this file). Both are hybrids: **canonical identifiers** (latin and closed-list abbreviations) + connective prose in the edition's language. The policy is fixed by the [EN Style Guide](../../reference/en/06-en-style-guide.md) for the EN corpus and by [reference/06](../../reference/06-ru-style-guide.md) for the RU corpus, alongside [§4.2](04-terms.md#4.2). Artifacts and IDs, lifecycle statuses, VCS domain terms, and accepted technical terms stay latin in both editions; the editions differ only in the connective prose and in RFC-2119 polarity (EN UPPERCASE vs RU lowercase).

**The language of project artifacts** is not the subject of this paragraph: the normative sections of BR / SR / SPEC / TC / ADAPT / ACTZ are written by the rules of [chapter 15](15-description-language.md).

**Substrate.** The storage concept is, in the EN corpus, simply **`substrate`** — the canonical term used in prose, field names (`substrate-capabilities`), code/YAML, and file names alike. (The RU corpus renders the prose form as «носитель» and keeps `substrate` only in identifiers; see [§4.2](04-terms.md#4.2).)

---

**[Table of contents](README.md)** · **[Next: 01. Scope →](01-scope.md)**
