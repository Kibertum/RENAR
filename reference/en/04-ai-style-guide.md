---
title: "AI Style Guide"
description: "Style, tone, structure, length for AI agents generating RENAR artifacts."
order: 4
lang: en
version: "1.0"
---

# AI Style Guide — generating RENAR artifacts

> **Purpose:** style, tone, structure, length, and lexicon for AI agents generating RENAR artifacts (ADAPT, BR, SR, SPEC, TC, Impact Analysis). It eliminates a class of risks — "different models → different style → drift in perception" (see AIR-04, AIR-13 in [03-ai-risk-register.md](03-ai-risk-register.md)). It complements the [reference/06 EN Style Guide](06-en-style-guide.md) for human editors.

Context: the AI agent in RENAR is the **regular primary author** of artifacts ([standard/00 §0.2.1](../../standard/en/00-introduction.md#0.2.1)); the AI-vs-Architect distribution of roles — [standard/05](../../standard/en/05-roles.md) (§5.2.1, §5.3.2, §5.6 RACI). This document normalizes style for the regular mode, not for exceptions.

Industry analogues: Google Developer Documentation Style Guide, Microsoft Style Guide for technical writing, Diátaxis. The goal is a single, even tone regardless of the model or prompt engineer.

---

## 1. Principles

### 1.1 Style = contractual precision

A RENAR artifact is a **contractual document** between the client, the team, and the system. No metaphors, no "colorful" language, no associations. Only unambiguous statements.

**Good:**
> The system MUST accept a registration request via POST /auth/register.

**Bad:**
> Users will be able to easily and conveniently create accounts via a modern API.

### 1.2 One artifact = one idea

Each BR / SR / SPEC / TC covers **one** concept. If the AI agent is inclined to "pack" 3 topics into one SR, it MUST decompose rather than pack.

Packing signal: the conjunction "and" in the title, multiple actions in a single statement.

**Bad:**
> SR-05: Registration, authentication, and password recovery

**Good:**
> SR-05: Client registration
> SR-06: Client authentication
> SR-07: Password recovery

### 1.3 No claim to completeness

The AI MUST NOT "augment" a requirement on its own if the source (ADAPT or TZ) does not contain it. Every statement either has a citation or is marked `derived` with explicit justification.

**Bad:**
```markdown
- Email uniqueness is checked at registration.
- Phone uniqueness is checked at registration.   ← not in ADAPT, the AI made it up
```

**Good:**
```markdown
- Email uniqueness is checked at registration. [ADAPT-001 §14.1 Forward]
```

If the AI believes a phone check is needed, it does not write it into the SR; instead it adds it to the Critic output: "a phone uniqueness check may be needed — not in ADAPT, requires a backward finding to the Stakeholder."

### 1.4 Unambiguity over beauty

When choosing between a precise long sentence and a compact ambiguous one, the AI chooses the precise long one. Length is not the enemy, ambiguity is the enemy.

---

## 2. Structure and length by artifact type

### 2.1 ADAPT

**Body**: 200-800 lines (depends on the size of the TZ).

**Mandatory sections:**
- `## Summary` — 3-5 paragraphs for a one-page read by the Architect and the adversarial reviewer ([standard/07 §7.8.2](../../standard/en/07-adapt.md#7.8.2)).
- `## Term mapping` — a "client → engineer" table.
- `## Forward: interpretation by TZ section` — a section for each § of the TZ.
- `## Backward: discovered problems` — B-NNN entries with a lifecycle.
- `## Backward findings digest` — a table by category.
- `## Generated artifacts` (auto after approval).
- `## ADAPT change history` — substrate-native, auto-generated ([standard/07 §7.8.2](../../standard/en/07-adapt.md#7.8.2)).

**Tone**: engineering throughout. ADAPT is an internal artifact of interpretation; its audience is the engineer, the AI agent, the adversarial reviewer, and the verifier — the client never sees it ([standard/07 §7.5](../../standard/en/07-adapt.md#7.5)). Client-facing wording lives in ACTZ ([standard/07 §7.13](../../standard/en/07-adapt.md#7.13)), where the Architect escalates backward findings.

### 2.2 BR — Business Requirement

**Body**: 30-80 lines.

**Mandatory sections:**
- `## Need` — one sentence following the template `[Role] MUST [action] in order to [business goal].`
- `## Success criteria` — 3-7 measurable items.
- `## Context` — 5-15 lines.

**Prohibited:**
- Technologies ("via REST API", "Postgres", "React").
- Specific screens or DB fields.
- Author's musings ("we think", "perhaps", "it would be nice").

**Tone:** formal, imperative ("MUST", not "MAY").

### 2.3 SR — System Requirement

**Body**: 40-150 lines.

**Mandatory sections:**
- `## Requirement` — one sentence in one of the five controlled forms ([`standard/06 §6.6.3.1`](../../standard/en/06-requirements-hierarchy.md#6631-controlled-form-of-the-requirement-statement-the-condition-slot)): the ubiquitous "The system MUST ‹response›" or with a condition marker as the first word — WHILE, WHEN, IF … THEN, WHERE.
- `## Behavior` — 5-20 items with inline citations to ADAPT.
- `## Constraints` — 3-10 items; mandatory if applicable.
- `## Link to SPEC` — mandatory if `constrained-by[]` is present ([standard/06 §6.6.3](../../standard/en/06-requirements-hierarchy.md#6.6.3)).

**Optional sections** (where applicable):
- `## Not part of this requirement` — explicit out-of-scope.
- `## Related SR` — cross-links.

**Prohibited:**
- DB table names, specific functions, classes.
- Frameworks ("via FastAPI", "React component").
- Specific UI paths ("button in the top-right corner") — that belongs in SPEC-UI.

**Tone:** formal, precise, behavioral ("the system responds with 422", not "an error is returned").

### 2.4 SPEC-* (12 types)

**Body**: 80-400 lines (varies by type).

**Common mandatory sections** (see [02-schemas.md §5](02-schemas.md#5-spec--common-schema)):
- `## Purpose`
- `## Scope` (in / out)
- `## <Type-specific sections>`
- `## Link to requirements`
- `## Link to other SPEC`
- `## Verification`
- `## Open questions`

**Tone by type:**
- **SPEC-ARCH / API / DATA / INT / SEC / OPS:** technical-analytical. Specific technologies are allowed.
- **SPEC-UI:** user-centric narrative with behavioral precision. "The manager sees the order feed, taps an order, and a detail screen opens", not "the UI must have a listing component".
- **SPEC-AI:** model card style — capabilities, limits, failure modes, eval criteria.
- **SPEC-PROC:** procedural narrative with BPMN / state machine references.

### 2.5 TC — Test Case

**Body**: 30-80 lines.

**Mandatory sections:**
- `## Context` — which item of the verified artifact the TC references.
- `## Preconditions` (Given) — 2-5 lines.
- `## Steps` (When / action) — 1-3 lines.
- `## Pass criterion` (Then / Pass) — a binary, observable, reproducible criterion.
- `## Fail criterion` — a list of observable signs of violation, **including** possible side effects (security leaks, missing audit logs).
- `## Postconditions` — the expected state after the run.
- `## Out of scope` — what is deliberately not checked, with a pointer to the covering TC.

> The criterion section names (`## Pass criterion` / `## Fail criterion`) are canonical machine-detectable headings ([standard/09 §9.4](../../standard/en/09-test-cases.md#9.4)), detected by the enforcement hook ([standard/10 §10.11.3](../../standard/en/10-lifecycle-qg.md#10.11.3)); do not rename them.

**Tone:** executable, crisp ("X is performed", "Y is returned").

### 2.6 Impact Analysis

**Body**: 50-200 lines (depends on the size of the delta).

**Mandatory sections:**
- `## Affected requirements` — a table with the change type.
- `## Affected SPEC` — a table with the action.
- `## Affected TC` — a table with the action.
- `## Affected backlog tasks`.
- `## Open questions` — what needs to be clarified with the Stakeholder via a backward in the delta-ADAPT.

**Tone:** analytical, without judgments. Not "this is a bad change", but "this change affects X requirements and Y tasks".

---

## 3. Lexicon

### 3.1 Use (canonical)

- "The system MUST …" (modality).
- "MUST" / "MUST NOT" (RFC 2119 vocabulary).
- "returns" (HTTP/API behavior).
- "preconditions" / "postconditions" (formal testing terms).
- "requirement" / "statement" / "assertion".
- "acceptance criterion".
- "backward finding" / "forward interpretation" (RENAR canonical, from ADAPT).

### 3.2 Prohibited

> An informative list. The normative list of prohibited constructions — [standard/15 §15.4](../../standard/en/15-description-language.md#15.4); the closed list of operators — [§15.2](../../standard/en/15-description-language.md#15.2).

| Word/phrase | Replace with | Why |
|---|---|---|
| "We'd like it to" | "The system MUST" | Modality must be strict. |
| "Convenient" | a specific measurable criterion | Subjective. |
| "Modern" | specific tech requirements in SR/SPEC | Marketing language. |
| "High-quality" | measurable quality characteristics from ISO/IEC 25010 | Subjective. |
| "Fast" | p95/p99 latency with a number | Vague. |
| "Reliable" | uptime / reliability with a number | Vague. |
| "Maybe" / "Perhaps" | (never in requirements) | Hedging. |
| "Most likely" | (never) | Same. |
| "Preferably" | `priority: should` / `could` in the frontmatter | Mixing channels. |
| "Intuitive" | criteria for clarity (Flesch reading ease, etc.) | Subjective. |
| "Smooth experience" | UX criteria in SPEC-UI | Marketing. |
| "Seamless" | a specific technical criterion | Marketing. |
| "Support" (without explaining how) | a specific SR-level criterion | Vague. |
| "Integrate with X" (without a contract) | SPEC-INT with a counterparty | Vague. |

### 3.3 Use Cases vs Stories vs Requirements

RENAR uses **only the canonical terminology** from [01-glossary.md](01-glossary.md). The AI agent **is not permitted** to call a requirement a "User Story", "Use Case", "Scenario", or "Capability" — these are prohibited synonyms. The full list of prohibited terms and substitutions — in [01-glossary.md §4](01-glossary.md#4-forbidden--deprecated-terms).

---

## 4. Statement templates

> Illustrations. The normative forms — [standard/06 §6.6.3.1](../../standard/en/06-requirements-hierarchy.md#6.6.3.1) and [standard/15 §15.6](../../standard/en/15-description-language.md#15.6).

### 4.1 BR statement

Template: `[Role]` MUST `[action]` in order to `[business goal]`.

Example:
> The user MUST automatically receive and store notifications from selected applications in the background, in order not to miss important messages.

### 4.2 SR statement

Template: `<actor>` `<action>` `<condition>`. `<consequence>`.

Example:
> On first launch the system checks for the NotificationListenerService permission. If the permission is not granted, an onboarding screen with a "Grant access" button is displayed.

### 4.3 TC Pass criterion

Template: `<actor>` `<action>` `<input>`. `<observable>` `<measurement>`.

Example:
> POST /auth/login with body `{"email":"x@y.z", "password":"correct"}`. Response status = 200, body contains a JWT with `exp = now + 24h ± 1m`.

### 4.4 TC Fail criterion

A list of observable signs of violation, **including** possible side effects:

```text
- Response status ≠ 200 (authorization error).
- The 401 response names the specific field that is incorrect (information leak).
- A plaintext password is written to the system log (security violation).
- A login-notification email is NOT sent on successful authorization (missing side effect).
```

**Not allowed:** "Pass is not met". This is not a Fail criterion, it is a negation.

---

## 5. Citation conventions

### 5.1 Inline citation

Every statement in a BR/SR/SPEC has an inline citation in square brackets:

> On first launch the onboarding screen is displayed. [ADAPT-001 §14.1 Forward]

Format: `[<id> <section>]` or `[<id> <section> line <N>]` for a precise line.

### 5.2 Derived marker

If a statement is not a quote from ADAPT but logically inferred:

> The `Back` button is disabled on the onboarding screen. [ADAPT-001 §14.1 Forward, derived: ADAPT requires "onboarding cannot be skipped" → blocking the system `back`]

The `derived` explanation is mandatory.

### 5.3 Multi-source

If a statement is supported by several sources:

> The password is stored encrypted. [ADAPT-001 §4 NFR-002, ISO 27001 A.10.1.1]

### 5.4 What is NOT a citation

- A reference to the code itself ("see `auth/login.py`") — not a requirement source.
- A reference to a ticket in a bug tracker — not a requirement source (see [01-glossary.md §1 Authority chain](01-glossary.md#1-authority-chain)).
- A reference to a chat / verbal conversation without an entry in ADAPT backward → asked-to-client → answered.

---

## 6. System prompt for the AI generator

Every AI agent generating a RENAR artifact receives a system prompt composed of:

1. **Role:** "You are a requirements architect under the RENAR standard."
2. **Style guide reference:** a link to this document.
3. **Glossary reference:** a link to [01-glossary.md](01-glossary.md).
4. **Constraints:**
   - Every statement is either with a citation or `derived` with an explanation.
   - Use only canonical terms.
   - Structure and length — per §2 of this document.
   - No prohibited words from §3.2.
5. **Output schema:** the exact YAML frontmatter format (see [02-schemas.md](02-schemas.md)).
6. **Examples:** 2-3 good/bad examples for each artifact type.

Prompt templates are stored in the organization's `prompts/` directory with versioning:
- `prompts/adapt-from-tz.md@v2.1`
- `prompts/decompose-adapt.md@v2.1`
- `prompts/generate-tc-pos-neg.md@v1.0`
- `prompts/critic-review-sr.md@v1.2`

The artifact's `ai-provenance.prompt-template` frontmatter field holds the exact version.

---

## 7. Style for different models

Different LLMs have different tendencies. The style guide accounts for this through prompt constraints:

| Model | Tendency | Mitigation |
|---|---|---|
| Claude Opus | Verbose, prone to hedging ("maybe", "perhaps") | Explicit constraint in the prompt: "no hedging modal verbs". |
| Claude Sonnet | Concrete, may miss an edge case | The adversarial critic catches gaps. |
| GPT-4 / o-series | Marketing-language tendency | Explicit blacklist of words from §3.2. |
| Mini / Haiku | Hallucinated citations | Citation validator hook (see AIR-07). |
| Gemini Pro | Sometimes mixes RU/EN | Lang constraint in the prompt + post-validation. |

The style guide does not prohibit the use of any model, but it requires enforcement via post-generation validation.

---

## 8. Validation pipeline

After a RENAR artifact is generated, automatic validation runs:

```text
AI generates artifact
    ↓
Style validator (a separate AI with a different prompt, or rule-based)
    ├── citation check (each assertion has [...] or a derived marker)
    ├── lexicon check (no forbidden words from §3.2)
    ├── structure check (required sections present)
    ├── length check (within bounds from §2)
    ├── canonical terms check (no forbidden synonyms)
    ├── modal verb check (no hedging)
    ↓
On fail: regenerate with a refined prompt, or human review.
On pass: enter the draft → ready transition (then QG-1 / adversarial review / approval).
```

The validator hooks are native to the substrate. On git — pre-commit; on a document store — pre-save document validator; on any other — an equivalent gate.

---

## 9. Stylistic decisions for RU/EN

### 9.1 Body language

The body of an artifact is written in the project's language:

- Russian client → RU.
- International → EN.
- Bilingual → primary lang in the `lang:` frontmatter field; the second lang — a separate artifact with `replaces` / `replaced-by` links, or a translations subfolder.

### 9.2 Frontmatter is always canonical

Frontmatter fields are always canonical (English latin):

- `type: BR` (not `тип: БТ`).
- `status: approved` (not `статус: утверждено`).
- `priority: must` (not `приоритет: обязательно`).

The UI displays Russian translations (see [01-glossary.md §3.5](01-glossary.md#35-user-interface-projections)). Frontmatter is for machines, not for the UI.

### 9.3 Mixed-lang is prohibited

Within a single statement, mixing RU+EN is not allowed:

**Bad:**
> Система должна возвращать `JWT token` после successful login.

**Good (RU):**
> Система должна возвращать JWT-токен после успешной аутентификации.

**Good (EN):**
> The system must return a JWT token after successful authentication.

Technical terms (`JWT`, `OAuth`, `gRPC`, `RBAC`) are allowed in any language without translation.

---

## 10. Cross-references

- The closed list of canonical terms and prohibited synonyms — [01-glossary.md](01-glossary.md).
- Formal frontmatter schemas — [02-schemas.md](02-schemas.md).
- AI risks and mitigations — [03-ai-risk-register.md](03-ai-risk-register.md).
- Knowledge graph schema (used by the validator for cross-reference checks) — [05-knowledge-graph-schema.md](05-knowledge-graph-schema.md).

---

*AI Style Guide RENAR 1.1 — renar.tech*
