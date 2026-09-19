---
title: "Normative references"
order: 14
lang: en
---
# 14. Normative references

> **Part of the RENAR Standard v1.1** · [← Table of contents](README.md)

## 14.1 Which standards RENAR builds on

An engineer who opens this chapter has one practical question: which of the listed standards must I actually comply with, and which are just background? RENAR answers directly. It does not rewrite other people's standards — it builds on them and claims conformance only in part, not in whole. Hence the split: **normative references** ([§14.4](#14.4)) — what RENAR actually declares conformance to (ISO/IEC/IEEE 29148, ISO/IEC 5338, and others); **informative references** ([§14.5](#14.5)) — methodologies and terminology used for positioning, not required for a conformance claim; **conformance positioning** ([§14.3](#14.3)) — a single formulation that places RENAR among related standards; **what RENAR does not adopt** ([§14.7](#14.7)) — a closed list of non-borrowed practices. The full extended catalog of informative mappings — [reference/11](../../reference/en/11-external-standards-mapping.md).

If a normative reference conflicts with a clause of this chapter, the clause of this chapter prevails (RENAR is a specialization and adaptation). RENAR makes a conformance claim against an external standard **only** in the stated part, not in whole. All dates are the publication date of the referenced edition; the reference is **dated** ([§14.4.1](#14.4.1)).

---

## 14.2 SENAR as the parent standard

RENAR is a **specialization of SENAR** ([§1.6.1](01-scope.md#1.6.1)) in the requirements-engineering domain. RENAR does not duplicate the following SENAR clauses; they apply as-is:

| SENAR clause | Used without rewriting |
|---|---|
| 5 values and 14 rules | Context for all of RENAR; see [§1.6.1](01-scope.md#1.6.1) |
| QG-0 / QG-1 / QG-2 as a concept | RENAR makes the state machines concrete in [§10](10-lifecycle-qg.md) |
| 10 common process metrics | RENAR adds 11 domain metrics in [§12.3](12-metrics.md#12.3) |
| 5 levels of general maturity | RENAR-M is a separate dimension ([§11.2](11-maturity-model.md#11.2)) |
| 5 roles (Supervisor, AI agent, Architect / Tech Lead, Reviewer, Stakeholder) | RENAR does not redefine the roles; see [§5](05-roles.md) |
| Agent instrumentation (control levels, profiles) | RENAR extends it for requirements specifics |

RENAR begins where SENAR ends: SENAR is the general methodology of AI-native development; RENAR is the normative requirements-management document for SENAR-compatible systems.

---

## 14.3 Conformance positioning

> **RENAR — requirements management aligned with ISO/IEC/IEEE 29148:2018, adapted for development with AI agents, built on top of the SENAR methodology, and compatible with SAFe 6.0 coordination.**

This formulation is the point of reference for all conformance claims that projects issue based on RENAR ([§13.4](13-conformance.md#13.4)).

---

## 14.4 Normative dated references

Each entry in [§14.4.2](#14.4.2)–[§14.4.6](#14.4.6) contains the blocks **"What it normalizes"**, **"How RENAR relates"**, and **"Conformance claim"**. The blocks **"What RENAR adapts"** and **"What RENAR does not adopt"** appear only in deep-adaptation entries (29148 and 5338); the other entries are shorter — this reflects the depth of the claim, not a structural defect.

### 14.4.1 The notion of a dated reference

RENAR uses **dated references**: each normative reference cites a specific edition of a standard (with its year). Undated references do not apply — semantics change between editions, and a conformance claim MUST be verifiable against a pinned edition.

**Lifecycle of a normative reference.** *Active* — the reference is cited in the current version of RENAR (`renar-version` in the conformance manifest, [§13.4.2](13-conformance.md#13.4.2)). *Updated* — the referenced standard has released a new edition; RENAR is updated within a reasonable timeframe (the project manifest pins a `renar-version`, which in turn pins the editions of external references). *Withdrawn upstream* — the referenced standard has been retired (like IEEE 830-1998); RENAR moves the reference to [§14.5](#14.5) and names the successor.

**Triggers for immediate re-evaluation.** When a new edition of one of the references in [§14.4](#14.4) is released, an immediate re-evaluation ([§13.7.3](13-conformance.md#13.7.3)) of project manifests is REQUIRED: checking the RENAR chapters for consistency (a changelog entry); updating `renar-version` in project manifests; an entry in the substrate audit-trail.

**Negative scenario:** a RENAR conformance claim of `renar-version: 1.0`, when a new edition of ISO/IEC 5338 is released, **without** updating `renar-version` in the manifest — is invalid; the substrate hook ([§13.8.1](13-conformance.md#13.8.1)) detects the stale version.

### 14.4.2 ISO/IEC/IEEE 29148:2018 (requirements engineering)

*Official title (EN): Systems and software engineering — Life cycle processes — Requirements engineering.*

**What it normalizes:** the international requirements-engineering standard — stakeholder needs, specification, validation, verification, attributes, traceability, lifecycle.

**How RENAR relates:**

| 29148 | RENAR | Type |
|---|---|---|
| Requirement classes: stakeholder, system, software | BR, SR, TR | Borrows with renaming |
| Requirement attributes (18 in 29148) | Mandatory minimum in frontmatter ([§6.5.2](06-requirements-hierarchy.md#6.5.2), [§6.6.2](06-requirements-hierarchy.md#6.6.2)) — 7–8 fields | Simplifies |
| SRS structure | The requirements substrate structure is isomorphic | Borrows |
| Verification methods: inspection, analysis, demonstration, test | TC ([§9](09-test-cases.md)) — a full-fledged artifact; inspection — via the `[test-spec-change]` workflow ([§9.13](09-test-cases.md#9.13)) | Adapts |

**What RENAR adapts:**

- 29148 provides for 18 attributes; RENAR keeps 7–8 mandatory ones, the rest being auto-derived ([§4.12](04-terms.md#4.12)) or optional. Rationale: in development with AI agents, excessive attribution increases the risk of hallucinations ([§12.3.3](12-metrics.md#12.3.3)).
- 29148 does not single out TC as a separate artifact. RENAR makes TC a full-fledged artifact ([§9](09-test-cases.md)).

**What RENAR does not adopt:** the formal review meetings and walkthroughs of 29148 — replaced by QG-0 / QG-2 and adversarial AI review ([§11.7](11-maturity-model.md#11.7) for RENAR-4+).

**Conformance claim:** RENAR claims conformance with ISO/IEC/IEEE 29148:2018 in the part covering requirement classes, attributes, lifecycle, and verification methods (pinned in the manifest, [§13.4.2](13-conformance.md#13.4.2)).

### 14.4.3 ISO/IEC 25010:2023 (product quality model)

*Official title (EN): SQuaRE — Product quality model.*

**What it normalizes:** nine software quality characteristics (the 2023 edition), including the new Safety.

**How RENAR relates:** the 25010 characteristics are **mandatory categories** for non-functional SR. The frontmatter of a non-functional SR ([§6.6.2](06-requirements-hierarchy.md#6.6.2)) MUST contain a `quality-characteristic` from the 25010 list; for a functional SR the field is omitted.

**Conformance claim:** RENAR claims conformance with ISO/IEC 25010:2023 in the part covering the category vocabulary for NFRs.

### 14.4.4 ISO/IEC 25022:2016 / 25023:2016 (quality measures)

**What it normalizes:** formal measures for each 25010 characteristic (for example, response time in ms).

**How RENAR relates:** the Pass criteria in TC ([§9.11.1](09-test-cases.md#9.11.1)) MUST be expressed through 25022/25023 measures where applicable. Example: "p95 < 200 ms at 100 RPS" instead of "performance is acceptable".

**Conformance claim:** RENAR claims conformance in the part covering measurable Pass criteria for TC.

### 14.4.5 ISO/IEC 5338:2023 (AI-system lifecycle)

**What it normalizes:** the first international standard for the AI-system lifecycle (an adaptation of ISO/IEC 12207).

**How RENAR relates:**

- **Decision logs** — material decisions by the AI agent are documented; implementation: audit-trail ([§10.13](10-lifecycle-qg.md#10.13)) + `ai-provenance` ([§4.10.1](04-terms.md#4.10.1)).
- **Data versioning** — eval-datasets with version pin V5 ([§3.3.5](03-substrate-versioning.md#3.3.5)).
- **Model versioning** — `ai-provenance.generated-by` is mandatory at RENAR-4+ ([§11.7.1](11-maturity-model.md#11.7.1)).
- **Continuous validation** — scheduled eval-runs ([§11.8.1](11-maturity-model.md#11.8.1) for RENAR-5).

**What RENAR adapts:**

- 5338 describes the full lifecycle of an AI system (derived from ISO/IEC 12207); RENAR borrows from it only the processes concerning requirements and the provenance of AI-generated artifacts — decision logs, data and model versioning, continuous validation — and expresses them through `ai-provenance` ([§4.10.1](04-terms.md#4.10.1)) and the RENAR-4 / RENAR-5 levels ([§11.7](11-maturity-model.md#11.7), [§11.8](11-maturity-model.md#11.8)).

**What RENAR does not adopt:** the operational layer of the AI-system lifecycle — model training, deployment, and operation — is outside the scope of RENAR ([§1.3](01-scope.md#1.3)); the standard normalizes only the requirements axis and the provenance of AI-generated artifacts (`ai-provenance`).

**Conformance claim:** RENAR claims conformance in the part covering the AI-artifact lifecycle and AI-assisted artifact generation.

**Negative scenario:** a conformance claim against 5338 without `ai-provenance` ([§4.10.1](04-terms.md#4.10.1)) — is invalid. RENAR MUST refuse to issue a manifest for such projects.

### 14.4.6 ISO/IEC 23894:2023 (AI risk management)

**What it normalizes:** AI risk classes and mitigation strategies.

**How RENAR relates:**

| Risk (23894) | Mitigation in RENAR |
|---|---|
| Hallucinations in AI output | Source citation ([§4.10.2](04-terms.md#4.10.2)), the Hallucination Rate metric ([§12.3.3](12-metrics.md#12.3.3)) |
| Model drift | pinning `last-run.set-version` ([§9.12](09-test-cases.md#9.12)) + periodic re-run |
| Prompt injection via input data | Sanitization on TZ import (substrate-specific, in guide/) |
| Bias in AI generation | Multi-model agreement for critical BR (RENAR-5, [§11.8.1](11-maturity-model.md#11.8.1)) |
| Adversarial inputs | Adversarial review ([§11.7.1](11-maturity-model.md#11.7.1), [§11.8.3](11-maturity-model.md#11.8.3)) |
| Single point of failure (one model) | Multi-model agreement; isolation of the judge model ([§9.13.4](09-test-cases.md#9.13.4)) |

**Conformance claim:** RENAR claims conformance in the part covering identification and mitigation of AI risks in the requirements domain; the full register — [reference/03](../../reference/en/03-ai-risk-register.md).

---

## 14.5 Informative references

Informative references are methodologies and terminology used for positioning; RENAR does **not** make a conformance claim against them.

### 14.5.1 The five key ones (start here)

| Source | Why for RENAR |
|---|---|
| **SAFe 6.0** | Mapping of the Epic/Feature/Story hierarchy → BR/SR/TR ([§4.13.1](04-terms.md#4.13.1)) |
| **Spec-Driven Development** | Source-of-Truth inversion ([§2.3.1](02-methodology-positioning.md#2.3.1)) as a formal paradigm |
| **EARS** (Mavin et al.) | Phrasing templates for SR and TC ([§6.6.3](06-requirements-hierarchy.md#6.6.3)) |
| **BDD / Gherkin / Specification by Example** | Prior art for a full-fledged TC ([§9](09-test-cases.md)) |
| **NIST AI RMF 1.0** | Functional mapping of Govern/Map/Measure/Manage |

Brief explanations — in [reference/11 §1–§2](../../reference/en/11-external-standards-mapping.md).

### 14.5.2 Extended catalog

| Source | Role for RENAR |
|---|---|
| IEEE 830-1998 (deprecated) | Historical reference; the normative successor — [§14.4.2](#14.4.2) |
| BABOK v3 | BA terminology; the elicitation gap — in guide/ |
| PMBOK 7 | Principles instead of processes ([§2.5](02-methodology-positioning.md#2.5)) |
| ISTQB Foundation | Testing vocabulary, compatible with `tc-type` |
| CMMI v2.0 | Prior art for maturity levels ([§11](11-maturity-model.md)) |
| ISO/IEC 42001:2023 | Organizational governance over RENAR |
| ISO/IEC 25059:2023 | AI-system quality (an extension of 25010) |
| EU AI Act (Reg. 2024/1689) | The `ai-act.risk-class` field; legal conformance — outside RENAR |
| SysML / MBSE | Prior art for "requirements as a graph" ([reference/05](../../reference/en/05-knowledge-graph-schema.md)) |

Detailed mappings — [reference/11](../../reference/en/11-external-standards-mapping.md).

---

## 14.6 Conformance summary

### 14.6.1 Summary table

| Standard | Type | Level | RENAR chapters |
|---|---|---|---|
| SENAR | Parent | Specialization | All |
| ISO/IEC/IEEE 29148:2018 | Normative | High | [06](06-requirements-hierarchy.md), [09](09-test-cases.md) |
| ISO/IEC 25010:2023 | Normative | Medium | [06](06-requirements-hierarchy.md), [08](08-specifications.md) |
| ISO/IEC 25022/25023 | Normative | Medium | [09](09-test-cases.md) |
| ISO/IEC 5338:2023 | Normative | High | [04 §4.10](04-terms.md#4.10), [11](11-maturity-model.md) |
| ISO/IEC 23894:2023 | Normative | Medium | [11](11-maturity-model.md), [reference/03](../../reference/en/03-ai-risk-register.md) |
| The rest (§14.5) | Informative | — | see [reference/11](../../reference/en/11-external-standards-mapping.md) |

### 14.6.2 Aggregate claims

A manifest ([§13.4.2](13-conformance.md#13.4.2)) MAY contain **several** conformance claims against the references in [§14.4](#14.4) at the same time. Each is verified independently. A partial claim is not provided for: a project is either conformant through RENAR, or it is not.

**Mandatory clauses and external standards.** The mandatory clauses of [§13.3](13-conformance.md#13.3) are RENAR's internal requirements. The claims of [§14.4](#14.4) are external, optional above the minimum (for example, RENAR-1 without a claim against 5338, if there is no AI generation of artifacts).

---

## 14.7 What RENAR fundamentally does not adopt

A closed list of practices from related standards:

| Practice | Source | Why it is not adopted |
|---|---|---|
| Heavy document review meetings, IEEE 1028 inspections | RUP, SWEBOK | Incompatible with AI-agent speed; replaced by adversarial AI review ([§11.7.1](11-maturity-model.md#11.7.1)) |
| Manual verification **only** (29148 inspection meetings) — substitutive; a complementary manual walkthrough record ([§9.20](09-test-cases.md#9.20)) is permitted and replaces no TC | ISO/IEC/IEEE 29148 §6.4 | Substrate hooks ([§10.11](10-lifecycle-qg.md#10.11)) + AI review |
| Process-first CMMI (CCB, OSP) | CMMI v2.0 | Principles + automated enforcement ([§2.5](02-methodology-positioning.md#2.5)) |
| Formal methods for all requirements (B, Z, TLA+) | Formal methods | Critical safety domains only, not the base level |
| Undated references to standards | Industry practice | Dated only ([§14.4.1](#14.4.1)) |
| Self-declared conformance without a manifest | Industry practice | A manifest is mandatory ([§13.4](13-conformance.md#13.4)) |

---

## 14.8 Relationship to other chapters

| Chapter | Relationship |
|---|---|
| [04 Terms](04-terms.md) | [§4.13](04-terms.md#4.13) — detailed mapping to related standards |
| [02 Methodology](02-methodology-positioning.md) | [§2.3.4](02-methodology-positioning.md#2.3.4) SDD; [§2.6](02-methodology-positioning.md#2.6) implications for mandatory clauses |
| [06 Hierarchy](06-requirements-hierarchy.md) | frontmatter — implementation of the 29148 attributes |
| [09 Test cases](09-test-cases.md) | TC — an extension of 29148; ISTQB-compatible terminology |
| [10 Lifecycle and QG](10-lifecycle-qg.md) | QG — concretization of SENAR QG-0..2 |
| [11 Maturity](11-maturity-model.md) | The RENAR-1..5 levels |
| [13 Conformance](13-conformance.md) | `renar-version`, `external-claims[]` |
| [reference/11](../../reference/en/11-external-standards-mapping.md) | Full informative catalog of §14.5 |
| [reference/03 AI risk](../../reference/en/03-ai-risk-register.md) | Risk register per 23894 + NIST |
