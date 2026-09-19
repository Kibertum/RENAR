---
title: "Compliance"
description: "Mapping RENAR ↔ ISO 27001 / GDPR / FZ-152 / EU AI Act / NIST AI RMF / ISO/IEC 23894 / ISO/IEC 5338 / PCI-DSS, self-assessment checklists, and auto-generated audit artifacts."
order: 6
lang: en
version: "1.0"
---

# 06. Compliance

> RENAR is a requirements-engineering standard; it is not a compliance standard in its own right. But RENAR provides the **traceability infrastructure** that makes conformance to other standards (ISO 27001, GDPR, FZ-152, EU AI Act, and others) automatically verifiable. This chapter is a mapping of RENAR artifacts onto 8 key compliance frameworks, plus self-assessment checklists, plus a list of auto-generated artifacts for the auditor.
>
> **Prerequisites:** [RENAR Core](../../core/en/renar-core.md), [reference/02-schemas.md](../../reference/en/02-schemas.md), [reference/03-ai-risk-register.md](../../reference/en/03-ai-risk-register.md).

---

## 1. Compliance principles in RENAR

**1.1 Compliance frontmatter.** Every requirement with a regulatory rationale MUST carry a `compliance` field in its `frontmatter` — you cannot trace conformance through an external table that is not linked to the artifact. The field is repeatable (one requirement MAY close controls across several standards):

```yaml
compliance:
  - { standard: "ISO 27001:2022", control: "A.5.34", rationale: "Privacy and protection of PII" }
  - { standard: "GDPR", article: "Art.32", rationale: "Security of processing" }
  - { standard: "FZ-152", article: "Art.19", rationale: "Measures to ensure the security of personal data" }
```

**1.2 Data classification.** Every BR/SR that operates on data MUST declare a classification. When `contains-pii: true`, SR-encryption + SR-audit-log + SR-erasure automatically become mandatory.

```yaml
data-classification:
  contains-pii: true              # GDPR / FZ-152 trigger
  contains-financial: false       # PCI-DSS trigger
  contains-health: false          # HIPAA / FZ-152 special categories
  contains-children-data: false   # COPPA trigger
  retention-days: 1095
  data-residency: ["RU", "EU"]
```

**1.3 Traceability as the audit foundation.** The chain `BR → SR → SPEC → TC → implementation → CI run` is itself the audit trail. The auditor opens the coverage report and sees: which requirements close a control; which TCs verify them; `last-run.date`; `last-run.set-version` (the set version the run passed on). Audit time: 1–2 days instead of 2–3 weeks. The reconciliation hook (drift detection) guarantees that the evidence has not gone stale between audits.

---

## 2. ISO/IEC 27001:2022 — Information Security

| ISO 27001:2022 Annex A control | RENAR artifact |
|---|---|
| A.5.7 Threat intelligence | SPEC-SEC with a threat model; SR with `compliance: A.5.7` |
| A.5.8 Information security in project management | RENAR itself as process compliance |
| A.5.34 Privacy and protection of PII | SR with encryption + `data-classification.contains-pii: true` |
| A.6.3 Information security awareness | The onboarding process includes reading RENAR |
| A.8.1 User endpoint devices | SR with device requirements (if in scope) |
| A.8.5 Secure authentication | SR-AUTH-* + SPEC-SEC with an authentication flow |
| A.8.7 Protection against malware | SR + adversarial review (the AI critic) |
| A.8.10 Information deletion | SR with deletion logic + `retention-days` |
| A.8.16 Monitoring activities | SR with logging + SPEC-OPS audit-log |
| A.8.24 Use of cryptography | SR with an explicit crypto algorithm + ISO 25010 Security |
| A.8.25 Secure development life cycle | SENAR + RENAR as evidence |
| A.8.28 Secure coding | TC with security checks; SPEC-SEC with STRIDE coverage |
| A.12.1.2 Change management (legacy 27001:2013) | Delta-TZ + Impact Analysis ([§7.6](../../standard/en/07-adapt.md)) |

**Audit deliverable.** Auto-generates a conformance report: scope (BR/SR/TC counts with `compliance.iso27001`) + Coverage by Annex A (control ↔ mapped SR ↔ status). The normative mechanism is capability V4 reporting from versioned artifacts.

---

## 3. GDPR (Regulation EU 2016/679)

