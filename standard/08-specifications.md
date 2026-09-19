---
title: "Спецификации (12 типов SPEC)"
order: 8
lang: ru
---
# 08. Спецификации — 12 типов SPEC

> **Часть RENAR Standard v1.1** · [← Оглавление](README.md)

## 8.1 Зачем отдельная ось спецификаций

Возьмём требование «система создаёт заказ». Оно говорит, **что** должно происходить — но молчит о том, **как** система для этого устроена: какой у неё API-контракт, в какой таблице лежит заказ, по каким правилам доступа, на каком экране. Втиснуть всё это в само требование не выйдет — оно превратится в кашу. Поэтому RENAR разводит описание на две оси: **поведение** (BR / SR / TR, [глава 6](06-requirements-hierarchy.md)) и **структуру** — спецификации, SPEC.

Спецификация — не «более детальный SR» и не его ребёнок. Одно требование «создать заказ» обычно опирается сразу на пять-семь спецификаций (архитектура, API, данные, процесс, безопасность, экран), поэтому связь между осями — граф типизированных рёбер (`constrained-by[]`, `implements-spec[]`), а не дерево. Типов спецификаций ровно двенадцать, и список закрыт: `SPEC-ARCH`, `API`, `DATA`, `INT`, `PROC`, `UI`, `AI`, `SEC`, `OPS`, `TEST`, `DOC`, `UC` — новый тип вводится только через формальную процедуру изменения стандарта ([глава 13](13-conformance.md)).

