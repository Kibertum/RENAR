---
title: "Mapping to External Standards"
description: "Informative. Extended catalog of informative references from standard/14 §14.5."
order: 11
lang: en
version: "1.0"
---

# Mapping to External Standards

> **Informative.** Elaboration of [standard/14 §14.5](../../standard/en/14-normative-refs.md#14.5). Does not alter the mandatory clauses.

## 1. SAFe 6.0

A scaled-Agile framework. RENAR borrows the hierarchy mapping ([§4.13.1](../../standard/en/04-terms.md#4.13.1)): Portfolio Epic → BR, Feature → SR, Story → TR. Built-in quality — TC before `approved`. Prioritization: the normative `priority` field uses the MoSCoW scale; WSJF appears only as an informative alternative in the common frontmatter schema ([reference/02 §1](02-schemas.md)) and in the ISO 29148 matrix ([reference/07](07-iso29148-trace-matrix.md)), and does not occur in the normative chapters.

## 2. Spec-Driven Development

An industry term from 2024–2025: under AI acceleration, the correctness of the specification becomes critical. RENAR formalizes the Source-of-Truth inversion ([§2.3.1](../../standard/en/02-methodology-positioning.md#2.3.1)) — a normative structure not tied to a single vendor tool.

## 3. EARS (Mavin et al., 2009)

Prior art for the form of a requirement statement: five controlled-natural-language templates in which the precondition and the trigger are carried by a keyword.

**The templates are not adopted in v1.0.** [§6.6.3](../../standard/en/06-requirements-hierarchy.md#6.6.3) asks the “Requirement” section for a single sentence in normative form (“The system shall …”) and prescribes no form for the precondition; the closest thing to EARS is the proto-template `The system shall [behaviour]. [Condition]` in [reference/04 §2.3](04-ai-style-guide.md) — that is, in an appendix rather than in the norm. Pass/Fail criteria for TC are normed by [§9.4](../../standard/en/09-test-cases.md#9.4) and do not descend from EARS.

## 4. BDD / Gherkin / Specification by Example

Prior art for a full-fledged TC ([§9](../../standard/en/09-test-cases.md)): executable examples double as a specification. How RENAR differs is stated in [§9.1](../../standard/en/09-test-cases.md#9.1) — pos/neg pairing ([§9.7](../../standard/en/09-test-cases.md#9.7)), judge ≠ production isolation ([§9.13](../../standard/en/09-test-cases.md#9.13)) and pinning a TC to the requirement version (V5) are moved from a recommendation to blocking normative clauses. The TC body has its own breakdown ([§9.4](../../standard/en/09-test-cases.md#9.4): Context, Preconditions, Steps, Pass, Fail, Postconditions, Out of scope); the Given / When / Then form is not used in the corpus.

## 5. NIST AI RMF 1.0

Govern / Map / Measure / Manage — a functional mapping to RENAR roles ([§5](../../standard/en/05-roles.md)), metrics ([§12](../../standard/en/12-metrics.md)), and the deprecate lifecycle.

## 6. IEEE 830-1998 (deprecated)

Withdrawn in favor of ISO/IEC/IEEE 29148. The normative successor is [§14.4.2](../../standard/en/14-normative-refs.md#14.4.2).

## 7. BABOK v3

Gap: elicitation is out of scope (the TZ is already fixed); Solution Evaluation — partially QG-4 and [§12.5](../../standard/en/12-metrics.md#12.5).

## 8. PMBOK 7

"Principles over processes" — RENAR standardizes **what**, not **how** ([§2.5](../../standard/en/02-methodology-positioning.md#2.5)).

## 9. ISTQB Foundation

A testing vocabulary; compatible with RENAR terminologically, but there is no value-for-value correspondence: TC types are RENAR's own closed list `tc-type` ([§9.5](../../standard/en/09-test-cases.md#9.5)): `business` / `ux` / `system` / `contract` / `eval` / `security`; test levels are expressed by the separate `level` field ([§9.3](../../standard/en/09-test-cases.md#9.3)): `system` / `subsystem` / `module`.

## 10. CMMI v2.0

Prior art for the RENAR-1..5 levels ([§11](../../standard/en/11-maturity-model.md)); CMMI's process-heavy artifacts are not the baseline level.

## 11. ISO/IEC 42001:2023 (AIMS)

Organizational governance; RENAR provides the evidence base for the requirements slice (ai-provenance, manifest).

## 12. ISO/IEC 25059:2023

An extension of SQuaRE to the quality of AI systems. The value set of the normative `quality-characteristic` field is taken from ISO/IEC 25010:2023 ([§6.6.2](../../standard/en/06-requirements-hierarchy.md#6.6.2), [§14.4.3](../../standard/en/14-normative-refs.md#14.4.3)); in the corpus 25059 is an informative reference ([§14.5](../../standard/en/14-normative-refs.md#14.5)) and is not the source of those values.

## 13. EU AI Act (Reg. 2024/1689)

The `ai-act.risk-class` field in BR; legal conformance is outside RENAR.

## 14. SysML / MBSE

Prior art for "requirements as a graph" — [reference/05](../05-knowledge-graph-schema.md); RENAR derives the graph from textual artifacts.

---

*Reference RENAR 1.1 — renar.tech*
