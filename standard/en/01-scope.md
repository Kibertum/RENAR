---
title: "Scope"
order: 1
lang: en
---
# 01. Scope

> **Part of the RENAR Standard v1.1** · [← Table of contents](README.md)

## 1.1 Where RENAR works and where it does not

Two teams write code with AI agents. The first builds a banking module against a signed TZ: there is a client, an acceptance, an audit — every requirement must be demonstrable. The second tests hypotheses at startup speed: a "requirement" exists today and is gone tomorrow after a pivot. RENAR is for the first. It is built where there is a contractual TZ and a party held accountable for it: contract development, regulated industries, consulting against someone else's TZ. In pure product discovery — "first build, then understand what was built" — RENAR is not merely redundant, it is structurally inapplicable: there is no immutable TZ, nothing from which to build an ADAPT. This chapter draws both boundaries — the **subject-matter** one (what the standard governs) and the **contextual** one (when it should be used at all) — through the closed lists of §1.2–§1.5.

The substantive norms of artifacts and the lifecycle are **not** set by this chapter — that is the domain of chapters 06–14; substrate-native implementation mechanisms are the domain of [chapter 3](03-substrate-versioning.md) and `guide/03-tool-guide-*.md`.

---

## 1.2 What RENAR governs (closed list)

The closed list of RENAR's normative areas at version v1.0. Each area is covered in the indicated chapter:

| # | Area | Chapter |
|---|---|---|
| 1 | Requirements hierarchy (BR / SR / TR) | [06](06-requirements-hierarchy.md) |
| 2 | ADAPT (two-way adaptation of the TZ): Forward interpretation + Backward findings + the Architect's signature; ACTZ with the dual signature | [07](07-adapt.md) |
| 3 | Closed list of 12 specification types (SPEC-ARCH / API / DATA / INT / PROC / UI / AI / SEC / OPS / TEST / DOC / UC) | [08](08-specifications.md) |
| 4 | Test Cases as a full-fledged artifact (TC); pos/neg pairing; spec-specific TC obligations | [09](09-test-cases.md) |
| 5 | Lifecycle states + Quality Gates (QG-0 / QG-1 / QG-2 mandatory; QG-3 / QG-4 optional) | [10](10-lifecycle-qg.md) |
| 6 | substrate-independent versioning capabilities V1–V6 | [03](03-substrate-versioning.md) |
| 7 | Maturity model (RENAR-1..RENAR-5) | [11](11-maturity-model.md) |
| 8 | Requirements-engineering metrics (RDLT, Hallucination Rate, DRA, ACR, etc.) | [12](12-metrics.md) |
| 9 | Conformance (mandatory clauses, manifest, self-assessment, third-party assessment) | [13](13-conformance.md) |
| 10 | Roles and responsibility for artifacts (specializations over SENAR §4) | [05](05-roles.md) |

All of the listed areas have normative content: mandatory requirements and a prohibition on project-local extensions of the corresponding closed lists.

### 1.2.1 What falls within the normative area

The RENAR normative area covers:

