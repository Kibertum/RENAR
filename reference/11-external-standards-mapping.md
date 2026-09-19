---
title: "Сопоставление с внешними стандартами"
description: "Informative. Расширенный каталог информативных ссылок из standard/14 §14.5."
order: 11
lang: ru
version: "1.0"
---

# Сопоставление с внешними стандартами

> **Информативно.** Детализация [standard/14 §14.5](../standard/14-normative-refs.md#14.5). Не изменяет обязательные положения.

## 1. SAFe 6.0

Framework масштабированного Agile. RENAR заимствует маппинг иерархии ([§4.13.1](../standard/04-terms.md#4.13.1)): Portfolio Epic → BR, Feature → SR, Story → TR. Встроенное качество — TC до `approved`. Приоритизация: нормативное поле `priority` использует шкалу MoSCoW; WSJF назван как информативная альтернатива в общей схеме frontmatter ([reference/02 §1](02-schemas.md)) и в матрице ISO 29148 ([reference/07](07-iso29148-trace-matrix.md)), в нормативных главах не встречается.

## 2. Spec-Driven Development

Индустриальный термин 2024–2025: при AI-ускорении критична корректность спецификации. RENAR формализует инверсию источника истины ([§2.3.1](../standard/02-methodology-positioning.md#2.3.1)) — нормативная структура, не привязанная к одному vendor-tool.

## 3. EARS (Mavin et al., 2009)

Prior art для формы утверждения требования: пять шаблонов контролируемого естественного языка, где предусловие и триггер выражаются ключевым словом.

**В v1.0 шаблоны не приняты.** [§6.6.3](../standard/06-requirements-hierarchy.md#6.6.3) требует от раздела «Требование» одного предложения нормативной формы («Система должна …») и формы предусловия не задаёт; ближайший к EARS образец — прото-шаблон `Система должна [поведение]. [Условие]` в [reference/04 §2.3](04-ai-style-guide.md) — то есть в приложении, а не в норме. Pass/Fail-критерии TC нормированы [§9.4](../standard/09-test-cases.md#9.4) и к EARS не восходят.

## 4. BDD / Gherkin / Specification by Example

Prior art для полноценного TC ([§9](../standard/09-test-cases.md)): исполняемые примеры служат спецификацией. Отличие RENAR сформулировано в [§9.1](../standard/09-test-cases.md#9.1) — pos/neg-парность ([§9.7](../standard/09-test-cases.md#9.7)), изоляция судьи от реализации ([§9.13](../standard/09-test-cases.md#9.13)) и привязка TC к версии требования (V5) переведены из рекомендации в блокирующие нормативные положения. Разбиение тела TC своё ([§9.4](../standard/09-test-cases.md#9.4): Контекст, Предусловия, Шаги, Pass, Fail, Постусловия, Out of scope), форма Given / When / Then в корпусе не применяется.

## 5. NIST AI RMF 1.0

Govern / Map / Measure / Manage — функциональное сопоставление с ролями RENAR ([§5](../standard/05-roles.md)), метриками ([§12](../standard/12-metrics.md)), жизненный цикл deprecate.

## 6. IEEE 830-1998 (deprecated)

Отозван в пользу ISO/IEC/IEEE 29148. Нормативный правопреемник — [§14.4.2](../standard/14-normative-refs.md#14.4.2).

## 7. BABOK v3

Gap: elicitation вне scope (ТЗ уже зафиксировано); Solution Evaluation — частично QG-4 и [§12.5](../standard/12-metrics.md#12.5).

## 8. PMBOK 7

«Принципы вместо процессов» — RENAR нормирует **что**, не **как** ([§2.5](../standard/02-methodology-positioning.md#2.5)).

## 9. ISTQB Foundation

Словарь тестирования; терминологически совместим с RENAR, но соответствия значение-в-значение нет: типы TC — собственный закрытый список `tc-type` ([§9.5](../standard/09-test-cases.md#9.5)): `business` / `ux` / `system` / `contract` / `eval` / `security`; уровни тестирования выражаются отдельным полем `level` ([§9.3](../standard/09-test-cases.md#9.3)): `system` / `subsystem` / `module`.

## 10. CMMI v2.0

Prior art для уровней RENAR-1..5 ([§11](../standard/11-maturity-model.md)); process-heavy артефакты CMMI не базовый уровень.

## 11. ISO/IEC 42001:2023 (AIMS)

Организационный governance; RENAR даёт доказательную базу для requirements-среза (ai-provenance, манифест).

## 12. ISO/IEC 25059:2023

Расширение SQuaRE на качество AI-систем. Перечень значений нормативного поля `quality-characteristic` берётся из ISO/IEC 25010:2023 ([§6.6.2](../standard/06-requirements-hierarchy.md#6.6.2), [§14.4.3](../standard/14-normative-refs.md#14.4.3)); 25059 в корпусе — информативная ссылка ([§14.5](../standard/14-normative-refs.md#14.5)) и источником значений не является.

## 13. EU AI Act (Reg. 2024/1689)

Поле `ai-act.risk-class` в BR; юридическое соответствие — вне RENAR.

## 14. SysML / MBSE

Prior art «требования как граф» — [reference/05](05-knowledge-graph-schema.md); RENAR выводит граф из текстовых артефактов.

---

*Reference RENAR 1.1 — renar.tech*
