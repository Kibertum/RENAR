---
title: "RENAR Guide"
order: 1
lang: en
---
# RENAR Guide

## Why this guide

The **standard** states "what MUST be." The **guide** shows "how to do it by hand" — on your project, with your team, without halting development.

Here you will find: a 30-minute quickstart, a full end-to-end example from TZ to release, a step-by-step migration from Jira/Notion to RENAR, substrate guides (git and document store), compliance checklists, and the common pitfalls of adoption.

> Normative wording and closed lists live only in [standard/](../../standard/en/README.md). If the debate is "are we obligated to" — see the standard; if the debate is "where do I start on Monday" — you are in the right place.

---

## Table of contents

| # | Document | Link | What you get |
|---|---|---|---|
| 00 | Quickstart | [00-quickstart.md](00-quickstart.md) | In 30 minutes: TZ → ADAPT → BR/SR → SPEC → TC |
| 01 | Walkthrough | [01-walkthrough.md](01-walkthrough.md) | The full cycle "Login Flow for AcmeCorp" — from ADAPT to an accepted release |
| 02 | Transition to RENAR | [02-transition-guide.md](02-transition-guide.md) | Step by step from RENAR-1 to RENAR-5, no big-bang |
| 03 | Substrate: git | [03-tool-guide-git.md](03-tool-guide-git.md) | V1–V6 on git: the `.req` repo, review, pinning |
| 04 | Substrate: document store | [04-document-store-substrate.md](04-document-store-substrate.md) | An overview of V1–V6 on a document-oriented store (no vendor names) |
| 05 | Comparison with SAFe | [05-safe-comparison.md](05-safe-comparison.md) | Where RENAR complements PI Planning, WSJF, ART |
| 06 | Compliance | [06-compliance.md](06-compliance.md) | Checklists for ISO 27001, GDPR, FZ-152, the AI Act, NIST AI RMF |
| 07 | Failure modes | [07-failure-modes.md](07-failure-modes.md) | 8 drift classes and the common adoption pitfalls |
| 08 | Developer guide | [08-developer-guide.md](08-developer-guide.md) | Onboarding: `.req` / `.src`, the git process, common scenarios |
| 09 | Worked examples | [09-worked-examples.md](09-worked-examples.md) | E1–E6: login, GDPR export, webhook, RAG eval, delta-TZ |
| 10 | Migration to v1.0 | [10-migration-v1.md](10-migration-v1.md) | Deprecated types → canonical; bump the manifest |
| 11 | Working with the AI agent | [11-ai-agent-guide.md](11-ai-agent-guide.md) | Loading the standard into the agent, commands, output, approval points |
| 12 | Migration to v1.1 | [12-migration-v11.md](12-migration-v11.md) | The map of the v1.1 wave changes (ADR-015…026), what an implementation does for each, re-assessment of the claim per §13.7.3 |

---

## Routes by role

| Role | Start | Then | Why RENAR |
|---|---|---|---|
| **Developer** | [Core](../../core/en/renar-core.md) → [00](00-quickstart.md) | [08](08-developer-guide.md) → [03](03-tool-guide-git.md) | TR, TC, hooks on the substrate |
| **Architect / Tech Lead** | [00](00-quickstart.md) → [01](01-walkthrough.md) | [standard/02](../../standard/en/02-methodology-positioning.md) → [10](10-migration-v1.md) | SoT, ADAPT, schema migration |
| **PM / RTE** | [Core](../../core/en/renar-core.md) | [05](05-safe-comparison.md) → [09 §E3](09-worked-examples.md#2-e3--personal-data-export-gdpr-art-15--fz-152) | BR, priorities, SAFe mapping |
| **Legal / Compliance** | [09 §E3](09-worked-examples.md#2-e3--personal-data-export-gdpr-art-15--fz-152) | [06](06-compliance.md) → [reference/07](../../reference/en/07-iso29148-trace-matrix.md) | Traceability, GDPR/FZ-152 evidence |
| **Regulator / Auditor** | [reference/07](../../reference/en/07-iso29148-trace-matrix.md) | [reference/08](../../reference/en/08-conformance-self-assessment.md) → [standard/13](../../standard/en/13-conformance.md) | ISO 29148 mapping, manifest |
| **Assessor (third-party)** | [standard/13 §13.3](../../standard/en/13-conformance.md#13.3) | [reference/08](../../reference/en/08-conformance-self-assessment.md) | Mandatory-clause checklist |

---

## Reading order

**Newcomer:** 00 → 01 → 02 — and you can work at RENAR-1.

**Tech Lead:** 02 → 03 or 04 (by substrate) → 07 — before enabling hooks.

**PM / RTE:** 05 → 06 — when you need the link to PI and compliance.

[← To the root README](https://github.com/Kibertum/RENAR/blob/main/README.ru.md)