| GDPR Article | RENAR artifact |
|---|---|
| Art.5 Principles | BR explicitly states the lawful basis |
| Art.6 Lawfulness | BR with `gdpr.lawful-basis: consent / contract / legal-obligation / vital-interests / public-task / legitimate-interests` |
| Art.7 Conditions for consent | SR with a consent-management workflow |
| Art.13 Information to data subject | SPEC-UI with a privacy-notice screen |
| Art.15 Right of access | SR with export-user-data |
| Art.16 Right to rectification | SR with edit-user-profile |
| Art.17 Right to erasure | SR with delete-user-data + propagation to linked entities |
| Art.18 Right to restriction | SR with suspend-processing |
| Art.20 Right to data portability | SR with export in a machine-readable format |
| Art.25 Data protection by design and by default | `data-classification` is mandatory at the BR level |
| Art.30 Records of processing activities | Auto-generated from BRs with `contains-pii` |
| Art.32 Security of processing | SR with encryption + access control |
| Art.33 Notification of personal data breach | SR with a breach-notification workflow + a 72h timer |
| Art.35 DPIA | For high-risk — a separate `dpia/<feature>.md` |

**GDPR frontmatter:**

```yaml
gdpr:
  data-categories: ["identification: email, name", "contact: phone, address", "behavioral: usage logs"]
  lawful-basis: contract              # Art.6
  retention-period-days: 1095
  cross-border-transfer: false        # true → SCCs or an adequacy decision are mandatory
  dpia-required: false                # true → link to the DPIA
  data-subject-rights: [access, rectification, erasure, portability]   # Art.15/16/17/20
```

**DPIA.** For high-risk processing — `<system>.req/dpia/DPIA-NN-<slug>.md` with frontmatter (`id`, `type: DPIA`, `gdpr-article: 35`, `related-br[]`, `risks-identified`, `mitigations`) and sections: the processing operation, necessity, risks, mitigation measures, and sign-off with the DPO.

---

## 4. FZ-152 "On Personal Data" (RF)

| FZ-152 Article | RENAR artifact |
|---|---|
| Art.5 Processing principles | BR with a stated purpose (`business-context.business-goal`) |
| Art.6 Processing conditions | BR with a `lawful-basis` |
| Art.9 Consent to processing | SR with consent management |
| Art.13.1 Storage within the RF | `data-classification.data-residency: ["RU"]` for Russian clients |
| Art.14 Right of access to information | SR with export-user-data |
| Art.15 Right to rectification | SR with edit-user-profile |
| Art.16 Deletion | SR with delete-user-data |
| Art.18 Operator obligations | The audit trail via RENAR traceability |
| Art.18.1 Localization of RF citizens' personal data | `data-residency` is mandatory |
| Art.19 Protection measures | SR with encryption + access control + ISO 25010 Security |
| Art.21 Breach notification | SR with a breach-notification workflow |
| Art.22 Notification to Roskomnadzor | operational, outside RENAR scope |

**Data residency enforcement.** For projects with Russian clients, `data-residency` is mandatory. The substrate hook checks: when `data-residency: ["RU"]`, all upstream SRs (storage, backups, replication) MUST have evidence of storage within the RF; adding a foreign jurisdiction for an RU-resident BR → a block at QG-0.

---

## 5. EU AI Act (Regulation EU 2024/1689)

The EU AI Act classifies AI systems by risk level. RENAR requires that the class be stated explicitly:

```yaml
ai-act:
  risk-class: limited                 # prohibited | high | limited | minimal
  rationale: "Generative AI for document drafting; no autonomous decisions affecting users' legal rights"
  high-risk-domain: false             # true → a conformity assessment is required
  general-purpose-ai: true            # GPAI — Claude / GPT, etc.
```

**High-risk AI requirements (Art.9–15)** — for the `high` class (financial scoring, hiring, public services, medical diagnostics):

| AI Act requirement | RENAR artifact |
|---|---|
| Art.9 Risk management system | SPEC-AI + AI risk register ([reference/03](../../reference/en/03-ai-risk-register.md)) |
| Art.10 Data and data governance | `eval-datasets/` with provenance + spot-check |
| Art.11 Technical documentation | SPEC-AI + ISO/IEC 5338 conformance (§7) |
| Art.12 Record-keeping | tool_event audit + `ai-provenance` |
| Art.13 Transparency to users | SPEC-UI with an indication of AI processing |
| Art.14 Human oversight | One-click approval + spot-check at QG-0 / QG-2 |
| Art.15 Accuracy, robustness, cybersecurity | Eval-tests (`tc-type: eval`) + adversarial review |

