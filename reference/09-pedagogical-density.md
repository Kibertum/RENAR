---
title: "Педагогическая плотность глав"
description: "Нормативные оценки плотности standard/00–14; signposts для hotspots (WC-02)."
order: 9
lang: ru
version: "1.0"
---

# Педагогическая плотность (WC-02)

> **Информативно.** Оценка плотности нормативного текста для калибровки читательского маршрута. Не изменяет обязательные положения.

**Метод (2026-05-22):** `density score = (RFC-2119 RU markers + closed-list refs + state machine tables) / 1000 lines`, ручная калибровка + подсчёт строк. Шкала: **L** (low ≤3), **M** (3–6), **H** (6–9), **VH** (≥9).

## По главам (оценки плотности)

| Глава | Lines | Score | Tier | Signpost |
|---|---:|---:|---|---|
| 00 Introduction | 122 | 4.2 | M | [standard/README](../standard/README.md) → начните с core |
| 01 Scope | 222 | 5.8 | M | closed lists §1.7 — см. glossary |
| 02 Methodology | 218 | 5.0 | M | Источник истины — [core «Как работает RENAR»](../core/renar-core.md) сначала |
| 03 носитель V1–V6 | 245 | 5.2 | M | → [guide/03](../guide/03-tool-guide-git.md) или [guide/04](../guide/04-document-store-substrate.md) |
| 04 Terms | 425 | 7.2 | H | → [reference/01](01-glossary.md) для примеров |
| 05 Roles | 243 | 4.5 | M | → [guide/01 walkthrough](../guide/01-walkthrough.md) |
| 06 Hierarchy | 679 | 8.4 | H | → [guide/00 quickstart](../guide/00-quickstart.md) перед frontmatter |
| 07 ADAPT | 633 | 6.8 | H | → [core ADAPT section](../core/renar-core.md) |
| 08 Specifications | 490 | 7.9 | H | → [guide/09 E3 SPEC examples](../guide/09-worked-examples.md) |
| 09 Test cases | 733 | 9.1 | VH | → [reference/02 §8](02-schemas.md#8-tc--test-case) + [§9.1.1 decision tree](../standard/09-test-cases.md#9.1.1) |
| 10 Жизненный цикл QG | 704 | 9.5 | VH | → [§10.1.1 QG tree](../standard/10-lifecycle-qg.md#10.1.1) + [guide/00](../guide/00-quickstart.md) |
| 11 Maturity | 304 | 4.8 | M | → [guide/02 transition](../guide/02-transition-guide.md) |
| 12 Metrics | 217 | 3.9 | M | provisional targets §12.4 — калибровка по условию достаточности данных, не по версии |
| 13 Соответствие | 399 | 8.0 | H | → [reference/08](08-conformance-self-assessment.md) kit first |
| 14 Normative refs | ~220 | 2.8 | M | → [reference/11](11-external-standards-mapping.md) расширенный каталог; §14.5 «пять ключевых» |

## Топ-5 «горячих точек» (signposted)

1. **§10 Жизненный цикл** — см. quickstart QG flow  
2. **§9 TC** — см. schemas §8 + состязательный §guide/07  
3. **§6 Hierarchy** — см. quickstart + walkthrough  
4. **§13 Соответствие** — см. reference/08 kit  
5. **§8 SPEC** — см. guide/09 E3  

---

*Reference RENAR 1.1 — renar.tech*
