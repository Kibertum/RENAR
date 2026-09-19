---
title: "Профиль реализации для агента"
description: "Абстрактный контракт исполнения RENAR для implementer-ов. Машиночитаемый индекс: normative-index.yaml."
order: 10
lang: ru
version: "1.0"
---

# Профиль реализации для агента

> **Назначение:** informative contract для агента или нативного для носителя runtime, который **имплементирует** RENAR без догадок. Нормативный текст — `standard/`; этот документ — operational mapping без привязки к конкретному vendor tooling.  
> **Машиночитаемо:** [`normative-index.yaml`](normative-index.yaml)

---

## 1. Как читать таблицу

| Колонка | Смысл |
|---|---|
| `clause_id` | Стабильный ID из `normative-index.yaml` |
| `Действие агента (абстрактно)` | Что должен уметь runtime **без** vendor CLI |
| `Входы / выходы` | Артефакты носителя |
| `Gate` | Контрольная точка качества или проверка под управлением runner |

---

## 2. MVR ↔ действия агента

| clause_id | Действие агента (абстрактно) | Входы | Выходы | Gate |
|---|---|---|---|---|
| MVR-1 | Блокировать reverse-engineering SR/SPEC из кода без bug-fix justification; источник истины = иерархия требований | code diff, SR/SPEC | audit record / blocked promotion | — |
| MVR-2 | Проверить, что носитель декларирует V1–V6 в манифест; прогнать capability checks | `RENAR-CONFORMANCE.yaml` | pass/fail report | — |
| MVR-3 | Реактивный стадийно-независимый ADAPT: создавать ADAPT (0..N на ТЗ) только при findings состязательного обзора; нет findings → source.tz-section + adversarial-review-ref; delta-ТЗ → тот же реактивный паттерн; дезавуирование через superseded | TZ, состязательный вердикт, ADAPT draft | ADAPT approved / вердикт-свидетельство | QG-ADAPT-approve |
| MVR-4 | Отклонять SPEC с type ∉ закрытого списка | SPEC frontmatter | validation error | QG-0 |
| MVR-5 | Обеспечить pos/neg пару TC на каждое нормативное утверждение | SR/SPEC, TC set | paired TC | QG-2 |
| MVR-6 | Отклонять custom gate types; требовать QG-0..2 | project config | манифест | — |
| MVR-7 | Требовать подписанный `RENAR-CONFORMANCE.yaml` с `senar-version` | манифест | claim соответствия | — |

---

<a id="3-mandatory-143-bijection"></a>

## 3. Взаимное соответствие MVR ↔ обязательные положения §13.3

Полное взаимное соответствие MVR ↔ §13.3 — [`08-conformance-self-assessment.md §1`](08-conformance-self-assessment.md#1-взаимное-соответствие-mvr--обязательные-пункты-133). Агент **не должен** считать проект соответствующим, если любое поле `mandatory-clauses-confirmed` = false.

---

## 4. Контракт gate runner (абстрактно)

| Gate | Pre (проверки агента) | Post (записи агента) |
|---|---|---|
| QG-0 | Комплект: schema valid по каждому артефакту; parent links в той же версии; источник разрешён: `source.adapt` на ADAPT в `approved` **либо** `source.tz-section` + `source.adversarial-review-ref` на AR в статусе `issued` ([§7.4.1](../standard/07-adapt.md#7.4.1)); пара TC-норм на каждое утверждение (`coverage-presence`) | запись версии `N.M` + audit-trail |
| QG-1 | Только TC ([§10.3.2](../standard/10-lifecycle-qg.md#10.3.2)): `automation.status: automated` (валидный `automation.location`) либо `manual-pending`; реализация привязана к версии комплекта (`automation.set-version`, V5); pos/neg парность; обязательные секции тела TC ([§9.4](../standard/09-test-cases.md#9.4)) | TC `draft` → `ready` + запись в лог переходов |
| QG-2 | Все TC-нормы версии комплекта с допущенной реализацией `pass` при `last-run.set-version = N.M`; обе TC каждой пары; красная история | запись верификации в записи версии ([§10.5.4](../standard/10-lifecycle-qg.md#10.5.4)) |

Decision trees: [standard/09 §9.1.1](../standard/09-test-cases.md), [standard/10 §10.1.1](../standard/10-lifecycle-qg.md).

---

## 5. Правило нейтральности к носителю для implementer-ов

Runtime **должен** map-ить abstract actions на нативные для носителя primitives через `RENAR-CONFORMANCE.yaml#substrate-capabilities` ([§3.7](../standard/03-substrate-versioning.md#3.7)). Vendor-specific команды **не** могут быть единственным способом выполнить обязательное положение.

---

## 6. Статус покрытия (v1.0)

| Область | Index entries | Profile rows | Примечание |
|---|---|---|---|
| MVR | 7/7 | 7/7 | complete |
| §13.3 обязательные | 8/8 | MVR-1..MVR-6; §13.3.7 / §13.3.8 — вне MVR | биекции нет: MVR-7 отображается на §13.4 |
| QG-0..2 | 3/3 | 3/3 | QG-3/4 deferred optional |
| Обязательные положения по главам | partial | — | expand in later pass |
