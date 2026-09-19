---
title: "ISO/IEC 29148 — Trace Matrix"
description: "Mapping of ISO/IEC/IEEE 29148:2018 attributes onto RENAR frontmatter fields (BR/SR/TR/SPEC/TC)."
order: 7
lang: en
version: "1.0"
---

# ISO/IEC 29148 — Trace Matrix

> **Purpose:** verifiable conformance of a conformance claim to ISO/IEC/IEEE 29148:2018 ([standard/14 §14.4.2](../../standard/en/14-normative-refs.md#14.4.2)). Normative field definitions are in [standard/06](../../standard/en/06-requirements-hierarchy.md), [standard/08](../../standard/en/08-specifications.md), [standard/09](../../standard/en/09-test-cases.md), [02-schemas.md](../02-schemas.md).

RENAR simplifies the set of mandatory 29148 attributes (18 → 7–8 per artifact) and adds TC as a first-class artifact, ADAPT, and the SPEC axis. The table below is the **complete** trace for a conformance assessor and for populating `external-claims[]` in the manifest.

---

## 1. Requirement classes (29148 §5)

| ISO/IEC 29148 class | RENAR artifact | Normative source |
|---|---|---|
| Stakeholder requirement | `BR` | [§6.5](../../standard/en/06-requirements-hierarchy.md#6.5) |
| System requirement | `SR` (level: system / subsystem / module) | [§6.6](../../standard/en/06-requirements-hierarchy.md#6.6) |
| Software requirement (implementation unit) | `TR` | [§6.7](../../standard/en/06-requirements-hierarchy.md#6.7) |
| Interface / design specification | `SPEC-*` (12 types) | [§8](../../standard/en/08-specifications.md) |
| Verification item | `TC` | [§9](../../standard/en/09-test-cases.md) |
| Requirements validation (agreement with the client) | `ACTZ` + `AT` | [§7.13](../../standard/en/07-adapt.md#7.13), [§9.19](../../standard/en/09-test-cases.md#9.19) |
| TZ interpretation (internal artifact; an extension of 29148) | `ADAPT` | [§7](../../standard/en/07-adapt.md) |

---

## 2. Requirement attributes (29148 Table B.1 → RENAR)

| # | ISO/IEC 29148 attribute | RENAR field / mechanism | Mandatoriness | Note |
|---|---|---|---|---|
| 1 | Unique ID | `id` (immutable) | mandatory | V1; see [§3.3.1](../../standard/en/03-substrate-versioning.md#3.3.1) |
| 2 | Requirement statement | body (Need / Behavior / …) | mandatory | The SR statement form is [§6.6.3](../../standard/en/06-requirements-hierarchy.md#6.6.3): a single sentence in normative form. EARS templates are not adopted in v1.0 — see [reference/11 §3](11-external-standards-mapping.md) |
| 3 | Rationale | body "Context" + `source.adapt-section` (when an ADAPT exists) or `source.adversarial-review-ref` | mandatory (BR/SR) | Traceability to ADAPT, or to the AR under a "no findings" verdict ([§7.4.1](../../standard/en/07-adapt.md#7.4.1)) |
| 4 | Source | `source.tz-section` (always); `source.adapt` **or** `source.adversarial-review-ref` (conditional, [§7.4.1](../../standard/en/07-adapt.md#7.4.1)); `source.document-ref` | mandatory | V5 pinning via `document-ref` |
| 5 | Fit criterion | body "Success criteria" (BR) / Pass criteria of TC | mandatory | Measurability via 25022/25023: [§14.4.4](../../standard/en/14-normative-refs.md#14.4.4) |
| 6 | Priority | `priority` (MoSCoW) | mandatory | WSJF — informative in the SAFe mapping |
| 7 | Owner | `owner` (BR/SR) — [§6.5.2](../../standard/en/06-requirements-hierarchy.md#6.5.2); `business-context.stakeholder` — a schema field of [reference/02 §1](02-schemas.md), absent from the normative chapters | mandatory (`owner`) | Responsibility hierarchy — [§5.3](../../standard/en/05-roles.md#5.3) |
| 8 | Status | `status` (lifecycle enum) | mandatory | State machines: [§10](../../standard/en/10-lifecycle-qg.md) |
| 9 | Verification method | `verified-by[]` → `TC` + `tc-type` | mandatory for verify | TC as a first-class artifact — an extension of 29148 |
| 10 | Parent / child | `parent.id`, auto `children[]` | mandatory (SR/TR) | Hierarchy BR→SR→TR |
| 11 | Traceability (derived) | `verified-by`, `constrained-by[]`, `implements-spec[]`, KG edges | derived | [reference/05 §3](05-knowledge-graph-schema.md#3) |
| 12 | Version | description set version `set-version` (standard/10 §10.5.4) + `automation.set-version` / `last-run.set-version` in TC | mandatory (V5) | [§3.3.5](../../standard/en/03-substrate-versioning.md#3.3.5) |
| 13 | Author | V6 author + `ai-provenance` | mandatory (RENAR-4+ AI) | [§4.10.1](../../standard/en/04-terms.md#4.10.1) |
| 14 | Date created / modified | substrate change-record timestamps | derived (V6) | Audit log: [§10.13](../../standard/en/10-lifecycle-qg.md#10.13) |
| 15 | Risk | `compliance[]`, AIR register link | optional / domain | [03-ai-risk-register.md](../03-ai-risk-register.md) |
| 16 | Assumption | ADAPT backward findings `type: hidden-assumption` | via ADAPT | [§7.4.4](../../standard/en/07-adapt.md#7.4.4) |
| 17 | Dependency | `depends-on[]` (SPEC), `constrained-by[]` (SR) | mandatory where applicable | DAG invariant: [02 §9](../02-schemas.md#9-validation-rules-cross-field) |
| 18 | Approval authority | QG-0 / QG-2 + ADAPT architect signature + ACTZ dual signature | mandatory | Replacement for the formal walkthrough: [§14.4.2](../../standard/en/14-normative-refs.md#14.4.2) |

**Not adopted from 29148:** review meetings and inspection-only verification without TC evidence — see [§14.7](../../standard/en/14-normative-refs.md#14.7).

---

## 3. Verification methods (29148 §6.4)

| 29148 method | RENAR implementation |
|---|---|
| Test | `TC` with `tc-type: system \| business \| contract \| …` |
| Demonstration | `AT` against the effective TZ ([§9.19](../../standard/en/09-test-cases.md#9.19)); `tc-type: business` + QG-4 (optional) |
| Inspection | `[test-spec-change]` workflow + adversarial review ([§9.13](../../standard/en/09-test-cases.md#9.13)) |
| Analysis | SR with `quality-characteristic` + eval-TC (`tc-type: eval`) for SPEC-AI |

---

## 4. Lifecycle processes (29148 §6)

| 29148 process | RENAR chapter | Gate |
|---|---|---|
| Requirements elicitation | ADAPT backward + TZ | QG-3 (ADAPT approve, [§10.4.1](../../standard/en/10-lifecycle-qg.md#10.4.1)) |
| Requirements analysis | BR/SR decomposition | QG-0 (BR/SR approve) |
| Requirements specification | SPEC axis | QG-3 Architecture (optional/required) |
| Requirements verification | TC + QG-2 | QG-2 Verification |
| Requirements validation | A signed ACTZ ([§7.13](../../standard/en/07-adapt.md#7.13)) + AT against the effective TZ | AT release gate ([§10.4.3](../../standard/en/10-lifecycle-qg.md#10.4.3)) — mandatory and not a Quality Gate; QG-4 (optional) measures the business outcome |
| Requirements management | lifecycle §10 + substrate V1–V6 | Continuous |

---

## 5. Use in conformance assessment

1. For each `ISO/IEC/IEEE 29148:2018` claim in the manifest — walk through the rows of §2–§5.
2. By spot-check (≥10% of artifacts, or all system-level BR/SR) verify the presence of mandatory fields and the `source.adapt` trace.
3. Non-conformance of any **mandatory** row of §2 — a partial claim is not permitted ([§14.6.2](../../standard/en/14-normative-refs.md#14.6.2)).

---

*Reference RENAR 1.1 — renar.tech*
