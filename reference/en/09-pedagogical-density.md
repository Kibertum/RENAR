---
title: "Pedagogical Density of Chapters"
description: "Normative density ratings for standard/00–14; signposts for hotspots (WC-02)."
order: 9
lang: en
version: "1.0"
---

# Pedagogical Density (WC-02)

> **Informative.** A density rating of the normative text for calibrating the reader's route. It does not change any mandatory clauses.

**Method (2026-05-22):** `density score = (RFC-2119 RU markers + closed-list refs + state machine tables) / 1000 lines`, manual calibration + line counting. Scale: **L** (low ≤3), **M** (3–6), **H** (6–9), **VH** (≥9).

## By Chapter (density ratings)

| Chapter | Lines | Score | Tier | Signpost |
|---|---:|---:|---|---|
| 00 Introduction | 122 | 4.2 | M | [standard/README](../../standard/en/README.md) → start with core |
| 01 Scope | 221 | 5.8 | M | closed lists §1.7 — see glossary |
| 02 Methodology | 218 | 5.0 | M | Source of Truth — [core "How RENAR Works"](../../core/en/renar-core.md) first |
| 03 substrate V1–V6 | 245 | 5.2 | M | → [guide/03](../../guide/en/03-tool-guide-git.md) or [guide/04](../../guide/en/04-document-store-substrate.md) |
| 04 Terms | 425 | 7.2 | H | → [reference/01](01-glossary.md) for examples |
| 05 Roles | 242 | 4.5 | M | → [guide/01 walkthrough](../../guide/en/01-walkthrough.md) |
| 06 Hierarchy | 678 | 8.4 | H | → [guide/00 quickstart](../../guide/en/00-quickstart.md) before frontmatter |
| 07 ADAPT | 632 | 6.8 | H | → [core ADAPT section](../../core/en/renar-core.md) |
| 08 Specifications | 489 | 7.9 | H | → [guide/09 E3 SPEC examples](../../guide/en/09-worked-examples.md) |
| 09 Test cases | 732 | 9.1 | VH | → [reference/02 §8](02-schemas.md#8-tc--test-case) + [§9.1.1 decision tree](../../standard/en/09-test-cases.md#9.1.1) |
| 10 Lifecycle QG | 703 | 9.5 | VH | → [§10.1.1 QG tree](../../standard/en/10-lifecycle-qg.md#10.1.1) + [guide/00](../../guide/en/00-quickstart.md) |
| 11 Maturity | 304 | 4.8 | M | → [guide/02 transition](../../guide/en/02-transition-guide.md) |
| 12 Metrics | 216 | 3.9 | M | provisional targets §12.4 — calibration bound to a data-sufficiency condition, not to a version |
| 13 Conformance | 399 | 8.0 | H | → [reference/08](08-conformance-self-assessment.md) kit first |
| 14 Normative refs | ~220 | 2.8 | M | → [reference/11](11-external-standards-mapping.md) extended catalogue; §14.5 "the five key ones" |

## Top 5 "Hotspots" (signposted)

1. **§10 Lifecycle** — see quickstart QG flow  
2. **§9 TC** — see schemas §8 + adversarial §guide/07  
3. **§6 Hierarchy** — see quickstart + walkthrough  
4. **§13 Conformance** — see reference/08 kit  
5. **§8 SPEC** — see guide/09 E3  

---

*Reference RENAR 1.1 — renar.tech*
