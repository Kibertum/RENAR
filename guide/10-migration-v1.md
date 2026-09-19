---
title: "Миграция на v1.0"
description: "Переход с pre-RENAR / v0.1-draft практик на RENAR v1.0: deprecated типы, поля frontmatter, manifest."
order: 10
lang: ru
version: "1.0"
---

# 10. Миграция на v1.0

> **Историческое руководство v0 → v1.0.** Поля `requirement-version`, статусы артефактов и переходы `approved → verified` ниже описывают v1.0; в v1.1 они заменены версией комплекта описания (`set-version`, [standard/10 §10.5](../standard/10-lifecycle-qg.md#10.5)) — руководство по миграции v1.0 → v1.1 выходит вместе с v1.1.
>
> Для команд, которые уже вели требования «по-своему» или на ранних черновиках RENAR. Цель — **без big-bang**: сохранить неизменяемые ID, заменить deprecated конструкции, выпустить новый conformance manifest. Нормативная база — [standard/04 §4.14](../standard/04-terms.md#4.14), [CHANGELOG §Migration](https://github.com/Kibertum/RENAR/blob/main/CHANGELOG.md).

**Не путать с** [02-transition-guide](02-transition-guide.md) — там поэтапный вход в RENAR-1..5 с нуля; здесь — **breaking rename** и выравнивание схемы при переходе на текущую редакцию стандарта.

---

## 1. Когда нужна эта миграция

| Ситуация | Действие |
|---|---|
| Проект без RENAR, но есть ТЗ + Jira | Сначала [02-transition-guide](02-transition-guide.md), не этот документ |
| Файлы с `INT-SR`, `INT-TC`, `AIC`, `UIC`, `TS` | Это руководство |
| `verifies[].version` без `requirement-version` | Обновить TC frontmatter ([reference/02 §8](../reference/02-schemas.md#8-tc--test-case)) |
| Manifest `renar-version: 0.1-draft` или отсутствует | Выпустить новый manifest ([reference/08](../reference/08-conformance-self-assessment.md)) |

---

## 2. Таблица замен типов (closed list v1.0)

| Deprecated | Canonical | Шаг миграции |
|---|---|---|
| `INT-SR` | `SR` + `constrained-by: [SPEC-INT-N]` | Переименовать type; создать/привязать SPEC-INT |
| `INT-TC` | `TC` + `tc-type: contract` | Добавить `type: TC`, `tc-type: contract` |
| `AIC` | `SPEC-AI` | Перенести body в SPEC-AI frontmatter |
| `UIC` | `SPEC-UI` | Перенести baselines в `specs/ui/baselines/` |
| `TS` | `SPEC-ARCH` или `SPEC-OPS` | По содержанию (архитектура vs ops runbook) |
| `TM` (module SR type) | `SR` + `level: module` | Убрать pseudo-type, задать level |
| `tc-type: acceptance` | `tc-type: business` | Переименовать значение. Приёмок теперь две: `business`-TC меряет бизнес-цель BR (внутренний контур), а соответствие контракту проверяет `AT` ([standard/09 §9.19](../standard/09-test-cases.md#9.19)) — одно имя на два разных предмета убрано ([§9.5](../standard/09-test-cases.md#9.5)) |
| `approval.client-signature` на ADAPT | Подпись только архитектора | Убрать клиентскую подпись из ADAPT; решения клиента перенести в подписанные `ACTZ` и проставить `decided-in` на находках ([standard/07 §7.5](../standard/07-adapt.md#7.5), [§7.13](../standard/07-adapt.md#7.13)) |
| Состояние ADAPT `client-ready` | `asked` | Изъято из машины состояний: вынесение вопросов клиенту — предмет ACTZ, а не состояние ADAPT ([standard/10 §10.8](../standard/10-lifecycle-qg.md#10.8)) |

**ID не менять.** Если filename содержит legacy prefix — допустимо поле `legacy-id` во frontmatter (informative traceability).

---

## 3. Пошаговый план (1–2 спринта)

### Фаза A — инвентаризация (1–2 дня)

1. `grep` / search по носителю: `INT-SR`, `INT-TC`, `AIC`, `UIC`, `type: system` (ошибочный TC type).
2. Список артефактов с broken `verifies` / missing `source.adapt`.
3. Зафиксировать текущий `RENAR-CONFORMANCE.yaml` (если есть) как опорное состояние.

### Фаза B — проход по схеме (3–5 дней)

1. Batch-rename типов по таблице §2 (один PR на тип или один atomic change-set на delta-ТЗ).
2. TC: `type: TC`, `tc-type`, `verifies[].requirement-version`, `last-run.requirement-version`.
3. SR: `constrained-by[]` на все используемые SPEC.
4. BR: источник адаптации — `source.adapt` на approved ADAPT либо `source.adversarial-review-ref` на issued AR при вердикте «no findings»; `source.tz-section` обязателен всегда ([standard/07 §7.4.1](../standard/07-adapt.md#7.4.1)).

### Фаза C — валидация (1–2 дня)

1. Hooks носителя / CI: frontmatter validator ([reference/02](../reference/02-schemas.md)).
2. Прогнать TC; обновить `last-run`.
3. Чек-лист самооценки — [reference/08 §2](../reference/08-conformance-self-assessment.md#2-чек-лист-самооценки-обязательные-положения).

### Фаза D — manifest (1 день)

1. Bump `renar-version: "1.0"`.
2. Increment `manifest-version`; новый `manifest-id`.
3. Подпись Architect / Tech Lead (V6).

---

## 4. Частые ловушки

| Ошибка | Последствие | Исправление |
|---|---|---|
| Переименовать `SR-05` → `SR-05-v2` | Нарушение V1 immutable ID | `deprecated` + новый ID с `replaces` |
| Оставить `type: system` на TC | Validator fail; KG drift | `type: TC` + `tc-type: system` |
| Мигрировать код раньше `.req` | SoT inversion нарушена | Сначала SR/SPEC/TC approved, потом TR |
| Skip состязательного обзора «только для новых ТЗ» | Non-conformant для legacy TZ | Ретроспективный состязательный обзор каждого активного ТЗ; ADAPT — только при вердикте «findings present» ([standard/07 §7.4.1](../standard/07-adapt.md#7.4.1)) |

---

## 5. Откат

Миграция идёт через историю носителя (V1). Откат — revert change-set / PR, **не** delete артефактов. Deprecated артефакты остаются с `status: deprecated` для audit.

---

## 6. Связанные документы

| Документ | Зачем |
|---|---|
| [02-transition-guide](02-transition-guide.md) | RENAR-1..5 без schema breaking changes |
| [09-worked-examples](09-worked-examples.md) | Эталонные frontmatter после миграции |
| [reference/07](../reference/07-iso29148-trace-matrix.md) | Внешнее заявление ISO 29148 |
| [standard/13 §13.7](../standard/13-conformance.md#13.7) | Re-assessment после миграции |

---

*Guide RENAR 1.1 — renar.tech*