**GPAI obligations (Art.51–55).** If a General-Purpose AI Model (Claude, GPT, Gemini) is used: `ai-provenance` in the frontmatter of any AI-generated artifact (model id, version, prompt hash); the model's technical document (if it is a GPAI with systemic risk) — an external document + a reference from SPEC-AI.

---

## 6. NIST AI RMF 1.0 (US jurisdiction)

| NIST AI RMF Function | RENAR mechanism |
|---|---|
| **Govern** | RENAR + SENAR roles + compliance frontmatter |
| **Map** | BR with `business-context`, `data-classification`, `ai-act.risk-class` |
| **Measure** | Eval-tests with metric thresholds + RENAR metrics (Hallucination Rate, Coverage Velocity) |
| **Manage** | Lifecycle with QGs + reconciliation hook + AI risk register |

**RMF-specific extensions** (optional for US projects; not in conflict with ISO/IEC 23894 §7):

```yaml
nist-ai-rmf:
  applicable: true
  govern: { role: "AI Governance Lead", policy-link: "..." }
  map: { use-case-category: "content-generation", affected-stakeholders: ["clients", "internal-team"] }
  measure: { metrics-tracked: ["accuracy", "fairness", "robustness"], baseline-run-id: "eval-2026-04-01" }
  manage: { risk-tolerance: "low", incident-response-plan: "<link>" }
```

---

## 7. ISO/IEC 23894:2023 — AI Risk Management

Guidance on AI risk management. It does not certify, but it provides a structured framework for managing the risks of AI systems. Compatible with NIST AI RMF and the EU AI Act.

| ISO/IEC 23894 clause | RENAR artifact |
|---|---|
| §6.4.1 Risk identification | AI risk register ([reference/03](../../reference/en/03-ai-risk-register.md)) |
| §6.4.2 Risk analysis | SPEC-AI with a risk-assessment section |
| §6.4.3 Risk evaluation | A threshold for each risk in `eval-tests` |
| §6.4.4 Risk treatment | SR-mitigations + post-mitigation evaluation |
| §6.4.5 Communication and consultation | RACI matrix ([05-safe-comparison §9](05-safe-comparison.md)) |
| §6.4.6 Monitoring and review | Reconciliation hook (drift detection) |

**Conformance criteria:** every AI-generated artifact carries `ai-provenance`; for each AI use case, SPEC-AI documents the identified risks (hallucination, bias, robustness), the mitigations (eval thresholds, adversarial review, human-in-the-loop), and the residual risks; the risk register is updated whenever SPEC-AI changes (reconciliation catches the drift).

---

## 8. ISO/IEC 5338:2023 — AI System Life Cycle Processes

An extension to ISO/IEC/IEEE 12207. A convenient framework for AI Act Art.11 technical documentation.

