---
title: "Сравнение с SAFe"
description: "Mapping RENAR ↔ SAFe 6.0: WSJF, PI Planning, ART координация, RACI lifecycle, Built-in Quality."
order: 5
lang: ru
version: "1.0"
---

# 05. Сравнение с SAFe

> Mapping RENAR (стандарт **требований**) на SAFe 6.0 (стандарт **scaled agile координации**). Документы совместимы, но имеют разный scope: SAFe нормирует *как* команды координируют работу на масштабе; RENAR нормирует *что* собой представляет требование и *как* оно верифицируется. Эта глава — для команд, которые уже работают по SAFe (enterprise multi-team ART — типичный пример) и хотят сохранить SAFe ceremonies, добавив RENAR-артефакты как первичный источник истины о требованиях.
>
> **Предпосылки:** знакомство с [RENAR Core](../core/renar-core.md) — концептуальным обзором для человека-читателя и базовой SAFe 6.0 терминологией (Epic / Capability / Feature / Story, WSJF, PI, ART).

---

## 1. Scope: что нормирует RENAR, что — SAFe

RENAR и SAFe пересекаются на уровне *артефактов работы* (Feature, Story), но решают разные задачи.

| Аспект | SAFe 6.0 | RENAR |
|---|---|---|
| Тип стандарта | Scaled Agile coordination framework | Стандарт инженерии требований |
| Первичный артефакт | Epic / Capability / Feature / Story | ТЗ → ADAPT → BR → SR → SPEC → TC |
| Что нормирует | Cadence, roles, ceremonies, flow | Schema, lifecycle, verifiability, drift control |
| Носитель артефактов | Выбор средства управления задачами (Jira / Rally / ADO / др.) | Без привязки к носителю; нормативно — возможности **V1–V6** ([глоссарий §2.7](../reference/01-glossary.md#2.7)) |
| Контрольные точки качества | Definition of Done на уровне Feature | `QG-0` (готовность к старту) + `QG-2` (проверено) |
| AI-нативность | Не нормирует процесс работы с ИИ | ИИ как полноценный участник (контрастная экспертиза *adversarial*, оценивающие сценарии, модели-судьи `judge`) |

**Ключевой принцип:** SAFe и RENAR совместимы. SAFe говорит «как координировать команды на ART», RENAR — «что должно быть истинно про каждое требование, чтобы оно считалось `verified`». Feature в SAFe ≡ SR в RENAR — это **один и тот же артефакт**, описанный с разных сторон.

---

## 2. Главная таблица соответствия

| SAFe artefact | RENAR artefact | Где живёт | Owner | Acceptance criteria |
|---|---|---|---|---|
| Strategic Theme | (вне scope RENAR — корпоративная стратегия) | стратегические доки | Executive | бизнес-уровень |
| Portfolio Epic | Группа BR одной системы | `<system>.req/br/` aggregated | Lean Portfolio Mgmt | Все BR группы входят в верифицированную версию комплекта |
| Capability | BR подсистемы (если у подсистемы свой stakeholder) | `<subsystem>.req/br/` | Solution Architect | Все BR подсистемы входят в верифицированную версию комплекта |
| Program Epic | Опционально — крупная инициатива внутри ART, aggregated SR | `<system>.req/sr/` aggregated | Product Manager | Заданная outcome metric достигнута |
| Feature | SR (или связанная группа SR) | `<subsystem>.req/sr/` | Product Owner | TC из `verified-by` зелёные при `last-run.set-version` = текущей версии комплекта (QG-2) |
| Story | TR (Task Requirement) | `<subsystem>.req/tr/` или task tracker (Jira, Linear, GitLab Issues) | Команда | Все AC выполнены, evidence зафиксирован, code merged |
| Enabler Epic | SPEC-AI / SPEC-ARCH / SPEC-OPS | `<system>.req/specs/` | Architect | Eval-метрики в пределах thresholds; эталон зафиксирован |
| Spike | Исследовательский TR + Decision в decision log | `<subsystem>.req/tr/` + decision log | Команда | Decision записан и связан с originating SR |
| Defect | TR с `parent: SR-NN` (нарушенный SR) | task tracker + auto-derived `children[]` в SR | Команда | Negative TC проходит, regression test добавлен |

Запрещённые соответствия: INT-SR — устаревший термин ([§4.14.1 Terms](../standard/04-terms.md#4.14.1)), заменён `SR` с `constrained-by: [SPEC-INT-N]`. См. §6 ниже.

---

## 3. WSJF prioritization для RENAR

SAFe использует **WSJF (Weighted Shortest Job First)** как приоритизационный framework. RENAR адаптирует его для уровня BR / SR.

### 3.1 Формула

```text
WSJF = (User-Business Value + Time Criticality + Risk Reduction & Opportunity Enablement) / Job Size
```

Каждый компонент оценивается по шкале 1-20 (modified Fibonacci); WSJF — относительная метрика, имеет смысл только при сравнении BR/SR внутри одного backlog.

### 3.2 Расширение frontmatter BR

```yaml
---
id: BR-12
title: "Сократить время регистрации сотрудников до < 2 минут"
priority: must
prioritization:
  framework: WSJF
  components:
    user-business-value: 10
    time-criticality: 8
    risk-reduction-opportunity: 6
    job-size: 5
  wsjf-score: 4.8                # auto-calculated: (10+8+6)/5
  prioritized-at: 2026-05-03
  prioritized-by: "@product-owner"
---
```

Поле `prioritization` — опциональное в Core RENAR, обязательное на ART, который применяет SAFe.

### 3.3 Когда применяется

- **Обязательно** для BR с `priority: must` в проектах, координируемых через ART.
- **Рекомендуется** для всех BR в backlog, который проходит PI Planning.
- **Не применяется** для SR — SR наследует приоритет родительского BR. Перешафливание SR внутри одного BR — задача Product Owner на decomposition, не WSJF.

### 3.4 Альтернативы

Если команда не использует SAFe / WSJF — RENAR допускает другие frameworks:

- **MoSCoW** (Must / Should / Could / Won't) — простейший, для маленьких проектов. Уже есть как `priority` enum в RENAR Core.
- **RICE** (Reach × Impact × Confidence / Effort) — для product-driven команд (типично B2C).

RENAR нормирует **поле и его schema**; выбор framework — на проекте. См. также [reference/02-schemas.md](../reference/02-schemas.md) для допустимых значений `prioritization.framework`.

---

## 4. PI Planning интеграция

### 4.1 Что такое PI

**Program Increment (PI)** — фиксированный отрезок времени (обычно 8-12 недель) в SAFe, в течение которого ART коммитится на набор Features из общего backlog. PI Planning — двух- или трёхдневный event перед каждым PI, где команды совместно фиксируют commitment.

### 4.2 Где RENAR-артефакты попадают в PI flow

```text
[До PI Planning]                  [PI Planning event]                [Iteration 1..N]                 [System Demo / I&A]
       │                                  │                                  │                                │
       ▼                                  ▼                                  ▼                                ▼
 Backlog: SR со статусом      Команды берут SR              SR → TR в трекере             SR со статусом
 approved, WSJF-               (= Features) на следующий     Реализация Story              verified
 prioritized                   PI                            TC прогон на каждом
       │                       Декомпозиция SR → TR          merge
       │                       Identify cross-team           QG-2 проверка
       │                       dependencies через            на завершении SR
       │                       SPEC-INT
       │                                                                                  RENAR метрики feed
       └──────────────────────────────────────────────────────────────────────────────►  в SAFe Inspect & Adapt
```

### 4.3 Артефакты RENAR в каждой ceremony

| SAFe ceremony | RENAR input | RENAR output |
|---|---|---|
| Backlog refinement | BR / SR в черновике или в утверждённой версии комплекта | Уточнённый ADAPT, обновлённый WSJF |
| PI Planning | WSJF-sorted SR backlog, ADAPT-доки | Commitment на SR в PI; SPEC-INT для cross-team |
| Iteration Planning | SR + декомпозированные TR | TR в `approved` |
| System Demo | SR верифицированной версии комплекта | Evidence из записи верификации и TC last-run |
| Inspect & Adapt | Метрики RDLT, Coverage Velocity, Hallucination Rate | Корректировки backlog / процесса |

---

## 5. ART координация и роли

### 5.1 SAFe роли ↔ RENAR ответственности

| SAFe role | RENAR ответственность |
|---|---|
| **RTE** (`Release Train Engineer`) | Координация cross-team dependencies через SPEC-INT; владелец PI Objectives ↔ RENAR метрик mapping |
| **Product Manager** | Owner портфельных Epic = групп BR на уровне системы |
| **Product Owner** | Owner Feature = SR на уровне команды; accountable за QG-0 (утверждение версии) и QG-2 (верификация версии) |
| **System Architect** | Owner SPEC-* (особенно SPEC-ARCH, SPEC-INT); consulted при декомпозиции BR → SR |
| **Tech Lead** | Accountable за QG-2 на уровне SR; владеет TR-backlog команды |
| **Business Owner** | Approver BR / ADAPT с бизнес-стороны |
| **Solution Architect** | Owner BR на уровне подсистемы (когда подсистема имеет свой stakeholder) |
| **Scrum Master** | Owner ceremonies; не владеет RENAR-артефактами напрямую |

### 5.2 Cross-team координация

В типичной SAFe-организации с несколькими командами в одном ART:

- **Каждая команда** владеет своими SR / TR в `<subsystem>.req/`.
- **RTE / Solution Architect** владеют SPEC-INT в `<system>.req/specs/int/` — общими integration contracts между подсистемами.
- **Изменение SPEC-INT** требует cross-team согласования (QG-0 от всех затронутых команд).

**Правило:** ART-уровневые роли (RTE) **не редактируют** SR в подсистемах напрямую. Координация — через SPEC-INT, который явно `constrained-by` для каждой затронутой SR.

---

## 6. Cross-team dependencies через SPEC-INT

Когда Feature в одной подсистеме блокирует / зависит от другой:

### 6.1 SR с зависимостью

```yaml
# В <subsystem-a>.req/sr/SR-05.md
---
id: SR-05
parent: BR-12
title: "Регистрация пользователя через корпоративный email"
constrained-by:
  - id: SPEC-INT-01
    ref: "specs/int/SPEC-INT-01-auth-handshake.md"
verified-by:
  - TC-23
  - TC-24
---
```

### 6.2 SPEC-INT как контракт

```yaml
# В <system>.req/specs/int/SPEC-INT-01-auth-handshake.md
---
id: SPEC-INT-01
type: SPEC-INT
title: "Auth handshake между Subsystem A и Subsystem B"
version: 1.2
participants:
  - subsystem: "subsystem-a"
    role: "client"
  - subsystem: "subsystem-b"
    role: "provider"
verified-by:
  - TC-INT-01    # contract test
---
```

### 6.3 Breaking changes в SPEC-INT

Любое изменение `SPEC-INT.version` с breaking-семантикой ([§4.11 Drift классы](../standard/04-terms.md#4.11)) требует:

1. ADAPT-уровневое обсуждение (зачем меняем).
2. Согласование от всех `participants` (QG-0 от каждой команды).
3. Migration plan для existing implementors (как старые SR останутся valid или будут адаптированы).

RTE **обязан** проверять SPEC-INT consistency между подсистемами регулярно (обычно раз в спринт) — это часть Inspect & Adapt и feed в conformance self-assessment ([§13 Conformance](../standard/13-conformance.md)).

---

## 7. Definition of Done на каждом уровне иерархии

DoD — **формальные** условия, проверяемые автоматически (hooks носителя). На каждом уровне иерархии — свой DoD.

| Level | RENAR artefact | DoD criterion |
|---|---|---|
| Strategic Theme | — | (вне scope RENAR) |
| Portfolio Epic | Группа BR | Все BR группы — в верифицированной версии комплекта, KPI impact подтверждён через outcome metric |
| Capability | BR подсистемы | Все BR — в верифицированной версии; outcome metric измерен |
| Feature | SR | QG-2 passed: все TC из `verified-by` имеют `last-run.result = pass` при `last-run.set-version` = версии комплекта |
| Story | TR | Все AC выполнены, evidence зафиксирован, code merged, automated test exists |
| Enabler | SPEC-AI / SPEC-ARCH / SPEC-OPS | Eval-runs прошли пороги (для SPEC-AI); эталон зафиксирован (для всех) |

**Ключевое:** на уровне Feature DoD в SAFe **совпадает** с QG-2 в RENAR. Нет двух разных «definition of done» — это один и тот же gate, описанный с разных сторон.

---

## 8. Built-in Quality ↔ RENAR mechanisms

SAFe принцип Built-in Quality: качество встроено в процесс, не ad-hoc. RENAR реализует Built-in Quality через нормативные механизмы:

| SAFe Built-in Quality practice | RENAR mechanism |
|---|---|
| Continuous Integration | hooks носителя + CI на каждый change в требованиях ([§13 Conformance](../standard/13-conformance.md)); reconciliation hook (drift detection, [§4.11](../standard/04-terms.md#4.11)) |
| Test-First | TC-нормы создаются **до** реализации и утверждаются в той же версии комплекта, что и SR; QG-0 комплекта требует пару TC-норм на каждое утверждение |
| Refactoring | Continuous reconciliation hook ([§2.4.2](../standard/02-methodology-positioning.md#2.4.2)); обнаруживает дрейф между требованиями и кодом |
| Pairing / Mobbing | AI-генератор + AI-критик ([§5.2 Roles](../standard/05-roles.md#5.2)) — pair generation/review как нормативная роль |
| Definition of Done | QG-2 как формальный gate, проверяемый автоматически ([§10 Lifecycle и QG](../standard/10-lifecycle-qg.md)) |
| Version Control | Возможность **V1** (неизменяемая история) без привязки к классу сред хранения |
| Automation | Capability V2-V6 — все верификации автоматизируемы |

RENAR **не предписывает** instrument (Jenkins / GitLab CI / GitHub Actions / Tekton) — только **что** должно проверяться (capability), не **как**.

---

## 9. RACI lifecycle артефакта (с SAFe ролями)

Полный жизненный цикл RENAR-артефакта с распределением ответственности по SAFe ролям.

| Активность | Responsible | Accountable | Consulted | Informed |
|---|---|---|---|---|
| Импорт ТЗ | AI-агент | System Architect | Business Owner | Команда, RTE |
| Декомпозиция ТЗ → ADAPT | AI-генератор | System Architect | Stakeholder, AI-критик | RTE, Команда |
| Декомпозиция → BR | AI-генератор | Product Owner | Business Owner, AI-критик | RTE, Команда |
| WSJF prioritization | Product Manager | Product Owner | RTE, Stakeholder | Команда |
| Декомпозиция BR → SR | AI-генератор | System Architect | AI-критик | Команда, RTE |
| Декомпозиция SR → SPEC-* | System Architect | System Architect | AI-критик, Tech Lead | Команда |
| Генерация TC | AI-агент | Test Architect | — | Команда |
| QG-0 approval | System Architect | Tech Lead | AI-критик | Business Owner, RTE |
| Выбор SR на PI | Product Owner | RTE | Команда (capacity) | Stakeholder |
| Декомпозиция SR → TR | Команда | Product Owner | Tech Lead | RTE |
| Реализация TR | Разработчик | Tech Lead | — | Команда |
| Прогон TC (automated) | hook носителя | — | — | Команда |
| QG-2 (версия верифицирована) | System Architect | Tech Lead | — | Business Owner, RTE |
| System Demo | Команда + RTE | Product Owner | Stakeholder | RTE, Executive |
| Утверждение delta-ТЗ | System Architect + Stakeholder | Product Owner | AI-impact analysis, RTE | Команда |
| Spot-check 5 TC (audit) | System Architect | — | — | RTE |
| Reconciliation MR | AI-агент-reconciler | System Architect | — | Команда |

---

## 10. PI Objectives ↔ RENAR метрики

PI Objectives — SMART outcomes на квартал. RENAR-метрики ([§12 Metrics](../standard/12-metrics.md), [reference/02-schemas.md](../reference/02-schemas.md)) feed в PI Objectives:

| PI Objective (example) | RENAR метрика |
|---|---|
| «Сократить время от подписания ТЗ до первого commit до < 2 дней» | **RDLT** (Requirement Decomposition Lead Time) |
| «Достичь Coverage Velocity ≥ 60% в спринт» | **Coverage Velocity** (% SR с `status: verified` per sprint) |
| «Снизить Hallucination Rate в новых требованиях до < 2%» | **Hallucination Rate** (% AI-сгенерированных утверждений, отклонённых на review) |
| «0 disputed requirements на acceptance в этом PI» | **Dispute Rate at Acceptance** |
| «Drift detection — все SR consistent с code в течение 24 часов после merge» | **Reconciliation Findings per Week** ([§12.3.10](../standard/12-metrics.md#12.3.10)) |

Это превращает RENAR из «standard for documents» в **measurable contribution** к ART success. Метрики feed автоматически в Inspect & Adapt; RTE использует их при ретроспективе на завершении PI.

---

## 11. Что из SAFe оставить, что заменить

Для команд, переходящих на RENAR с уже работающего SAFe-процесса:

### 11.1 Оставить как есть

- **Cadence** (PI, Iterations) — RENAR не нормирует время; используйте свой ритм.
- **Ceremonies** (PI Planning, System Demo, I&A, Daily Standup) — RENAR-артефакты прозрачно вписываются в эти events.
- **WSJF** — оставить для приоритизации BR / SR; вписывается в `prioritization.framework: WSJF`.
- **ART, RTE, Scrum Master** — структура и роли сохраняются.
- **PI Objectives** — оставить; mapping на RENAR-метрики (§10) делает их измеримыми.

### 11.2 Заменить RENAR-эквивалентом

- **Feature description в Jira / Rally / ADO** → SR со полным frontmatter в `<subsystem>.req/sr/`. Tracker-запись становится **зеркалом** RENAR-артефакта, не первичным источником.
- **Acceptance criteria в Feature** → `verified-by: [TC-NN]` с автоматически проверяемыми TC.
- **Definition of Done на Feature** → QG-2 (нет двух разных DoD — это один gate).
- **Integration agreements между командами** → SPEC-INT (заменяет INT-SR из старых SAFe-реализаций).
- **Architecture Decision Records (ADR)** → SPEC-ARCH / SPEC-AI / SPEC-OPS (один из 12 типов SPEC, [§4.4](../standard/04-terms.md#4.4)).

### 11.3 Добавить (новое в RENAR)

- **ADAPT** — мостовой артефакт между ТЗ и иерархией требований. В SAFe нет прямого аналога; создаётся **реактивно** ([standard/07 §7.4.1](../standard/07-adapt.md#7.4.1)) — только когда состязательный рецензент вынес вердикт «findings present». При вердикте «no findings» ADAPT не создаётся, и BR / SR / SPEC выводятся из ТЗ напрямую через `source.tz-section` + `source.adversarial-review-ref`. Обязателен не ADAPT, а сам состязательный обзор.
- **AI-критик роль** — адверсариальная проверка AI-генерации. В SAFe не нормирована, в RENAR Core — обязательна для всех AI-сгенерированных артефактов.
- **Reconciliation (drift detection)** — непрерывная сверка требований с реализацией (опирается на V5 — сквозную фиксацию версии). В SAFe — manual reconciliation, в RENAR — нормативный механизм.

---

## 12. Negative: чего эта глава не утверждает

- **RENAR — не замена SAFe.** RENAR нормирует *требования*, SAFe — *координацию работы*. Команда может работать по RENAR без SAFe (маленький проект, одна команда) или с SAFe (enterprise multi-team ART scope).
- **RENAR — не стандарт планирования.** Cadence, capacity planning, velocity tracking — все за пределами scope. RENAR говорит «что должно быть истинно про требование», не «когда команда должна его доделать».
- **RENAR не запрещает другие frameworks.** WSJF, MoSCoW, RICE — все совместимы. RENAR фиксирует **поле** `prioritization.framework`, не выбор framework.
- **RENAR не нормирует Jira / Rally / ADO workflow.** tracker-уровневая реализация — деталь носителя; нормативный источник — `<subsystem>.req/`.
- **RENAR не предписывает PI как обязательный.** Команда может работать по непрерывному flow без PI; в этом случае разделы 4 и 10 этой главы становятся informative.

---

## 13. Resolved decisions для v1.0

- **`priority: must` НЕ требует WSJF score даже в SAFe-проектах.** Per §12 этой главы: RENAR фиксирует `prioritization.framework`, не предписывает выбор framework. `priority: must` — это RENAR MoSCoW marker ([reference/02-schemas](../reference/02-schemas.md)), независимый от WSJF. WSJF — оптимизация SAFe-команд, не нормативное требование RENAR.
- **Feature/Capability/Story mapping — специфично для носителя.** RENAR нормирует closed list типов требований (BR/SR/TR + 11 SPEC) и не вводит SAFe Feature-уровни. Проекты, применяющие SAFe, фиксируют mapping в нативном для носителя `safe-mapping/` manifest (см. [reference/02-schemas](../reference/02-schemas.md) — informative extension).
- **PI Objectives — informative, вне scope RENAR.** PI — SAFe coordination artifact, RENAR не нормирует. Если команда хочет traceability, рекомендуется informative `cross-link.pi-objective-id` поле в BR-frontmatter — это не conformance-gating, но облегчает RTE-side queries.
- **Inspect & Adapt: метрики нативно для носителя автоматизированы.** Per [§10.13](../standard/10-lifecycle-qg.md#10.13) Logging + [§12](../standard/12-metrics.md) — носитель обязан нативно для носителя выставлять COVERAGE / audit-trail. RTE использует те же данные через query API, manual extraction — anti-pattern (drift).

### 13.1 Отложено на v1.1 (бэклог фазы 8)

- **Эвристика ИИ для оценки поля WSJF Job Size** (шкала 1–20 по числу AC, сложности `TC` и площади кода). В v1.0 не нормируется; специфично для связки SAFe–RENAR. Расширенный informative mapping — [reference/11](../reference/11-external-standards-mapping.md).

---

## 14. Связь с другими главами

- [00-quickstart](00-quickstart.md) — базовый цикл RENAR без SAFe-надстройки.
- [01-walkthrough](01-walkthrough.md) — полный example на small-scale проекте (без PI Planning).
- [reference/01-glossary](../reference/01-glossary.md) — точная семантика BR / SR / SPEC / TR / TC.
- [reference/02-schemas](../reference/02-schemas.md) — frontmatter schemas, включая `prioritization`.
- [standard/04-terms](../standard/04-terms.md) — нормативные определения; mapping на SAFe закреплён в [§4.13](../standard/04-terms.md#4.13).
- [standard/08-specifications](../standard/08-specifications.md) — closed list 12 типов SPEC, включая SPEC-INT и SPEC-UC.

