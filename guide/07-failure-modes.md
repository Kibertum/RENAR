---
title: "Режимы отказа"
description: "8 классов дрейфа, 14 AI-рисков, organizational failure patterns. Для каждого: симптом, detection, prevention, recovery."
order: 7
lang: ru
version: "1.0"
---

# 07. Режимы отказа

> Систематический обзор всех известных способов, которыми RENAR-проект может выйти из строя: технический дрейф между артефактами и реализацией, AI-специфичные риски, и (главное) организационные паттерны, при которых процесс существует на бумаге, но не работает. Классы дрейфа нормированы в [standard/00 §0.3](../standard/00-introduction.md#0.3); AI-риски — в [reference/03](../reference/03-ai-risk-register.md). Для каждого failure mode — симптом, как обнаружить, как предотвратить, как восстановиться.
>
> **Предпосылки:** [RENAR Core](../core/renar-core.md), [reference/03-ai-risk-register.md](../reference/03-ai-risk-register.md).

---

## 1. Карта failure modes

Три класса проблем:

| Класс | Где живёт | Как обнаруживается |
|---|---|---|
| **Drift** | Несоответствие между разными представлениями одной сущности (frontmatter ↔ DB, требование ↔ код, TC ↔ требование) | Reconciliation hook (drift detection, [§4.11](../standard/04-terms.md#4.11)) |
| **AI risks** | Свойства AI-генерации (hallucination, bias, injection, model drift) | Adversarial review + eval-тесты + AI risk register ([reference/03](../reference/03-ai-risk-register.md)) |
| **Organizational** | Несоответствие между формальным процессом и реальными практиками команды | Поведенческие сигналы: паттерн утверждений, частота споров, частота обхода |

Drift и AI risks ловятся механизмами носителя. Organizational — никаким носителем не ловятся; нужны human-уровневые рецензии процесса. Эта глава покрывает все три.

---

## 2. 8 классов дрейфа

Для каждого: симптом (как выглядит со стороны), detection (как обнаружить автоматически), prevention (как не допустить), recovery (что делать если уже случилось).

### 2.1 Schema drift

**Симптом:** Поля во frontmatter артефакта расходятся с теми, что носитель ожидает / поддерживает.

**Обнаружение:** На каждое изменение артефакта носитель валидирует frontmatter по schema ([reference/02-schemas.md](../reference/02-schemas.md)). При расхождении — блок integration (QG-0 фейлит).

**Предотвращение:** Schema — single source of truth (closed list), не редактируется на проекте. Изменения schema — только через изменение полного RENAR Standard.

**Восстановление:** Откатить frontmatter к schema-valid состоянию; если изменение нужно — открыть RFC на изменение стандарта.

### 2.2 Lifecycle drift

**Симптом:** Состояния (черновик / версия комплекта `N.M` / статусы TR и ADAPT) и названия контрольных точек качества понимаются по-разному в разных подсистемах или у разных команд.

**Обнаружение:** Сравнить переходы статусов в журнале аудита с нормативной state machine ([standard/10-lifecycle-qg](../standard/10-lifecycle-qg.md)). Аномалии (переход без соответствующих pre-conditions) — флаг.

**Предотвращение:** Переходы выполняются механизмом носителя, не ручной правкой frontmatter. Capability V3 (state machine enforcement).

**Восстановление:** Откатить нелегитимный переход; провести QG-0 / QG-2 заново через корректный механизм.

### 2.3 Source-of-truth drift

**Симптом:** Одна и та же сущность редактируется в двух местах (например, и в `.req` директории, и в Jira-трекере). Версии расходятся.

**Обнаружение:** Periodic reconciliation между носителем и tracker; diff показывает расхождения.

**Предотвращение:** В каждый момент времени для проекта **выбран ровно один SSoT-носитель**. Tracker — derived view, не источник истины. Hook носителя блокирует tracker-only изменения требований.

**Восстановление:** Объявить один носитель-winner; смерджить второй в первый; убрать редактирование во второй до миграции.

### 2.4 Implementation drift

**Симптом:** Код в реализации ссылается на SR, которого больше нет (deprecated, удалён, переименован). Или: SR существует, но реализация ушла от него (поведение не соответствует).

**Обнаружение:** Reconciliation hook (drift detection):
- Forward: пройти по requirement → найти реализующий код → запустить TC.
- Backward: пройти по коду → найти ссылки на SR / TC → проверить, что они входят в верифицированную версию комплекта.

**Предотвращение:** ID требований **неизменяемы** — переименование запрещено. Deprecated требования остаются в репозитории со статусом `deprecated`, не удаляются.

**Восстановление:** Открыть delta-ТЗ, который явно adopts текущую реализацию (или, наоборот, требует откат кода до соответствия требованию).

### 2.5 Terminological drift

**Симптом:** «Verified», «implemented», «approved» означают разное у разных людей / команд.

**Обнаружение:** Code review checklist: «использован термин не из глоссария?» — флаг. Аналогично — валидатор носителя проверяет, что значения enum-полей frontmatter только из closed list.

**Предотвращение:** Глоссарий — единственный источник терминов ([reference/01-glossary](../reference/01-glossary.md)). Каждый термин = ровно одно состояние lifecycle.

**Восстановление:** Провести ревизию всех артефактов проекта на использование out-of-glossary терминов; заменить или подать RFC на расширение глоссария.

### 2.6 Order / provenance drift

**Симптом:** Delta-ТЗ #2 ссылается на SR, который был создан в Delta-ТЗ #1, но применение пошло в обратном порядке — SR не существовал на момент применения #2.

**Обнаружение:** Delta-ТЗ нумеруются и применяются строго в порядке номеров. Hook носителя проверяет, что upstream delta уже применена.

**Предотвращение:** Delta-ТЗ нельзя перенумеровать. Каждый артефакт хранит `created-by-order` (delta-ТЗ создания) и `last-modified-by-order` (последний апдейт).

**Восстановление:** Откатить out-of-order применение; перепримерить в правильном порядке.

### 2.7 TC ↔ requirement provenance drift

**Симптом:** TC верифицирует требование, но норма уже изменена в новой версии комплекта — `automation.set-version` реализации ниже версии, в которой норма изменена. Тест зелёный, но проверяет устаревшее поведение.

**Обнаружение:** Coverage report показывает категорию «Stale» — реализации с устаревшим `automation.set-version` ([standard/10 §10.9.4](../standard/10-lifecycle-qg.md#10.9.4)). Reconciliation ловит это автоматически (по V5-pin версии комплекта).

**Предотвращение:** В TC обязательное поле `automation.set-version` — версия комплекта, против которой написана реализация. QG-2 не записывает верификацию версии, если хотя бы одна реализация устарела или её `last-run.set-version` не равен версии.

**Восстановление:** Перепрогнать stale TC на текущей версии требования; обновить, если TC сам устарел.

### 2.8 Test-fitting drift

**Симптом:** AI-агент имеет тривиальный путь зеленения failing-теста — ослабить Pass/Fail-критерий вместо исправления кода. Без защиты тесты дрейфуют от «строгий проверщик» к «зелёная пустота».

**Обнаружение:** Изменение Pass/Fail-критериев в TC без явного `[test-spec-change]` тега — флаг носителя. Periodic spot-check 5 случайных passing TC раз в спринт.

**Предотвращение:**
- MR / change, изменяющий Pass/Fail-критерии, обязан иметь тег `[test-spec-change]` и отдельный approval инженера (не совмещённый с approval кода-фикса).
- Изоляция judge-модели: production-модель ≠ judge-модель.
- Метрика test-fitting drift rate trending.

**Восстановление:** Восстановить старые критерии; провести root cause analysis — почему AI-агент выбрал зеленение вместо фикса; обновить prompt / system instructions.

---

## 3. 14 AI-рисков (краткая сводка)

Полные описания, mitigations и owner — в [reference/03-ai-risk-register](../reference/03-ai-risk-register.md). Здесь — operational summary: id, название, severity, главный detection signal.

| ID | Название | Severity | Detection signal |
|---|---|---|---|
| AIR-01 | Hallucination в AI-генерируемых требованиях | High | Hallucination Rate metric > threshold; adversarial critic flags |
| AIR-02 | Prompt injection через ТЗ от клиента | High | Suspicious pattern в imports; sandbox violation |
| AIR-03 | Model drift / version change | Medium | diff regression при смене модели; сбой эталона eval |
| AIR-04 | Bias в AI-генерации требований | Medium | Stakeholder map gaps; missing accessibility/locale considerations |
| AIR-05 | Single-model failure (no diversity) | Medium | Все артефакты с одним `ai-provenance.model`; нет multi-model agreement |
| AIR-06 | Test-fitting / зеленение тестов | High | diff в TC Pass/Fail без `[test-spec-change]` тега |
| AIR-07 | Hallucinated citations | Medium-High | Citation validator hook fails |
| AIR-08 | Adversarial inputs в client data | High | Application-level (out-of-scope RENAR, tracked в SPEC-SEC) |
| AIR-09 | Privacy leakage через AI logs | High | PII в tool_event audit; redaction скип |
| AIR-10 | Knowledge graph poisoning | Medium | Incorrect edges; circular dependencies в graph |
| AIR-11 | Reconciliation false positive overload | Low-Medium | Findings/week trending up без real issues; dismissal rate высокий |
| AIR-12 | Cost runaway (uncontrolled AI spend) | Medium | Project AI cost approaching budget cap |
| AIR-13 | Stakeholder не понимает AI-сгенерированные требования | Medium | Dispute rate at acceptance растёт; длинные циклы утверждения |
| AIR-14 | Vendor lock-in to specific LLM provider | Medium | All prompts работают только на одном provider |

Risk matrix и периодичность рецензирования — [reference/03 §5-§2](../reference/03-ai-risk-register.md).

### 3.5 Adversarial review (процедура)

> **Информативно.** Операционная процедура для WC-13; нормативные требования — [standard/09 §9.4](../standard/09-test-cases.md#9.4), [standard/13 §13.2](../standard/13-conformance.md#13.2) (RENAR-5).

**Когда обязательно (normative):** adversarial review — QG-0 для RENAR-5 ([§11.8.1](../standard/11-maturity-model.md#11.8.1)); для `SPEC-SEC` / `SPEC-AI` — external reviewer на QG-0 ([§5](../standard/05-roles.md)); declared-stricter может расширить scope ([standard/00 §0.6](../standard/00-introduction.md#0.6)).

| Шаг | Актор | Артефакт | Exit criterion |
|---|---|---|---|
| 1. Scope | Architect | Список TC + связанные SR/SPEC | Каждая TC-норма утверждённой версии в scope имеет `tc-type` и `verifies[]` |
| 2. Critic pass | AI-критик (отдельная модель/промпт) | Журнал находок с id, severity, ссылкой на TC/SR | Находки прослеживаемы к конкретному clause §9.x; без «общих» рекомендаций |
| 3. Triage | Architect + RE engineer | Disposition: fix / accept / reject | Каждый finding — owner + rationale; dismiss без rationale запрещён (см. §4.6) |
| 4. Re-run | AI-агент или human | Обновлённые TC + diff | QG-2 pre-condition: `passing-tests / total-tests` для scope ([§9.10](../standard/09-test-cases.md#9.10)) |
| 5. Журнал аудита | носитель (V1) | commit/change unit с `adversarial-review` tag | provenance: model id, prompt version, findings hash ([§10.13](../standard/10-lifecycle-qg.md#10.13)) |

**Дисциплина утверждений:** метрики «100%» в §9 — это **target при QG-2**, не гарантия качества продукта. Severity AI-рисков — из [reference/03](../reference/03-ai-risk-register.md), не редакторское переопределение.

**Agent panel (без human reviewers):** informative процедура — [§3.5](#35-adversarial-review-процедура) (шаги 1–5); rubric и severity — [reference/03](../reference/03-ai-risk-register.md).

---

## 4. Organizational failure patterns

Эти проблемы не ловятся механизмами носителя — это поведенческие паттерны команд. Появляются типично через 2-6 месяцев после внедрения RENAR.

### 4.1 ACTZ как формальность

**Симптом:** Протокол уточнения ТЗ подписывается не глядя. Вопросы вынесены клиенту сырым выводом агента — списком на три страницы, из которого клиент не понимает, за что расписывается. Backward-секция ADAPT пустая или содержит yes/no без контекста.

**Признак:** ACTZ подписан за < 24 часов после генерации черновика; доля спорных требований на приёмке растёт.

**Смягчение:** Клиенту выносится **подготовленный** список решений, а не сырой вывод агента ([standard/07 §7.13.4](../standard/07-adapt.md#7.13)): вопросы агрегирует и переформулирует архитектор, на языке обязательств. Двусторонняя подпись — у ACTZ, и только у него; ADAPT подписывает один архитектор ([§7.5](../standard/07-adapt.md#7.5)), и требовать под ним подпись клиента бессмысленно — это и была та фикция, которая порождала «подписал не читая». Backward-секция обязана содержать ≥ 1 не риторический вопрос. Spot-check ACTZ и ADAPT в I&A.

### 4.2 SPEC overload

**Симптом:** Команда создаёт SPEC для каждой задачи, даже когда SR + TR достаточно. SPEC-каталог разрастается; каждый PR обновляет 5+ SPEC.

**Признак:** Rate SPEC / SR > 1.5 (ожидаемое < 0.3 для проектов средней сложности).

**Смягчение:** Pre-review checklist: «нужен ли SPEC для этого изменения?» SPEC оправдан только когда есть несколько SR с общим constraint. См. [standard/08-specifications.md](../standard/08-specifications.md) §8.2 — когда SPEC обязателен.

### 4.3 Hooks как препятствие

**Симптом:** Команда регулярно обходит hooks носителя (`--no-verify`, манипуляции с timestamp, ручная правка statuses).

**Признак:** Git log / журнал аудита носителя показывает частоту обхода commits; QG-0/QG-2 проходят за подозрительно короткое время.

**Смягчение:** Root cause — hooks слишком медленные / слишком noisy / слишком жёсткие. Не «запретить обход», а починить hooks. Метрика частоты обхода как trending — если растёт, провести retro с командой.

### 4.4 Drift detection без действия

**Симптом:** reconciliation hook генерирует находки дрейфа, но никто на них не реагирует. Бэклог находок растёт, старые находки игнорируются.

**Признак:** Находки старше 14 дней > 30; resolution rate < 20% / неделю.

**Смягчение:** Каждая находка дрейфа — owner и SLA (resolve / accept / reject в течение N дней). Неразрешённые находки выше SLA — escalation. Reconciliation без human ownership = шум.

### 4.5 tracker as parallel universe

**Симптом:** Команда живёт в Jira / Linear / ADO; `.req` директория обновляется раз в неделю «для галочки». Tracker — реальный источник истины, RENAR — формальный артефакт для аудита.

**Признак:** diff `.req` vs tracker > 30% по any given week; commits в `.req` редкие и батчевые.

**Смягчение:** Single source of truth должен быть размещён в носителе, не tracker-resident. Tracker — derived view только. Если команда не может работать без tracker — носитель должен пушить *в* tracker, не наоборот.

### 4.6 Critic burnout

**Симптом:** AI-критик (adversarial review) генерирует много находок; постепенно дев / архитектор начинают игнорировать его output. Находки отклоняются без рассмотрения.

**Признак:** Dismissal rate AI-критика > 80%; time-to-dismiss < 30 секунд per finding.

**Смягчение:** Tunable thresholds для критика. Если ratio false positive высокий — recalibrate prompt / model. Метрика «находки критика → real issue» (% от отклонённых находок, которые потом всплыли как defect) — если 0%, критик useless.

### 4.7 Single-engineer dependence

**Симптом:** Только один инженер на проекте «понимает RENAR». Все QG-0 / QG-2 проходят через него. Если он в отпуске — процесс встаёт.

**Признак:** Bus factor RENAR-владения = 1. Distribution of QG approvals heavily skewed к одному человеку.

**Смягчение:** Парный onboarding (минимум 2 инженера на проекте умеют RENAR). Rotation QG-approver роли. Documentation проектных конвенций в `<project>.req/CONVENTIONS.md`.

### 4.8 Ad-hoc delta

**Симптом:** Изменения требований происходят без оформления delta-ТЗ — «давай просто поменяем SR-12 прямо в репозитории».

**Признак:** Direct commits в `<system>.req/sr/*` без соответствующего delta-ТЗ; `created-by-order` поле пустое.

**Смягчение:** hook носителя блокирует mutation существующих требований без `delta-ref` в commit metadata. Все изменения — через delta-ТЗ workflow ([standard/07-adapt §7.6](../standard/07-adapt.md)).

### 4.9 TC abandonment

**Симптом:** TC создаются вместе с требованиями, но затем не прогоняются. `last-run` старше N месяцев; coverage report показывает «зелёные» TC, которые в реальности никогда не запускались за полгода.

**Признак:** Median `last-run` age > 90 дней; TC count растёт, run count не растёт.

**Смягчение:** носитель автоматически прогоняет TC по schedule (capability V4). Реализации без `last-run` за N дней автоматически помечаются устаревшими; QG-2 блокирует запись верификации, пока не перепрогнаны.

### 4.10 Тест, рождённый зелёным

**Симптом:** TC написан тем же агентом и в той же сессии, что и реализация, — и с рождения зелёный. Формально покрытие есть; фактически тест проверяет код, а не требование.

**Признак:** В истории прогонов TC нет ни одного красного результата; провенанс TC совпадает с провенансом единицы изменения реализации.

**Смягчение:** изоляция авторства по трём осям — время (TC-норма утверждена в версии и реализация допущена до старта TR), автор (агент теста ≠ агент реализации) и изменение (правка Pass/Fail только через `[test-spec-change]`), [standard/09 §9.18](../standard/09-test-cases.md#9.18). Фиксирующий прогон до реализации обязан быть красным; зелёный на нём — сигнал разбора, а не успех. Единственное исключение — TC класса `implementation-originated`, у которого красной истории не бывает по построению: там зубастость доказывается убитым мутантом.

### 4.11 Приёмка, подтверждающая саму себя

**Симптом:** AT сгенерированы агентом, который «для контекста» получил доступ к SR и ADAPT — или просто той же моделью, что писала реализацию. Все AT зелёные, приёмка у заказчика проваливается.

**Признак:** `generator` у AT не подтверждает изоляцию; либо `tz-version` у AT отстаёт от действующей редакции итогового ТЗ.

**Смягчение:** AT выводятся **только** из итогового ТЗ, изолированным агентом на другой модели, без доступа к внутреннему контуру ([standard/09 §9.19.2](../standard/09-test-cases.md#9.19)) — иначе уровень приёмки схлопывается в уровень верификации и ошибка толкования получает ложное подтверждение соответствия. AT перегенерируются перед **каждыми** испытаниями: программа, выведенная при планировании, к концу длинного заказа проверяет систему против контракта годичной давности.

---

## 5. Failure recovery playbook

Что делать, когда система уже сломана. Последовательность общая для всех failure modes; specifics зависят от класса.

### Шаг 1: Stop the bleeding

Найти и остановить ongoing damage:
- Drift: заморозить дальнейшие изменения в затронутой области.
- AI risk: приостановить AI-генерацию для затронутого класса артефактов.
- Organizational: вынести на retro / I&A — это не technical fix.

### Шаг 2: Quantify

Измерить ущерб:
- Сколько артефактов в drift-состоянии?
- Сколько релизов с момента возникновения проблемы?
- Какие SR / SPEC / TC затронуты? (Capability V4 — coverage / drift report)

### Шаг 3: Triage

Сегментировать ущерб на:
- **Critical** — уже в production, влияет на пользователей. Hot-fix.
- **Active** — в текущем PI, влияет на ongoing work. Block PI exit.
- **Historical** — старые артефакты, не активно используются. Batch fix.

### Шаг 4: Fix

Для каждого класса — соответствующий fix:
- Schema drift → откат frontmatter; RFC если schema нужно расширить.
- Implementation drift → delta-ТЗ adopt OR откат кода.
- TC drift → пересоздать реализацию против текущей версии комплекта (`automation.set-version`).
- Test-fitting → revert критерии; root cause AI-агента.
- Organizational → process retro + специфичные mitigations (§4).

### Шаг 5: Prevent recurrence

- Усилить detection (нижний threshold, новая metric).
- Добавить mitigation в processed артефакт.
- Зафиксировать lessons learned в project decision log или ADAPT backward findings (категория `scope` / `terminology`).

### Шаг 6: Verify

После fix — пройти QG-2 на затронутых артефактах заново. Drift detection должна показать clean state.

---

## 6. Negative: чего эта глава не покрывает

- **Security incidents** — breach response, forensics, regulatory notification. Это процесс уровня organization security, не RENAR scope.
- **AI red team / penetration testing** — отдельный security workflow; RENAR трекает только что соответствующие SR / SPEC-SEC должны быть.
- **Compliance breach response** — нарушение GDPR / ФЗ-152 / PCI-DSS требует юридического процесса с DPO / regulator, не technical recovery.
- **Production incidents** — outages, performance regressions. Это operational, см. SPEC-OPS runbook.
- **Stakeholder conflicts** — диспуты на acceptance, scope disagreements. RENAR даёт журнал аудита (кто что approved когда), но resolution — human process.

---

## 7. Связь с другими материалами о режимах отказа

| Документ | Что в нём | Когда читать |
|---|---|---|
| [reference/03-ai-risk-register](../reference/03-ai-risk-register.md) | Полный реестр 14 AIR-рисков с mitigations | При планировании AI use-case; при review eval-стратегии |
| [standard/04-terms §4.11](../standard/04-terms.md#4.11) | Closed list drift классов с нормативными определениями | При спорах о терминологии failure modes |
| [05-safe-comparison §9](05-safe-comparison.md) | RACI matrix — кто accountable за каждую активность | При расследовании organizational failure |
| [reference/04-ai-style-guide](../reference/04-ai-style-guide.md) | Стиль AI-провенанса; минимальный контракт для AI-сгенерированных артефактов | При диагностике AIR-01 (hallucination), AIR-07 (citations) |

---

## 8. Resolved decisions для v1.0

- **Набор шагов восстановления без привязки к платформе.** Последовательность из §5 носит универсальный характер. Детали «как именно заморозить изменения» для `git` и document-store — в [03-tool-guide-git §3](03-tool-guide-git.md) и [04-document-store-substrate](04-document-store-substrate.md). Объём нормативного минимума задан здесь же, в главе 7.
- **Подстройка критика событийно.** Повторная настройка промпта критика выполняется при выходе за порог метрик drift / галлюцинаций ([§12.3.3](../standard/12-metrics.md#12.3)); уровень RENAR-5 требует непрерывной оценки ([§11.8.1](../standard/11-maturity-model.md#11.8.1)), поэтому регулярный «общий пересмотр без причины» избыточен. По срабатыванию метрик — допустимо.

### 8.1 Отложено на v1.1 (бэклог фазы 8)

- **Числовые пороги для организационных паттернов (§4).** Сейчас даны качественные «признаки». Набор допустимых значений понадобится после накопления полевых данных. Ответственные: команда стандарта RENAR и внедренческие организации.
- **Формальный замер коэффициента «техавтобусности» для §4.7.** Вспомогательные средства не зафиксированы; возможный подход — графовый запрос по авторам коммитов в цепочке ревизий (встроенное в носитель сочетание **V6**). Ответственные: авторы средств для конкретных сред хранения.
- **Калибровка provisional-порогов метрик для RENAR-4 / RENAR-5.** Целевые значения [§12.4](../standard/12-metrics.md#12.4) заданы как ориентиры направления и подлежат уточнению, когда по метрике накопятся данные от пяти независимых организаций при завершённом цикле у каждой; до уточнения действуют как есть, но превышение неоткалиброванного порога само по себе соответствие не снимает. Условие выполненным объявляет только процедура изменения стандарта ([§13.9](../standard/13-conformance.md#13.9)) публикацией отчёта о калибровке — не отдельное лицо и не внедренческая организация.

---
