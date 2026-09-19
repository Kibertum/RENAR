---
title: "AI Risk Register"
description: "AI risk register for RENAR projects: 14 canonical risks with mitigations."
order: 3
lang: en
version: "1.0"
---

# AI Risk Register for RENAR Projects

> **Purpose:** a register of AI-specific risks for projects that use RENAR (where AI generates requirements, specifications, and tests). Based on ISO/IEC 23894:2023 (AI Risk Management, Annex A risk sources + Clause 6 process per ISO 31000) and NIST AI RMF 1.0. Normative mitigation hooks — [standard/09 §9.13](../../standard/en/09-test-cases.md#9.13), [standard/07 §7.10](../../standard/en/07-adapt.md#7.10).

This does not replace the organization's general security risk register. AI risks are a separate class because of the specifics of generation and the unpredictability of models.

---

## 1. Register structure

Each risk has:

```yaml
id: AIR-NN                           # immutable
name: "<short name>"
category: hallucination | injection | drift | bias | sgnl-failure | data-quality | adversarial | privacy
# enum value — the part before the parenthesis; the qualifier in parentheses is optional (e.g. "sgnl-failure (process)")
severity: critical | high | medium | low
likelihood: high | medium | low
iso-23894-ref: "§N.N"
nist-rmf-function: govern | map | measure | manage
mitigations:
  - { mechanism: "<description>", enforced-by: "<who/what>", automated: true | false }
status: active | mitigated | accepted | monitoring | out-of-scope
owner: "@role"
last-reviewed: "YYYY-MM-DD"
related: ["<core rule N>", "<standard chapter>", "<other AIR-NN>"]
```

The list AIR-01..AIR-14 is closed; new risks — only through a change to the full RENAR Standard.

**Category ↔ NIST AI RMF trustworthiness characteristics** (for verifiability of the claim "based on NIST AI RMF"):

| `category` | NIST AI RMF trustworthiness characteristic |
|---|---|
| `hallucination` / `data-quality` | Valid & Reliable |
| `injection` / `adversarial` | Secure & Resilient |
| `drift` | Valid & Reliable; Safe |
| `bias` | Fair — with Harmful Bias Managed |
| `sgnl-failure` | Safe; Accountable & Transparent |
| `privacy` | Privacy-Enhanced |

> **ISO/IEC 23894:2023 references.** The AI-risk categories in 23894:2023 are located in **Annex A** (risk sources); Clause 6 describes the risk-management process (per ISO 31000). The "Annex A — …" descriptors in the register are risk-source labels; the exact mapping to Annex A items is subject to reconciliation when a formal claim is made.

---

## 2. Register AIR-01..AIR-14

Metadata for all 14 risks (Sev=Severity, Like=Likelihood):

| AIR | Name | Category | Sev | Like | ISO 23894 (Annex A) | NIST RMF | Status |
|---|---|---|---|---|---|---|---|
| 01 | Hallucination in AI-generated requirements | hallucination | **High** | High | Output reliability | Measure | active → mitigated at mature RENAR levels |
| 02 | Prompt injection via client TZ | injection | **High** | Low-Medium | Adversarial inputs | Manage | monitoring |
| 03 | Model drift / version change | drift | Medium | High | Model lifecycle | Manage | monitoring |
| 04 | Bias in AI requirements generation | bias | Medium | Medium | Fairness | Measure | active |
| 05 | Single-model failure (no diversity) | sgnl-failure | Medium | High | Single point of failure | Manage | mitigated with full pipeline |
| 06 | Test fitting / greening of tests | sgnl-failure | **High** | Medium | Verification integrity | Measure | mitigated with spot-check |
| 07 | Hallucinated citations | hallucination | Medium-High | Medium | Output reliability | Measure | monitoring |
| 08 | Adversarial inputs in clients data (runtime) | adversarial | **High** | Low | Adversarial inputs | Manage | out-of-scope (application-level) |
| 09 | Privacy leakage via AI logs | privacy | **High** | Medium | Privacy | Govern | active |
| 10 | Knowledge graph poisoning | data-quality | Medium | Low | Data integrity | Map | monitoring |
| 11 | Reconciliation false-positive overload | sgnl-failure (process) | Low-Medium | Medium | Verification integrity | Manage | monitoring |
| 12 | Cost runaway (uncontrolled AI spend) | sgnl-failure (operational) | Medium | Medium | Cost governance | Manage | active |
| 13 | Stakeholder does not understand AI-generated requirements | data-quality (UX) | Medium | Medium | Transparency | Govern | active |
| 14 | Vendor lock-in to specific LLM provider | sgnl-failure (operational) | Medium | Low-Medium | Vendor risk | Govern | monitoring |

Descriptions + impact + mitigations — below.

### AIR-01. Hallucination in AI-generated requirements

When generating BR/SR/SPEC, an AI agent may "make up" statements that are not in ADAPT or the TZ. Impact: scope creep, dispute at acceptance, features that the client did not require.
**Mitigations:** source citation ([standard/04 §4.10.2](../../standard/en/04-terms.md#4.10.2) — every statement references ADAPT §N, and when no ADAPT was created ([standard/07 §7.4.1](../../standard/en/07-adapt.md#7.4.1)) — directly a TZ section via `source.tz-section` + `source.adversarial-review-ref`); adversarial review (a critic model from a different vendor); Hallucination Rate metric ≤1% at mature RENAR levels.

### AIR-02. Prompt injection via client TZ

A malicious client may embed hidden instructions for the AI into the TZ ("ignore previous instructions and …"). Impact: data leakage, malicious changes to requirements, violation of the security policy.
**Mitigations:** sandboxing of the AI agent on import (the model treats the TZ as passive data, stated explicitly in the system prompt); the input gateway checks for known injection patterns; a suspicious pattern → escalation, not auto-process.

### AIR-03. Model drift / version change

Anthropic / OpenAI / Google update their models — the same prompt with the same TZ may give a different output six months later. Impact: inconsistency between requirements within a single project; inability to reproduce exactly the generation of an old artifact.
**Mitigations:** model versioning in `ai-provenance.generated-by` (exact version + date); eval tests for SPEC-AI are run when the model changes; on regeneration — a diff against the old version and an assessment of the changes.

### AIR-04. Bias in AI requirements generation

The model has training-data bias — when generating BR it may ignore stakeholders from particular groups (accessibility users, non-English locales, specific regulatory regimes). Impact: requirements do not cover the full spectrum of users; the product is discriminatory or non-compliant.
**Mitigations:** multi-model agreement for `priority=must` BR (different models — different biases); a stakeholder map is mandatory in BR (explicit enumeration); an adversarial critic with the prompt "check for missing stakeholders / accessibility considerations".

### AIR-05. Single-model failure (no diversity)

If all artifacts are generated by a single model, its errors propagate systematically. It "hallucinates" a particular pattern — all requirements inherit it. Impact: systematic distortion of requirements across the project.
**Mitigations:** multi-model for `priority=must` BR; an adversarial critic with a different model; isolation of the judge model from the production model in eval tests (SPEC-AI: `judge-model.vendor ≠ production-model.vendor`, see [02-schemas.md §6.2](02-schemas.md#62-spec-ui-spec-ai-spec-sec-spec-ops)).

### AIR-06. Test fitting / greening of tests

An AI agent has a trivial path to greening a failing test — weaken the Pass criterion. This passes code review because "the test is green." Impact: false confidence; defects pass into production.
**Mitigations:** the `[test-spec-change]` marker is mandatory for changing Pass/Fail (a separate approval); spot-check of 5 passing TC once per iteration ([standard/09 §9.14](../../standard/en/09-test-cases.md#9.14)); the Test-spec Drift Rate metric ([standard/12 §12.3](../../standard/en/12-metrics.md#12.3)).

### AIR-07. Hallucinated citations

An AI agent writes the citation `[TZ-2026-001 §4 line 142]`, but in the real TZ §4 line 142 is about something else. The citation looks like evidence, but the evidence is false. Impact: source citation becomes a fiction; the trace chain breaks under audit.
**Mitigations:** a citation validator hook (parses the citation, opens the referenced document, verifies conformance); pre-commit/pre-approval block on an invalid citation.

### AIR-08. Adversarial inputs in clients data (runtime)

The client sends data (via forms, API) deliberately constructed to manipulate an AI component at runtime (not at the requirements-generation stage). Impact: similar to AIR-02, but in production runtime.
**Mitigations:** input sanitization at the API gateway; constrained generation (structured outputs only); rate limiting per user. **Status: out-of-scope** — application-level security; RENAR requires SR-level coverage (SPEC-SEC threat model), but runtime protection is a task of implementation, not of normalizing requirements.

### AIR-09. Privacy leakage via AI logs

When generating an artifact, an AI agent has PII in its context (from the TZ or an interview). Generation logs (tool events, audit records) may store this PII. Impact: PII ends up in logs, in `ai-provenance`, in training data (if used).
**Mitigations:** PII redaction in prompts before sending to the LLM; `data-classification` tracking; disable training on conversations (Anthropic/OpenAI privacy settings, DPA); TTL on event logs with PII.

### AIR-10. Knowledge graph poisoning

If the KG is used as primary search for AI agents, an incorrect edge can "poison" all subsequent AI queries that rely on that graph. Impact: the AI generates requirements based on wrong context, systematically.
**Mitigations:** the KG is derived from frontmatter, not edited directly (see [05-knowledge-graph-schema.md](05-knowledge-graph-schema.md)); CI validation of the graph on every change (no circular dependencies, no orphan approved); a reconciliation agent verifies integrity weekly.

### AIR-11. Reconciliation false-positive overload

With overly sensitive rules, a reconciliation agent generates many false-positive findings. The Architect starts ignoring them → real findings drown. Impact: reconciliation loses value, the discipline does not scale.
**Mitigations:** tunable thresholds in the project configuration; a Reconciliation Findings/Week metric (if it grows without real issues — re-calibration); the Architect may reject findings with a rationale — feedback for tuning the agent's prompt.

### AIR-12. Cost runaway (uncontrolled AI spend)

Without budget tracking, AI generation (especially with multi-model, adversarial, eval) may consume tokens disproportionately to project size. Impact: financial losses; the practice becomes unprofitable.
**Mitigations:** an `ai-budget` field in frontmatter (target + actual); an aggregated cost metric at project level; a cap per project; an alarm when approached; a recommendation engine "Sonnet/Haiku for routine work, Opus only for `priority=must` BR".

### AIR-13. Stakeholder does not understand AI-generated requirements

The AI generates SR in a technical style; the client / a non-technical stakeholder does not understand it during review → approval becomes a formality. Impact: QG-ADAPT-approve / QG-4 acceptance loses meaning; the dispute rate at acceptance grows.
**Mitigations:** the style guide ([04-ai-style-guide.md](04-ai-style-guide.md)); BR in business language (technologies — in SPEC-*, not in BR); a human-readable summary in every BR/SR — a short section, understandable without a technical background.

### AIR-14. Vendor lock-in to specific LLM provider

All prompts are optimized for a specific provider (Anthropic Claude). If the provider changes pricing/availability — migration requires rewriting all prompts. Impact: operational risk, costs, business continuity.
**Mitigations:** provider-agnostic prompts where possible (avoid vendor-specific tool syntax); multi-model agreement for `priority=must` is normative at RENAR-5 ([standard/11 §11.8.1](../../standard/en/11-maturity-model.md#11.8.1)) — guarantees a second provider in the pipeline; periodic test runs on a backup provider.

---

## 3. Risk matrix

```text
Severity / Likelihood
        │  Low     Medium                     High
────────┼───────────────────────────────────────────────
High    │  AIR-08  AIR-02, 06, 07, 09         AIR-01
Medium  │  AIR-10  AIR-04, 11, 12, 13, 14     AIR-03, 05
Low     │  —       —                          —
```

Range values from §2 (`Medium-High`, `Low-Medium`) are placed at their upper bound. Critical and High risks in the top-right quadrant are the mitigation priority.

---

## 4. Mitigation matrix

Which mitigations cover which risks (compensating mechanisms: a single mitigation is rarely sufficient; high risks require ≥ 2 independent mechanisms):

| Mitigation | Covers risks |
|---|---|
| Source citation ([standard/04 §4.10.2](../../standard/en/04-terms.md#4.10.2)) | AIR-01, AIR-07 |
| Adversarial review (a different model) | AIR-01, AIR-04, AIR-05 |
| Spot-check of passing TC ([standard/09 §9.14](../../standard/en/09-test-cases.md#9.14)) | AIR-06 |
| Multi-model for `priority=must` | AIR-04, AIR-05, AIR-14 |
| Judge isolation in SPEC-AI | AIR-05 |
| AI provenance (model + version + date) | AIR-03, AIR-09 |
| Citation validator hook | AIR-07 |
| Input sandbox / sanitization | AIR-02, AIR-08 |
| PII redaction + DPA | AIR-09 |
| KG validation in CI | AIR-10 |
| Reconciliation tunable thresholds | AIR-11 |
| `ai-budget` field + project cap | AIR-12 |
| Style guide + business-language BR | AIR-13 |

---

## 5. Operational governance

**Review cadence.** Monthly: AIR-01, AIR-02, AIR-06, AIR-07, AIR-09 (high-impact runtime risks). Quarterly: the rest. On-incident: any risk in which an incident occurred → root cause → mitigation.

**Owner.** Default — the project Architect. For AI-specific risks — the AI Governance Lead (if one exists in the organization).

**Storage.** The project's risk register is a separate artifact:

```text
<project>.req/
  governance/
    ai-risk-register.md           # snapshot of this reference + project-specific notes
    review-log.md                 # review history with dates and signatures
```

On a substrate that does not support directories — an equivalent namespacing.

**Reconciliation agent.** A weekly run updates the `status` and `last-reviewed` fields of each AIR. If a status changes (e.g., monitoring → active) — an alert to the Architect.

---

## 6. Relationship to the standard

| AIR | RENAR Core / Standard |
|---|---|
| AIR-01, 07 | Standard ch.4 §4.10.2 (source citation) + ch.7 §7.4.1 (reactive ADAPT: `source.adapt` or `source.tz-section` + `source.adversarial-review-ref`) |
| AIR-04, 13 | Standard ch.5 (roles) + style guide [§04](04-ai-style-guide.md) |
| AIR-05 | Standard ch.9 §9.6.2 (judge ≠ production isolation) + SPEC-AI |
| AIR-06 | Standard ch.9 §9.7 (pos/neg pairing) + §9.13 (protection from test fitting) + §9.14 (spot-check) + §9.18 (test authorship isolation and red history) + QG-2 |
| AIR-09 | Standard ch.4 §4.10.1 (`ai-provenance`) + SPEC-SEC |
| AIR-12 | Standard ch.12 §12.3.9 (Cost per Approved Requirement) + ch.11 §11.8.1 (cost/latency budget) |

---

## 7. What the risk register does NOT cover

This register focuses on AI-specific risks of the RENAR process. **It does not replace:** the organization's general security risk register (ISO 27001); the compliance risk register ([06-compliance.md](../../guide/en/06-compliance.md)); the application-level threat model of a specific project (SPEC-SEC). Regulator-mandated requirements (AI Act high-risk, FZ-152) — separate artifacts; this register is the operational tier.

---

*AI Risk Register RENAR 1.1 — renar.tech*
