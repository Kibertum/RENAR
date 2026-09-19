---
title: "Specifications (12 SPEC types)"
order: 8
lang: en
---
# 08. Specifications — 12 SPEC types

> **Part of the RENAR Standard v1.1** · [← Table of contents](README.md)

## 8.1 Why a separate specification axis

Take the requirement "the system creates an order." It says **what** must happen — but it is silent about **how** the system is built to do it: what its API contract is, in which table the order lives, under which access rules, on which screen. Cramming all of that into the requirement itself does not work — it turns into mush. So RENAR splits the description into two axes: **behavior** (BR / SR / TR, [chapter 6](06-requirements-hierarchy.md)) and **structure** — specifications, SPEC.

A specification is not a "more detailed SR" and not its child. A single "create order" requirement typically rests on five to seven specifications at once (architecture, API, data, process, security, screen), so the link between the axes is a graph of typed edges (`constrained-by[]`, `implements-spec[]`), not a tree. There are exactly twelve specification types, and the list is closed: `SPEC-ARCH`, `API`, `DATA`, `INT`, `PROC`, `UI`, `AI`, `SEC`, `OPS`, `TEST`, `DOC`, `UC` — a new type is introduced only through the formal change procedure of the standard ([chapter 13](13-conformance.md)).

A specification describes **four different subjects**, and they MUST NOT be conflated. Nine types describe **how the system is built**; `SPEC-UC` — **a path through it** ([§8.5.12](#8512-spec-uc)). `SPEC-TEST` describes **the construction of the proof** of its conformance: the test bench of an integration system is not an installation but an engineering product (emulators, datasets on both sides of every integration, rules for reconciling results against the data of foreign systems). `SPEC-DOC` describes **the composition of the delivered result**: the documentation the client receives as part of the delivery.

---

## 8.2 Architectural decision: SPEC is a parallel axis, not children of SR

### 8.2.1 Two axes of describing the system

Requirements and specifications answer different questions:

| Axis | Artifacts | Question |
|---|---|---|
| Behavioral | BR / SR / TR ([chapter 6](06-requirements-hierarchy.md)) | What the system must do |
| Structural | SPEC-* (9 types) | How the system is structurally built to fulfill those requirements |
| Evidential | `SPEC-TEST` | On what bench and on what data conformance is proven ([§8.3.2](#832-revision-of-the-test-bench-decision)) |
| Delivery | `SPEC-DOC` | What enters the delivery to the client besides the system itself |

### 8.2.2 SPEC as a parallel axis: links through a typed graph

The links between the requirements axis (BR / SR / TR) and the SPEC axis are organized as a **dependency graph**, not a tree of parents. An SR has exactly one parent in the requirements tree (BR), but many typed `constrained-by[]` edges to SPEC. A single SR MUST reference every SPEC that constrains its behavior on the API / data / UI / process / security / ops axes; conversely, one SPEC MAY constrain many SR.

Example: the SR "create order" rests on SPEC-ARCH (where the orders component lives), SPEC-API (the endpoint contract), SPEC-DATA (the table schema), SPEC-PROC (workflow), SPEC-SEC (access rules), SPEC-UI (the form).

Normatively: every `SR.constrained-by[]` and `TR.implements-spec[]` edge MUST reference one of the closed SPEC categories listed in [§8.3](#83-the-closed-list-of-twelve-spec-types); ad-hoc categories are not allowed (see [§1.7](01-scope.md#1.7) closed-list policy).

```text
Requirements tree (behavioral axis):       Parallel specification axis:

BR                                          SPEC-ARCH    SPEC-API
 └── SR  ←──── constrained-by[] ────►       SPEC-DATA    SPEC-INT
      └── TR ─── implements-spec[] ────►    SPEC-PROC    SPEC-UI
                                            SPEC-AI      SPEC-SEC
                                            SPEC-OPS     SPEC-TEST
                                            SPEC-DOC

Requirements tree:
  SR.parent              → BR              (single parent)
  TR.parent              → SR              (single parent)

Link graph (typed edges):
  SR.constrained-by[]    → SPEC-*
  TR.implements-spec[]   → SPEC-*
  SPEC-*.depends-on[]    → SPEC-*          (between specifications)
  SPEC-*.referenced-by[] → SR / TR          (auto-derived inverse)
```

### 8.2.3 Rationale

| Argument | Consequence |
|---|---|
| SPEC and SR answer different questions | SPEC does not refine an SR at a deeper level — it is a separate category of description |
| One SR rests on 5–7 SPEC | A "SPEC as parent of SR" tree leads to multiple parenthood |
| Industry standards (arc42, C4, OpenAPI, BPMN, ERD) live in parallel with requirements | RENAR follows this proven practice |
| An AI agent can parallelize SR and SPEC generation | Without one type blocking the other |

---

## 8.3 The closed list of twelve SPEC types

| Type | Purpose | Industry reference |
|---|---|---|
| `SPEC-ARCH` | System / subsystem architecture: contexts, containers, components, deployment view, quality attributes | arc42, C4 model (Brown), ISO/IEC/IEEE 42010 |
| `SPEC-API` | API contracts: REST / GraphQL / gRPC / async events; versioning, error model, rate limits | OpenAPI 3.x, AsyncAPI 2.x, gRPC IDL |
| `SPEC-DATA` | Data model: schema, ERD, indices, migrations, retention, PII classification | ISO/IEC 11179, JSON Schema |
| `SPEC-INT` | Integration: interaction between subsystems and external systems; protocols, contracts, SLA | Enterprise Integration Patterns (Hohpe) |
| `SPEC-PROC` | Process / workflow: business processes, state machines, saga, choreography, orchestration | BPMN 2.0, ISO/IEC 19510 |
| `SPEC-UI` | UI / UX: screens, navigation, user journeys, accessibility, i18n, baseline images | Material Design / Apple HIG, WCAG 2.2 |
| `SPEC-AI` | AI / ML: model cards, RAG, prompt engineering, eval strategy, cost budget | ISO/IEC 23894, NIST AI RMF |
| `SPEC-SEC` | Security: authn / authz, threat model, secrets management, data classification | STRIDE, OWASP ASVS, ISO/IEC 27001 |
| `SPEC-OPS` | Operations: deployment, observability, SLO / SLA, runbook, disaster recovery | Google SRE, ITIL v4, ISO/IEC 20000 |
| `SPEC-TEST` | Test benches and data: topology, emulators and sandbox instances of external systems, run configuration (what is mocked, what is live), datasets on both sides of every integration, rules for reconciling results against the data of foreign systems, volume and anonymization | ISO/IEC/IEEE 29119-4 (test techniques), Testcontainers, Pact |
| `SPEC-DOC` | Delivered project documentation: composition (user manual, administrator manual, training materials), the structure of each document (mandatory sections), the checkable requirements on it (completeness across roles and scenarios, version currency, format) | ISO/IEC/IEEE 26511, ISO/IEC/IEEE 26514 |
| `SPEC-UC` | Use case: an end-to-end path of a user or an agent through several SR / SPEC; the executor role (`human` — through the interface, `agent` — through the API) sets the run channel of the covering TCs; every step references the statement it touches | Use cases (Cockburn), ISO/IEC/IEEE 29148 §6.4 (scenarios), Gherkin (step form) |

**Mandatory types.** A description set of the `system` level contains non-empty `SPEC-ARCH` and `SPEC-SEC` regardless of whether the TZ mentions them; their absence or an empty mandatory body is a violation of the structural completeness of the version ([§10.7.2](10-lifecycle-qg.md#10.7.2), [§10.7.3](10-lifecycle-qg.md#10.7.3)). The silence of the TZ about security or about the structure of the system is a gap, not a client's decision: the description closes it with a statement of its own (with client-observable consequences — through a backward finding and the contractual contour, [§6.13.2](06-requirements-hierarchy.md#6.13.2)), not inherits it. The remaining types apply where there is a subject ([chapter 11](11-maturity-model.md)).

### 8.3.1 What did NOT make it into v1.0 (with rationale)

| Candidate | Decision | Rationale |
|---|---|---|
| `SPEC-EVENT` | Not a separate type | Events / queues — part of SPEC-API (asynchronous APIs) |
| `SPEC-CONFIG` | Not a separate type | Feature flags / env vars / secrets — part of SPEC-OPS |
| `SPEC-PERF` | Not a separate type | Performance / NFR — part of SPEC-ARCH (quality attributes) or SPEC-OPS (SLO) |
| ~~`SPEC-TEST-ENV`~~ | **Decision reversed in v1.0** | The former decision ("test environments — part of SPEC-OPS") is reversed: see [§8.3.2](#832-revision-of-the-test-bench-decision). The test bench is introduced as a separate type, `SPEC-TEST` |
| `SPEC-DOMAIN` | Not a separate type | Domain model — absorbed into SPEC-ARCH (decomposition) + SPEC-DATA (entities) |
| `SPEC-MIGRATION` | Not a separate type | Migration — part of SPEC-DATA (lifecycle) |
| `SPEC-COMPLIANCE` | Not a separate type | Compliance — links between SR/SPEC and regulations through `compliance-refs[]`, not a separate artifact |

A reversed decision is kept in the table struck through rather than deleted: the history of decisions is part of the standard, and the reader is entitled to see what was revised and why.

### 8.3.2 Revision of the test-bench decision

The former decision identified the test bench with a **deployment environment** and sent it to `SPEC-OPS` (the `environments[]` field: dev / staging / prod). For a simple system that is correct: the bench there is staging plus a dataset. The decision is reversed for three reasons.

**1. The bench of an integration-heavy system is a different object.** It is not an installation of the system but **the construction of the proof**: emulators and sandbox instances of external systems; agreed datasets **on both sides** of every integration; rules for reconciling results against the data of foreign systems — the expected result lives **not in our system**; the run configuration (what is mocked, what is live). The standard already sensed this: [§9.6.3](09-test-cases.md#9.6.3) requires, for SPEC-INT, a run "against a real or sandbox counterparty — a mocked contract is not enough," yet it gave no carrier for describing that counterparty.

**2. False invalidations — the decisive argument.** [§10.9.4](10-lifecycle-qg.md#10.9.4) marks a TC implementation stale on **any** change of the norm it references, automatically and without inspecting the cause. If a TC norm referenced the bench through SPEC-OPS, then an edit to the **deployment procedure** — unrelated to the bench — would make the implementations of **every** TC referencing that SPEC-OPS stale. Deployment is edited more often than the bench, and the body of evidence would be devalued regularly and falsely. A separate `SPEC-TEST` makes invalidation **precise**: TC are invalidated if and only if what they are valid against has changed.

**3. An alien signature.** Sensitive test data (volume, anonymization, the use of the client's real data) is agreed by the **client**. A client signature on a section of an operations document is alien; on a separate artifact it is natural.

The boundaries of the new types:

| Type | Answers the question |
|---|---|
| `SPEC-OPS` | **How the system lives** in operation (deployment, observability, SLO, runbook) |
| `SPEC-TEST` | **How its conformance is proven** (benches, data, run configuration) |
| `SPEC-DOC` | **What enters the delivery** to the client (documents as a subject of the contract) |

**The SPEC-DOC ↔ SPEC-OPS boundary.** The criterion is **inclusion in the composition of the delivered result**, and that composition is set by the contract **before** acceptance rather than settled during it:

- **The administrator manual is always `SPEC-DOC`**: if the client operates the system, the manual for the client's administrators enters the composition of the delivery. No "internal version" of this genre exists.
- **The runbook is always `SPEC-OPS`**: it is the internal operational regulation of the contractor's operating team; it is not presented to the client and is not a subject of acceptance.

Dual membership of a document is excluded by construction — the `TR.implements-spec[]` edge is always unambiguous.

Closed-list policy: if subsequent work reveals that one of the excluded types is genuinely needed — it is added through the formal change procedure of the standard with rationale.

---

## 8.4 Common schema (shared frontmatter fields)

All 12 SPEC types share a common set of frontmatter fields. Type-specific fields are added as extensions on top (§8.5). The full machine-readable data model — in [reference/02-schemas.md](../../reference/en/02-schemas.md).

```yaml
---
# === Identity (mandatory) ===
id: SPEC-<TYPE>-NN[.N]              # immutable; TYPE ∈ {ARCH,API,DATA,INT,PROC,UI,AI,SEC,OPS,TEST,DOC}
title: "<short, descriptive>"
type: SPEC-ARCH | SPEC-API | SPEC-DATA | SPEC-INT | SPEC-PROC | SPEC-UI | SPEC-AI | SPEC-SEC | SPEC-OPS | SPEC-TEST | SPEC-DOC
slug: "<kebab-case>"                # auto-derived

# === Scope (mandatory) ===
level: system | subsystem | module
scope:
  system: "<system-id>"
  subsystem: "<subsystem-id>"       # null if level=system

# === Ownership and priority ===
# No status or version of its own: a SPEC is approved as part of a description set
# version, its state is derived from it ([§10.7](10-lifecycle-qg.md#10.7)).
priority: must | should | could     # not all types use; mostly SPEC-SEC / SPEC-OPS

# === Source: provenance (conditional, see chapter 7 §7.4.1) ===
# source.adapt — conditional (present when an ADAPT was created; §7.4.1.1).
# source.tz-section — always mandatory.
# source.adversarial-review-ref — mandatory when source.adapt is omitted.
source:
  adapt: ADAPT-NNN                  # conditional
  adapt-section: "Forward §N"       # mandatory if adapt is present
  tz-section: "§N.N"                # always mandatory
  adversarial-review-ref: AR-NNN   # mandatory if adapt is omitted (§7.4.6)

# === Addressee of the obligation (conditional, §6.14.3) ===
applies-to: self                    # self | consumer; only in a component's SPEC; default self

# === Link graph (auto-managed except mandatory ones) ===
referenced-by: []                   # auto-derived; SR/TR/SPEC referencing here
depends-on: []                      # mandatory if present; SPEC-* this SPEC rests on
verified-by: []                     # auto-derived; list of verifying TC IDs

# === AI provenance (mandatory at RENAR-4+; canonical schema — §4.10.1) ===
ai-provenance:
  generated-by: "<vendor>-<model>-<version>@<date>"
  generated-at: "<ISO-8601>"
  prompt-template: "<template-path>@<version>"
  context-tokens: integer
  output-tokens: integer
  human-edits: boolean
  generation-time-ms: integer        # optional; see §4.10.1
  # optional at RENAR-4, mandatory at RENAR-5:
  # cost-budget, cost-actual

# === Replacement (mandatory if applicable) ===
replaces: "<old-id>"                 # removal and the back-reference replaced-by — in the set version record (§10.5.3)
deprecated-date: "<ISO date>"

# === Compliance (optional) ===
compliance-refs: []                 # references to ISO/GDPR/AI Act/NIST AI RMF
---
```

### 8.4.1 Mandatory body sections

The body of any SPEC MUST contain:

1. **Purpose** — 1–3 paragraphs.
2. **Scope** — what is in, what is out.
3. **Type-specific sections** — see §8.5.
4. **Link to requirements** — which SR/BR reference it.
5. **Link to other SPEC** — `depends-on[]`.
6. **Verification** — which TC verify this SPEC.

**Coverage completeness.** The mandatory body MUST describe the subject of the specification exhaustively. Restricting coverage to a subset of elements by a subjective selection criterion is prohibited — in all twelve types and regardless of the wording used. The prohibition applies to the subject, not to the vocabulary: an evaluative adjective ("key", "critical", "main") or a phrase of measure ("to a sufficient extent", "essential", "relevant") is prohibited only where it narrows what is subject to description. The same words remain legitimate where they name a property of the subject itself — such as the severity class of alerts in [§8.5.9](#8.5.9).

**The subject of a specification** is checkable: it is everything named by the linked requirements (the "Link to requirements" section) and, for a SPEC-UI, by the screen list ([§8.5.6.1](#8561-screen-and-completeness-across-the-system)); narrowing is permitted only by an explicit Scope section listing the excluded elements and the reason for each exclusion.

The rationale is the inversion of the source of truth ([§2.3.1](02-methodology-positioning.md#2.3.1)): whatever did not enter the SPEC as "insignificant" has no source of truth at all. An agent implementing the system is left with two ways out, and both violate the standard: leave the undescribed part unimplemented (product incompleteness) or implement it arbitrarily (guesswork, prohibited by [§2.3.3](02-methodology-positioning.md#2.3.3)).

---

## 8.5 Schema extensions by SPEC type

A brief description of type-specific fields and mandatory body sections. The full machine-readable extension schema — in [reference/02-schemas.md](../../reference/en/02-schemas.md). Industry references in detail — in the listed standards.

### 8.5.1 SPEC-ARCH

**Type-specific frontmatter**: `arch-style`, `deployment-model`, `tech-stack`, `quality-attributes`.

**Mandatory body**: system context (C4 L1), containers (C4 L2), components (C4 L3) for every L2 container, quality attributes (latency / throughput / availability), ADR log, the list of the system's screens — the code, name and source of every screen from the description ([§8.5.6.1](#8561-screen-and-completeness-across-the-system)); a system without an interface records that explicitly.

**Spec-specific TC** ([chapter 9](09-test-cases.md)): architecture conformance tests (zoning), reference tests of quality attributes. Coverage of the screen list is not a TC but a precondition of version approval ([§10.7.2](10-lifecycle-qg.md#10.7.2), [§8.5.6.1](#8561-screen-and-completeness-across-the-system)).

### 8.5.2 SPEC-API

**Type-specific frontmatter**: `api-style` (rest / graphql / grpc / async-events), `api-version`, `versioning-strategy`, `authentication`, `rate-limits`, `contract-file` (location of machine-readable contract).

**Mandatory body**: endpoints / operations with payload / response / errors, versioning rules (breaking vs non-breaking), error model, authn/authz reference to SPEC-SEC, rate limits, 2–3 example requests per endpoint.

**Spec-specific TC**: contract tests, authentication negative, rate limit tests.

### 8.5.3 SPEC-DATA

**Type-specific frontmatter**: `data-style` (relational / document / graph / columnar), `storage-engine`, `schema-version`, `pii-classification[]`, `retention-policies[]`, `migration-strategy`.

**Mandatory body**: domain entities, ERD (text / Mermaid / link), entity fields (type / constraints / indices / defaults), relationships (FK / cardinality / cascade), PII / sensitive data classification + encryption at-rest + retention, migration approach, index strategy.

**Spec-specific TC**: migration tests, constraint tests (FK / NOT NULL / unique), PII handling tests, data retention tests.

### 8.5.4 SPEC-INT

**Type-specific frontmatter**: `integration-pattern` (request-response / event-driven / message-queue / webhook / file-transfer), `direction`, `counterparty`, `sla`, `idempotency`.

**Mandatory body**: integrated systems, exchange contract, failure modes + retry strategy, idempotency + dedup, security between systems, observability (correlation IDs).

**Spec-specific TC**: contract tests with a counterparty mock, failure injection, idempotency, end-to-end TC `tc-type: contract`.

**Note**: SPEC-INT replaces the existing `INT-SR` ([§8.7](#87-migration-uic--aic--int-sr--ts--spec-) migration).

### 8.5.5 SPEC-PROC

**Type-specific frontmatter**: `process-style` (bpmn / state-machine / saga / choreography / orchestration), `state-count`, `participants[]`, `sla` end-to-end and per step, `compensation` (defined / not-applicable / manual).

**Mandatory body**: process diagram (BPMN-flavor / Mermaid / link), states and transitions (for a state machine), participants and their roles, happy path, alternative scenarios and exceptions (every scenario step with a reference to the statement it touches, [§8.5.12.1](#85121-step-reference-to-a-statement)), timeouts and compensation (for saga), SLA.

**Spec-specific TC**: happy path E2E, alternative paths, compensation tests (for saga), SLA tests.

### 8.5.6 SPEC-UI

**Type-specific frontmatter**: `ui-platform`, `target-users[]` (with references to ADAPT persona sections), `design-system`, `accessibility-level` (WCAG-A / AA / AAA), `i18n`, `mockup-links[]`, `baseline-images[]` for VLM-judge tests, `screens[]` (the codes of the screens from the SPEC-ARCH list that the document covers, [§8.5.6.1](#8561-screen-and-completeness-across-the-system)).

**Mandatory body**: overall interface structure, all screens in the declared Scope — each under a code from the SPEC-ARCH screen list ([§8.5.6.1](#8561-screen-and-completeness-across-the-system)), user journeys without technical details covering every user action available through the interface (every journey step with a reference to the statement it touches, [§8.5.12.1](#85121-step-reference-to-a-statement)), cross-cutting elements (access rights / notifications / error / empty states), tone and style, accessibility, i18n.

**Spec-specific TC**: VLM-judge against a baseline (judge ≠ production isolation), accessibility (axe-core / Pa11y), i18n (string overflow / RTL), user journey E2E.

**Note**: SPEC-UI replaces the existing `UIC` ([§8.7](#87-migration-uic--aic--int-sr--ts--spec-) migration).

#### 8.5.6.1 Screen and completeness across the system

Coverage completeness ([§8.4.1](#841-mandatory-body-sections)) acts inside the declared Scope of one document and does not guarantee that the sum of all SPEC-UIs covers the interface of the system: for a document that does not exist there are no norms it could violate. Completeness across the system is given by the screen list and the coverage rule.

**A screen** is a state of the interface in which the user performs an action or observes its result, addressable independently of the implementation technique: a route, a page, a modal window, a wizard step, a screen of a mobile or desktop application — when an action is performed or its result is shown in it. A state without an address of its own — a waiting indicator, a pop-up notification over a screen — is not a screen: it is described by the screen on which it appears.

**The screen list** is kept in the mandatory body of SPEC-ARCH ([§8.5.1](#851-spec-arch)): an immutable screen code (V1), a name, a source — the SR or the section of the SPEC-ARCH itself from which the screen follows. The source of the list is the description only. A list built from the routes of the implementation would make the code the authority on "what MUST be described" and would violate the inversion of the source of truth ([§2.3.1](02-methodology-positioning.md#2.3.1), the prohibition of [§2.3.3](02-methodology-positioning.md#2.3.3)); a screen that exists in the implementation and is absent from the list is a source-of-truth drift ([§4.11](04-terms.md#4.11), class 4.11.3) and a backward finding, not grounds to extend the list after the fact. A system without an interface records "no interface" in the list — explicitly, not by omitting the section.

**The coverage rule.** Every screen of the list is covered by at least one SPEC-UI of the same set version: a SPEC-UI declares the codes of the screens it covers in `screens[]` and describes each in its "Screens" section under the same code. A screen of the list without a covering SPEC-UI is a violation of the structural completeness of the version ([§10.7.2](10-lifecycle-qg.md#10.7.2)): the version is not approved until the screen is described or removed from the list by a decision visible in the version record. A code in `screens[]` absent from the list is the same error from the other side. The number of SPEC-UIs is not limited; completeness inside each is per §8.4.1. The rule was introduced in v1.1 (ADR-023 rev. 3); it introduces no new artifacts or types.

### 8.5.7 SPEC-AI

**Type-specific frontmatter**: `ai-pattern` (rag / fine-tuning / prompt-engineering / tool-use / multi-agent), `production-model` (vendor / model / version), `judge-model` (MUST differ from production), `context-strategy`, `eval-strategy` (metric / threshold / baseline-dataset), `cost-budget`.

**Mandatory body**: AI component architecture (pipeline / orchestration / fallback), model card (capabilities / limits / known failure modes), context strategy, eval strategy with judge ≠ production isolation, cost management, hallucination mitigation, adversarial aspects.

**Spec-specific TC**: eval against a baseline (judge isolated), adversarial (prompt injection as a negative TC), cost regression, hallucination tests.

**Note**: SPEC-AI replaces the existing `AIC`. Isolation judge ≠ production model is a mandatory requirement of the standard for all eval-TC.

### 8.5.8 SPEC-SEC

**Type-specific frontmatter**: `security-domains[]`, `auth-model` (authn / authz strategies), `data-classification[]` (PII-high / PCI / internal), `threat-model-method` (STRIDE / PASTA / OCTAVE), `compliance[]`, `incident-response` reference in SPEC-OPS.

**Mandatory body**: auth model (authn flow / authz rules), data classification with protection, threat model (STRIDE table with a mitigation for each threat), secrets management, audit (what is logged / retention / access), encryption (at-rest / in-transit / key management), compliance mapping (references to specific clauses).

**Spec-specific TC**: authn (pos + neg), authz (RBAC matrix), threat-test (each STRIDE threat → at least 1 negative TC), audit log, secrets leakage.

### 8.5.9 SPEC-OPS

**Type-specific frontmatter**: `deployment-style`, `environments[]` (dev / staging / prod with purpose and scale), `slo`, `observability` (logs / metrics / traces / alerting), `runbook-link`, `disaster-recovery` (rto / rpo / backup-strategy).

**Mandatory body**: environments, deployment process (CI/CD pipeline / gating / rollout strategy), SLO (availability / latency / error budget), observability, alerting (critical alerts / escalation), runbook, capacity planning, disaster recovery.

**Spec-specific TC**: deployment tests (smoke), SLO regression (load testing), failover (DR drills), observability (alerts fire when expected).

### 8.5.10 SPEC-TEST

**Type-specific frontmatter**: `benches[]` (bench topology: nodes, versions, what is live / what is emulated), `counterparties[]` (emulators and sandbox instances of external systems — one entry per integration), `datasets[]` (a dataset on both sides of the integration: volume, provenance, `anonymized: boolean`, `contains-real-client-data: boolean`), `reconciliation-rules[]` (rules for reconciling results against the data of foreign systems), `run-config` (what is mocked, what is live, the run order), `client-signature` (**mandatory** if at least one dataset carries the client's real or personal data).

**Mandatory body**: bench topology; the list of external systems and the way each is represented (real / sandbox / emulator — with rationale); datasets on both sides of every integration; reconciliation rules (**where the expected result lives**, if it is not in our system); run configuration; the data policy (volume, anonymization, retention period).

**Spec-specific TC**: reproducibility (a repeated run on the same bench yields the same result); counterparty validity (the sandbox answers like the real system — otherwise the proof is fictitious); dataset integrity (the data conforms to the declared schema and volume).

**Link to TC.** Every TC with `automation.kind: dynamic` MUST carry an `environment-ref` to a SPEC-TEST ([§9.3](09-test-cases.md#9.3)): "the test passed" is meaningful only against a concrete bench and concrete data. Static checks (the doc-lint of §9.8, the structural TC of §9.8.1) require no bench — the analyzer works over artifacts — and the field does not apply to them. A change of bench or dataset increments the SPEC-TEST version and, per [§10.5.4](10-lifecycle-qg.md#10.5.4), invalidates `verified` on the dependent TC — **precisely**, without touching anything else.

**The client signature.** Volume, anonymization, and the admissibility of using the client's real data are a matter for **the client's** decision, not the contractor's. When `contains-real-client-data: true` or anonymization is incomplete, `client-signature` is mandatory.

### 8.5.11 SPEC-DOC

**Type-specific frontmatter**: `deliverables[]` (one entry per document: `kind` — user manual / administrator manual / training materials / other; `audience`; `format`; `language`), `required-sections[]` (the mandatory sections of each document — **set as a requirement**, not left to the contractor's discretion), `traces-to[]` (the BR / SR / SPEC whose behavior the document describes), `version-binding` (the rule matching the document version to the system version), `acceptance-criteria[]` (against what the document is accepted).

**Mandatory body**: the composition of the delivery (which documents and for which audience); the structure of each document; the requirements on it (completeness across the roles and scenarios declared in the requirements; version currency; language and format); the readiness criteria.

**Spec-specific TC**: a **doc lint** ([§9.8](09-test-cases.md#9.8)) — mandatory.

**Why the doc lint is mandatory.** A non-verifiable SPEC does not pass QG-2 ([§9.7](09-test-cases.md#9.7), MVR-5 requires coverage of every normative statement). A SPEC-DOC without a machine check would either block the delivery or force a hole in MVR-5 for the sake of a single type. Its statements are therefore normed checkably: the presence of the mandatory sections, coverage of all roles and scenarios from the linked BR / SR, the match of the document version to the system version, format and language — all of this is checked mechanically (`automation.kind: static`). Substantive conformance to the implemented behavior ("the text does not lie about the system") is checked optionally by a judge agent through the already existing P7 / `eval` mechanism — no new entities are required.

---

### 8.5.12 SPEC-UC

**Type-specific frontmatter**: `role` (`human` — the path is walked by a person through the interface; `agent` — the path is walked by an agent through the API / CLI; the field sets the run channel of the covering TCs), `persona` (a reference to the persona section of the ADAPT, for `human`), `covers[]` (the SR / SPEC the scenario stitches together; at least two), `entry` (the SPEC-UI screen or SPEC-API operation the path starts from), `steps[]` (one entry per step: `n`, `action`, `ref` — the address `<id>#<n>` of the SR / SPEC statement the step touches ([§15.1.1](15-description-language.md#15.1.1)), **mandatory**; `expects` — the observable result of the step).

**Mandatory body**: the goal of the scenario and its executor (role, persona); preconditions (state of the system and data); the main path — numbered steps, each with a reference to the statement it touches ([§8.5.12.1](#85121-step-reference-to-a-statement)); alternative and negative branches (what happens on failure at each step, with the same reference); postconditions; covering TCs (which TCs walk the path step by step and end to end).

**Spec-specific TC**: for `role: human` — a `ux` TC per step and an end-to-end journey E2E; for `role: agent` — `system` / `contract` per the channel of the step ([§9.8](09-test-cases.md#9.8)). Negative branches — separate TCs, mandatory on a par with the steps of the main path ([§9.7](09-test-cases.md#9.7)).

**Why a separate type.** A user journey in SPEC-UI and a happy path in SPEC-PROC live inside a document of their own type and do not leave its Scope; a use case by definition stitches several SR and SPEC together and carries a property of its own — the executor role, on which the run channel depends: a person's path through the interface is checked by `ux` TCs, an agent's path through the API — by `system` / `contract`. Bypassing the interface with a `system` TC for a green run while the interface is broken is the observed failure for which the type and the rule of [§9.8](09-test-cases.md#9.8) were introduced. The type was added in v1.1 under the procedure of [§13.9](13-conformance.md#13.9).

#### 8.5.12.1 Step reference to a statement

Every scenario step — in SPEC-UC, in a SPEC-UI user journey ([§8.5.6](#856-spec-ui)) and in the happy path / alternative scenarios of SPEC-PROC ([§8.5.5](#855-spec-proc)) — MUST carry a `ref` to the SR or SPEC statement it touches, in the address form `<id>#<n>` ([§15.1.1](15-description-language.md#15.1.1)); `#<n>` is omitted only for an artifact with a single statement. In a SPEC-UC the reference is the `steps[].ref` field; in a SPEC-UI user journey and SPEC-PROC scenarios the steps are written as a numbered list, and every step ends with the reference `→ <id>#<n>` — that is how the structural-completeness check finds a step without a reference in prose. A step without a reference violates structural completeness ([§10.7.2](10-lifecycle-qg.md#10.7.2)); a purely navigational step references the SPEC-UI statement about the screen or transition. The reference gives the reverse answer — which statements are touched by no scenario — and makes the TC coverage of a step checkable: the TC covering a step names the same address in `verifies[]` (`id` + `statement`, [§9.3](09-test-cases.md#9.3)).

---

## 8.6 Link to requirements and tasks

### 8.6.1 SR.constrained-by[]

An SR receives a `constrained-by[]` field in its frontmatter — typed references to SPEC. This is a **graph**, not a tree of parents. The SR's parent in the requirements tree is single (BR).

```yaml
# SR frontmatter (example)
id: SR-05
parent:
  id: BR-02
constrained-by:
  - SPEC-UI-01
  - SPEC-API-02
  - SPEC-DATA-03
  - SPEC-PROC-01
  - SPEC-SEC-01
verified-by:
  - TC-12
  - TC-13
source:
  adapt: ADAPT-001
  adapt-section: "Forward §3"       # see canonical identifier §8.4
```

### 8.6.2 TR.implements-spec[]

A TR (task) references its SR (parent in the tree) + one or more SPEC through `implements-spec[]`:

```yaml
id: TR-42
title: "Implement endpoint POST /orders"
parent:
  id: SR-05
implements-spec:
  - SPEC-API-02
  - SPEC-DATA-03
verified-by:
  - TC-14
```

### 8.6.3 SPEC.depends-on[]

A SPEC MAY rest on another SPEC:

```yaml
id: SPEC-API-02
title: "Orders REST API"
type: SPEC-API
depends-on:
  - SPEC-DATA-03         # stable data schema
  - SPEC-SEC-01          # auth model for endpoints
```

When an upstream SPEC changes (for example SPEC-DATA-03), all downstream artifacts (SPEC-API-02 and the SR linked through it) MUST be reviewed: either the change is compatible and the downstream enters the new version unchanged, or the downstream is edited in the same draft; the TC implementations affected by the change become stale ([§10.9.4](10-lifecycle-qg.md#10.9.4)), and no verification record of the new version is created until they are run.

### 8.6.4 Auto-derived inverse edges

`SPEC.referenced-by[]` is recomputed by a substrate hook after each change to SR / TR / SPEC. An orphan SPEC (without `referenced-by[]` and without an active status) is a warning in the quality report.

---

## 8.7 Migration UIC / AIC / INT-SR / TS → SPEC-*

### 8.7.1 Mapping table

| Old type | New type | Migration kind |
|---|---|---|
| `UIC-NN` | `SPEC-UI-NN` | Rename ID + move to `specs/ui/` |
| `AIC-NN` | `SPEC-AI-NN` | Rename ID + move to `specs/ai/` |
| `INT-SR-NN` | `SPEC-INT-NN` | Rename ID + move to `specs/int/` |
| `TS-NN` | `SPEC-<TYPE>-NN` (distribution) | Manual review of each TS; an AI agent classifies the content, the architect approves in one click |

### 8.7.2 Atomic migration

Migration is one atomic change unit ([V2](03-substrate-versioning.md#3.3.2)) at the project level. Parallel existence of the old types (UIC / AIC / INT-SR / TS) and SPEC-* as the source of truth is prohibited.

Procedure (substrate-independent):

1. Preparation: an AI agent classifies each existing TS-NN into one of the 12 SPEC types.
2. The architect approves the classification.
3. Atomic change: rename IDs (UIC→SPEC-UI; AIC→SPEC-AI; INT-SR→SPEC-INT; TS→SPEC-*), move files to `specs/<type>/`, update all references in BR / SR / TR / TC frontmatter (`parent: UIC-NN` → `constrained-by: [SPEC-UI-NN]`).
4. Regeneration of auto-derived files (REQUIREMENTS.md, SPECS.md, inverse edges).
5. CI check: absence of orphan references and old IDs.

### 8.7.3 ID immutability

After migration, SPEC IDs are **immutable** (see [V1, §3.3.1](03-substrate-versioning.md#3.3.1)). Renaming `SPEC-API-02` → `SPEC-API-08` is prohibited. Replacement is through `deprecated` + a new ID with `replaces[]`.

---

## 8.8 Quality gates for SPEC

A SPEC has no state machine of its own: its state is derived from the description set version ([chapter 10 §10.7](10-lifecycle-qg.md#10.7)). Conditions by stage:

| Stage | Condition |
|---|---|
| draft | Created; mandatory frontmatter fields are being filled; present only in the set draft |
| structural completeness | Mandatory body sections (§8.4.1) and type-specific ones (§8.5) are present — a precondition of version approval ([§10.7.2](10-lifecycle-qg.md#10.7.2)) |
| approved | Belongs to the approved version `N.M`: `depends-on[]` is consistent within the same version ([§10.7.3](10-lifecycle-qg.md#10.7.3)); the Architect's signature on the version |
| verified on a product version | All mandatory spec-specific TCs ([chapter 9 §9.8](09-test-cases.md#9.8)) passed with `last-run.set-version = N.M` — QG-2 of the version ([§10.3.3](10-lifecycle-qg.md#10.3.3)) |
| removed | Absent from the current version; `replaced-by` is mandatory in the version record ([§10.5.3](10-lifecycle-qg.md#10.5.3)) |

Link to QG-0 / QG-2 of SENAR:

- QG-0 (the task has a goal/AC) is extended: for tasks implementing a SPEC, `implements-spec[]` in the TR frontmatter is mandatory.
- QG-2 (a `done` task has evidence) is extended: for tasks implementing a SPEC, a TC of the corresponding spec-specific kind ([chapter 9 §9.7](09-test-cases.md)) is mandatory.

---

## 8.9 Storage layout

### 8.9.1 At the system level

```text
[requirements-substrate]/      # root of the requirements substrate (layout — guide/03 or guide/04)
  br/
  sr/
  specs/
    arch/   SPEC-ARCH-NN-*.md
    api/    SPEC-API-NN-*.md
    data/   SPEC-DATA-NN-*.md
    ui/     SPEC-UI-NN-*.md
    ai/     SPEC-AI-NN-*.md
    int/    SPEC-INT-NN-*.md
    proc/   SPEC-PROC-NN-*.md
    sec/    SPEC-SEC-NN-*.md
    ops/    SPEC-OPS-NN-*.md
    test/   SPEC-TEST-NN-*.md
    doc/    SPEC-DOC-NN-*.md
  adapt/
  tz/
  SPECS.md                # auto-generated index
```

> All 12 SPEC types ([§8.3](#83-the-closed-list-of-twelve-spec-types)) are allowed at any `level`; the `specs/<type>/` subfolders are created as needed — not all are mandatory at the system level.

### 8.9.2 At the subsystem level

```text
[subsystem-substrate]/         # subsystem scope
  br/                     # if it has its own business side
  sr/
  specs/
    arch/   SPEC-ARCH-NN-*.md      # subsystem architecture
    api/    SPEC-API-NN-*.md
    data/   SPEC-DATA-NN-*.md
    ui/     SPEC-UI-NN-*.md
    ai/     SPEC-AI-NN-*.md
    int/    SPEC-INT-NN-*.md
    proc/   SPEC-PROC-NN-*.md
    sec/    SPEC-SEC-NN-*.md
    ops/    SPEC-OPS-NN-*.md
    test/   SPEC-TEST-NN-*.md
    doc/    SPEC-DOC-NN-*.md
  adapt/
  SPECS.md
```

The substrate-native storage implementation is substrate-specific (see [guide/03](../../guide/en/03-tool-guide-git.md), [guide/04](../../guide/en/04-document-store-substrate.md)).

### 8.9.3 SPECS.md — auto-generated index

`SPECS.md` is an auto-generated registry of all SPEC: ID, type, title, status, link to the verifiable requirement, link to the file. Marked `linguist-generated=true`. Regeneration triggers — every change to SPEC frontmatter or every approve / verify gate.

---

## 8.10 Links to other chapters

| Chapter | Link |
|---|---|
| [02 Methodology positioning](02-methodology-positioning.md) | SPEC as a parallel axis — a consequence of the Source-of-Truth inversion |
| [06 Requirements hierarchy](06-requirements-hierarchy.md) | `SR.constrained-by[]`, `TR.implements-spec[]` |
| [07 ADAPT](07-adapt.md) | SPEC references ADAPT through `source.adapt` |
| [09 Test cases](09-test-cases.md) | Spec-specific TC types (the table of mandatory TC kinds for each SPEC type) |
| [10 Lifecycle and QG](10-lifecycle-qg.md) | The SPEC state is derived from the set version (§10.7); preconditions by SPEC type at version approval (§10.7.3) |
| [03 Substrate versioning](03-substrate-versioning.md) | SPEC IDs are immutable (V1); migration is atomic (V2) |
| [11 Maturity model](11-maturity-model.md) | RENAR-3+: all 12 SPEC types where applicable |
| [reference/02 — schemas](../../reference/en/02-schemas.md) | Full machine-readable schema for each type-specific extension |
| [reference/05 — knowledge graph schema](../../reference/en/05-knowledge-graph-schema.md) | `constrained-by[]`, `implements-spec[]`, `depends-on[]` as edge types in the graph |
