---
title: "RENAR Standard v1.1"
order: 1
lang: en
---
# RENAR Standard v1.1

16 normative chapters. **This is the normative Source of Truth** — the precise "MUST / SHALL / SHOULD" lives here.

> **If you are reading for the first time** — do not start here. Start with [RENAR Core](../../core/en/renar-core.md) (≤ 10 min conceptual overview), then the [quickstart](../../guide/en/00-quickstart.md) (≈ 30 min end-to-end example). Come back to the standard when you need the precise wording.

## Architecture

```
   client TZ            your artifacts            evidence
       │                     │                          │
       ▼                     ▼                          ▼
  ┌─────────┐    ADAPT ──► BR / SR ──► SPEC ──► TC ──► QG ──► release
  │immutable│   (contract   │         │       │      │
  └─────────┘   on meaning) └──── TR ─┘       │      └── conformance manifest
```

The chapters are laid out in the order of the argument — read straight through **00 → 14**. The foundation (positioning in the typology, §2) and the substrate infrastructure (V1–V6, §3) are deliberately moved up front: the chapters on artifacts (06–10) rely on them.

## Table of contents

| # | Chapter | Link | About |
|---|---|---|---|
| 00 | Introduction | [00-introduction.md](00-introduction.md) | MVR (the minimum set of rules), relationship to SENAR, closed-list policy |
| 01 | Scope | [01-scope.md](01-scope.md) | Where RENAR is mandatory, where it is excessive, what is out of scope |
| 02 | Positioning in the methodology typology | [02-methodology-positioning.md](02-methodology-positioning.md) | Source-of-Truth inversion (requirements → code); "waterfall shape" ≠ classical waterfall |
| 03 | Substrate versioning | [03-substrate-versioning.md](03-substrate-versioning.md) | V1–V6 — what the artifact substrate MUST be capable of, independently of git / wiki / document store |
| 04 | Terms and definitions | [04-terms.md](04-terms.md) | Canonical term registry; one term — one meaning |
| 05 | Roles | [05-roles.md](05-roles.md) | Who is responsible for ADAPT, signatures, QG approval; the AI agent as a regular implementer |
| 06 | Requirements hierarchy | [06-requirements-hierarchy.md](06-requirements-hierarchy.md) | BR / SR / TR: from business need to implementation task |
| 07 | ADAPT | [07-adapt.md](07-adapt.md) | The reactive bridge between the TZ and the requirements; forward + backward findings; the Architect's signature; ACTZ and the effective TZ |
| 08 | Specifications | [08-specifications.md](08-specifications.md) | Twelve SPEC types (ARCH…OPS, TEST, DOC, UC) and the link graph with requirements |
| 09 | Test cases | [09-test-cases.md](09-test-cases.md) | TC as an artifact with its own lifecycle; pos/neg pairing; protection against "test fudging" |
| 10 | Lifecycle and Quality Gates | [10-lifecycle-qg.md](10-lifecycle-qg.md) | States, QG-0…QG-4, hooks |
| 11 | Maturity model | [11-maturity-model.md](11-maturity-model.md) | RENAR-1…5 maturity levels |
| 12 | Metrics | [12-metrics.md](12-metrics.md) | Measurability of the requirements process |
| 13 | Conformance | [13-conformance.md](13-conformance.md) | Manifest, mandatory clauses, self-assessment + independent assessment |
| 14 | Normative references | [14-normative-refs.md](14-normative-refs.md) | ISO 29148, NIST AI RMF, EU AI Act and other frameworks |
| 15 | Description language | [15-description-language.md](15-description-language.md) | The controlled dialect of the normative sections of artifacts: operators, glossary discipline, prohibited constructions, atomicity, forms |

## Role-based routes

| Role | Route |
|---|---|
| **Architect / Tech Lead** | chapter 2 → 3 → 10 → [transition to RENAR](../../guide/en/02-transition-guide.md) |
| **PM / RTE** | [Core](../../core/en/renar-core.md) → [comparison with SAFe](../../guide/en/05-safe-comparison.md) |
| **Legal / compliance / auditor** | [guide/06](../../guide/en/06-compliance.md) → [reference/07](../../reference/en/07-iso29148-trace-matrix.md) → [§13](13-conformance.md) |
| **Need a term with an example** | [glossary](../../reference/en/01-glossary.md) — chapter 4 is for the assessor, not for learning |

## Status

- **v1.3-draft (2026-06-05)** — ADAPT: stage-agnostic / multiplicity / supersession (ADR-007, GitLab #7): stage-agnostic trigger, multiplicity of ADAPTs per TZ, supersession (`superseded`).
- **v1.2-draft (2026-05-26)** — partner pushback iteration; reactive ADAPT (ADR-006) + Core pivot (ADR-005).
- **v1.0 (2026-07-17)** — corpus finalization: draft removed, appendices #28, thorough review. See [CHANGELOG.md](https://github.com/Kibertum/RENAR/blob/main/CHANGELOG.md).
- **v1.1 (2026-09-19)** — the partner-proposal wave (ADR-015…026, #30–#54): the description set, SPEC-UC, MW, the screen list, the component, first party, chapter 15 «Description language»; migration — [guide/12](../../guide/en/12-migration-v11.md).

Change history — [CHANGELOG.md](https://github.com/Kibertum/RENAR/blob/main/CHANGELOG.md).

[← To the root README](https://github.com/Kibertum/RENAR/blob/main/README.ru.md)
