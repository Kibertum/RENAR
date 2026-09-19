---
title: "Glossary"
description: "Canonical RENAR terminology and mapping to ISO/IEC 29148, BABOK, SAFe, ISTQB, SENAR."
order: 1
lang: en
version: "1.0-reconciled"
---

# RENAR Glossary

> **Purpose:** the single source of canonical RENAR terms with examples and a mapping to industry standards. On a wording conflict, [`standard/04-terms.md`](../../standard/en/04-terms.md) wins **normatively**; this glossary is an informative lookup for the reader and the assessor.

---

<a id="1-authority-chain"></a>

## 1. Authority chain

When a term is disputed, the following order applies:

1. **[`standard/04-terms.md`](../../standard/en/04-terms.md)** — the normative canon: a standard chapter's definitions win on any conflict.
2. **This document** — informative clarifications and mapping to industry standards; does not override `standard/04`.
3. **ISO/IEC/IEEE 29148** — the international requirements-engineering standard.
4. **BABOK Guide v3** — the *Business Analysis Body of Knowledge*.
5. **A change via an amendment proposal to the full RENAR Standard** — when all sources are silent.

**Closed lists:** the master index of the closed lists is [`standard/01 §1.7.5`](../../standard/en/01-scope.md#1.7.5).

**Not used as a source of terms:** bug-tracker tickets, team chats (slang), outdated slide decks, marketing materials.

---

## 2. Canonical terms

### 2.1 Requirement levels

The v1.0 canon is a closed list (see [`standard/04-terms.md §4.3`](../../standard/en/04-terms.md#4.3)):

| RENAR (canonical) | Full name | RU UI label (reference) | Description |
|---|---|---|---|
| **BR** | Business Requirement | Бизнес-требование | A customer-level business goal: what the organization wants to obtain. |
| **SR** | System Requirement | Системное требование | An engineering requirement for the software; verifiable. One `SR` is one verifiable unit. |
| **TR** | Task Requirement | Требование к задаче | An implementation-level requirement derived from an `SR`; the unit of work planning. |
| **TC** | Test Case | Контрольный пример (TC) | A verifiable artifact that confirms an `SR`/`BR`. Describes behavior, not implementation. |

**Identification rule:** in `frontmatter`, `id` is the canonical RENAR identifier (`BR-01`, `SR-05`). On export to another substrate, a target mapping applies.

Refinements of `SR` (frontmatter fields, not separate artifact types):

- **Module SR** — an `SR` with `level: module` ([`standard/06 §6.7`](../../standard/en/06-requirements-hierarchy.md#6.7)). Formerly the separate label `TM` ([§2.1.1](#211-legacy-labels-deprecated)).
- **Integration SR** — an `SR` with `constrained-by: [SPEC-INT-N]`. Formerly the separate label `INT-SR` ([§2.1.1](#211-legacy-labels-deprecated)).
- **Contract TC** — a `TC` with `tc-type: contract`. Formerly the separate label `INT-TC` ([§2.1.1](#211-legacy-labels-deprecated)).

The `SPEC` family (UX / AI / architecture / integration specifications) — see [§2.5 The `SPEC` family](#2.5).

#### 2.1.1 Legacy labels (deprecated)

When migrating from pre-v1.0 material, deprecated labels appear. Their replacements in the v1.0 canon ([`standard/04-terms.md §4.14.1`](../../standard/en/04-terms.md#4.14.1)):

| Legacy label | v1.0 canonical replacement | Note |
|---|---|---|
| `TM` (Module/Submodule SR) | `SR` with `level: module` | A refinement of `SR`, not a separate artifact type |
| `UIC` (UI Concept) | `SPEC-UI` ([`standard/08 §8.5.6`](../../standard/en/08-specifications.md#8.5.6)) | Part of the `SPEC` family ([§2.5](#2.5)) |
| `AIC` (AI Concept) | `SPEC-AI` ([`standard/08 §8.5.7`](../../standard/en/08-specifications.md#8.5.7)) | Part of the `SPEC` family |
| `INT-SR` (Integration SR) | `SR` with `constrained-by: [SPEC-INT-N]` | A subclass of `SR` |
| `INT-TC` (Integration TC) | `TC` with `tc-type: contract` | A subclass of `TC` |
| `TS` (Technical Specification) | `SPEC-ARCH` or `SPEC-OPS`, depending on content | Part of the `SPEC` family |

**Migrating existing artifacts:** the substrate-native binding to a change set MUST automatically recognize deprecated labels and offer the canonical replacements. The canonical legacy → v1.0 mapping table is [`standard/04-terms.md §4.14.1`](../../standard/en/04-terms.md#4.14.1).

### 2.2 ADAPT artifact family

| Term | Description |
|---|---|
| **ADAPT** | The Adaptive Document for Articulating a Project's TZ. An **internal** artifact of interpretation: the forward direction (construal) and the backward direction (findings). Signed by the **Architect only** ([`standard/07 §7.5`](../../standard/en/07-adapt.md#7.5)) — the client never sees it. |
| **ACTZ** | The TZ Clarification Protocol (*Agreed Clarification of TZ*). A **contractual** artifact: decisions put to the client and signed by **both parties**. Its contractual name is "TZ Clarification Protocol No. N". Lifecycle: `draft → sent → signed → superseded` ([`standard/07 §7.13`](../../standard/en/07-adapt.md#7.13)). The boundary with ADAPT is drawn **by audience**: shown to the client and approved → an obligation; not shown → an interpretation. |
| **effective TZ** | The baseline for delivery and acceptance: the initial TZ (with its annexes) **plus every signed ACTZ**; on a divergence, the later signed document prevails. AT are derived from the effective TZ ([`standard/07 §7.14`](../../standard/en/07-adapt.md#7.14)). ADAPT is **not** part of the acceptance baseline. |
| **Concept** | A written description of the future system, frozen by a responsible person of the organization; takes the place of the TZ in the first-party confirmation kind ([`standard/01 §1.4.4`](../../standard/en/01-scope.md#1.4.4)) — there is no external client, the ACTZ and the AT have no subject, decisions on backward findings are recorded by a revised concept. |
| **First-party confirmation** (ISO/IEC 17050-1) | The kind of conformance claim for an organization without a second party: `confirmation: first-party` in the manifest, adversarial review without exception, ACR mandatory. Not a second-party acceptance and not its imitation. |
| **Reusable component** | A system in its own tree used by many projects ([`standard/06 §6.14`](../../standard/en/06-requirements-hierarchy.md#6.14)): the client side is the component's Product Owner (path B) or declared assumptions confirmed by every consumer on connection (path C; the component's conformance is conditional and completed by the connection). |
| **Screen** | A state of the interface in which the user performs an action or observes its result, addressable independently of the technique (a route, a page, a modal window, a wizard step). The list of the system's screens is kept by SPEC-ARCH from the description, not from the implementation; every screen of the list is covered by at least one SPEC-UI ([`standard/08 §8.5.6.1`](../../standard/en/08-specifications.md#8.5.6.1)). |
| **Description language** | The controlled dialect of the normative sections of artifacts ([`standard/15`](../../standard/en/15-description-language.md)): five modal operators, glossary discipline, ten prohibited constructions, atomicity (one pair of TCs per statement), forms by artifact type. Mandatory from `RENAR-2`; the forms are a recommendation in v1.1. |
| **Modal operator** | One of the five words of the closed list expressing obligation in a normative section of an artifact: MUST / MUST NOT / SHOULD / SHOULD NOT / MAY ([`standard/15 §15.2`](../../standard/en/15-description-language.md#15.2)); exactly one per statement, synonyms prohibited. |
| **`decided-in`** | A field of a backward-finding record: a reference to the clause of a **signed** ACTZ in which the decision is recorded. A finding cannot be `resolved` without it ([`standard/07 §7.4.5`](../../standard/en/07-adapt.md#7.4.5)). |
| **delta-ADAPT** | An `ADAPT` for a delta-TZ. Chain: ADAPT-001 → ADAPT-001-delta-1 → ADAPT-001-delta-2; applied in order. |
| **errata-ADAPT** | A correction to an approved `ADAPT` (our own interpretation error). It does not alter the frozen document — a separate artifact is added. |
| **Forward (in ADAPT)** | The engineering interpretation of each TZ section: quote → interpretation → elaborated scenarios → coverage. |
| **Backward (in ADAPT)** | The list of problems and questions for the client. Lifecycle: `open` → `asked-to-client` → `answered` → `resolved` → `frozen`. |
| **Term mapping (in ADAPT)** | A "client term → engineering understanding" table. |

### 2.3 Backward categories (closed list)

Every ADAPT backward entry belongs to one of 7 categories. The list is closed; new ones are added only through a change to the full RENAR Standard:

| Category | Description |
|---|---|
| `contradiction` | A contradiction within the TZ. |
| `gap` | A gap — the TZ is silent on this. |
| `hidden-assumption` | A hidden engineering assumption that needs confirmation. |
| `feasibility` | A technically infeasible or expensive requirement. |
| `regulatory` | Touches legislation / compliance. |
| `terminology` | An unclear or conflicting client term. |
| `scope` | A scope-boundary clarification (in / out). |

### 2.4 Quality Gates (closed list, v1.0 canon)

The closed list of RENAR Quality Gates (per [`standard/10-lifecycle-qg.md §10.3–10.4`](../../standard/en/10-lifecycle-qg.md#10.3)):

| Gate | Applies to | Pass condition |
|---|---|---|
| **QG-0** Approval Gate | Approval of a description set version (`draft` → `approved N.M`), of a task (TR), of an ADAPT | Goal, acceptance criteria, at least one negative scenario, and coverage; for `SR` — the parent `BR` in `approved`+; for an `SR` with `constrained-by` — the `SPEC` in `approved`+. |
| **QG-1** Implementation Gate | Admission of a `TC` implementation to runs (**only** the `TC` implementation) | `automation.set-version` points to the approved version of the norm; `automation.status` + `location` valid; dry-run passed; red history recorded. |
| **QG-2** Verification Gate | Verification of a set version on a product version; task → `done` | All `TC`s of the version are green with `last-run.set-version` equal to the version; both TCs of every pair; the spot-check passed; the verification entry is written into the version record. |
| **QG-3** Architecture Gate (*optional*) | `SPEC-ARCH` approval / the Architect's signature on an ADAPT | An ADR-style artifact is recorded in the substrate; the Architect's signature. Not REQUIRED for declared RENAR conformance. |
| **QG-4** Acceptance Gate (*optional*) | Acceptance of the business outcome of a BR of a set version; task acceptance | The version is verified and presented; the business outcome is measured and has reached the threshold; the Stakeholder's signature. Not REQUIRED for declared RENAR conformance. |

**RENAR conformance:** `QG-0` / `QG-1` / `QG-2` are mandatory for declared conformance ([`standard/13 §13.3`](../../standard/en/13-conformance.md#13.3)). `QG-3` / `QG-4` are optional extensions.

The list is closed; new gate numbers are added only through the formal change procedure of the full RENAR Standard.

#### 2.4.1 Legacy QG names (deprecated)

Before v1.0, RENAR used different gate names. The mapping per [`standard/04-terms.md §4.14.1`](../../standard/en/04-terms.md#4.14.1):

| Legacy (pre-v1.0) | v1.0 canonical replacement | Note |
|---|---|---|
| `QG-0 Context Gate` | `QG-0 Approval Gate` | Rename only; meaning preserved |
| `QG-1 Requirements Gate` | `QG-1 Implementation Gate` | **Meaning shift:** formerly `BR`/`SR` approval, now only the admission of the `TC` implementation to a run ([`standard/10 §10.3.2`](../../standard/en/10-lifecycle-qg.md#10.3.2)) |
| `QG-2 Implementation Gate` | `QG-1 Implementation Gate` | Renumbered and merged into a single `QG-1` |
| `QG-3 Verification Gate` | `QG-2 Verification Gate` | Renumbered |
| `QG-4 Acceptance Gate` | `QG-4 Acceptance Gate` | Same name; now **optional** for declared RENAR conformance |

**Migrating existing artifacts:** automation rewrites references per the table above. The canonical legacy QG → v1.0 mapping table is [`standard/04-terms.md §4.14.1`](../../standard/en/04-terms.md#4.14.1).

#### 2.4.2 Local ADAPT gates

The `ADAPT` lifecycle ([`standard/07-adapt.md`](../../standard/en/07-adapt.md)) uses local `ADAPT` gates alongside the canonical `QG-N`. These are not a separate `QG` numbering but local lifecycle events of the `ADAPT` artifact:

| Gate | Application | Pass condition |
|---|---|---|
| **QG-ADAPT-draft** | `ADAPT` creation | The Forward section covers every TZ section. |
| **QG-ADAPT-review** | Transition to `review` | All backward entries are `open` or `asked-to-client`; none are `draft`. |
| **QG-ADAPT-asked** | Putting the questions to the client | All backward entries are `asked-to-client`; the questions are put to the client as part of an ACTZ (`status: sent`). |
| **QG-ADAPT-answered** | After the client answers | All backward entries are `answered`. |
| **QG-ADAPT-approve** | `ADAPT` approval | All backward entries are `resolved` with `decided-in` on a clause of a signed ACTZ; the Architect's signature. |
| **QG-ADAPT-frozen** | After approval | Immutability; generation of `BR`/`SR`/`SPEC` is permitted. |

Local `ADAPT` gates are **not** part of the `QG-0`/`QG-1`/`QG-2` set for declared RENAR conformance. An implementation MAY express `QG-ADAPT-approve` as a local alias for `QG-3` (`Architecture Gate`, where applicable) or store it separately — at the substrate's discretion.

<a id="25-spec-family-closed-list"></a>

### 2.5 The SPEC family (closed list)

| Type | Purpose | Source |
|---|---|---|
| **SPEC-ARCH** | System / subsystem architecture: contexts, containers, components, deployment view, quality attributes | ADAPT Forward section |
| **SPEC-API** | API contracts (REST / GraphQL / gRPC / async events); versioning, error model, rate limits | ADAPT Forward section |
| **SPEC-DATA** | Data model: schema, ER diagram, indexes, migrations, storage, personal-data classification | ADAPT Forward section |
| **SPEC-INT** | Integration: interaction between subsystems and external systems; protocols, contracts, SLAs | ADAPT Forward section |
| **SPEC-PROC** | Process / workflow: business processes, state machines, saga patterns, orchestration and choreography | ADAPT Forward section |
| **SPEC-UI** | UI / UX: screens, navigation, user scenarios, accessibility, localization, baseline images | ADAPT Forward section |
| **SPEC-AI** | AI / ML: model cards, RAG, prompt engineering, evaluation strategy, cost budget | ADAPT Forward section |
| **SPEC-SEC** | Security: authentication / authorization, threat model, secrets management, data classification | ADAPT Forward section, backward regulatory findings |
| **SPEC-OPS** | Operations: deployment, observability, SLO / SLA, runbooks, disaster recovery | ADAPT Forward section |
| **SPEC-TEST** | Test benches and data: topology, emulators and sandbox instances of external systems, run configuration, datasets on both sides of every integration, reconciliation rules, volume and anonymization; a client signature when real data is used | ADAPT Forward section, integration requirements |
| **SPEC-DOC** | Delivered documentation: composition (user manual, administrator manual, training materials), the mandatory sections of each document, version binding, acceptance criteria | ADAPT Forward section, the contractual composition of the delivery |
| **SPEC-UC** | Use case: an end-to-end path of a user (`role: human`, through the interface) or an agent (`role: agent`, through the API) through several SR / SPEC; every step references the statement it touches; the run channel of the covering TCs is set by the role | [`standard/08 §8.5.12`](../../standard/en/08-specifications.md#8.5.12) |

The list is closed — exactly twelve types (`SPEC-UC` added in v1.1 under §13.9) (canon per [`standard/08 §8.3`](../../standard/en/08-specifications.md#8.3)); new `SPEC` types are added only through a change to the full RENAR Standard.

Three subjects of description are kept apart and MUST NOT be conflated: the nine types (including `SPEC-OPS`) state **how the system is built and how it lives**; `SPEC-TEST` — **how its conformance is proven** (benches, data, run configuration); `SPEC-DOC` — **what enters the delivery** to the client. Hence the boundaries: an administrator manual is always `SPEC-DOC` (the client receives it as part of the delivery), while the vendor team's internal runbook is always `SPEC-OPS`. Every dynamic `TC` is bound to a bench through `environment-ref` pointing at a `SPEC-TEST` (static checks require no bench), and every `SPEC-DOC` is verified by the doc-lint.

### 2.6 Lifecycle statuses

| Status | Applies to | Meaning |
|---|---|---|
| `draft` | description set, TR, ADAPT, ACTZ, AR | In progress, not for use by others. BR / SR / SPEC / TC norm have no status of their own — "draft" means presence only in the set draft ([standard/10 §10.7](../../standard/en/10-lifecycle-qg.md#10.7)) |
| `review` | ADAPT | Under review, changes possible (for SPEC — withdrawn: structural completeness became a precondition of version approval) |
| `approved` | description set (version `N.M`), TR, ADAPT | Approved; immutable after signature (the set — the Architect's, §10.5.2; `ADAPT` — the Architect's, §7.5). BR / SR / SPEC are "approved" when they belong to an approved version |
| `superseded` (set) | description set | A version followed by a released successor; kept with its verification record (V1) |
| verified (a property of the pair) | set version × product version | All TCs of the version passed on the product version; recorded by a verification entry in the version record, not by an artifact status |
| `frozen` | ADAPT | Approved and immutable; used as the source for generating `BR`/`SR`/`SPEC` |
| removed (derived) | BR, SR, SPEC, TC norm | Absent from the current set version; the replacement is given by `replaced-by` in the version record ([standard/10 §10.5.3](../../standard/en/10-lifecycle-qg.md#10.5.3)). The former `deprecated` status is withdrawn |
| `obsolete` | TR | Terminal task status: no longer current and not deleted (V1); `ADAPT` has no `obsolete` state |
| `superseded` | ADAPT, ACTZ, AR | The terminal supersession status: a previously correct decision is revoked by a later artifact; the record is retained for audit ([`standard/07 §7.6.4`](../../standard/en/07-adapt.md#7.6)) |

<a id="27-substrate-capabilities-v1-v6"></a>

### 2.7 Substrate capabilities (V1–V6)

RENAR is not tied to a specific artifact substrate. An artifact MAY reside in any substrate that satisfies the following capabilities.

Normative definition — [`standard/03 §3.3`](../../standard/en/03-substrate-versioning.md#3.3):

| Capability | Description |
|---|---|
| **V1 — immutable history** | Any past state of an artifact can be addressably restored without loss (the revision chain is preserved for audit). |
| **V2 — atomic change unit** | A change to an artifact (or a consistent group) commits as a single transaction: fully succeeds or fully rolls back; intermediate states are not externally visible. |
| **V3 — diff & review** | A proposed change can be presented as a diff against a base version and accepted or rejected before it reaches the approved state (the basis of approval and the `QG` gates). |
| **V4 — branching & change sets** | Work in progress is separated from the approved Source of Truth; several independent changes proceed in parallel without affecting the Source of Truth. |
| **V5 — cross-substrate version pin** | A specific version of an artifact in another substrate can be pinned as a resolvable identifier (the basis of the `automation.set-version` / `last-run.set-version` fields and of the manifest `set-version`). |
| **V6 — author + timestamp** | For each atomic edit, an unambiguous author and timestamp are recorded (the basis of the `ADAPT` signature and the `ai-provenance` block). |
| **Description set** | The complete description of the system (BR, SR, SPEC, the normative parts of TC), approved and versioned as a single whole ([standard/10 §10.5](../../standard/en/10-lifecycle-qg.md#10.5)). |
| **Set version** (`set-version`, `N.M`) | An immutable version record that resolves into the content of all artifacts ([standard/10 §10.5.4](../../standard/en/10-lifecycle-qg.md#10.5.4)); a minor version — on every approved change, a major one — the presented version after a full audit. |

Example substrates: a distributed VCS (`git` with merge-request review), a document-oriented store with a revision chain and signatures, any DBMS with change history and signatures. RENAR does not require `git` — only the **V1–V6** capabilities.

### 2.8 AI provenance (`ai-provenance`, canonical fields)

In the `frontmatter` of any AI-generated artifact:

| Field | Type | Description |
|---|---|---|
| `ai-provenance.generated-by` | string | The model, as `<vendor>-<model>-<version>@<date>`. Example: `anthropic-claude-opus-4-7@2026-05-15`. |
| `ai-provenance.prompt-template` | string | Path to the prompt template and its version. Example: `prompts/adapt-from-tz.md@v2.1`. |
| `ai-provenance.context-tokens` | integer | Token count of the input context. |
| `ai-provenance.output-tokens` | integer | Token count of the model output. |
| `ai-provenance.generation-time-ms` | integer | Generation time in milliseconds. |
| `ai-provenance.human-edits` | boolean | `true` if a human edited the text after generation. The field is **informational**, not a gating flag: `true` does not trigger auto-rejection ([standard/04 §4.10.1.1](../../standard/en/04-terms.md#4.10.1.1)). There is exactly one exception: for an **ADAPT** in status `approved` the value MUST be `true` — the architect has proofread the text ([standard/07 §7.8.1](../../standard/en/07-adapt.md#7.8)). The rule does not extend to other artifact types. |

### 2.9 Test-case types

A closed list — six types (canon per [`standard/09 §9.5`](../../standard/en/09-test-cases.md#9.5)):

| TC type (`tc-type` field) | ISTQB correspondence | Application |
|---|---|---|
| `business` | Acceptance Testing | Verifies that the business goal of a BR is achieved (the internal contour). The former name `acceptance` is withdrawn: there are two acceptances, and one name for two subjects caused confusion. |
| `ux` | usability extension | Verifies a `SPEC-UI` via a VLM judge model / visual comparison against a baseline. |
| `system` | System Testing | Verifies SR, SPEC-PROC, SPEC-ARCH. |
| `contract` | Component Integration Testing | Verifies SPEC-API / SPEC-INT / SPEC-DATA via contract testing (Pact and similar). |
| `eval` | AI-specific | Verifies a `SPEC-AI` via an evaluator LLM and metrics (BLEU, accuracy, hallucination rate). |
| `security` | security extension | Verifies a `SPEC-SEC`: security invariants (STRIDE); normatively negative scenarios only ([`standard/09 §9.6.4`](../../standard/en/09-test-cases.md#9.6.4)). |

### 2.9bis AT and test-evidence principles

| Term | Description |
|---|---|
| **AT** (*Acceptance Test*) | The acceptance test of the **contractual contour** ([`standard/09 §9.19`](../../standard/en/09-test-cases.md#9.19)). It is derived by an **isolated agent** exclusively from the effective TZ — without access to ADAPT, BR/SR/SPEC, TC, or the code. It is regenerated before every round of trials. It verifies conformance to the **contract**, not to the interpretation. |
| **MW** (*manual walkthrough record*) | Manual walkthrough record ([`standard/09 §9.20`](../../standard/en/09-test-cases.md#9.20)) — the evidence class: the outcome of a person walking a scenario (SPEC-UC / SPEC-UI user journey) with the description in hand. Replaces no TC, is not part of the QG-2 evidence base; every finding is routed per §6.13.2 (a candidate `implementation-originated` or the contractual contour). |
| **Test authorship isolation** (P8) | The test is frozen before the implementation is started; it is written by an agent **other than** the one writing the implementation; an edit to the criteria is made only through `[test-spec-change]` ([`standard/09 §9.18.1`](../../standard/en/09-test-cases.md#9.18)). |
| **Red history** (P9) | A test **never once observed red is not evidence**. The fixing run before the implementation MUST be red ([`standard/09 §9.18.2`](../../standard/en/09-test-cases.md#9.18)). Isolation guarantees that the test was written before the code, but not that it **verifies anything**; red history closes that remainder. |
| **Killed mutant** | The compensation for red history in the `implementation-originated` class: such a test is written after the code and is born green, so its working order is proved by a killed mutant ([`standard/06 §6.13.3`](../../standard/en/06-requirements-hierarchy.md#6.13)). |
| **`implementation-originated`** | The narrow legal class of requirements originated by the implementation: **only** internal technical details with no client-observable behavior; with provenance, human approval, a TC before the merge, and a counter in the drift metrics ([`standard/06 §6.13`](../../standard/en/06-requirements-hierarchy.md#6.13)). |

### 2.10 Links (frontmatter fields)

| Field | Purpose |
|---|---|
| `parent` | Parent in the hierarchy (BR → SR → TR); a single source. |
| `children` | Child artifacts (auto-derived). |
| `source.adapt` | The `ADAPT` the artifact is derived from (`BR`/`SR`/`SPEC`). The field is **conditional**: present when an ADAPT was created; omitted when the adversarial reviewer returned «no findings» ([`standard/07 §7.4.1`](../../standard/en/07-adapt.md#7.4.1)). |
| `source.adapt-section` | The `ADAPT` section (Forward §N). |
| `source.tz-section` | The TZ section; always mandatory (the dual traceability chain). |
| `source.adversarial-review-ref` | A reference to an `AR` — the adversarial-review record; mandatory whenever `source.adapt` is omitted ([`standard/07 §7.4.6`](../../standard/en/07-adapt.md#7.4.6)). |
| `verifies` (in `TC`) | The `SR`/`BR`/`SPEC` the `TC` covers; the description version — `automation.set-version` (once per implementation). |
| `verified-by` (in `SR`) | The `TC`s that confirm the `SR` (auto-derived). |
| `derived-from` | Template and version (if the artifact was created from a template). |
| `replaces` / `replaced-by` | Replacement when an artifact is removed from a set version; `replaced-by` lives in the version record (`changes[].removed`). |
| `supersedes` (in the new one) | Which requirement is being superseded. |
| `linked-tasks` | Tasks implementing the `SR` (via the runtime environment, not via files). |
| `uses[]` (in the consumer's root BR) | Connection of a reusable component: `component`, `set-version` (the component's set version, V5), `assumptions-confirmed[]` — confirmation of the declared assumptions by name ([`standard/06 §6.14.4`](../../standard/en/06-requirements-hierarchy.md#6.14.4)). Not a parent edge. |
| `assumptions[]` (in the component's root BR) | Declared assumptions about the consumer (`A-NN`), closed for the set version; contestable in the adversarial review, confirmed by the consumer on connection ([`standard/06 §6.14.2`](../../standard/en/06-requirements-hierarchy.md#6.14.2)). |
| `applies-to` (in a component's SR / SPEC) | `self` — an obligation of the component (proven by its TCs once); `consumer` — an obligation of the consumer (a static TC at every consumer) ([`standard/06 §6.14.3`](../../standard/en/06-requirements-hierarchy.md#6.14.3)). |
| `screens[]` (in a SPEC-UI) | The codes of the screens from the SPEC-ARCH list of the same set version that the document covers; every screen of the list MUST appear in at least one `screens[]` ([`standard/08 §8.5.6.1`](../../standard/en/08-specifications.md#8.5.6.1)). |

### 2.11 File-naming convention (default)

| Type | Pattern | Example |
|---|---|---|
| ADAPT | `adapt/ADAPT-NNN[-delta-N].md` | `adapt/ADAPT-001-main.md`, `adapt/ADAPT-001-delta-1.md` |
| BR | `br/BR-NN-<slug>.md` | `br/BR-01-notification-capture.md` |
| SR | `sr/SR-NN-<slug>.md` | `sr/SR-05-notification-feed.md` |
| Subsystem SR | `sr/<MODULE>-SR-NN.N-<slug>.md` | `sr/WMS-SR-01.2-pick.md` |
| SPEC-UI | `specs/ui/SPEC-UI-NN-<slug>.md` | `specs/ui/SPEC-UI-02-notification-feed.md` |
| SPEC-AI | `specs/ai/SPEC-AI-NN-<slug>.md` | `specs/ai/SPEC-AI-01-rag-strategy.md` |
| SPEC-INT | `specs/int/SPEC-INT-NN-<slug>.md` | `specs/int/SPEC-INT-01-auth-billing.md` |
| SPEC (generic) | `specs/<type>/SPEC-<KIND>-NN-<slug>.md` | `specs/api/SPEC-API-03-orders.md` |
| TC | `tests/TC-NN-<slug>.md` | `tests/TC-01-login-success.md` |
| TZ | `tz/TZ-YYYY-NNN.md` | `tz/TZ-2026-001.md` |
| Delta-TZ | `tz/TZ-YYYY-NNN-delta-N.md` | `tz/TZ-2026-001-delta-1.md` |
| UX baseline | `specs/ui/baselines/SPEC-UI-NN-<scenario>.png` | `specs/ui/baselines/SPEC-UI-02-feed-default.png` |
| Eval dataset | `specs/ai/eval-datasets/SPEC-AI-NN-<slug>.jsonl` | `specs/ai/eval-datasets/SPEC-AI-01-typical-queries.jsonl` |

This is the default convention; substrate-native stores MAY use a different layout, provided identifiers are stable (capability **V1**).

### 2.12 Change-record markers (informative)

In a substrate where atomic changes carry metadata (a commit message in `git`, a change-record description in a document-oriented store), the following markers are permitted:

| Marker | Purpose |
|---|---|
| `[delta:TZ-YYYY-NNN]` | A change driven by a delta-TZ. |
| `[test-spec-change]` | A change to a `TC`'s pass/fail criteria (a separate approval). |
| `[baseline-update]` | An update to a UX baseline or an eval dataset (a separate approval). |
| `[coverage]` | Automatic regeneration of coverage, test-plan, and requirement summaries (a bot). |
| `[reconciliation]` | A change by a reconciliation agent. |
| `[multi-model-disagreement]` | An artifact where AI model outputs disagree — requires manual review. |
| `[AI]` | A prefix for AI-generated changes. |

A substrate-native mechanism MAY express these markers as change-record fields, labels, or tags — the details are not normative.

---

## 3. Mapping to standards

### 3.1 Requirement levels

RENAR v1.0 canonical labels and external standards:

| RENAR | ISO/IEC 29148 | BABOK | SAFe | Document store (example enum) | SENAR (RU) |
|---|---|---|---|---|---|
| BR | Business Requirement | Business Need | Portfolio Epic / Strategic Theme | `BT` | БТ |
| SR | System / Software Requirement | Solution Requirement (Functional) | Feature | `ST` | СТ |
| SR (`level: module`) | (subcomponent scope, extension) | (subcomponent scope) | Story (sometimes) | `TM` (legacy) | СТ модуля |
| SR (`constrained-by: SPEC-INT-N`) | Interface requirement | Interface solution | Cross-feature integration | (related) | INT-СТ (legacy) |
| TR | (no direct class; refinement of a system / system-element requirement) | Detailed solution requirement | Story | `TK` | ТЗ |
| TC | Test Case | Verification | Story acceptance test | `test_case` | ТК |
| TC (`tc-type: contract`) | Interface test | Component Integration Testing | Contract test | (related) | INT-ТК (legacy) |
| SPEC-UI | (between BR/SR — design specification) | Stakeholder requirement (UX fragment) | (design level) | (extension) | UIC (legacy) |
| SPEC-AI | (REQ extension) | (n/a) | Enabler | (extension) | AIC (legacy) |
| SPEC-ARCH / SPEC-OPS | Design description | Solution component | Enabler tech spec | (extension) | ТС (legacy) |

**Note:** the "document store (example enum)" and "SENAR (RU)" columns contain historical labels (`TM`, `UIC`, `AIC`, `INT-СТ`, etc.) for traceability with pre-v1.0 systems. The RENAR canon column holds the v1.0 labels per [§2.1.1](#211-legacy-labels-deprecated). On export to a document store or a SENAR substrate, a target mapping applies.

### 3.2 Quality Gates

A mapping of RENAR v1.0 canonical `QG-N` to external models. Legacy `QG` names ([§2.4.1](#241-legacy-qg-names-deprecated)) are retained in the SENAR column for historical traceability.

| RENAR (v1.0) | SENAR (legacy mapping) | Document store (example) | CMMI activity |
|---|---|---|---|
| **QG-0** Approval Gate | QG-0 (context) | `VK-1` (start) | Requirements review before commitment |
| **QG-1** Implementation Gate | QG-2 (implementation, legacy) | `VK-1` | Implementation baseline (`TC` readiness) |
| **QG-2** Verification Gate | QG-3 (verification, legacy) | `VK-2` | Verification |
| **QG-3** Architecture Gate *(optional)* | (n/a) | `VK-3` (partial) | Architecture-decision approval |
| **QG-4** Acceptance Gate *(optional)* | QG-4 (Acceptance) | `VK-4` | Customer acceptance |

**Note:** before v1.0, `QG-1 Requirements Gate` is effectively split between the canonical `QG-0 Approval Gate` (`BR`/`SR`/`SPEC` approval) and `QG-1 Implementation Gate` (`TC` readiness). See [§2.4.1](#241-legacy-qg-names-deprecated) for the full mapping.

### 3.3 Lifecycle statuses

| RENAR | Document store (example) | ISO/IEC 29148 | CMMI |
|---|---|---|---|
| `draft` | `draft` | proposed | identified |
| approved (belongs to a set version) | `approved` | agreed-to / baselined | committed |
| verified (set version × product version) | `verified` | verified | validated |
| removed from the version | `obsolete` | retired | obsolete |

### 3.4 Process artifacts

| RENAR | BABOK | SAFe | SENAR |
|---|---|---|---|
| ADAPT (Forward + Backward) | Requirements Analysis Document | Solution Intent (fixed + variable) | (n/a — RENAR extension) |
| Work Order / TZ | Stakeholder commitment artifact | Customer order | (context) |
| delta-TZ | Change Request | (n/a — handled via Solution Intent updates) | (context) |
| Impact Analysis | Impact Analysis (BABOK §8) | (derived) | (context) |
| Spot-check | Random sampling QA | (n/a) | Rule 9.5 |
| Adversarial review | Independent verification | (n/a — REQ extension) | (via ADR metric) |
| Reconciliation | Continuous improvement audit | Inspect & Adapt | Quality Sweep |

<a id="35-multilingual-ui-projection"></a>

### 3.5 User-interface projections

`frontmatter` fields, identifiers, and file names are always canonical (latin). RU labels are permitted in the UI:

| Canonical | UI (RU) |
|---|---|
| Business Requirement | Бизнес-требование |
| System Requirement | Системное требование |
| Test Case | Контрольный пример (`TC`) |
| Quality Gate | Контрольная точка качества |
| Acceptance | Приёмка |
| Verified | Проверено |
| Approved | Утверждено |
| Deprecated | Устарело |
| Frozen | Замороженный |
| Backward finding | Замечание к ТЗ |
| Forward interpretation | Инженерная интерпретация |

A UI projection does not replace the canonical identifiers in the substrate.

---

## 4. Forbidden / deprecated terms

RENAR does not use the following terms (even where they appear in SENAR / industry literature):

| Term | Use instead | Why |
|---|---|---|
| **User Story** as a requirement | SR | A story is a unit of planning, not a requirement. A story MAY implement an SR, but is not itself a requirement. |
| **Use Case** (free form) | `SPEC-UC` | A use case is the `SPEC-UC` type ([`standard/08 §8.5.12`](../../standard/en/08-specifications.md#8.5.12)): an end-to-end path with an executor role and a reference of every step to a statement. A free-form use case without step references is not a RENAR artifact. |
| **Spec** (without a qualifier) | SR / BR / SPEC-API / SPEC-DATA / ... | "Spec" is ambiguous. Use the precise terms. |
| **Business logic** as a requirement | SR | "Business logic" is a code term, not a requirements term. |
| **Functionality** | `SR` / `TR` | Too broad; not unambiguously verifiable. |
| **Feature** (loose use) | Feature (SAFe context) or SR (the canonical RENAR term) | Ambiguous without a frame of reference. |
| **Wish / "nice-to-have"** | (never) | A contractual document is not written this way. |
| **Epic** as a requirement | BR (business level) or Portfolio Epic (SAFe) | An epic is a unit of planning, not a requirement. |
| **"Test it by hand"** | spot-check (Core Rule 5) or a manual TC with `tc-type: business` | Vague; no verifiable evidence. |
| **"To finish later"** (as a status) | `draft` / `review` | Not from the closed lifecycle list. |
| **TODO** in `frontmatter` | a backward entry in the `ADAPT` (if about a requirement) or a task in the tracker (if about implementation) | Open questions live in the right artifact, not in a field comment. |

On such findings in project-local artifacts, raise a substrate-side warning (`pre-commit` in `git`, a validation rule in the document store).

---

## 5. Glossary versioning

The glossary is a standalone document with its own version. A change to a canonical term is a major-version bump (1.0 → 2.0) and a migration scenario for all project artifacts.

**Current version:** 1.0-reconciled (phase-1.5 reconciliation with [`standard/04-terms.md §4.14.1`](../../standard/en/04-terms.md#4.14.1)).

<a id="51-open-questions-closed"></a>

### 5.1 Open questions — closed (phase 1.5, 2026-05-16)

Four open questions from the earlier draft were closed by reconciliation with [`standard/04-terms.md §4.14.1`](../../standard/en/04-terms.md#4.14.1):

| # | Was an open question | Outcome | Source |
|---|---|---|---|
| 1 | Canonical language: latin (`BR`/`SR`) or Russian (БТ/СТ)? | **Canon is latin**; Russian only in the UI projection ([§3.5](#3.5)) | [`standard/04 §4.13.3`](../../standard/en/04-terms.md#4.13.3) + [`reference/06-ru-style-guide.md §1.3`](../06-ru-style-guide.md#1.3) |
| 2 | `TM` as a separate label or an `SR` refinement? | **`SR` with `level: module`** — a refinement, not a separate artifact type | [`standard/04 §4.14.1`](../../standard/en/04-terms.md#4.14.1) (`TM` deprecated) + [§2.1.1](#211-legacy-labels-deprecated) |
| 3 | AIC ← AAC / AIA / AIC? | **`SPEC-AI`** (v1.0 canon); AIC is a deprecated label | [`standard/04 §4.14.1`](../../standard/en/04-terms.md#4.14.1) + [§2.1.1](#211-legacy-labels-deprecated) |
| 4 | `INT-TC` a separate type or a naming convention? | **`TC` with `tc-type: contract`** — a refinement, not a separate type | [`standard/04 §4.14.1`](../../standard/en/04-terms.md#4.14.1) (`INT-TC` deprecated) + [§2.1.1](#211-legacy-labels-deprecated) |

### 5.2 Reconciliation history

| Date | Version | Change |
|---|---|---|
| 2026-05-16 | 1.0-reconciled | Phase-1.5 reconciliation with [`standard/04-terms.md §4.14.1`](../../standard/en/04-terms.md#4.14.1). Deprecated labels moved from the `§2.1` table to `§2.1.1`; `QG` names aligned with the v1.0 canon in §2.4; deprecated names → `§2.4.1`. Mapping tables §3.1 and §3.2 updated. Four open questions closed ([§5.1](#5.1)). Task: `ru-reconcile-glossary-vs-standard`. |
| (early drafts) | 1.0 | Draft fill-in during phase 7; had open questions and divergences from [`standard/04 §4.14.1`](../../standard/en/04-terms.md#4.14.1). Commit history recorded; replaced by the 1.0-reconciled release. |

### 5.3 Cross-references

- **EN Style Guide** ([`reference/en/06-en-style-guide.md`](06-en-style-guide.md)) — EN editorial rules for normative text; §1.9 fixes the canonical term list alongside this glossary. On a conflict, editorial-pass wording priority belongs to the Style Guide.
- **Canonical definitions** ([`standard/04-terms.md`](../../standard/en/04-terms.md)); §4.14.1 — the deprecated → canon mapping.

---

*RENAR Glossary 1.0-reconciled (EN) — part of `reference/`. See also [02-schemas.md](../02-schemas.md), [03-ai-risk-register.md](../03-ai-risk-register.md), [06-en-style-guide.md](06-en-style-guide.md).*
