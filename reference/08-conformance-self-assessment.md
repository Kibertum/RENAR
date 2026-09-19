---
title: "Самооценка соответствия"
description: "Печатный чек-лист, взаимное соответствие MVR↔§13.3, шаблон RENAR-CONFORMANCE.yaml для самооценки."
order: 8
lang: ru
version: "1.0"
---

# Самооценка соответствия

> **Назначение:** практический kit для Tech Lead / Архитектор перед выпуском [RENAR-CONFORMANCE.yaml](../standard/13-conformance.md#13.4). Нормативная база — [standard/13](../standard/13-conformance.md). Этот документ **informative**; при расхождении побеждает standard/13.

---

<a id="1-mvr-mandatory-clauses-133-bijection"></a>

## 1. Взаимное соответствие MVR ↔ обязательные пункты §13.3

| MVR ([§0.5](../standard/00-introduction.md#0.5)) | Положение §13.3 | Поле `mandatory-clauses-confirmed` |
|---|---|---|
| MVR-1 инверсия источника истины | §13.3.1 | `sot-inversion: true` |
| MVR-2 V1–V6 | §13.3.2 | `substrate-v1-v6: { v1..v6: true }` |
| MVR-3 ADAPT per ТЗ | §13.3.3 | `adapt-per-tz: true` |
| MVR-4 12 типов SPEC | §13.3.4 | `spec-types-closed-list: true` |
| MVR-5 TC pos/neg | §13.3.5 | `tc-pos-neg-pairing: true` |
| MVR-6 QG — закрытый список | §13.3.6 | `quality-gates-closed-list: true` |
| MVR-7 манифест соответствия | §13.4 (артефакт) | манифест существует + подписан |
| — (политика закрытых списков) | §13.3.7 | `closed-lists-backward-findings: true` |
| — (межуровневая прослеживаемость подсистем) | §13.3.8 | `implements-edge-subsystem: true` |

Все семь MVR + §13.3.7 + §13.3.8 обязательны для **любого** уровня RENAR-1..5.

**О §13.3.8.** На v1.0 положение было рекомендованным; с v1.1 оно **обязательно** и подтверждается полем `implements-edge-subsystem` в манифесте. Действующая модальность фиксируется в [`reference/normative-index.yaml`](normative-index.yaml), запись `MC-13.3.8`; гейт `check-implements-edge` читает её оттуда и проверяет **наличие** ребра при сработавшем условии, а не только валидность существующего. Проекты, соответствовавшие v1.0 без ребра, приводят описание в порядок по руководству по миграции.

---

<a id="2-self-assessment-checklist-mandatory-clauses"></a>

## 2. Чек-лист самооценки (обязательные положения)

Отметьте после проверки доказательной базы в носителе:

### §13.3.1 Инверсия источника истины

- [ ] Иерархия BR/SR/SPEC/TC — авторитетный источник о поведении
- [ ] Нет SR, восстановленных из кода без обоснования исправления дефекта
- [ ] Drift-hooks / политика обзора блокируют молчаливую адаптацию SR←код

### §13.3.2 Возможности V1–V6 (носитель)

- [ ] V1 immutable history — включён
- [ ] V2 atomic change unit — включён
- [ ] V3 diff & review — включён
- [ ] V4 branching / change-set — включён
- [ ] V5 сквозная фиксация версии между носителями — включена (`automation.set-version`, `last-run.set-version`, `set-version` манифеста)
- [ ] V6 author + timestamp — включён

### §13.3.3 Реактивный ADAPT

- [ ] Каждое ТЗ прошло состязательный обзор, исход выпущен как AR в статусе `issued`
- [ ] При вердикте «findings present»: ADAPT в статусе `approved` с подписью Архитектора
- [ ] При вердикте «no findings»: ADAPT не создан; BR/SR/SPEC несут `source.tz-section` + `source.adversarial-review-ref`
- [ ] Каждая находка в `resolved` несёт `decided-in` на пункт подписанного ACTZ
- [ ] Подписанные ACTZ несут двустороннюю подпись (клиент + исполнитель)
- [ ] Подпись клиента на ADAPT не объявлена обязательной (допустима только как `declared-stricter`)
- [ ] Delta-ТЗ: delta-ADAPT создан по факту находок обзора delta-ТЗ, а не автоматически
- [ ] Дезавуированные ADAPT сохранены в терминальном `superseded`; висячих `source.adapt` нет

### §13.3.4 SPEC types

- [ ] Все SPEC ∈ {ARCH, API, DATA, INT, PROC, UI, AI, SEC, OPS, TEST, DOC, UC} — закрытый список из 12 типов
- [ ] Нет локальных `SPEC-CUSTOM-*`
- [ ] Тестовые стенды и данные описаны как `SPEC-TEST`, а не разделом `SPEC-OPS`
- [ ] Поставляемая документация описана как `SPEC-DOC`; внутренний runbook остался в `SPEC-OPS`
- [ ] На каждом `SPEC-TEST`, где хотя бы один датасет несёт реальные или персональные данные клиента, есть `client-signature`

### §13.3.5 TC pos/neg

- [ ] Каждое нормативное утверждение версии комплекта покрыто парой pos + neg TC-норм в той же версии (или исключение негативного инварианта); наличие покрытия, а не только парность, проверяется на QG-0 комплекта (`coverage-presence`, §10.11.1)
- [ ] QG-0 комплекта блокирует утверждение версии при непокрытом или непарном утверждении
- [ ] Каждый TC с `automation.kind: dynamic` несёт `environment-ref` на существующий `SPEC-TEST` (отсутствие — fatal; статические проверки стенда не требуют)
- [ ] Каждый `SPEC-DOC` покрыт док-линтом (TC с `automation.kind: static`); без него SPEC-DOC не проходит QG-2

### §13.3.6 Quality Gates

- [ ] QG-0, QG-1, QG-2 реализованы как `required`
- [ ] QG-3, QG-4 объявлены `required` | `declared` | `absent` в манифесте
- [ ] Нет локальных пользовательских гейтов

### §13.3.7 Закрытые списки

- [ ] Backward finding types — только закрытый список §7.4.4
- [ ] Типы декомпозиции SPEC — только закрытый список §8

### §13.3.8 Межуровневая прослеживаемость подсистем

Проверяется только при сработавшем условии: `BR.level = subsystem` И родительская система имеет ≥ 1 approved BR.

- [ ] При сработавшем условии `implements[]` непуст. Опустить его допустимо **только** когда родительская система — контейнер без собственных BR, и тогда обоснование фиксируется в разделе «Контекст» со ссылкой на ADAPT
- [ ] Точка контроля `implements`-edge validation реализована носителем — эта обязанность действует **уже на v1.0** и от модальности самого положения не зависит ([§13.3.8](../standard/13-conformance.md#13.3.8))

Невыполнение первого пункта — **несоответствие** ([§13.8.1](../standard/13-conformance.md#13.8.1)).

**Правило:** хотя бы один не отмечен → манифест **не выпускать** ([§13.5.1](../standard/13-conformance.md#13.5.1)).

---

## 3. Чек-лист уровня (выберите целевой RENAR-N)

Минимум для заявления уровня — [standard/11 §§11.4–11.8](../standard/11-maturity-model.md). Краткая сводка:

| Уровень | Ключевые доп. критерии |
|---|---|
| RENAR-1 | Только обязательные положения; frontmatter минимален |
| RENAR-2 | Базовый frontmatter (без строгой schema-валидации); ТЗ и delta-ТЗ — явные неизменяемые артефакты; статусы жизненного цикла ещё не обязательны |
| RENAR-3 | Полная схема frontmatter + статусы жизненного цикла; hooks обеспечивают QG-0 и QG-1, QG-2 — частично |
| RENAR-4 | ai-provenance обязателен; pos/neg парность на каждое нормативное утверждение; QG-2 обеспечивается нативно |
| RENAR-5 | Состязательный обзор как gate; согласие нескольких моделей для `priority: must`; граф знаний как первичный поиск; непрерывная оценка SPEC-AI |

- [ ] Выбранный `level` в манифесте **не выше** фактически пройденного чек-листа
- [ ] `declared-stricter` (если есть) документирован отдельно

---

## 4. Шаблон манифеста (минимальный)

Сохранить как `RENAR-CONFORMANCE.yaml` в корне носителя требований:

```yaml
manifest-version: 1
manifest-id: "CFM-YYYY-NNN"
renar-version: "1.1"
senar-version: "1.0"
level: RENAR-2
assessment-date: "2026-05-22"
assessment-mode: self          # self | third-party (§13.4.2)
next-assessment-due: "2026-08-22"

mandatory-clauses-confirmed:
  sot-inversion: true
  substrate-v1-v6: { v1: true, v2: true, v3: true, v4: true, v5: true, v6: true }
  adapt-per-tz: true
  spec-types-closed-list: true
  tc-pos-neg-pairing: true
  quality-gates-closed-list: true
  closed-lists-backward-findings: true

quality-gates:
  qg-0: required
  qg-1: required
  qg-2: required
  qg-3: declared
  qg-4: absent

external-claims:
  - standard: "ISO/IEC/IEEE 29148:2018"
    clause-ref: "§14.4.2"
    claim: "partial"           # full | partial | aligned

substrate-capabilities:
  v1-immutable-history: declared
  v2-atomic-change-unit: declared
  v3-diff-review: declared
  v4-branching: declared
  v5-version-pin: declared
  v6-author-timestamp: declared
  substrate-id: "<нативный для носителя pointer>"

spec-types-supported: ["SPEC-ARCH", "SPEC-API", "SPEC-DATA", "SPEC-INT",
                       "SPEC-PROC", "SPEC-UI", "SPEC-AI", "SPEC-SEC", "SPEC-OPS",
                       "SPEC-TEST", "SPEC-DOC"]

assessor:
  id: "<V6 author identifier>"
  role: architect              # architect | authorized-role-holder | external-assessor
  signature-ref: "<нативный для носителя pointer на signature event>"
```

Полный список полей — [§13.4.2](../standard/13-conformance.md#13.4.2).

---

## 5. Периодичность

- Самооценка: **квартально** (по умолчанию)
- После delta-ТЗ с воздействием на обязательные положения — **внепланово**
- Триггеры потери соответствия — [§13.8](../standard/13-conformance.md#13.8)

---

*Reference RENAR 1.1 — renar.tech*