- **The artifact data model** — mandatory frontmatter fields, types, enum values, consistency invariants.
- **Lifecycle states + transitions** — the state machines of the description set, the task, ADAPT and the TC implementation ([§10.7](10-lifecycle-qg.md#10.7)); pre/post-conditions; the gate-id for each transition.
- **Identity and provenance** — immutable identifiers; substrate-native recording of authorship and time (V6); version pin between artifacts (V5).
- **Cross-artifact constraints** — `verifies[]`, `constrained-by[]`, `parent`, `delta-of`, `verified-by`, `implements-spec` — the normative semantics of links and the consistency rules.
- **AI provenance** — mandatory recording of the generator model, prompt-template, and token volume; the human-edits rules for approval.
- **Conformance procedures** — mandatory clauses, the manifest schema, the assessment cadence, the handling of conformance loss.

**The order of work is outside the normative area.** The standard sets what MUST exist, and in what state, before a version is approved, a transition is made or a result is presented — the preconditions and the rules linking artifacts. The sequence and grouping of actions, the distribution across the roles of a production pipeline, planning — are the subject of the organization's production model ([§1.3](#1.3), item 7). Any order of work under which the preconditions hold conforms to the standard; a sequence given in a guide or an example is one possible order, not a norm. An organization MAY add artifacts and rules of its own — that is `declared-stricter` ([§1.7.2](#1.7.2)) as long as the closed lists and the preconditions are not violated.

---

## 1.3 What RENAR explicitly does NOT govern (closed list)

The closed list of areas left **deliberately** outside the standard. Implementation in these areas is a free choice and does not affect conformance.

| # | Area | What is out of scope |
|---|---|---|
| 1 | **The SENAR methodology as a whole** | The 5 values, 14 rules, common Quality Gates, agent instrumentation — governed by SENAR; RENAR neither duplicates nor overrides them. |
| 2 | **A specific substrate / VCS** | The choice of a specific version-control / document-database / wiki-platform is at the implementation's discretion; RENAR governs only the capabilities V1–V6 ([§3](03-substrate-versioning.md)). |
| 3 | **The implementation tech stack** | Programming languages, frameworks, databases, infrastructure components — out of scope; RENAR governs requirements, not the implementation. |
| 4 | **A specific UI / IDE / artifact editor** | Web interfaces, IDE extensions, CLI tools — out of scope; only the storage format of artifacts in the substrate is governed. |
| 5 | **Specific test runners** | pytest, jest, playwright, ragas, etc. — substrate-specific; RENAR governs only the mandatory recording of `automation.location` and `last-run` (V5+V6). |
| 6 | **Sales / contract business processes** | Pre-sale, formulation of the contractual price, the legal structure of contracts — out of scope; RENAR treats the TZ as an **input** and does not govern the process of its creation on the client side. |
| 7 | **Project management practices and the production process** | Agile ceremonies, sprint planning, kanban boards, story-point estimation, the order and grouping of work on artifacts, the distribution across pipeline roles — out of scope; RENAR governs the lifecycles of artifacts and the preconditions of their transitions ([§1.2.1](#1.2.1)), not the order of work (item text refined in v1.1, ADR-027). |
| 8 | **AI model selection and prompt engineering** | The choice of LLM provider, prompt-templates, fine-tuning strategies — out of scope; RENAR governs only the mandatory recording of the `ai-provenance` block in the composition of [§4.10.1](04-terms.md#4.10.1) — including `prompt-template` as a versioned pointer, but not the template's content — and adversarial review ([§7.10.2](07-adapt.md#7.10.2)). |
| 9 | **Specific substrate-native commands and hooks** | substrate-specific CLI commands (for example, specific VCS command names), the implementation of hooks ([§10.11](10-lifecycle-qg.md#10.11)) — substrate-specific and belong in `guide/03-tool-guide-*.md`. |
| 10 | **Legal interpretation of artifact signatures** | Electronic signature, legal force, GDPR processing of personal data in the TZ — outside the standard's scope; governed by applicable law. |

### 1.3.1 The substrate-independence principle

The RENAR Standard's normative chapters use substrate-independent terminology ([§3.1](03-substrate-versioning.md#3.1)). Substrate-dependent names (specific products, protocols, commands) do not appear in the normative text; they are present only in `guide/` (substrate-specific tools) and `reference/` (examples).

---

## 1.4 Scope of applicability (primary scope)

### 1.4.1 Contract-oriented development

RENAR's normative primary context is **contract-oriented development**: a project in which:

1. **A TZ exists** (an explicitly formulated requirement from the client side) which, once signed, becomes **immutable** ([§7.4.1](07-adapt.md#7.4.1) ADAPT source).
2. **An identifiable client party exists** (a stakeholder with signing authority, [§5.3.6](05-roles.md#5.3.6)) — able to confirm acceptance ([§10.4.2](10-lifecycle-qg.md#10.4.2)) and to sign an ACTZ ([§5.5](05-roles.md#5.5)).
3. **Reactive two-way client-side validation** — adversarial review of the TZ is mandatory; if findings are discovered or clarification is needed, the questions are put to the client as an ACTZ ([§7.13](07-adapt.md#7.13)), and the client's decisions are recorded in the ADAPT ([§7.3](07-adapt.md#7.3), [§7.4.1](07-adapt.md#7.4.1)). If the conversion is unambiguous, validation reduces to a recorded "no findings" verdict (with no client interaction).
4. **A delta-TZ workflow** is possible — scope changes are formalized through a delta-ADAPT ([§7.6](07-adapt.md#7.6)), not through a hidden reinterpretation.
5. **An AI agent is available as the primary author** of RENAR artifacts ([§0.2.1](00-introduction.md#0.2.1)). Without an AI agent the standard remains applicable, but the process overhead of maintaining artifacts by hand — with full frontmatter, lifecycle transitions, and graph links — makes RENAR conformance impractical; the team either accepts that overhead or applies a `declared-stricter` limited scope ([§1.7.2](#1.7.2)) to a critical subset of requirements.

### 1.4.2 Typical representatives of contract-oriented development

| Context | Characteristics |
|---|---|
| **Contract development against a TZ** | Independent vendor + identifiable client; formal contract; acceptance criteria. |
| **Regulated industries** | A compliance audit is mandatory (healthcare, finance, public sector); traceability requirements → tests → code is required by regulation. |
| **Enterprise consulting** | A third party implements against the corporate client's TZ; approval by several stakeholders; an audit log. |
| **Long-lived product with an explicit product owner** | The Product Owner plays the role of the client's representative for internal feature TZs; an internal SLA + audit are mandatory. |
| **Public-sector / government IT** | Tender TZs; formal acceptance; multi-year contracts. |

### 1.4.3 Spec-Driven Development (SDD) — the modern name

Contract-oriented development with AI acceleration is a form of **Spec-Driven Development** ([§2.3.4](02-methodology-positioning.md#2.3.4)). RENAR is the normative standard for AI-native SDD; it is not an alternative to SDD but its specialization in the requirements-management domain.

---

### 1.4.4 First-party confirmation — a concept as the input

A separate **kind of applicability**, added in v1.1 ([§13.9](13-conformance.md#13.9)): there is no external client, and nobody imitates that role. Indicators:

1. **The input is a concept**, not a TZ: a written description of the future system, composed by a responsible person of the organization and frozen by them (V6 author and timestamp). The concept takes the place of the TZ as the source (`source.tz-section` points to its section) and carries the same immutability obligations ([§7.4.1](07-adapt.md#7.4.1)).
2. **No contractual contour arises.** The ACTZ ([§5.5](05-roles.md#5.5), [§7.13](07-adapt.md#7.13)) has no subject — there is nobody to put decisions to; the dual signature is **not imitated** by one person. A backward finding that requires a decision on the concept is closed by a revised section of the concept, signed by the same responsible person: `decided-in` points to it ([§7.4.5](07-adapt.md#7.4.5)).
3. **The adversarial review is mandatory without exception** ([§7.4.1](07-adapt.md#7.4.1), §13.3.3): it is the only independent check in this kind, so an AR with the verdict "no findings" MUST carry a presentable rationale, the reviewer model is recorded, and the ACR ([§12.3.6](12-metrics.md#12.3.6)) is kept mandatorily.
4. **Presentation — against the concept.** The AT release gate ([§10.4.3](10-lifecycle-qg.md#10.4.3)) has no subject: ATs are derived from a contract, and there is none. Readiness is presented by the verification entry of the set version (QG-2, [§10.3.3](10-lifecycle-qg.md#10.3.3)) signed by the responsible person; manual walkthrough records over the scenarios ([§9.20](09-test-cases.md#9.20)) are an additional detection channel and are not part of the proof of readiness.
5. **Labelling of the applicability kind.** The manifest declares `confirmation: first-party` ([§13.4.2](13-conformance.md#13.4.2)); the conformance claim is a first-party confirmation in the sense of ISO/IEC 17050-1, not a second-party acceptance.

The kind is orthogonal to the maturity level: it sets no RENAR-N ceiling. The appearance of an identifiable stakeholder with acceptance authority moves the project into the contract-oriented kind ([§1.4.1](#1.4.1)) by withdrawing the declaration; no retrospective re-assessment is required.

**Combining roles** in this kind is the norm, not a deviation: one person MAY combine roles ([§5.5.5](05-roles.md#5.5.5)); adversarial checking by people is then absent, and this is an accepted trade-off that the standard does not compensate with additional mechanisms but names honestly.

---

## 1.5 Where RENAR is inapplicable (negative scope)

RENAR is normatively inapplicable in contexts where the preconditions of §1.4.1 are structurally absent. A claim of RENAR conformance ([§13.4](13-conformance.md#13.4)) for projects in these contexts is non-conformant ([§13.8](13-conformance.md#13.8)).

### 1.5.1 Lean startup / pure discovery

A product team builds an MVP under market uncertainty; "requirements" are hypotheses validated against users, not immutable agreements. Out of scope: there is no immutable TZ (hypotheses are re-checked after a pivot); a client representative is structurally absent (the internal product manager = author + sole assessor — a violation of [§5.5.3](05-roles.md#5.5.3)); the delta-TZ workflow does not apply (a pivot = a change of scope **as a whole**, not a delta). Lean startup teams MAY borrow **individual practices** of RENAR (AI provenance, adversarial review of TC) without claiming conformance — permitted as a source of ideas.

### 1.5.2 Pure R&D / research projects

A research project with no defined resulting scope (exploratory ML research, novel-algorithm prototyping without a client). Out of scope: there is no TZ as an immutable artifact; acceptance criteria ([QG-4 §10.4.2](10-lifecycle-qg.md#10.4.2)) cannot be formalized — the "success" criterion of scientific research is not signed in advance; the ACTZ dual signature does not apply.

### 1.5.3 Exploratory hackathon / proof-of-concept

Time-boxed exploratory work with no mandatory client acceptance. The same reasons as §1.5.1 + an explicit waiver of formal acceptance.

### 1.5.4 Internal product without a responsible person and a written concept

An internal tool that has neither a responsible person ready to freeze a concept and sign a first-party confirmation ([§1.4.4](#1.4.4)) nor a written concept at all: requirements live in discussions, no "client" is named. The absence of an external client by itself does **not** take a project out of the applicability area — the absence of a person responsible for the description does. Imitating the ACTZ dual signature by one person (`client-signature.signed-by == vendor-signature.signed-by`) remains a violation of [§5.5.3](05-roles.md#5.5.3): the correct path is the kind of §1.4.4, where the ACTZ has no subject.

An implementation MAY apply a subset of RENAR practices **locally** (immutable identifiers, lifecycle states, V1–V6 for its own discipline), but it MUST NOT claim RENAR-N conformance. The manifest either does not exist or explicitly declares "non-conformance."

**Negative scenario:** an attempt to declare RENAR-N for an internal product that has neither a frozen concept nor a responsible person is non-conformant ([§13.8](13-conformance.md#13.8)). If the product acquires a responsible person and a written concept — the scenario moves out of §1.5 into §1.4.4 (first-party confirmation); if it acquires an identifiable stakeholder with acceptance authority — into §1.4.2 "Long-lived product with an explicit product owner," and full conformance applies.

### 1.5.5 Negative scenarios — specifics

| Scenario | Why it is non-conformant |
|---|---|
| A project with no written TZ; requirements are verbal discussions | Violation of §1.4.1 (1): there is no TZ as an immutable artifact; ADAPT has no source ([§7.4.1](07-adapt.md#7.4.1)). |
| A project without a second party in which one person signs the ACTZ for both sides | Violation of [§5.5.3](05-roles.md#5.5.3): the dual signature is not imitated; without a second party the ACTZ has no subject, the kind of §1.4.4 with `confirmation: first-party` applies. |
| A project where scope is revised without a formal delta-TZ record | Violation of the [§7.6](07-adapt.md#7.6) delta-ADAPT workflow; violation of the [§13.3](13-conformance.md#13.3) mandatory clause §13.3.3 "Reactive ADAPT: adversarial review for every TZ, an ADAPT per the verdict." |
| A manifest that declares tech-stack-specific requirements (for example, "Python is mandatory") | Violation of §1.3 (3): the tech stack is outside the standard's scope; the manifest is non-conformant. |

---

## 1.6 Relationship to SENAR

### 1.6.1 RENAR as a specialization of SENAR

SENAR is the **methodological base**: the 5 values of AI-native development, the 14 rules, agent instructions, common Quality Gates ([§10.2.3](10-lifecycle-qg.md#10.2.3)), 5 base roles ([§5.2](05-roles.md#5.2)).

RENAR is **a specialization of SENAR in the requirements-engineering domain**: it governs only those aspects that SENAR leaves to the domain standard's discretion (the artifact data model, the lifecycle, the conformance manifest). RENAR does not duplicate SENAR and does not override SENAR constructs.

### 1.6.2 Compatibility

A RENAR-conformant implementation is **always** SENAR-compatible. The converse does not necessarily hold: a SENAR-compatible project MAY **not** claim RENAR conformance if it operates outside the primary scope of §1.4.

An incompatibility of RENAR with SENAR at any normative point is a bug in the RENAR Standard; it is resolved through the formal change procedure of the SENAR Standard, with a corresponding alignment in RENAR.

### 1.6.3 RENAR is not an alternative to SENAR

An implementation MUST NOT claim "we follow RENAR instead of SENAR" — this is a violation of §1.6.1. RENAR is used on top of SENAR; the project's conformance manifest declares both the SENAR version and the RENAR version simultaneously ([§13.4](13-conformance.md#13.4)).

---

## 1.7 Closed-list policy (closed list)

### 1.7.1 What is closed at v1

The lists §1.2 (normative areas), §1.3 (exclusions), §1.4 (primary scope), §1.5 (negative scope) are closed. Project-local extensions and attempts to govern excluded areas through the manifest ([§1.3 (3)](#1.3) tech stack, [§1.3 (7)](#1.3) PM practices) are non-conformant. Adding new primary contexts or moving a scenario from §1.5 into §1.4 is done only through the formal change procedure of the standard.

### 1.7.2 Declared-stricter is permitted

An implementation MAY **tighten** scope relative to the normative minimum, declaring it explicitly in the conformance manifest ([§13.4](13-conformance.md#13.4)) with the `declared-stricter` marker ([§10.10.2](10-lifecycle-qg.md#10.10.2)): apply RENAR only to a subset of requirements (security-critical SR); require additional artifact types (threat-models); prohibit RENAR without a Client representative. Declared-stricter is a permitted local policy; conformance to the normative level is preserved.

### 1.7.3 Declared-weaker is prohibited

An implementation MUST NOT declare-weaker relative to §1.2 / §1.3 / §1.4: claim RENAR conformance for a project in the §1.5 negative scope; apply RENAR without ADAPT (a violation of [§13.3.3](13-conformance.md#13.3.3)); govern excluded §1.3 areas through a project-local manifest.

### 1.7.4 The extension path

A change to §1.2 / §1.3 / §1.4 / §1.5 is done only through the formal change procedure of the standard ([§13.9](13-conformance.md#13.9)): a research draft with justification → public review → a minor- or major-version bump → migration guidance for existing conformant projects.

### 1.7.5 Master list of closed lists in RENAR

This paragraph is the single index of all closed lists in the RENAR Standard. Each listed list is non-extensible locally at the project level; changes are possible only through the formal change procedure of the standard ([§13.9](13-conformance.md#13.9)). The canonical source for each list is in the indicated section; the other mentions are cross-references.

| # | Closed list | Canonical source | Cross-refs |
|---|---|---|---|
| 1 | Normative areas of the standard (10 items) | [§1.2](#1.2) | §1.7.1 |
| 2 | Exclusions from the normative area (10 items) | [§1.3](#1.3) | §1.7.1 |
| 3 | Primary scope: applicability contexts (5 indicators + 5 typical representatives; the kind "first-party confirmation" §1.4.4 with 5 indicators, v1.1) | [§1.4](#1.4) | §1.7.1 |
| 4 | Negative scope: inapplicability contexts (4 contexts + 4 negative scenarios; §1.5.4 and scenario 2 redefined in v1.1) | [§1.5](#1.5) | §1.7.1 |
| 5 | RENAR roles (specializations over SENAR §4) | [§5](05-roles.md) | §1.2 (10) |
| 5bis | Requirement axis types (3 types: BR / SR / TR) | [§6.2](06-requirements-hierarchy.md#6.2) | [§13.3](13-conformance.md#13.3) |
| 5ter | Decomposition levels (3: system / subsystem / module) | [§6.3](06-requirements-hierarchy.md#6.3) | [§6.4](06-requirements-hierarchy.md#6.4), [§4.9](04-terms.md#4.9) |
| 6 | ADAPT Backward findings categories (7 categories) | [§7.4.4](07-adapt.md#7.4.4) | [§13.3.7](13-conformance.md#13.3.7) |
| 7 | SPEC-* specification types (12 types: ARCH/API/DATA/INT/PROC/UI/AI/SEC/OPS/TEST/DOC/UC) | [§8.3](08-specifications.md#8.3) | [§4.4.1](04-terms.md#4.4.1), [§13.3.4](13-conformance.md#13.3.4) |
| 8 | Normative TC principles (P1–P9) | [§9.2](09-test-cases.md#9.2) | [§13.3](13-conformance.md#13.3) |
| 9 | TC types (`tc-type`: 6 values; `business` replaces the former `acceptance`) | [§9.5](09-test-cases.md#9.5) | [§4.5.2](04-terms.md#4.5.2) |
| 10 | Lifecycle states by object: description set (`draft` / `approved` / `superseded`), TR, ADAPT, TC implementation; BR / SR / SPEC / TC norm have no states of their own | [§10.5–§10.9](10-lifecycle-qg.md#10.5) | [§4.7](04-terms.md#4.7)–[§4.10](04-terms.md#4.10) |
| 11 | Quality Gates: mandatory {QG-0, QG-1, QG-2}, optional {QG-3, QG-4} — transitions of the description set, TR, ADAPT and admission of a TC implementation | [§10.3, §10.4](10-lifecycle-qg.md#10.3) | [§10.10](10-lifecycle-qg.md#10.10), [§13.3.6](13-conformance.md#13.3.6) |
| 12 | substrate-independent versioning capabilities V1–V6 | [§3.x](03-substrate-versioning.md) | §1.2 (6) |
| 13 | Maturity levels RENAR-1..RENAR-5 (5 levels) | [§11.3](11-maturity-model.md#11.3) | [§13.2](13-conformance.md#13.2), [§13.9](13-conformance.md#13.9) |
| 14 | REQ-specific metrics (11 metrics) | [§12.3](12-metrics.md#12.3) | [§12.5](12-metrics.md#12.5) |
| 15 | Drift classes (violations of the requirements infrastructure) | [§4.11](04-terms.md#4.11) | [§4.2](04-terms.md#4.2) |
| 16 | Prohibited / deprecated terms (non-canonical) | [§4.14](04-terms.md#4.14) | [§4.2](04-terms.md#4.2) |
| 17 | Modal operators of the description language (5: MUST / MUST NOT / SHOULD / SHOULD NOT / MAY; introduced in v1.1 per §13.9) | [§15.2](15-description-language.md#15.2) | [§11.5](11-maturity-model.md#11.5) |

The extension of any closed list goes through the same formal procedure as a change to §1.2–§1.5 ([§1.7.4](#1.7.4), [§13.9](13-conformance.md#13.9)).

Chapter-level closed-list policies ([§10.10](10-lifecycle-qg.md#10.10), [§12.3](12-metrics.md#12.3), [§13.9](13-conformance.md#13.9)) are specializations of this §1.7 for the corresponding lists and do **not** introduce independent procedures.

---

## 1.8 Cross-references

| Source | Use |
|---|---|
| SENAR (full methodology) | RENAR's methodological base (§1.6.1); RENAR does not duplicate SENAR norms. |
| [§5 Roles](05-roles.md) | Artifact ownership and roles — specializations over SENAR §4 (§1.2 (10), §1.6.1). |
| [§2 Methodology positioning](02-methodology-positioning.md) | Three foundational assertions (Source-of-Truth inversion, waterfall-form ≠ classical waterfall, substrate-independent versioning) — the justification of scope §1.4. |
| [§7 ADAPT](07-adapt.md) | ADAPT as the normative requirement §1.4.1 (3) for two-way client-side validation. |
| [§10 Lifecycle and QG](10-lifecycle-qg.md) | Lifecycle + Quality Gates — the normative area §1.2 (5). |
| [§3 Substrate versioning](03-substrate-versioning.md) | The capabilities V1–V6 — the normative area §1.2 (6); the substrate-independence principle §1.3.1. |
| [§13 Conformance](13-conformance.md) | Mandatory clauses, the manifest, conformance loss — the normative area §1.2 (9); negative scenarios §1.5.5. |
| `core/renar-core.md` | A conceptual overview of the standard for a human reader (non-normative). |
| `guide/02-transition-guide.md` | Practical guidance for projects transitioning from a lean style to contract-oriented development (substrate-specific). |

---

**[← Previous: 00. Introduction](00-introduction.md)** · **[Table of contents](README.md)** · **[Next: 02. Methodology positioning →](02-methodology-positioning.md)**