Спецификация описывает **четыре разных предмета**, и путать их нельзя. Девять типов описывают **устройство системы**; `SPEC-UC` — **путь через неё** ([§8.5.12](#8.5.12)). `SPEC-TEST` описывает **конструкцию доказательства** её соответствия: стенд интеграционной системы — не инсталляция, а инженерное изделие (эмуляторы, датасеты по обе стороны каждой интеграции, правила сверки с данными чужих систем). `SPEC-DOC` описывает **состав поставляемого результата**: документация, которую заказчик получает как часть поставки.

---

## 8.2 Архитектурное решение: SPEC — параллельная ось, не дети SR

### 8.2.1 Две оси описания системы

Требования и спецификации отвечают на разные вопросы:

| Ось | Артефакты | Вопрос |
|---|---|---|
| Поведенческая | BR / SR / TR ([глава 6](06-requirements-hierarchy.md)) | Что система должна делать |
| Структурная | SPEC-* (9 типов) | Как система структурно устроена для выполнения этих требований |
| Доказательная | `SPEC-TEST` | На чём и на каких данных доказывается соответствие ([§8.3.2](#8.3.2)) |
| Поставочная | `SPEC-DOC` | Что входит в поставку заказчику помимо самой системы |

### 8.2.2 SPEC как параллельная ось: связи через типизированный граф

Связи между осью требований (BR / SR / TR) и осью SPEC организованы как **граф зависимостей**, не дерево родителей. У SR ровно один родитель в дереве требований (BR), но множество типизированных рёбер `constrained-by[]` на SPEC. Один SR обязан ссылаться на каждую SPEC, ограничивающую его поведение в осях API / data / UI / process / security / ops; обратно — одна SPEC может ограничивать множество SR.

Пример: SR «создать заказ» опирается на SPEC-ARCH (где живёт компонент заказов), SPEC-API (контракт endpoint), SPEC-DATA (схема таблицы), SPEC-PROC (workflow), SPEC-SEC (правила доступа), SPEC-UI (форма).

Нормативно: каждое ребро `SR.constrained-by[]` и `TR.implements-spec[]` **должно** ссылаться на одну из закрытых категорий SPEC, перечисленных в [§8.3](#8.3); ad-hoc категории не допускаются (см. [§1.7](01-scope.md#1.7) closed-list policy).

```text
Дерево требований (поведенческая ось):     Параллельная ось спецификаций:

BR                                          SPEC-ARCH    SPEC-API
 └── SR  ←──── constrained-by[] ────►       SPEC-DATA    SPEC-INT
      └── TR ─── implements-spec[] ────►    SPEC-PROC    SPEC-UI
                                            SPEC-AI      SPEC-SEC
                                            SPEC-OPS     SPEC-TEST
                                            SPEC-DOC

Дерево требований:
  SR.parent              → BR              (единственный родитель)
  TR.parent              → SR              (единственный родитель)

Граф связей (типизированные рёбра):
  SR.constrained-by[]    → SPEC-*
  TR.implements-spec[]   → SPEC-*
  SPEC-*.depends-on[]    → SPEC-*          (между спецификациями)
  SPEC-*.referenced-by[] → SR / TR          (auto-derived inverse)
```

### 8.2.3 Обоснование

| Аргумент | Следствие |
|---|---|
| SPEC и SR отвечают на разные вопросы | SPEC не уточняет SR на более глубоком уровне — это отдельная категория описания |
| Один SR опирается на 5–7 SPEC | Дерево «SPEC как родитель SR» приводит к множественному родительству |
| Industry standards (arc42, C4, OpenAPI, BPMN, ERD) живут параллельно требованиям | RENAR следует этой проверенной практике |
| AI-агент может параллелить генерацию SR и SPEC | Без блокировки одного типа другим |

---

## 8.3 Закрытый список из двенадцати типов SPEC

| Тип | Назначение | Industry reference |
|---|---|---|
| `SPEC-ARCH` | Архитектура системы / подсистемы: контексты, контейнеры, компоненты, deployment view, quality attributes | arc42, C4 model (Brown), ISO/IEC/IEEE 42010 |
| `SPEC-API` | API contracts: REST / GraphQL / gRPC / async events; версионирование, error model, rate limits | OpenAPI 3.x, AsyncAPI 2.x, gRPC IDL |
| `SPEC-DATA` | Модель данных: schema, ERD, indices, миграции, retention, PII classification | ISO/IEC 11179, JSON Schema |
| `SPEC-INT` | Integration: взаимодействие между подсистемами и внешними системами; протоколы, контракты, SLA | Enterprise Integration Patterns (Hohpe) |
| `SPEC-PROC` | Process / workflow: бизнес-процессы, state machines, saga, choreography, orchestration | BPMN 2.0, ISO/IEC 19510 |
| `SPEC-UI` | UI / UX: экраны, навигация, user journeys, accessibility, i18n, эталонные изображения | Material Design / Apple HIG, WCAG 2.2 |
| `SPEC-AI` | AI / ML: model cards, RAG, prompt engineering, eval strategy, cost budget | ISO/IEC 23894, NIST AI RMF |
| `SPEC-SEC` | Security: authn / authz, threat model, secrets management, data classification | STRIDE, OWASP ASVS, ISO/IEC 27001 |
| `SPEC-OPS` | Operations: deployment, observability, SLO / SLA, runbook, disaster recovery | Google SRE, ITIL v4, ISO/IEC 20000 |
| `SPEC-TEST` | Тестовые стенды и данные: топология, эмуляторы и sandbox-инстансы внешних систем, конфигурация прогона (что мокается, что живое), датасеты по обе стороны каждой интеграции, правила сверки результатов с данными чужих систем, объём и анонимизация | ISO/IEC/IEEE 29119-4 (test techniques), Testcontainers, Pact |
| `SPEC-DOC` | Поставляемая проектная документация: состав (руководство пользователя, руководство администратора, обучающие материалы), структура каждого документа (обязательные разделы), проверяемые требования к нему (полнота по ролям и сценариям, актуальность версии, формат) | ISO/IEC/IEEE 26511, ISO/IEC/IEEE 26514 |
| `SPEC-UC` | Сценарий использования: сквозной путь пользователя или агента через несколько SR / SPEC; роль исполнителя (`human` — через интерфейс, `agent` — через API) задаёт канал прогона покрывающих TC; каждый шаг ссылается на затрагиваемое утверждение | Use cases (Cockburn), ISO/IEC/IEEE 29148 §6.4 (сценарии), Gherkin (форма шага) |

**Обязательные типы.** Комплект описания уровня `system` содержит непустые `SPEC-ARCH` и `SPEC-SEC` независимо от того, упоминает ли их ТЗ; их отсутствие или пустое обязательное тело — нарушение структурной полноты версии ([§10.7.2](10-lifecycle-qg.md#10.7.2), [§10.7.3](10-lifecycle-qg.md#10.7.3)). Молчание ТЗ о безопасности или об устройстве системы — пробел, а не решение заказчика: описание закрывает его собственным утверждением (при наблюдаемых клиентом последствиях — через обратную находку и контрактный контур, [§6.13.2](06-requirements-hierarchy.md#6.13.2)), а не наследует. Остальные типы применяются там, где есть предмет ([глава 11](11-maturity-model.md)).

### 8.3.1 Что НЕ вошло в v1.0 (с обоснованием)

| Кандидат | Решение | Обоснование |
|---|---|---|
| `SPEC-EVENT` | Не отдельный тип | События / очереди — раздел SPEC-API (asynchronous APIs) |
| `SPEC-CONFIG` | Не отдельный тип | Feature flags / env vars / secrets — раздел SPEC-OPS |
| `SPEC-PERF` | Не отдельный тип | Performance / NFR — раздел SPEC-ARCH (quality attributes) или SPEC-OPS (SLO) |
| ~~`SPEC-TEST-ENV`~~ | **Решение отменено в v1.0** | Прежнее решение («тестовые окружения — раздел SPEC-OPS») отменяется: см. [§8.3.2](#8.3.2). Тестовый стенд введён отдельным типом `SPEC-TEST` |
| `SPEC-DOMAIN` | Не отдельный тип | Domain model — поглощён SPEC-ARCH (decomposition) + SPEC-DATA (entities) |
| `SPEC-MIGRATION` | Не отдельный тип | Migration — раздел SPEC-DATA (жизненный цикл) |
| `SPEC-COMPLIANCE` | Не отдельный тип | Compliance — связи между SR/SPEC и нормативами через `compliance-refs[]`, не отдельный артефакт |

Отменённое решение сохраняется в таблице зачёркнутым, а не вычёркивается: история решений — часть стандарта, и читатель вправе видеть, что и почему было пересмотрено.

### 8.3.2 Ревизия решения по тестовым стендам

Прежнее решение отождествляло тестовый стенд с **окружением развёртывания** и отправляло его в `SPEC-OPS` (поле `environments[]`: dev / staging / prod). Для простой системы это верно: стенд там — стейдж плюс датасет. Решение отменено по трём причинам.

**1. Стенд интеграционно-тяжёлой системы — другой объект.** Это не инсталляция системы, а **конструкция доказательства**: эмуляторы и sandbox-инстансы внешних систем; согласованные датасеты **по обе стороны** каждой интеграции; правила сверки результатов с данными чужих систем — ожидаемый результат живёт **не в нашей системе**; конфигурация прогона (что мокается, что живое). Стандарт это уже чувствовал: [§9.6.3](09-test-cases.md#9.6.3) требует для SPEC-INT прогон «против реальной или sandbox-counterparty — мокированного контракта недостаточно», но носителя для описания этой counterparty не давал.

**2. Ложные инвалидации — решающий довод.** [§10.9.4](10-lifecycle-qg.md#10.9.4) помечает реализацию TC устаревшей при **любом** изменении нормы, на которую она ссылается, автоматически и без разбора причины. Если бы TC-норма ссылалась на стенд через SPEC-OPS, то правка **процедуры деплоя** — к стенду отношения не имеющая — делала бы устаревшими реализации **всех** TC, ссылающихся на этот SPEC-OPS. Деплой правят чаще стенда, и доказательная база обесценивалась бы регулярно и ложно. Отдельный `SPEC-TEST` делает инвалидацию **точной**: TC инвалидируются тогда и только тогда, когда изменилось то, против чего они валидны.

**3. Чужеродная подпись.** Чувствительные тестовые данные (объём, анонимизация, использование реальных данных клиента) согласует **клиент**. Клиентская подпись на разделе эксплуатационного документа чужеродна; на отдельном артефакте — естественна.

Границы новых типов:

| Тип | Отвечает на вопрос |
|---|---|
| `SPEC-OPS` | **Как система живёт** в эксплуатации (деплой, наблюдаемость, SLO, runbook) |
| `SPEC-TEST` | **Как доказывается** её соответствие (стенды, данные, конфигурация прогона) |
| `SPEC-DOC` | **Что входит в поставку** заказчику (документы как предмет договора) |

**Граница SPEC-DOC ↔ SPEC-OPS.** Критерий — **вхождение в состав поставляемого результата**, а состав задаётся договором **до** приёмки, а не выясняется на ней:

- **Руководство администратора — всегда `SPEC-DOC`**: если систему эксплуатирует заказчик, руководство для его администраторов входит в состав поставки. «Внутренней версии» этого жанра не существует.
- **Runbook — всегда `SPEC-OPS`**: внутренний операционный регламент эксплуатирующей команды исполнителя; заказчику не предъявляется, предметом приёмки не является.

Двойная принадлежность документа исключена по построению — ребро `TR.implements-spec[]` всегда однозначно.

Политика закрытого списка: если в дальнейшей работе обнаружится, что какой-то из исключённых типов реально нужен — он добавляется через формальную процедуру изменения стандарта с обоснованием.

---

## 8.4 Общая схема (общие поля frontmatter)

Все 12 типов SPEC делят общий набор frontmatter полей. Поля, специфичные для типа, добавляются как расширения поверх (§8.5). Полная machine-readable модель данных — в [reference/02-schemas.md](../reference/02-schemas.md).

```yaml
---
# === Identity (обязательно) ===
id: SPEC-<TYPE>-NN[.N]              # immutable; TYPE ∈ {ARCH,API,DATA,INT,PROC,UI,AI,SEC,OPS,TEST,DOC}
title: "<short, descriptive>"
type: SPEC-ARCH | SPEC-API | SPEC-DATA | SPEC-INT | SPEC-PROC | SPEC-UI | SPEC-AI | SPEC-SEC | SPEC-OPS | SPEC-TEST | SPEC-DOC
slug: "<kebab-case>"                # auto-derived

# === Scope (обязательно) ===
level: system | subsystem | module
scope:
  system: "<system-id>"
  subsystem: "<subsystem-id>"       # null если level=system

# === Владение и приоритет ===
# Собственного статуса и версии нет: SPEC утверждается в составе версии комплекта
# описания, его состояние производно от неё ([§10.7](10-lifecycle-qg.md#10.7)).
priority: must | should | could     # not all types use; mostly SPEC-SEC / SPEC-OPS

# === Source: provenance (conditional, см. глава 7 §7.4.1) ===
# source.adapt — conditional (present когда ADAPT создавался; §7.4.1.1).
# source.tz-section — обязательно всегда.
# source.adversarial-review-ref — обязательно когда source.adapt omitted.
source:
  adapt: ADAPT-NNN                  # conditional
  adapt-section: "Forward §N"       # обязательно если adapt present
  tz-section: "§N.N"                # обязательно всегда
  adversarial-review-ref: AR-NNN   # обязательно если adapt omitted (§7.4.6)

# === Адресат обязательства (conditional, §6.14.3) ===
applies-to: self                    # self | consumer; только в SPEC компонента; по умолчанию self

# === Граф связей (auto-managed except mandatory ones) ===
referenced-by: []                   # auto-derived; SR/TR/SPEC ссылающиеся сюда
depends-on: []                      # обязательно если есть; SPEC-* на которые опирается этот SPEC
verified-by: []                     # auto-derived; список TC IDs верифицирующих

# === AI provenance (обязательно на RENAR-4+; каноническая schema — §4.10.1) ===
ai-provenance:
  generated-by: "<vendor>-<model>-<version>@<date>"
  generated-at: "<ISO-8601>"
  prompt-template: "<template-path>@<version>"
  context-tokens: integer
  output-tokens: integer
  human-edits: boolean
  generation-time-ms: integer        # optional; см. §4.10.1
  # optional на RENAR-4, обязательно на RENAR-5:
  # cost-budget, cost-actual

# === Замена (обязательно если применимо) ===
replaces: "<old-id>"                 # исключение и обратная ссылка replaced-by — в записи версии комплекта (§10.5.3)
deprecated-date: "<ISO date>"

# === Compliance (optional) ===
compliance-refs: []                 # ссылки на ISO/GDPR/AI Act/NIST AI RMF
---
```

### 8.4.1 Обязательные разделы body

Body любого SPEC обязательно содержит:

1. **Назначение** — 1–3 параграфа.
2. **Scope** — что входит, что не входит.
3. **Разделы, специфичные для типа** — см. §8.5.
4. **Связь с требованиями** — какие SR/BR ссылаются.
5. **Связь с другими SPEC** — `depends-on[]`.
6. **Verification** — какие TC верифицируют этот SPEC.

**Полнота охвата.** Обязательное тело описывает предмет спецификации исчерпывающе. Ограничение охвата подмножеством элементов по субъективному критерию отбора **запрещено** — во всех двенадцати типах и вне зависимости от того, какими словами оно выражено. Запрет относится к предмету, а не к лексике: прилагательное оценки («ключевой», «критический», «основной») и оборот меры («в достаточном объёме», «существенные», «релевантные») запрещены только тогда, когда сужают то, что подлежит описанию. Те же слова остаются законными, когда называют свойство самого предмета — как класс оповещений по степени серьёзности в [§8.5.9](#8.5.9).

**Предмет спецификации** проверяем: это всё, что названо связанными требованиями (раздел «Связь с требованиями») и, для SPEC-UI, перечнем экранов ([§8.5.6.1](#8.5.6.1)); сужение допустимо только явным разделом Scope с перечнем исключённых элементов и причиной каждого исключения.

Основание — инверсия источника истины ([§2.3.1](02-methodology-positioning.md#2.3.1)): то, что не попало в SPEC как «незначимое», не имеет источника истины вовсе. Агенту, реализующему систему, остаются два выхода, и оба нарушают стандарт: оставить неописанную часть нереализованной (неполнота продукта) либо реализовать её произвольно (домысливание, запрещённое [§2.3.3](02-methodology-positioning.md#2.3.3)).

---

## 8.5 Расширения схемы по типам SPEC

Краткое описание специфичных для типа полей и обязательных body-разделов. Полная machine-readable extension schema — в [reference/02-schemas.md](../reference/02-schemas.md). Industry references детально — в указанных стандартах.

### 8.5.1 SPEC-ARCH

**Type-specific frontmatter**: `arch-style`, `deployment-model`, `tech-stack`, `quality-attributes`.

**Обязательное тело**: системный контекст (C4 L1), контейнеры (C4 L2), компоненты (C4 L3) для каждого контейнера L2, quality attributes (latency / throughput / availability), ADR-журнал, перечень экранов системы — код, название и источник каждого экрана из описания ([§8.5.6.1](#8.5.6.1)); система без интерфейса записывает это явно.

**Spec-specific TC** ([глава 9](09-test-cases.md)): тесты соответствия архитектуры (zoning), эталонные тесты атрибутов качества. Покрытие перечня экранов — не TC, а предусловие утверждения версии ([§10.7.2](10-lifecycle-qg.md#10.7.2), [§8.5.6.1](#8.5.6.1)).

### 8.5.2 SPEC-API

**Type-specific frontmatter**: `api-style` (rest / graphql / grpc / async-events), `api-version`, `versioning-strategy`, `authentication`, `rate-limits`, `contract-file` (location of machine-readable contract).

**Обязательное тело**: endpoints / operations с payload / response / errors, versioning rules (breaking vs non-breaking), error model, authn/authz reference на SPEC-SEC, rate limits, 2–3 example запросов на endpoint.

**Spec-specific TC**: contract tests, authentication negative, rate limit tests.

### 8.5.3 SPEC-DATA

**Type-specific frontmatter**: `data-style` (relational / document / graph / columnar), `storage-engine`, `schema-version`, `pii-classification[]`, `retention-policies[]`, `migration-strategy`.

**Обязательное тело**: domain entities, ERD (text / Mermaid / link), поля сущностей (type / constraints / indices / defaults), связи (FK / cardinality / cascade), PII / sensitive data classification + encryption at-rest + retention, migration approach, index strategy.

**Spec-specific TC**: migration tests, constraint tests (FK / NOT NULL / unique), PII handling tests, data retention tests.

### 8.5.4 SPEC-INT

**Type-specific frontmatter**: `integration-pattern` (request-response / event-driven / message-queue / webhook / file-transfer), `direction`, `counterparty`, `sla`, `idempotency`.

**Обязательное тело**: интегрируемые системы, контракт обмена, failure modes + retry strategy, идемпотентность + dedup, security между системами, observability (correlation IDs).

**Spec-specific TC**: contract tests с моком counterparty, failure injection, idempotency, end-to-end TC `tc-type: contract`.

**Замечание**: SPEC-INT заменяет существующий `INT-SR` ([§8.7](#8.7) migration).

### 8.5.5 SPEC-PROC

**Type-specific frontmatter**: `process-style` (bpmn / state-machine / saga / choreography / orchestration), `state-count`, `participants[]`, `sla` end-to-end и по шагам, `compensation` (defined / not-applicable / manual).

**Обязательное тело**: process diagram (BPMN-flavor / Mermaid / link), состояния и переходы (для машины состояний), participants и их роли, happy path, альтернативные сценарии и исключения (каждый шаг сценария — со ссылкой на затрагиваемое утверждение, [§8.5.12.1](#8.5.12.1)), timeouts и compensation (для saga), SLA.

**Spec-specific TC**: happy path E2E, alternative paths, compensation tests (для saga), SLA tests.

### 8.5.6 SPEC-UI

**Type-specific frontmatter**: `ui-platform`, `target-users[]` (с ссылками на persona разделы ADAPT), `design-system`, `accessibility-level` (WCAG-A / AA / AAA), `i18n`, `mockup-links[]`, `baseline-images[]` для VLM-judge тестов, `screens[]` (коды экранов из перечня SPEC-ARCH, которые документ покрывает, [§8.5.6.1](#8.5.6.1)).

**Обязательное тело**: общая структура интерфейса, все экраны в объявленном Scope — каждый под кодом из перечня экранов SPEC-ARCH ([§8.5.6.1](#8.5.6.1)), user journeys без технических деталей, покрывающие каждое доступное через интерфейс пользовательское действие (каждый шаг journey — со ссылкой на затрагиваемое утверждение, [§8.5.12.1](#8.5.12.1)), сквозные элементы (права доступа / уведомления / error / empty states), тон и стиль, accessibility, i18n.

**Spec-specific TC**: VLM-judge с эталоном (judge ≠ production изоляция), accessibility (axe-core / Pa11y), i18n (string overflow / RTL), user journey E2E.

**Замечание**: SPEC-UI заменяет существующий `UIC` ([§8.7](#8.7) migration).

#### 8.5.6.1 Экран и полнота по системе

Полнота охвата ([§8.4.1](#8.4.1)) действует внутри объявленного Scope одного документа и не гарантирует, что сумма всех SPEC-UI покрывает интерфейс системы: для отсутствующего документа нет норм, которые он мог бы нарушить. Полноту по системе дают перечень экранов и правило покрытия.

**Экран** — состояние интерфейса, в котором пользователь совершает действие или наблюдает его результат, адресуемое независимо от техники реализации: маршрут, страница, модальное окно, шаг мастера, экран мобильного или настольного приложения — если в нём совершается действие или показывается его результат. Состояние без собственного адреса — индикатор ожидания, всплывающее уведомление поверх экрана — экраном не является: его описывает экран, на котором оно появляется.

**Перечень экранов** ведётся в обязательном теле SPEC-ARCH ([§8.5.1](#8.5.1)): неизменяемый код экрана (V1), название, источник — SR или раздел самого SPEC-ARCH, из которого экран выводится. Источник перечня — только описание. Перечень, построенный по маршрутам реализации, сделал бы код авторитетом в вопросе «что обязано быть описано» и нарушил бы инверсию источника истины ([§2.3.1](02-methodology-positioning.md#2.3.1), запрет [§2.3.3](02-methodology-positioning.md#2.3.3)); экран, который есть в реализации и отсутствует в перечне, — дрейф источника истины ([§4.11](04-terms.md#4.11), класс 4.11.3) и обратная находка, а не основание пополнить перечень задним числом. Система без интерфейса записывает в перечне «интерфейса нет» — явно, а не пропуском раздела.

**Правило покрытия.** Каждый экран перечня покрыт хотя бы одним SPEC-UI той же версии комплекта: SPEC-UI объявляет коды покрываемых экранов в `screens[]` и описывает каждый в разделе «Экраны» под тем же кодом. Экран перечня без покрывающего SPEC-UI — нарушение структурной полноты версии ([§10.7.2](10-lifecycle-qg.md#10.7.2)): версия не утверждается, пока экран не описан либо не исключён из перечня решением, видимым в записи версии. Код в `screens[]`, отсутствующий в перечне, — та же ошибка с другой стороны. Число SPEC-UI не ограничено; полнота внутри каждого — по §8.4.1. Правило введено в v1.1 (ADR-023 ред. 3); новых артефактов и типов оно не вводит.

### 8.5.7 SPEC-AI

**Type-specific frontmatter**: `ai-pattern` (rag / fine-tuning / prompt-engineering / tool-use / multi-agent), `production-model` (vendor / model / version), `judge-model` (обязан отличаться от production), `context-strategy`, `eval-strategy` (metric / threshold / baseline-dataset), `cost-budget`.

**Обязательное тело**: архитектура AI-компонент (pipeline / orchestration / fallback), model card (capabilities / limits / known failure modes), context strategy, eval strategy с изоляцией judge ≠ production, cost management, hallucination mitigation, состязательные аспекты.

**Spec-specific TC**: eval против эталона (judge isolated), состязательные (prompt injection как negative TC), cost regression, hallucination tests.

**Замечание**: SPEC-AI заменяет существующий `AIC`. Изоляция judge ≠ production model — обязательное требование стандарта для всех eval-TC.

### 8.5.8 SPEC-SEC

**Type-specific frontmatter**: `security-domains[]`, `auth-model` (authn / authz strategies), `data-classification[]` (PII-high / PCI / internal), `threat-model-method` (STRIDE / PASTA / OCTAVE), `compliance[]`, `incident-response` reference в SPEC-OPS.

**Обязательное тело**: auth model (authn flow / authz rules), data classification с защитой, threat model (STRIDE-таблица с mitigation на каждую угрозу), secrets management, audit (что логируется / retention / доступ), encryption (at-rest / in-transit / key management), compliance mapping (ссылки на конкретные пункты).

**Spec-specific TC**: authn (pos + neg), authz (RBAC matrix), threat-test (каждая STRIDE-угроза → минимум 1 negative TC), журнал аудита, secrets leakage.

### 8.5.9 SPEC-OPS

**Type-specific frontmatter**: `deployment-style`, `environments[]` (dev / staging / prod с purpose и scale), `slo`, `observability` (logs / metrics / traces / alerting), `runbook-link`, `disaster-recovery` (rto / rpo / backup-strategy).

**Обязательное тело**: environments, deployment process (CI/CD pipeline / gating / rollout strategy), SLO (availability / latency / error budget), observability, alerting (критические алерты / escalation), runbook, capacity planning, disaster recovery.

**Spec-specific TC**: deployment tests (smoke), SLO regression (load testing), failover (DR drills), observability (alerts срабатывают когда ожидается).

### 8.5.10 SPEC-TEST

**Type-specific frontmatter**: `benches[]` (топология стенда: узлы, версии, что живое / что эмулируется), `counterparties[]` (эмуляторы и sandbox-инстансы внешних систем — по одной записи на интеграцию), `datasets[]` (датасет с обеих сторон интеграции: объём, происхождение, `anonymized: boolean`, `contains-real-client-data: boolean`), `reconciliation-rules[]` (правила сверки результатов с данными чужих систем), `run-config` (что мокается, что живое, порядок прогона), `client-signature` (**обязательна**, если хотя бы один датасет несёт реальные или персональные данные клиента).

**Обязательное тело**: топология стенда; перечень внешних систем и способ их представления (реальная / sandbox / эмулятор — с обоснованием); датасеты по обе стороны каждой интеграции; правила сверки (**где живёт ожидаемый результат**, если он не в нашей системе); конфигурация прогона; политика данных (объём, анонимизация, срок хранения).

**Spec-specific TC**: воспроизводимость (повторный прогон на том же стенде даёт тот же результат); валидность counterparty (sandbox отвечает как реальная система — иначе доказательство фиктивно); целостность датасета (данные соответствуют объявленной схеме и объёму).

**Связь с TC.** Каждый TC с `automation.kind: dynamic` обязан нести `environment-ref` на SPEC-TEST ([§9.3](09-test-cases.md#9.3)): «тест пройден» имеет смысл только против конкретного стенда и конкретных данных. Статические проверки (док-линт §9.8, структурный TC §9.8.1) стенда не требуют — анализатор работает по артефактам, — и поле к ним не применяется. Смена стенда или датасета инкрементит версию SPEC-TEST и по [§10.5.4](10-lifecycle-qg.md#10.5.4) инвалидирует `verified` у зависимых TC — **точечно**, не задевая всё остальное.

**Подпись клиента.** Объём, анонимизация и допустимость использования реальных данных клиента — предмет **его** решения, а не исполнителя. При `contains-real-client-data: true` или неполной анонимизации `client-signature` обязательна.

### 8.5.11 SPEC-DOC

**Type-specific frontmatter**: `deliverables[]` (по одной записи на документ: `kind` — руководство пользователя / руководство администратора / обучающие материалы / иное; `audience`; `format`; `language`), `required-sections[]` (обязательные разделы каждого документа — **задаются как требование**, а не оставляются на усмотрение исполнителя), `traces-to[]` (BR / SR / SPEC, поведение которых документ описывает), `version-binding` (правило соответствия версии документа версии системы), `acceptance-criteria[]` (по чему документ принимается).

**Обязательное тело**: состав поставки (какие документы и для какой аудитории); структура каждого документа; требования к нему (полнота по ролям и сценариям, заявленным в требованиях; актуальность версии; язык и формат); критерии готовности.

**Spec-specific TC**: **док-линт** ([§9.8](09-test-cases.md#9.8)) — обязателен.

**Почему док-линт обязателен.** Неверифицируемый SPEC не проходит QG-2 ([§9.7](09-test-cases.md#9.7), MVR-5 требует покрытия каждого нормативного утверждения). SPEC-DOC без машинной проверки либо заблокировал бы поставку, либо вынудил проделать дыру в MVR-5 ради одного типа. Поэтому его утверждения нормируются проверяемо: наличие обязательных разделов, покрытие всех ролей и сценариев из связанных BR / SR, совпадение версии документа с версией системы, формат и язык — всё это проверяется машинно (`automation.kind: static`). Содержательное соответствие реализованному поведению («текст не врёт о системе») проверяется опционально judge-агентом через уже существующий механизм P7 / `eval` — новых сущностей не требуется.

---

### 8.5.12 SPEC-UC

**Type-specific frontmatter**: `role` (`human` — путь проходит человек через интерфейс; `agent` — путь проходит агент через API / CLI; поле задаёт канал прогона покрывающих TC), `persona` (ссылка на persona-раздел ADAPT, для `human`), `covers[]` (SR / SPEC, которые сценарий сшивает; не менее двух), `entry` (экран SPEC-UI или операция SPEC-API, с которой путь начинается), `steps[]` (по записи на шаг: `n`, `action`, `ref` — адрес утверждения SR / SPEC `<id>#<n>` ([§15.1.1](15-description-language.md#15.1.1)), которое шаг затрагивает, **обязательно**; `expects` — наблюдаемый результат шага).

**Обязательное тело**: цель сценария и исполнитель (роль, persona); предусловия (состояние системы и данных); основной путь — пронумерованные шаги, каждый со ссылкой на затрагиваемое утверждение ([§8.5.12.1](#8.5.12.1)); альтернативные и негативные ветви (что происходит при отказе на каждом шаге, с той же ссылкой); постусловия; покрывающие TC (какие TC проходят путь шаг за шагом и целиком).

**Spec-specific TC**: для `role: human` — `ux`-TC на каждый шаг и сквозной journey E2E; для `role: agent` — `system` / `contract` по каналу шага ([§9.8](09-test-cases.md#9.8)). Негативные ветви — отдельные TC, обязательные наравне с шагами основного пути ([§9.7](09-test-cases.md#9.7)).

**Почему отдельный тип.** User journey в SPEC-UI и happy path в SPEC-PROC живут внутри документа своего типа и не выходят за его Scope; сценарий использования по определению сшивает несколько SR и SPEC и несёт собственный признак — роль исполнителя, от которой зависит канал прогона: путь человека через интерфейс проверяется `ux`-TC, путь агента через API — `system` / `contract`. Обход интерфейса `system`-TC ради зелёного прогона при неработоспособном интерфейсе — наблюдавшийся отказ, ради которого тип и правило [§9.8](09-test-cases.md#9.8) введены. Тип добавлен в v1.1 по процедуре [§13.9](13-conformance.md#13.9).

#### 8.5.12.1 Ссылка шага на утверждение

Каждый шаг сценария — в SPEC-UC, в user journey SPEC-UI ([§8.5.6](#8.5.6)) и в happy path / альтернативных сценариях SPEC-PROC ([§8.5.5](#8.5.5)) — обязан нести ссылку `ref` на утверждение SR или SPEC, которое он затрагивает, в форме адреса `<id>#<n>` ([§15.1.1](15-description-language.md#15.1.1)); `#<n>` опускается только у артефакта с единственным утверждением. В SPEC-UC ссылка — поле `steps[].ref`; в user journey SPEC-UI и сценариях SPEC-PROC шаги записываются нумерованным списком, и каждый шаг заканчивается ссылкой `→ <id>#<n>` — так проверка структурной полноты находит шаг без ссылки в прозе. Шаг без ссылки — нарушение структурной полноты ([§10.7.2](10-lifecycle-qg.md#10.7.2)); чисто навигационный шаг ссылается на утверждение SPEC-UI об экране или переходе. Ссылка даёт обратный ответ — какие утверждения не затронуты ни одним сценарием — и делает покрытие шага TC проверяемым: TC, покрывающий шаг, указывает в `verifies[]` тот же адрес (`id` + `statement`, [§9.3](09-test-cases.md#9.3)).

---

## 8.6 Связь с требованиями и задачами

### 8.6.1 SR.constrained-by[]

SR в frontmatter получает поле `constrained-by[]` — типизированные ссылки на SPEC. Это **граф**, не дерево родителей. Родитель SR в дереве требований — единственный (BR).

```yaml
# Frontmatter SR (пример)
id: SR-05
parent:
  id: BR-02
constrained-by:
  - SPEC-UI-01
  - SPEC-API-02
  - SPEC-DATA-03
  - SPEC-PROC-01
  - SPEC-SEC-01
verified-by:
  - TC-12
  - TC-13
source:
  adapt: ADAPT-001
  adapt-section: "Forward §3"       # см. канонический идентификатор §8.4
```

### 8.6.2 TR.implements-spec[]

TR (задача) ссылается на SR (родитель в дереве) + одна или более SPEC через `implements-spec[]`:

```yaml
id: TR-42
title: "Реализовать endpoint POST /orders"
parent:
  id: SR-05
implements-spec:
  - SPEC-API-02
  - SPEC-DATA-03
verified-by:
  - TC-14
```

### 8.6.3 SPEC.depends-on[]

SPEC может опираться на другой SPEC:

```yaml
id: SPEC-API-02
title: "REST API заказов"
type: SPEC-API
depends-on:
  - SPEC-DATA-03         # стабильная схема данных
  - SPEC-SEC-01          # auth model для endpoints
```

При изменении upstream SPEC (например SPEC-DATA-03) все downstream (SPEC-API-02 и через него связанные SR) обязаны быть пересмотрены: либо изменение совместимо и downstream входит в новую версию без правки, либо downstream правится в том же черновике; реализации TC, затронутые изменением, устаревают ([§10.9.4](10-lifecycle-qg.md#10.9.4)), и запись верификации новой версии не создаётся до их прогона.

### 8.6.4 Auto-derived обратные рёбра

`SPEC.referenced-by[]` пересчитывается хуком носителя после каждого изменения SR / TR / SPEC. Orphan SPEC (без `referenced-by[]` и без активного status) — предупреждение в отчёте качества.

---

## 8.7 Миграция UIC / AIC / INT-SR / TS → SPEC-*

### 8.7.1 Mapping таблица

| Старый тип | Новый тип | Тип миграции |
|---|---|---|
| `UIC-NN` | `SPEC-UI-NN` | Переименование ID + перенос в `specs/ui/` |
| `AIC-NN` | `SPEC-AI-NN` | Переименование ID + перенос в `specs/ai/` |
| `INT-SR-NN` | `SPEC-INT-NN` | Переименование ID + перенос в `specs/int/` |
| `TS-NN` | `SPEC-<TYPE>-NN` (распределение) | Manual review каждого TS; AI-агент классифицирует содержимое, архитектор утверждает в один клик |

### 8.7.2 Атомарная миграция

Миграция — одна atomic change unit ([V2](03-substrate-versioning.md#3.3.2)) на уровне проекта. Параллельное существование старых типов (UIC / AIC / INT-SR / TS) и SPEC-* как источника истины запрещено.

Процедура (независимо от носителя):

1. Подготовка: AI-агент классифицирует каждый существующий TS-NN в один из 12 типов SPEC.
2. Архитектор утверждает классификацию.
3. Atomic change: переименование IDs (UIC→SPEC-UI; AIC→SPEC-AI; INT-SR→SPEC-INT; TS→SPEC-*), перенос файлов в `specs/<type>/`, обновление всех ссылок в BR / SR / TR / TC frontmatter (`parent: UIC-NN` → `constrained-by: [SPEC-UI-NN]`).
4. Регенерация auto-derived файлов (REQUIREMENTS.md, SPECS.md, обратные рёбра).
5. CI-проверка: отсутствие orphan ссылок и старых IDs.

### 8.7.3 ID immutability

После миграции SPEC ID **неизменяемы** (см. [V1, §3.3.1](03-substrate-versioning.md#3.3.1)). Переименование `SPEC-API-02` → `SPEC-API-08` запрещено. Замена — через `deprecated` + новый ID с `replaces[]`.

---

## 8.8 Контрольные точки качества для SPEC

SPEC собственной машины состояний не имеет: его состояние производно от версии комплекта описания ([глава 10 §10.7](10-lifecycle-qg.md#10.7)). Условия по этапам:

| Этап | Условие |
|---|---|
| черновик | Создан; обязательные поля frontmatter заполняются; присутствует только в черновике комплекта |
| структурная полнота | Обязательные body-разделы (§8.4.1) и type-specific (§8.5) присутствуют — предусловие утверждения версии ([§10.7.2](10-lifecycle-qg.md#10.7.2)) |
| утверждён | Входит в утверждённую версию `N.M`: `depends-on[]` консистентен в той же версии ([§10.7.3](10-lifecycle-qg.md#10.7.3)); подпись Архитектора под версией |
| верифицирован на версии продукта | Все обязательные spec-specific TC ([глава 9 §9.8](09-test-cases.md#9.8)) прошли при `last-run.set-version = N.M` — QG-2 версии ([§10.3.3](10-lifecycle-qg.md#10.3.3)) |
| исключён | Отсутствует в текущей версии; `replaced-by` обязателен в записи версии ([§10.5.3](10-lifecycle-qg.md#10.5.3)) |

Связь с QG-0 / QG-2 SENAR:

- QG-0 (есть goal/AC у задачи) расширяется: для задач реализующих SPEC обязательны `implements-spec[]` в TR frontmatter.
- QG-2 (есть evidence у `done`) расширяется: для задач реализующих SPEC обязательны TC соответствующего spec-specific вида ([глава 9 §9.7](09-test-cases.md)).

---

## 8.9 Схема хранения

### 8.9.1 На уровне системы

```text
[requirements-substrate]/      # корень носителя требований (layout — guide/03 или guide/04)
  br/
  sr/
  specs/
    arch/   SPEC-ARCH-NN-*.md
    api/    SPEC-API-NN-*.md
    data/   SPEC-DATA-NN-*.md
    ui/     SPEC-UI-NN-*.md
    ai/     SPEC-AI-NN-*.md
    int/    SPEC-INT-NN-*.md
    proc/   SPEC-PROC-NN-*.md
    sec/    SPEC-SEC-NN-*.md
    ops/     SPEC-OPS-NN-*.md
    test/    SPEC-TEST-NN-*.md
    doc/     SPEC-DOC-NN-*.md
  adapt/
  tz/
  SPECS.md                # auto-generated index
```

> Все 12 типов SPEC ([§8.3](#8.3)) допустимы на любом `level`; подпапки `specs/<type>/` создаются по мере необходимости — не все обязательны на уровне системы.

### 8.9.2 На уровне подсистемы

```text
[subsystem-substrate]/         # scope подсистемы
  br/                     # если своя бизнес-сторона
  sr/
  specs/
    arch/   SPEC-ARCH-NN-*.md      # архитектура подсистемы
    api/    SPEC-API-NN-*.md
    data/   SPEC-DATA-NN-*.md
    ui/     SPEC-UI-NN-*.md
    ai/     SPEC-AI-NN-*.md
    int/    SPEC-INT-NN-*.md
    proc/   SPEC-PROC-NN-*.md
    sec/    SPEC-SEC-NN-*.md
    ops/     SPEC-OPS-NN-*.md
    test/    SPEC-TEST-NN-*.md
    doc/     SPEC-DOC-NN-*.md
  adapt/
  SPECS.md
```

Нативная для носителя реализация хранения специфична для носителя (см. [guide/03](../guide/03-tool-guide-git.md), [guide/04](../guide/04-document-store-substrate.md)).

### 8.9.3 SPECS.md — auto-generated index

`SPECS.md` — auto-generated реестр всех SPEC: ID, тип, заголовок, статус, ссылка на верифицируемое требование, ссылка на файл. Помечается `linguist-generated=true`. Триггеры перегенерации — каждое изменение SPEC frontmatter или каждый approve / verify gate.

---

## 8.10 Связь с другими главами

| Глава | Связь |
|---|---|
| [02 Позиционирование методологии](02-methodology-positioning.md) | SPEC как параллельная ось — следствие инверсии источника истины |
| [06 Иерархия требований](06-requirements-hierarchy.md) | `SR.constrained-by[]`, `TR.implements-spec[]` |
| [07 ADAPT](07-adapt.md) | SPEC ссылается на ADAPT через `source.adapt` |
| [09 Тест-кейсы](09-test-cases.md) | Spec-specific TC types (таблица обязательных видов TC для каждого типа SPEC) |
| [10 Жизненный цикл и QG](10-lifecycle-qg.md) | Состояние SPEC — производное от версии комплекта (§10.7); предусловия по типам SPEC при утверждении версии (§10.7.3) |
| [03 Версионирование носителя](03-substrate-versioning.md) | SPEC ID неизменяемы (V1); migration атомарно (V2) |
| [11 Модель зрелости](11-maturity-model.md) | RENAR-3+: все 12 типов SPEC где применимо |
| [reference/02 — schemas](../reference/02-schemas.md) | Полная machine-readable schema для каждого type-specific extension |
| [reference/05 — knowledge graph schema](../reference/05-knowledge-graph-schema.md) | `constrained-by[]`, `implements-spec[]`, `depends-on[]` как edge types в графе |