| ISO/IEC 5338 process | RENAR stage |
|---|---|
| Stakeholder needs and requirements definition | TZ + ACTZ (clarification protocols) + ADAPT + BR |
| Architecture definition | SPEC-ARCH + SPEC-AI |
| Design definition | SPEC-AI (model card, prompt design, RAG) |
| Implementation | TR + implementation |
| Verification | TC (`tc-type: eval` for AI) |
| Validation | AT — acceptance tests from the effective TZ ([§9.19](../../standard/en/09-test-cases.md#9.19)) + spot-check |
| Operation | Reconciliation hook (drift detection, [§4.11](../../standard/en/04-terms.md#4.11)) |
| Maintenance | Delta-TZ + ACTZ + ADAPT iteration |
| Disposal | Retirement workflow (outside Core RENAR scope) |

**Conformance evidence:** SPEC-AI for each AI use case with a full model card; eval-tests bound to SPEC-AI via `verifies[]`; `ai-provenance` recorded; drift detection active (V6).

---

## 9. PCI-DSS v4.0 — Payment Card Industry Data Security Standard

If the project handles payment-card data (CHD):

| PCI-DSS v4 requirement | RENAR artifact |
|---|---|
| Req.1 Network security controls | SPEC-OPS with network segmentation |
| Req.3 Protect stored CHD | `contains-financial: true` + SR with encryption-at-rest |
| Req.4 Encrypt CHD transmission | SR with TLS + SPEC-API requirements |
| Req.6 Develop secure systems | RENAR + SENAR as process compliance |
| Req.7 Restrict access by need to know | SR with RBAC + SPEC-SEC access-control matrix |
| Req.8 Authenticate access | SR-AUTH-* + MFA where mandatory |
| Req.10 Log and monitor access | SR with an audit trail + SPEC-OPS observability |
| Req.11 Test security regularly | TC `tc-type: security` + pen-test (outside RENAR scope) |
| Req.12 Information security policy | RENAR + SENAR as a documented policy |

**CHD scope minimization.** The substrate hook checks that new SRs with `contains-financial: true` explicitly justify the need to store CHD — without that justification, the requirement is halted at QG-0.

---

## 10. Self-assessment checklists

Short yes/no checklists for compliance teams. The full printable kit for a RENAR manifest — [reference/08](../../reference/en/08-conformance-self-assessment.md).

**ISO 27001:2022** — every BR with `contains-pii: true` has an SR closing A.5.34; the SoA is documented; every in-scope control has ≥1 verifying SR; the coverage report is green; an auditor goes from a control to a passing TC in under 5 minutes.

**GDPR** — all PII is in the Records of Processing (auto-generated); the lawful basis is stated per processing activity; data-subject rights Art.15–22 are verifiable through SR + TC; a DPIA is performed for high-risk; cross-border transfers are justified (SCCs/adequacy).

**FZ-152** — requirements with PII of RF citizens carry `data-residency: ["RU"]`; the substrate hook checks the upstream storage SRs; consent is implemented in an SR with TC evidence; the Art.19 protection measures are covered by an SR with a passing TC.

**EU AI Act** — every SPEC-AI carries `ai-act.risk-class`; for `high`: Art.9–15 evidence is in the substrate; human oversight at QG-0/QG-2 + spot-check; technical documentation is auto-generated; `ai-provenance` for GPAI components.

**NIST AI RMF** — all 4 functions (Govern/Map/Measure/Manage) have evidence; an AI Governance Lead is defined in roles; the eval baseline is recorded; an incident-response plan is linked to SPEC-AI.

**ISO/IEC 23894** — the AI risk register is filled out and linked to SPEC-AI; every risk has a mitigation in an SR; residual risks are accepted by the owner; V6 is active.

**ISO/IEC 5338** — SPEC-AI for each AI use case with a model card; eval-tests bound via `verifies[]`; `ai-provenance` recorded; V6 is active.

**PCI-DSS** — SRs with `contains-financial: true` have encryption (at-rest + in-transit); the CHD scope is minimized; RBAC is in SPEC-SEC; the audit-trail SR covers all CHD-touching flows; security TCs run regularly.

### 10.9 RENAR-conformance self-assessment (in an hour)

A project declares its **own** RENAR-conformance level via the `RENAR-CONFORMANCE.yaml` manifest ([standard/13 §13.4](../../standard/en/13-conformance.md#13.4)). Quick checklist (yes/no, single pass):

- The requirements substrate provides all of V1–V6 ([§11.4.1](../../standard/en/11-maturity-model.md#11.4.1) — Notion/Google Docs do not qualify).
- Each TZ has an issued AR (adversarial-review record); every ADAPT produced by a "findings present" verdict is in status approved with the **Architect's signature** ([§7.5](../../standard/en/07-adapt.md#7.5)). There may be no ADAPT at all (a "no findings" verdict) or several (findings at different stages) — both are regular cases.
- A mandatory client signature on the ADAPT is **not** declared: obligations to the client are carried by the ACTZ. Requiring a client signature on the ADAPT is permitted only as `declared-stricter`, never as base conformance ([§13.3.3](../../standard/en/13-conformance.md#13.3)).
- Every finding in status `resolved` carries a `decided-in` pointing at a clause of a **signed** ACTZ; no signed ACTZ is left unreflected in an ADAPT ([§7.4.5](../../standard/en/07-adapt.md#7.4.5), [§7.13](../../standard/en/07-adapt.md#7.13)).
- Acceptance runs against the **effective TZ** (the initial TZ + every signed ACTZ, [§7.14](../../standard/en/07-adapt.md#7.14)), not against the ADAPT.
- The ATs were derived by an isolated agent: a model different from the primary agent's, with no access to the ADAPT / BR / SR / SPEC / TC / code; each AT's `tz-version` matches the current revision of the effective TZ; every AT is `passing` before submission ([§9.19](../../standard/en/09-test-cases.md#9.19), [§10.4.3](../../standard/en/10-lifecycle-qg.md#10.4)).
- No TC counts as evidence without a red history; the exception is a TC of class `implementation-originated`, where a killed mutant serves as the compensation ([§9.18.2](../../standard/en/09-test-cases.md#9.18)).
- Requirements of class `implementation-originated` describe internal technical detail only: client-observable behavior is not legalized through this class ([§6.13.2](../../standard/en/06-requirements-hierarchy.md#6.13)).
- All 12 SPEC types are supported natively (a declaration of "type not used" is permitted, "not supported" is not).
- Every normative statement covered by a TC has a paired negative TC.
- QG-0 / QG-1 / QG-2 are declared `required`.
- The manifest contains `renar-version` + `senar-version` + `level` + confirmation of the mandatory clauses ([reference/08 §2](../../reference/en/08-conformance-self-assessment.md#2-self-assessment-checklist-mandatory-clauses)).
- `assessment-date` is recent, `next-assessment-due` is not overdue.

All 13 are `yes` → the project is conformant to the declared `level`. At least one `no` → the manifest is not issued ([§13.5.2](../../standard/en/13-conformance.md#13.5.2)).

Four items of the list — 3, 6, 7 and 8 — carry one point: **an obligation to the client cannot arise out of an engineering document the client never read, nor out of the code, nor out of a test written by the same agent and the same model as the implementation.** Their sources differ, and are worth naming: the client signature on an ADAPT is not required — [§13.3.3](../../standard/en/13-conformance.md#13.3) and [§7.5](../../standard/en/07-adapt.md#7.5); AT generator isolation — [§9.19.2](../../standard/en/09-test-cases.md#9.19) with the control point at [§10.11.1](../../standard/en/10-lifecycle-qg.md#10.11); red history — [§9.18.2](../../standard/en/09-test-cases.md#9.18); the bounds of the `implementation-originated` class — [§6.13.2](../../standard/en/06-requirements-hierarchy.md#6.13). Of the four, only the first belongs to the closed register of mandatory clauses in §13.3; the other three are declared mandatory in their own chapters.

A filled-in example (RENAR-2, self-assessment) — full schema in [§13.4.2](../../standard/en/13-conformance.md#13.4.2):

```yaml
renar-version: "1.1"
senar-version: "1.0"
manifest-version: 1
manifest-id: "CFM-2026-014"
level: "RENAR-2"
level-target: "RENAR-3"
assessment-mode: "self"
assessment-date: "2026-05-20"
assessor: { id: "architect-team-lead", role: "architect", signature-ref: "<pointer>" }
next-assessment-due: "2026-08-20"
mandatory-clauses-confirmed:
  sot-inversion: true
  substrate-v1-v6: { v1: true, v2: true, v3: true, v4: true, v5: true, v6: true }
  adapt-per-tz: true
  spec-types-closed-list: true
  tc-pos-neg-pairing: true
  quality-gates-closed-list: true
  closed-lists-backward-findings: true
quality-gates: { qg-0: required, qg-1: required, qg-2: required, qg-3: absent, qg-4: absent }
spec-types-supported: ["SPEC-ARCH","SPEC-API","SPEC-DATA","SPEC-INT","SPEC-PROC","SPEC-UI","SPEC-AI","SPEC-SEC","SPEC-OPS","SPEC-TEST","SPEC-DOC","SPEC-UC"]
exceptions: []
replaced-by: null
```

---

## 11. Auto-generated compliance artifacts

Artifacts generated from existing requirements for auditors:

| Artifact | Source | Contents |
|---|---|---|
| Records of Processing (GDPR Art.30) | BR/SR with `contains-pii: true` | Data categories, lawful basis, retention, recipients |
| Statement of Applicability (ISO 27001) | BR/SR with `compliance: ISO 27001:2022` | Annex A controls in/out of scope, justification |
| Data Inventory | All `data-classification` records | Categories, residency, retention, owners |
| AI System Cards | SPEC-AI | Model card in NIST AI RMF / AI Act format |
| Audit-trail report | Traceability BR → SR → SPEC → TC → last-run | The chain from control to evidence |
| DPIA Index | The `dpia/` folder | A list of DPIAs + their DPO sign-off status |
| AI Risk Register Snapshot | AI risk register | All identified risks + mitigations + residual |

Substrate-native reporting: aggregation of evidence from versioned artifacts (V4) into a compliance report on the assessor's request.

**Evidence pack — templates (informative; full E2E — [guide/09 §E3](09-worked-examples.md#2-e3--personal-data-export-gdpr-art-15--fz-152)).** GDPR Art.15 trace bundle — a `Control | BR/SR | SPEC | TC | last-run` table + lawful basis + retention + a link to the manifest. FZ-152 Art.14 — a mapping table `Requirement ↔ RENAR artifact ↔ Evidence field`. ISO 29148 trace excerpt — [reference/07](../../reference/en/07-iso29148-trace-matrix.md) (fill in "RENAR frontmatter" for each in-scope SR). Pre-audit self-assessment — [reference/08 §2](../../reference/en/08-conformance-self-assessment.md#2-self-assessment-checklist-mandatory-clauses).

---

## 12. What RENAR does NOT cover in compliance

RENAR is infrastructure for compliance, but not a **substitute** for: the DPO (Data Protection Officer — a human role with legal accountability); legal review of contracts and privacy notices; pen-testing the implementation; bug bounty / vulnerability management; physical security (data centers, the office); operational security (key rotation, incident response, the monitoring runbook); cyber insurance; regulatory filings (Roskomnadzor notifications, GDPR registration with a DPA, AI Act conformity assessment).

RENAR helps you do compliance **during requirements engineering**. Operational compliance consists of separate processes that *reference* RENAR artifacts as evidence.

---

## 13. Resolved decisions for v1.0

- **Compliance frontmatter — conditionally mandatory.** By default it is optional; it is mandatory when `contains-pii: true` (GDPR/FZ-152 trigger) or `contains-phi: true` (HIPAA trigger). An artifact with PII and no compliance frontmatter is non-conformant ([§13.3](../../standard/en/13-conformance.md#13.3) extensions via a manifest `declared-stricter`).
- **DPIA — mixed model.** The formal DPIA is an external document (legal owns it); the RENAR artifact `dpia/<slug>.md` holds a machine-readable summary + a pointer to the formal document. Separation of concerns while preserving traceability.
- **Data residency — outside RENAR scope.** Per [§1.3 (3)](../../standard/en/01-scope.md#1.3), tech stack / infra are out of scope. Enforcement is via DevOps-level controls (network policies, cloud regions). RENAR records the **requirement** (`data-classification.data-residency: ["EU"]`), not the enforcement.
- **Custom industry frameworks** (CB RF fintech, HIPAA healthcare, etc.) — separate documents (industry-specific addenda as `guide/06-compliance-<industry>.md` or external documents; they **MAY** be `declared-stricter` on top of RENAR conformance).

**Deferred to v1.1 (phase-8 backlog):** auto-export of evidence for Schrems II and EU ↔ RF transfers — partially addressed by the `cross-border-transfer` flags and links to SCCs, but full automation is unattainable (which SCCs apply is a lawyers' call). Owners: the legal-tech / adapters pairing.

---

## 14. Relationship to other chapters

- [00-quickstart](00-quickstart.md) — the basic RENAR cycle without the compliance layer.
- [05-safe-comparison](05-safe-comparison.md) — RACI with SAFe roles (including the DPO as Consulted).
- [reference/02-schemas](../../reference/en/02-schemas.md) — frontmatter schemas: `compliance`, `data-classification`, `gdpr`, `ai-act`.
- [reference/03-ai-risk-register](../../reference/en/03-ai-risk-register.md) — the AI risk register structure.
- [reference/04-ai-style-guide](../../reference/en/04-ai-style-guide.md) — the AI-provenance style.
- [standard/04-terms](../../standard/en/04-terms.md) — SPEC-SEC, SPEC-AI, SPEC-OPS terminology.
- [standard/13-conformance](../../standard/en/13-conformance.md) — RENAR conformance levels (≠ compliance: RENAR-N assesses the maturity of the *process*, compliance is conformance to *external norms*).
