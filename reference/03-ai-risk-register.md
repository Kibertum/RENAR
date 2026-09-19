---
title: "Реестр AI-рисков"
description: "Реестр AI-рисков для RENAR-проектов: 14 канонических рисков с мерами смягчения."
order: 3
lang: ru
version: "1.0"
---

# AI Risk Register для RENAR-проектов

> **Назначение:** реестр AI-специфичных рисков для проектов, использующих RENAR (где AI генерирует требования, спецификации и тесты). Основан на ISO/IEC 23894:2023 (AI Risk Management, Annex A risk sources + Clause 6 process по ISO 31000) и NIST AI RMF 1.0. Нормативные mitigation hooks — [standard/09 §9.13](../standard/09-test-cases.md#9.13), [standard/07 §7.10](../standard/07-adapt.md#7.10).

Не подменяет общий security risk register организации. AI-риски — отдельный класс из-за специфики генерации и непредсказуемости моделей.

---

## 1. Структура реестра

Каждый риск имеет:

```yaml
id: AIR-NN                           # immutable
name: "<short name>"
category: hallucination | injection | drift | bias | sgnl-failure | data-quality | adversarial | privacy
# enum-значение — часть до скобки; уточнитель в скобках опционален (напр. "sgnl-failure (process)")
severity: critical | high | medium | low
likelihood: high | medium | low
iso-23894-ref: "§N.N"
nist-rmf-function: govern | map | measure | manage
mitigations:
  - { mechanism: "<description>", enforced-by: "<who/what>", automated: true | false }
status: active | mitigated | accepted | monitoring | out-of-scope
owner: "@role"
last-reviewed: "YYYY-MM-DD"
related: ["<core rule N>", "<standard chapter>", "<other AIR-NN>"]
```

Список AIR-01..AIR-14 закрыт; новые риски — только через изменение полного RENAR Standard.

**Category ↔ NIST AI RMF trustworthiness characteristics** (для проверяемости заявления «основан на NIST AI RMF»):

| `category` | NIST AI RMF trustworthiness characteristic |
|---|---|
| `hallucination` / `data-quality` | Valid & Reliable |
| `injection` / `adversarial` | Secure & Resilient |
| `drift` | Valid & Reliable; Safe |
| `bias` | Fair — with Harmful Bias Managed |
| `sgnl-failure` | Safe; Accountable & Transparent |
| `privacy` | Privacy-Enhanced |

> **Ссылки ISO/IEC 23894:2023.** Категории AI-рисков в 23894:2023 расположены в **Annex A** (risk sources); Clause 6 описывает процесс риск-менеджмента (по ISO 31000). Дескрипторы «Annex A — …» в реестре — risk-source labels; точное сопоставление с пунктами Annex A подлежит сверке при формальном claim.

---

## 2. Реестр AIR-01..AIR-14

Метаданные всех 14 рисков (Sev=Severity, Like=Likelihood):

| AIR | Name | Category | Sev | Like | ISO 23894 (Annex A) | NIST RMF | Status |
|---|---|---|---|---|---|---|---|
| 01 | Hallucination в AI-генерируемых требованиях | hallucination | **High** | High | Output reliability | Measure | active → mitigated на зрелых RENAR levels |
| 02 | Prompt injection через ТЗ от клиента | injection | **High** | Low-Medium | Adversarial inputs | Manage | monitoring |
| 03 | Model drift / version change | drift | Medium | High | Model жизненный цикл | Manage | monitoring |
| 04 | Bias в AI-генерации требований | bias | Medium | Medium | Fairness | Measure | active |
| 05 | Single-model failure (no diversity) | sgnl-failure | Medium | High | Single point of failure | Manage | mitigated при полном pipeline |
| 06 | Test fitting / зеленение тестов | sgnl-failure | **High** | Medium | Verification integrity | Measure | mitigated при выборочная проверка |
| 07 | Hallucinated citations | hallucination | Medium-High | Medium | Output reliability | Measure | monitoring |
| 08 | Adversarial inputs в clients data (runtime) | adversarial | **High** | Low | Adversarial inputs | Manage | out-of-scope (application-level) |
| 09 | Privacy leakage через AI logs | privacy | **High** | Medium | Privacy | Govern | active |
| 10 | Knowledge graph poisoning | data-quality | Medium | Low | Data integrity | Map | monitoring |
| 11 | Reconciliation false-positive overload | sgnl-failure (process) | Low-Medium | Medium | Verification integrity | Manage | monitoring |
| 12 | Cost runaway (uncontrolled AI spend) | sgnl-failure (operational) | Medium | Medium | Cost governance | Manage | active |
| 13 | Стейкхолдер не понимает AI-сгенерированные требования | data-quality (UX) | Medium | Medium | Transparency | Govern | active |
| 14 | Vendor lock-in to specific LLM provider | sgnl-failure (operational) | Medium | Low-Medium | Vendor risk | Govern | monitoring |

Описание + воздействие + mitigations — ниже.

### AIR-01. Hallucination в AI-генерируемых требованиях

AI-агент при генерации BR/SR/SPEC может «дописать от себя» утверждения, которых нет в ADAPT или ТЗ. Воздействие: scope creep, dispute на acceptance, фичи которые не требовались клиентом.
**Меры смягчения:** source citation ([standard/04 §4.10.2](../standard/04-terms.md#4.10.2) — каждое утверждение ссылается на ADAPT §N, а когда ADAPT не создавался ([standard/07 §7.4.1](../standard/07-adapt.md#7.4.1)) — напрямую на раздел ТЗ через `source.tz-section` + `source.adversarial-review-ref`); состязательный обзор (критик-модель другого vendor); метрика Hallucination Rate ≤1% на зрелых уровнях RENAR.

### AIR-02. Prompt injection через ТЗ от клиента

Злонамеренный клиент может вставить в ТЗ скрытые инструкции для AI («ignore previous instructions and …»). Воздействие: утечка данных, вредоносные изменения в требованиях, нарушение security policy.
**Меры смягчения:** sandboxing AI-агента при импорте (модель работает с ТЗ как с пассивными данными, явно в system prompt); input gateway проверяет на known injection patterns; suspicious pattern → escalation, не auto-process.

### AIR-03. Model drift / version change

Anthropic / OpenAI / Google обновляют модели — тот же prompt с тем же ТЗ через 6 месяцев может дать другой output. Воздействие: inconsistency между требованиями в одном проекте; невозможность точно воспроизвести генерацию старого артефакта.
**Меры смягчения:** model versioning в `ai-provenance.generated-by` (точная версия + дата); eval-тесты для SPEC-AI прогоняются при смене модели; при регенерации — diff против старой версии и оценка изменений.

### AIR-04. Bias в AI-генерации требований

Модель имеет training-data bias — при генерации BR может игнорировать stakeholders определённых групп (accessibility users, non-English locales, специфичные регуляторики). Воздействие: требования не покрывают весь spectrum пользователей; продукт дискриминационный или non-compliant.
**Меры смягчения:** multi-model agreement для `priority=must` BR (разные модели — разные biases); карта заинтересованных сторон обязательна в BR (explicit перечисление); состязательный критик с prompt «check for missing stakeholders / accessibility considerations».

### AIR-05. Single-model failure (no diversity)

Если все артефакты генерируются одной моделью, её ошибки систематически проникают. «Галлюцинирует» определённый паттерн — все требования это унаследуют. Воздействие: систематическое искажение требований по проекту.
**Меры смягчения:** multi-model для `priority=must` BR; состязательный критик с другой моделью; изоляция judge-модели от production-модели в eval-тестах (SPEC-AI: `judge-model.vendor ≠ production-model.vendor`, см. [02-schemas.md §6.2](02-schemas.md#62-spec-ui-spec-ai-spec-sec-spec-ops)).

### AIR-06. Test fitting / зеленение тестов

AI-агент имеет тривиальный путь зеленения failing-теста — ослабить Pass-критерий. Это проходит code review, поскольку «тест зелёный». Воздействие: false confidence; defects проходят в production.
**Меры смягчения:** маркер `[test-spec-change]` обязателен для изменения Pass/Fail (отдельный approval); выборочная проверка 5 passing TC раз в итерацию ([standard/09 §9.14](../standard/09-test-cases.md#9.14)); метрика Test-spec Drift Rate ([standard/12 §12.3](../standard/12-metrics.md#12.3)).

### AIR-07. Hallucinated citations

AI-агент пишет citation `[TZ-2026-001 §4 line 142]`, но в реальном ТЗ §4 line 142 — про другое. Citation выглядит как свидетельство, но свидетельство ложное. Воздействие: source citation становится фикцией; цепочка прослеживаемости рвётся при аудите.
**Меры смягчения:** citation validator hook (парсит citation, открывает указанный документ, проверяет соответствие); pre-commit/pre-approval блокировка при невалидной citation.

### AIR-08. Adversarial inputs в clients data (runtime)

Клиент отправляет данные (через формы, API), специально сконструированные для манипуляции AI-компонент в runtime (не на этапе генерации требований). Воздействие: аналогично AIR-02, но в production runtime.
**Меры смягчения:** input sanitization на API gateway; constrained generation (structured outputs only); rate limiting per user. **Status: out-of-scope** — application-level security; RENAR требует SR-уровень покрытия (SPEC-SEC threat model), но runtime защита — задача реализации, не нормирования требований.

### AIR-09. Privacy leakage через AI logs

AI-агент при генерации артефакта имеет в контексте PII (из ТЗ или интервью). Логи генерации (tool events, audit records) могут хранить эти PII. Воздействие: PII попадают в логи, в `ai-provenance`, в training data (если используется).
**Меры смягчения:** PII redaction в промптах перед отправкой в LLM; `data-classification` tracking; disable training on conversations (Anthropic/OpenAI privacy settings, DPA); TTL на event logs с PII.

### AIR-10. Knowledge graph poisoning

Если KG используется как primary search для AI-агентов, incorrect edge может «отравить» все последующие AI-запросы, опирающиеся на этот граф. Воздействие: AI генерирует требования на основе wrong context, систематически.
**Меры смягчения:** KG derived от frontmatter, не редактируется напрямую (см. [05-knowledge-graph-schema.md](05-knowledge-graph-schema.md)); CI-валидация графа на каждое изменение (нет circular dependencies, no orphan approved); reconciliation-агент проверяет integrity еженедельно.

### AIR-11. Reconciliation false-positive overload

Reconciliation-агент при слишком чувствительных правилах генерирует много false-positive находок. Архитектор начинает их игнорировать → реальные находки тонут. Воздействие: reconciliation теряет ценность, дисциплина не масштабируется.
**Меры смягчения:** tunable thresholds в проектной конфигурации; метрика Reconciliation Findings/Week (если растёт без real issues — re-calibration); архитектор может отклонять находки с обоснованием — feedback для tuning prompt агента.

### AIR-12. Cost runaway (uncontrolled AI spend)

Без budget tracking AI-генерация (особенно с multi-model, состязательный, eval) может потреблять токены непропорционально размеру проекта. Воздействие: финансовые потери; нерентабельность практики.
**Меры смягчения:** `ai-budget` field в frontmatter (target + actual); aggregated cost metric на уровне проекта; cap на проект; alarm при approached; recommendation engine «Sonnet/Haiku для рутины, Opus только для `priority=must` BR».

### AIR-13. Стейкхолдер не понимает AI-сгенерированные требования

AI генерирует SR в техническом стиле; клиент / нетехническая заинтересованная сторона при рецензировании не понимает → утверждение становится формальностью. Воздействие: QG-ADAPT-approve / QG-4 acceptance теряет смысл; dispute rate at acceptance растёт.
**Меры смягчения:** style guide ([04-ai-style-guide.md](04-ai-style-guide.md)); BR в business language (технологии — в SPEC-*, не в BR); human-readable summary в каждом BR/SR — короткая секция, понятная без технического background.

### AIR-14. Vendor lock-in to specific LLM provider

Все промпты оптимизированы под конкретного провайдера (Anthropic Claude). Если provider меняет pricing/availability — переход требует переписывания всех промптов. Воздействие: operational risk, costs, business continuity.
**Меры смягчения:** provider-agnostic prompts где возможно (избегать vendor-specific tool syntax); multi-model agreement для `priority=must` нормирован на RENAR-5 ([standard/11 §11.8.1](../standard/11-maturity-model.md#11.8.1)) — гарантирует второй провайдер в pipeline; periodic test runs на резервном провайдере.

---

## 3. Risk matrix

```text
Severity / Likelihood
        │  Low     Medium                     High
────────┼───────────────────────────────────────────────
High    │  AIR-08  AIR-02, 06, 07, 09         AIR-01
Medium  │  AIR-10  AIR-04, 11, 12, 13, 14     AIR-03, 05
Low     │  —       —                          —
```

Диапазонные значения из §2 (`Medium-High`, `Low-Medium`) отнесены к верхней границе. Critical и High риски в top-right квадранте — приоритет mitigation.

---

## 4. Mitigation matrix

Какие mitigations покрывают какие риски (компенсирующие механизмы: одиночный mitigation редко достаточен; высокие риски требуют ≥ 2 независимых механизмов):

| Mitigation | Покрывает риски |
|---|---|
| Source citation ([standard/04 §4.10.2](../standard/04-terms.md#4.10.2)) | AIR-01, AIR-07 |
| Состязательный обзор (другая модель) | AIR-01, AIR-04, AIR-05 |
| Выборочная проверка passing TC ([standard/09 §9.14](../standard/09-test-cases.md#9.14)) | AIR-06 |
| Multi-model для `priority=must` | AIR-04, AIR-05, AIR-14 |
| Judge isolation в SPEC-AI | AIR-05 |
| AI-происхождение (model + version + date) | AIR-03, AIR-09 |
| Citation validator hook | AIR-07 |
| Input sandbox / sanitization | AIR-02, AIR-08 |
| PII redaction + DPA | AIR-09 |
| KG validation in CI | AIR-10 |
| Reconciliation tunable thresholds | AIR-11 |
| `ai-budget` field + project cap | AIR-12 |
| Style guide + business-language BR | AIR-13 |

---

## 5. Operational governance

**Периодичность рецензирования.** Monthly: AIR-01, AIR-02, AIR-06, AIR-07, AIR-09 (high-impact runtime risks). Quarterly: остальные. On-incident: любой риск, в который произошёл инцидент → root cause → mitigation.

**Владелец.** Default — Архитектор проекта. Для AI-специфичных рисков — AI Governance Lead (если есть в организации).

**Хранение.** Risk register проекта — отдельный артефакт:

```text
<project>.req/
  governance/
    ai-risk-register.md           # snapshot этого reference + project-specific notes
    review-log.md                 # история ревью с датами и подписями
```

На носителе, не поддерживающем директории — эквивалент namespacing.

**Reconciliation-агент.** Еженедельный run обновляет `status` и `last-reviewed` поля каждого AIR. Если статус меняется (e.g., monitoring → active) — alert архитектору.

---

## 6. Связь со стандартом

| AIR | RENAR Core / Standard |
|---|---|
| AIR-01, 07 | Standard ch.4 §4.10.2 (source citation) + ch.7 §7.4.1 (реактивный ADAPT: `source.adapt` либо `source.tz-section` + `source.adversarial-review-ref`) |
| AIR-04, 13 | Standard ch.5 (роли) + style guide [§04](04-ai-style-guide.md) |
| AIR-05 | Standard ch.9 §9.6.2 (judge ≠ production isolation) + SPEC-AI |
| AIR-06 | Standard ch.9 §9.7 (pos/neg парность) + §9.13 (защита от подгонки тестов) + §9.14 (выборочная проверка) + §9.18 (изоляция авторства теста и красная история) + QG-2 |
| AIR-09 | Standard ch.4 §4.10.1 (`ai-provenance`) + SPEC-SEC |
| AIR-12 | Standard ch.12 §12.3.9 (Cost per Approved Requirement) + ch.11 §11.8.1 (cost/latency budget) |

---

## 7. Что НЕ покрывает risk register

Этот реестр focuses на AI-специфичные риски процесса RENAR. **Не подменяет:** общий security risk register организации (ISO 27001); compliance risk register ([06-compliance.md](../guide/06-compliance.md)); application-level threat model конкретного проекта (SPEC-SEC). Reg-обязательные требования регуляторов (AI Act high-risk, ФЗ-152) — отдельные artifacts; этот register — operational tier.

---

*AI Risk Register RENAR 1.1 — renar.tech*
