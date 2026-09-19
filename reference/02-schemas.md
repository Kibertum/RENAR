---
title: "Схемы (formal)"
description: "Canonical YAML frontmatter schemas for all RENAR artifact types + cross-field validation rules."
order: 2
lang: ru
version: "1.0"
---

# Схемы (формальные)

> **Назначение:** машино-читаемые YAML frontmatter схемы для всех типов артефактов RENAR. Используются нативными для носителя валидаторами для проверки соответствия. **Нормативные определения структуры** — в [`standard/06`](../standard/06-requirements-hierarchy.md), [`standard/07`](../standard/07-adapt.md), [`standard/08`](../standard/08-specifications.md), [`standard/09`](../standard/09-test-cases.md). Валидация примеров: `node scripts/validate-schema-examples.js`. Этот документ — справочник (informative lookup).

---

## 1. Общий frontmatter (все артефакты требований и SPEC)

Поля, общие для BR/SR/TR/SPEC-* (канонические v1.0). Legacy типы `UIC` / `AIC` / `INT-SR` / `TS` deprecated в v1.0 ([standard/04 §4.14.1](../standard/04-terms.md#4.14.1)).

```yaml
# Identity
id: "<TYPE>-NN[.N]"
title: "<short, descriptive>"
type: BR | SR | TR | SPEC-ARCH | SPEC-API | SPEC-DATA | SPEC-INT | SPEC-PROC | SPEC-UI | SPEC-AI | SPEC-SEC | SPEC-OPS | SPEC-TEST | SPEC-DOC
slug: "<kebab-case>"

# Scope
level: system | subsystem | module
scope: { system: "<system-id>", subsystem: "<subsystem-id>" }   # subsystem=null если level=system

# Жизненный цикл — standard/04 §4.6, standard/10 §10.7: у BR / SR / SPEC собственного
# статуса и версии НЕТ. Состояние производно от принадлежности версии комплекта описания
# (черновик / утверждён / исключён; запись версии — §8.2 ниже). TR сохраняет свой статус
# (§4.6.2) — поле объявлено в разделе 4. ADAPT (§4.6.4) и TC (§4.6.5) — собственные схемы ниже.
priority: must | should | could       # MoSCoW; SAFe — через WSJF (BR-specific)

# Provenance (conditional, standard/07 §7.4.1): ADAPT реактивен.
# Findings present → source.adapt + adapt-section mandatory. No findings → adversarial-review-ref mandatory.
# source.tz-section — обязательно всегда.
source:
  adapt: "ADAPT-NNN"                  # conditional
  adapt-section: "Forward §N.N"       # mandatory если adapt present
  tz-section: "§N.N"                  # обязательно всегда
  adversarial-review-ref: "AR-NNN"    # mandatory когда adapt omitted → AR (standard/07 §7.4.6)
  document-ref: "<ссылка>"            # pinned ревизия source-документа
  implementation-originated: {}       # conditional; см. §1.1 (standard/06 §6.13)

# Hierarchy
parent: { id: "<parent-id>", ref: "<ссылка>" }   # id required для SR (→BR), TR (→SR); optional для BR
children: []                          # auto-derived

# Связь с SPEC (граф)
constrained-by: []                    # SR → SPEC-* (типизированные рёбра)
implements-spec: []                   # TR → SPEC-*
depends-on: []                        # между SPEC

# Verification
verified-by: []                       # auto-derived; TC IDs
verifies-business-goal: ""            # optional

# AI provenance (RENAR-4+ обязательно для approved)
ai-provenance:
  generated-by: "<vendor>-<model>-<version>@<date>"
  prompt-template: "<template-path>@<version>"
  context-tokens: integer
  output-tokens: integer
  generation-time-ms: integer
  generated-at: "<ISO-8601>"
  human-edits: boolean                # true required для approved

# AI cost budget (optional)
ai-budget: { context-tokens-target: integer, context-tokens-actual: integer, output-tokens-target: integer, output-tokens-actual: integer, generation-time-target-ms: integer }

# Замена + schema versioning
replaces: "<old-id>"
replaced-by: "<new-id>"
deprecated-date: "<ISO date>"
schema-version: "1.0"
```

### 1.1 `source: implementation-originated` — требование, порождённое реализацией

Узкий легальный класс происхождения BR / SR / SPEC ([standard/06 §6.13](../standard/06-requirements-hierarchy.md#6.13)): **внутренняя техническая деталь**, добавленная при реализации (защитная проверка, внутренняя валидация, журналирование, обработка граничного случая). Наблюдаемое клиентом поведение этим классом покрывать **запрещено** — оно проходит только через контрактный контур (ADAPT / [ACTZ](#7.2) с подписями).

```yaml
source:
  tz-section: "§N.N"                  # обязательно всегда (в том числе здесь)
  implementation-originated:
    change-unit-ref: "<ссылка на единицу изменения реализации>"   # обязательно — провенанс
    rationale: "<почему функциональность признана нужной>"        # обязательно
    human-approval:                                               # обязательно; V6
      approved-by: "<имя супервайзера-человека>"
      role: "<роль>"
      approved-at: "<ISO-datetime>"
    observable-by-client: false       # обязательно; true → несоответствие (standard/06 §6.13.4)
    covering-tc: "TC-NN"              # обязательно; TC существует и проходит до слияния
    mutation-check:                                               # обязательно — компенсация отсутствия красной истории
      mutants-killed: integer         # ≥ 1; иначе TC не является доказательством (standard/09 §9.18.2)
      report-ref: "<ссылка на отчёт мутационной проверки>"
```

| Правило | Уровень |
|---|---|
| Утверждение человека (`human-approval`) обязательно; агент не может легализовать собственную добавку | normative |
| `covering-tc` существует и проходит **до** включения изменения | hook-enforced |
| У `covering-tc` **не может быть** красной истории; вместо неё обязателен убитый мутант (`mutation-check.mutants-killed ≥ 1`) | hook-enforced |
| `observable-by-client: true` при `implementation-originated` — несоответствие (обязательство перед клиентом не возникает из кода) | normative |
| Доля артефактов класса — счётчик в метриках дрейфа ([standard/12 §12.3](../standard/12-metrics.md#12.3)) | normative |

---

## 2. BR — Business Requirement

```yaml
# Extends common §1, дополнительно:

level: system | subsystem             # BR на уровне модуля запрещён (standard/06 §6.4)
scope: { system: "<system-id>", subsystem: "<subsystem-id>" }

# Межуровневая связь BR подсистемы → BR системы (standard/06 §6.8.2)
implements:                            # массив; substrate-agnostic ссылка
  - { id: BR-NN, scope: { system: "<system-id>" }, rationale: "<short>" }   # rationale опционально
implemented-by: []                     # auto-derived (обратное ребро; не пишется автором)

business-context:
  stakeholder: "<role>"
  business-goal: "<short statement>"
  kpi-impact:
    - { kpi: "<name>", direction: increase|decrease, target: "<measurable>" }

# business-outcome — required для QG-4
business-outcome:
  measurement-type: kpi | survey | observation | usage
  kpi-name: "<KPI>"
  measurement-method: "<how>"
  baseline-value: number
  baseline-measured-at: "<ISO date>"
  target-value: number
  target-met-by: "<ISO date>"
  # измеренное значение — в записи версии комплекта: accepted-outcomes[].measured-value / achievement (standard/10 §10.5.4)

prioritization: { framework: WSJF|RICE|MoSCoW, wsjf-score: number, prioritized-at: "<ISO date>", prioritized-by: "<role>" }

data-classification:
  contains-pii: boolean
  contains-financial: boolean
  contains-health: boolean
  contains-children-data: boolean
  retention-days: integer
  data-residency: ["RU" | "EU" | "US" | ...]

compliance:
  - { standard: "ISO 27001:2022", control: "<id>", rationale: "..." }
  - { standard: "GDPR", article: "Art.NN" }
  - { standard: "ФЗ-152", article: "ст.NN" }

ai-act: { risk-class: prohibited|high|limited|minimal, rationale: "<reason>", high-risk-domain: boolean }
```

### Поля `implements[]` / `implemented-by[]` — нормативные правила

| Правило | Уровень |
|---|---|
| `implements[]` обязателен при `level: subsystem` И когда родительская система имеет ≥ 1 BR в утверждённой версии своего комплекта | обязательно (§13.3.8) |
| Target BR обязан входить в утверждённую версию комплекта своей системы на момент утверждения версии, содержащей данный BR (standard/10 §10.11.1) | hook-enforced |
| Cycle detection: цепочка `implements` не должна образовывать циклов | hook-enforced |
| `implements[]` — **не** parent-edge; запрет множественных parents (standard/06 §6.8.3) распространяется на SR/TR, не на BR | normative |
| Исключение target BR из новой версии комплекта → cascade-warning для всех `implemented-by` (не каскадное исключение) | hook-enforced |
| Cross-substrate синтаксис: `id + scope.system` не зависит от носителя | normative |
| Cardinality: array (0..N) | normative |

Гейт обеспечения соблюдения — `scripts/check-implements-edge.js`. Поле `implemented-by` — auto-derived; ручная запись запрещена.

---

### Поля переиспользуемого компонента (standard/06 §6.14)

```yaml
# Корневой BR компонента (level: system), путь C:
assumptions:                          # закрытый для версии комплекта список предположений о потребителе
  - { id: A-01, statement: "<предположение>", confirmed-how: "<чем потребитель подтверждает>" }

# Корневой BR потребителя (level: system):
uses:
  - component: "<system-id>"
    set-version: "N.M"                # версия комплекта компонента (V5), обязательно
    assumptions-confirmed:
      - { id: A-01, confirmed-by: "<участник>", confirmed-at: "<ISO-8601>" }

# Утверждение SR / SPEC компонента:
applies-to: self | consumer           # по умолчанию self; consumer — обязательство потребителя (статический TC у потребителя)
```

---

## 3. SR — System Requirement

```yaml
# Extends common §1, дополнительно:

parent:
  id: "BR-NN"                         # required

# Источник ADAPT — через канонические source.adapt / source.adapt-section (§1).
# Отдельного поля derived-from-adapt нет (standard/06 §6.6.2).

constrained-by:                       # типизированные рёбра к SPEC-*
  - "SPEC-UI-NN"
  - "SPEC-API-NN"
  - "SPEC-DATA-NN"
  - "SPEC-SEC-NN"

quality-characteristic:               # ISO/IEC 25010:2023 (9 характеристик; interaction-capability ← usability, flexibility ← portability в 25010:2011; safety — новая в 25010:2023)
  - functional-suitability | performance-efficiency | compatibility | interaction-capability | reliability | security | maintainability | flexibility | safety

# Inherited from parent BR (если применимо): data-classification, compliance, ai-act
```

---

## 4. TR — Task Requirement

```yaml
# Extends common §1, дополнительно:

parent:
  id: "SR-NN"                         # required

implements-spec: []                   # SPEC-* реализуемые этой задачей
status: draft | approved | done | obsolete   # standard/10 §10.6
source:
  set-version: "N.M"                # версия комплекта, против которой поставлена задача (standard/10 §10.5.4)
estimated-effort: "<short statement>" # optional, free-form
```

---

## 5. SPEC-* common schema

Все 12 типов SPEC делят общую структуру (§1) + следующие SPEC-specific поля:

```yaml
type: SPEC-ARCH | SPEC-API | SPEC-DATA | SPEC-INT | SPEC-PROC | SPEC-UI | SPEC-AI | SPEC-SEC | SPEC-OPS | SPEC-TEST | SPEC-DOC

referenced-by: []                     # auto-derived
depends-on: []                        # SPEC от которых этот зависит
compliance-refs: []                   # ISO / GDPR / ФЗ-152 / AI Act / NIST AI RMF
```

Обязательные разделы body: `## Назначение`, `## Scope`, `## <Type-specific sections — см. §6>`, `## Связь с требованиями`, `## Связь с другими SPEC`, `## Verification`, `## Open questions`.

---

## 6. SPEC type-specific extensions

Type-specific поля для 12 типов SPEC. Industry references — в [`standard/14`](../standard/14-normative-refs.md). Legacy замены: `UIC` → SPEC-UI, `AIC` → SPEC-AI, `INT-SR` → SPEC-INT.

### 6.1 SPEC-ARCH, SPEC-API, SPEC-DATA, SPEC-INT, SPEC-PROC

```yaml
# SPEC-ARCH
arch-style: monolith | microservices | modular-monolith | serverless | hybrid
deployment-model: cloud | on-prem | hybrid | edge
tech-stack: { languages: [], frameworks: [], data-stores: [], message-brokers: [] }
quality-attributes: [{ name: latency, target: "p95 < 200ms" }, { name: availability, target: "99.9%" }]
# перечень экранов — раздел обязательного тела «Перечень экранов»: код (V1), название, источник (SR / раздел SPEC-ARCH); standard/08 §8.5.6.1

# SPEC-API
api-style: rest | graphql | grpc | websocket | async-events
api-version: "v1.2.0"
versioning-strategy: url-path | header | query-param | content-negotiation
authentication: bearer-jwt | api-key | oauth2 | mtls | none
rate-limits: [{ endpoint: "*", limit: "1000/min/key" }]
contract-file: { format: openapi-3.1 | asyncapi-2.6 | proto3, location: "contracts/<name>.yaml" }

# SPEC-DATA
data-style: relational | document | graph | columnar | hybrid
storage-engine: postgresql | mysql | couchdb | mongodb | clickhouse | ...
schema-version: "1.4.0"
pii-classification: [{ entity: User, fields: [email, phone], level: PII-high }]
retention-policies: [{ entity: Order, period: "7 years", basis: "tax law" }]
migration-strategy: forward-only | reversible | dual-write

# SPEC-INT
integration-pattern: request-response | event-driven | message-queue | webhook | file-transfer
direction: outbound | inbound | bidirectional
counterparty: { system: "<external-name>", contract-owner: "<team-or-vendor>", contract-ref: "<external-spec-url>" }
sla: { availability: "99.5%", latency-p95: "500ms", fallback: "queue + retry; manual reconciliation after 24h" }
idempotency: guaranteed | best-effort | none

# SPEC-PROC
process-style: bpmn | state-machine | saga | choreography | orchestration
state-count: integer
participants: [{ role: customer, system: client-portal }, { role: agent, system: back-office }]
sla: { end-to-end: "2 business hours" }
compensation: defined | not-applicable | manual
```

### 6.2 SPEC-UI, SPEC-AI, SPEC-SEC, SPEC-OPS

```yaml
# SPEC-UI
ui-platform: web | mobile-ios | mobile-android | desktop | tv | embedded
target-users: [{ role: end-customer, persona: "ADAPT-NNN §X.Y" }]
design-system: "<reference-or-internal>"
accessibility-level: WCAG-A | WCAG-AA | WCAG-AAA
i18n: required | not-required
mockup-links: [{ tool: figma, url: "<link>", version: "v3" }]
baseline-images: ["ai-concepts/baselines/SPEC-UI-NN-screen-01.png"]
screens: ["Э-01", "Э-02"]            # коды экранов из перечня SPEC-ARCH той же версии, которые документ покрывает (standard/08 §8.5.6.1)

# SPEC-AI (judge-model.vendor ≠ production-model.vendor — нормативно)
ai-pattern: rag | fine-tuning | prompt-engineering | tool-use | multi-agent | embedding-only
production-model: { vendor: anthropic|openai|google|local, model: "<name>", version: "<exact>" }
judge-model: { vendor: "<different-vendor>", model: "<different-model>" }
context-strategy: { embedding-model: "<model>", chunk-size: integer, chunk-overlap: integer, vector-store: pinecone|weaviate|pgvector|qdrant }
eval-strategy: { metric: accuracy|f1|rouge|custom-rubric, threshold: number, baseline-dataset: "<path>" }
cost-budget: { tokens-per-request-target: integer, tokens-per-request-ceiling: integer, monthly-budget-usd: number }

# SPEC-SEC
security-domains: [authentication, authorization, data-protection, audit, secrets-management]
auth-model: { authn: jwt-bearer|oauth2-pkce|mtls|passkey, authz: rbac|abac|relbac }
data-classification: [{ class: PII-high, fields: [...] }, { class: PCI, fields: [...] }]
threat-model-method: STRIDE | PASTA | OCTAVE
compliance: [ISO-27001, GDPR, ФЗ-152, PCI-DSS-4]

# SPEC-OPS
deployment-style: kubernetes | vm | serverless | docker-compose | bare-metal
environments:
  - { name: dev, purpose: development, scale: minimal }
  - { name: staging, purpose: integration-testing, scale: half-prod }
  - { name: prod, purpose: production, scale: full }
slo: { availability: "99.9%", error-budget-month: "43m", latency-p95: "300ms" }
observability: { logs: elastic|loki|cloudwatch, metrics: prometheus|datadog|cloudwatch, traces: jaeger|tempo|x-ray }
disaster-recovery: { rto: "<duration>", rpo: "<duration>" }
```

### 6.3 SPEC-TEST — стенды и данные

Тестовый стенд — не окружение развёртывания, а конструкция доказательства ([`standard/08 §8.3.2`](../standard/08-specifications.md#8.3.2), [§8.5.10](../standard/08-specifications.md#8.5.10)). Датасет описывается **по обе стороны** каждой интеграции; ожидаемый результат нередко живёт не в нашей системе, поэтому правила сверки — обязательная часть схемы.

```yaml
# SPEC-TEST
benches:                              # топология стенда: узлы, версии, что живое / что эмулируется
  - name: "<bench-id>"
    nodes: [{ name: "<node>", version: "<version>", mode: live | emulated }]
    notes: "<ограничения стенда>"

counterparties:                       # по одной записи на интеграцию (ровно одна на SPEC-INT)
  - integration: SPEC-INT-NN
    representation: real | sandbox | emulator
    endpoint: "<нативный для носителя pointer>"
    fidelity-rationale: "<почему представление отвечает как реальная система>"

datasets:                             # по обе стороны каждой интеграции
  - name: "<dataset-id>"
    side: ours | counterparty
    integration: SPEC-INT-NN          # null для датасета вне интеграции
    volume: "<объём>"
    origin: synthetic | anonymized-production | client-provided
    anonymized: boolean
    contains-real-client-data: boolean   # true ⇒ client-signature обязательна
    retention: "<срок хранения>"

reconciliation-rules:                 # сверка результатов с данными чужих систем
  - name: "<rule-id>"
    integration: SPEC-INT-NN
    expected-result-source: "<где живёт ожидаемый результат, если не в нашей системе>"
    match-keys: []
    tolerance: "<допуск сверки>"

run-config:
  mocked: [SPEC-INT-NN]               # что мокается
  live: [SPEC-INT-NN]                 # что прогоняется вживую
  run-order: []                       # порядок прогона
  seed-mechanism: "<как готовится состояние стенда>"

client-signature:                     # обязательна при реальных / персональных данных клиента
  signed-by: "<name>"
  role: "<role>"
  organization: "<client-org>"
  signed-at: "<ISO-datetime>"
  signature-ref: "<ссылка>"
```

| Правило | Уровень |
|---|---|
| Хотя бы один `datasets[]` с `contains-real-client-data: true` (или `anonymized: false`) ⇒ `client-signature` заполнена; иначе — **fatal** | hook-enforced |
| `counterparties[]` — ровно одна запись на каждую интеграцию, представленную на стенде | hook-enforced |
| `reconciliation-rules[].expected-result-source` непусто, когда ожидаемый результат вне нашей системы | normative |
| Инкремент версии SPEC-TEST инвалидирует `verified` у TC, ссылающихся на него через `environment-ref` ([standard/10 §10.5.4](../standard/10-lifecycle-qg.md#10.5)) | hook-enforced |

### 6.4 SPEC-DOC — поставляемая документация

SPEC-DOC описывает состав поставляемого результата: документы, которые заказчик получает как часть поставки ([`standard/08 §8.5.11`](../standard/08-specifications.md#8.5.11)). Граница с SPEC-OPS — вхождение в состав поставки: руководство администратора — всегда SPEC-DOC, runbook — всегда SPEC-OPS.

```yaml
# SPEC-DOC
deliverables:                         # по одной записи на документ
  - name: "<document-id>"
    kind: user-manual | admin-manual | training-materials | other
    audience: "<роль читателя>"
    format: pdf | html | markdown | docx | other
    language: "<язык поставки>"

required-sections:                    # обязательные разделы — требование, а не усмотрение исполнителя
  - deliverable: "<document-id>"
    sections: ["<заголовок обязательного раздела>"]

traces-to: []                         # BR / SR / SPEC, поведение которых документ описывает

version-binding:
  rule: matches-system-version | matches-release-tag | manual
  system-version-ref: "<нативная для носителя ссылка на версию системы>"

acceptance-criteria:                  # по чему документ принимается; проверяется док-линтом
  - criterion: "<проверяемое утверждение>"
    checked-by: TC-NN                 # TC с automation.kind: static (standard/09 §9.8)
```

| Правило | Уровень |
|---|---|
| Док-линт обязателен: у SPEC-DOC существует минимум один TC с `automation.kind: static`; без него SPEC-DOC неверифицируем и **не проходит QG-2** ([standard/09 §9.8](../standard/09-test-cases.md#9.8)) | hook-enforced |
| `required-sections[]` непуст для каждого `deliverables[]` | hook-enforced |
| `traces-to[]` непуст: документ описывает поведение, заявленное в требованиях; покрытие ролей и сценариев проверяет док-линт | normative |
| Содержательное соответствие текста реализованному поведению — опционально, через judge-агента (P7 / `eval`); отдельного механизма не вводится | normative |

---

### 6.5 SPEC-UC — сценарий использования

```yaml
# Extends §5 (common SPEC):
spec-type: SPEC-UC
role: human | agent                   # human — путь через интерфейс (покрытие ux-TC); agent — через API / CLI (system / contract)
persona: "<ADAPT§persona-ref>"        # для role: human
covers: [SR-NN, SPEC-UI-NN, SPEC-API-NN]   # ≥ 2 артефакта той же версии комплекта
entry: SPEC-UI-NN | SPEC-API-NN       # экран или операция, с которой путь начинается
steps:
  - n: 1
    action: "<что делает исполнитель>"
    ref: SR-NN#n | SPEC-<TYPE>-NN#n   # ОБЯЗАТЕЛЬНО: адрес утверждения, которое шаг затрагивает (standard/08 §8.5.12.1)
    expects: "<наблюдаемый результат>"
```

| Правило | Проверка |
|---|---|
| `ref` непуст у каждого шага | hook-enforced; шаг без `ref` — нарушение структурной полноты |
| `covers[]` содержит ≥ 2 артефакта той же версии комплекта | hook-enforced |
| При `role: human` каждый шаг покрыт ≥ 1 TC с `tc-type: ux`, чей `verifies[]` содержит `steps[].ref` | coverage-presence (standard/13 §13.3.5) + тип по standard/09 §9.8 |
| При `role: agent` каждый шаг покрыт `system` / `contract` TC | то же |

---

## 7. ADAPT schema

ADAPT — отдельный артефакт ([`standard/07`](../standard/07-adapt.md)). Реактивный: существует только при findings от состязательного обзора ТЗ (§7.4.1). Вердикт «no findings» — ADAPT не создаётся; исход обзора в любом случае выпускается как AR (§7.1 ниже), и артефакты ссылаются на неё через `<artifact>.source.adversarial-review-ref`.

```yaml
# Identity
id: ADAPT-NNN
title: "Адаптация ТЗ <name>"
type: ADAPT
trigger-stage: import-tz | decompose-br | decompose-sr | spec | tc   # стадия-триггер (standard/07 §7.4.1.4)

# Source
source-tz: { id: TZ-YYYY-NNN, signed-date: "<ISO-date>", signed-by-client: "<name-role>", document-version-ref: "<нативный для носителя идентификатор версии>" }   # V5 pin
parent-adapt: { id: ADAPT-NNN, delta-tz: TZ-YYYY-NNN }   # для delta-ADAPT

# Supersession (standard/07 §7.6.4) — только для superseding-ADAPT
supersedes: ADAPT-MMM                                    # ссылка на дезавуируемый ADAPT
superseded-by: ADAPT-NNN                                 # auto-derived; на дезавуируемом
supersession-rationale: "<противоречащее BR/SR/SPEC + источник>"   # mandatory если supersedes присутствует

# Lifecycle (подмножество §1: ADAPT не использует verified/deprecated/obsolete; superseded — терминальное при дезавуировании, §7.6.4)
# Состояние client-ready ИЗЪЯТО: вынесение вопросов клиенту — предмет ACTZ (§7.13), а не состояние ADAPT (standard/10 §10.8.1).
status: draft | review | asked | answered | approved | frozen | superseded
created: "<ISO-date>"
last-updated: "<ISO-date>"

# Approval (required для approved)
approval:
  # client-signature ОТСУТСТВУЕТ: ADAPT — внутренняя интерпретация (standard/07 §7.5).
  # Клиентские обязательства несёт ACTZ (§7.13) с двусторонней подписью.
  architect-signature: { signed-by: "<name>", role: architect, signed-at: "<ISO-datetime>" }

# Auto-derived
generates-requirements: []
generates-specs: []
open-questions-count: integer         # должен быть 0 для approved
resolved-questions-count: integer

# AI provenance
ai-provenance: { generated-by: "<vendor>-<model>-<version>@<date>", prompt-template: "<template-path>@<version>", context-tokens: integer, output-tokens: integer, human-edits: boolean }
```

Backward записи внутри body:

```yaml
id: B-NNN
category: contradiction | gap | hidden-assumption | feasibility | regulatory | terminology | scope
status: open | asked-to-client | answered | resolved | revised | frozen
tz-section: "§N.N"
description: "..."
asked-to-client: "<ISO-date>"
client-answer:
  signed-by: "<name>"
  signed-at: "<ISO-datetime>"
  channel: email | docusign | zoom-transcript | written-letter
  text: "..."
resolution: "..."                     # как ответ интегрирован в Forward
decided-in: "ACTZ-NNN §M"             # mandatory для status=resolved; пункт подписанного ACTZ (§7.2)
```

Находка переходит в `resolved` только тогда, когда решение по ней **вынесено клиенту и подписано**: поле `decided-in` указывает на пункт `§M` протокола ACTZ в статусе `signed` ([standard/07 §7.13.2](../standard/07-adapt.md#7.13)). Ссылка на ACTZ в статусе `draft` запрещена ([standard/07 §7.13.4](../standard/07-adapt.md#7.13)).

### 7.1 AR schema — запись состязательного обзора

AR ([`standard/07 §7.4.6`](../standard/07-adapt.md#7.4.6)) — запись-свидетельство, фиксирующая факт и исход обязательного состязательного обзора. Выпускается в **обоих** исходах; при `no-findings` она и есть то свидетельство, на которое ссылается `<artifact>.source.adversarial-review-ref`. Не является артефактом требований и ни в один закрытый список не входит.

```yaml
# Identity
id: AR-NNN                            # сквозной в рамках родительского ТЗ; неизменяем
type: AR
tz-ref: TZ-YYYY-NNN                   # обозреваемое (delta-)ТЗ
trigger-stage: import-tz | decompose-br | decompose-sr | spec | tc   # согласовано с ADAPT.trigger-stage

# Reviewer (изоляция: модель ≠ модель основного агента, standard/07 §7.10.2)
reviewer: { vendor: "<provider>", model: "<model-id>" }
primary:  { vendor: "<provider>", model: "<model-id>" }   # модель основного агента на момент выпуска — второй операнд условия §7.10.2

# Verdict
verdict: findings-present | no-findings
produces-adapt: [ADAPT-NNN]           # mandatory непустой при findings-present; пуст при no-findings

# Lifecycle
status: draft | issued | superseded
superseded-by: AR-NNN                 # mandatory если status=superseded

# Signature (V6; mandatory для issued)
signature: { author: "<reviewer-id>", timestamp: "<ISO-8601>" }
```

### 7.2 ACTZ schema — протокол уточнения ТЗ

ACTZ ([`standard/07 §7.13`](../standard/07-adapt.md#7.13)) — артефакт **контрактного контура**: порция вопросов, предложений и решений, вынесенная клиенту и подписанная обеими сторонами. Граница с ADAPT проводится по аудитории: показанное клиенту и утверждённое — обязательство, непоказанное — интерпретация. Кардинальность: ТЗ : ACTZ = 1 : 0..N; ADAPT : ACTZ = 1 : 1..N.

```yaml
# Identity
id: ACTZ-NNN                          # сквозной в рамках родительского ТЗ; неизменяем
title: "Протокол уточнения ТЗ № N"
type: ACTZ
tz-ref: TZ-YYYY-NNN                   # обязательно; ТЗ, к которому относится протокол

# Закрываемые находки (conditional)
resolves:                             # записи B-NNN в ADAPT, которые закрывает протокол
  - { id: B-NNN, adapt: ADAPT-NNN }   # отсутствует у ACTZ по инициативе клиента (standard/07 §7.13.2)

# Решения (обязательно, непустой)
decisions:
  - number: "§M"                      # стабильный номер пункта; цель ссылки decided-in
    statement: "<решение на языке обязательств: «кнопка называется X»>"
    tz-section: "§N.N"                # уточняемый раздел ТЗ

# Приложения к ТЗ (conditional; standard/07 §7.13.5)
annexes:
  - { name: "<приложение>", version: "<новая версия>", document-ref: "<ссылка>" }

# Lifecycle
status: draft | sent | signed | superseded
superseded-by: ACTZ-NNN               # mandatory если status=superseded

# Подписи (обязательны для signed; V6)
client-signature: { signed-by: "<name>", role: "<role>", organization: "<client-org>", signed-at: "<ISO-datetime>", signature-ref: "<ссылка>" }
vendor-signature: { signed-by: "<name>", role: "<role>", signed-at: "<ISO-datetime>" }
```

| Правило | Уровень |
|---|---|
| `decisions[].number` стабилен и неизменяем: на него ссылаются `B-NNN.decided-in` и `AT.verifies[]` | normative |
| Подписанный ACTZ неизменяем; исправление — только новым ACTZ с `superseded-by` на прежнем | hook-enforced |
| Ссылка на ACTZ в статусе `draft` из `decided-in` запрещена | hook-enforced |
| `resolves[]` отсутствует у протокола по инициативе клиента; после подписания решение обязано быть отражено в ADAPT | normative |
| Приложение к ТЗ не адресуется отдельно: его новая версия утверждается через `annexes[]` той же двусторонней подписью | normative |
| Итоговое ТЗ = начальное ТЗ (с приложениями) + все подписанные ACTZ; приоритет — у более позднего подписанного документа ([standard/07 §7.14](../standard/07-adapt.md#7.14)) | normative |

---

## 8. TC — Test Case

```yaml
# Identity
id: "TC-NN[.N]"
title: "<descriptive>"
type: TC
slug: "<kebab-case>"

# Classification
tc-type: business | ux | system | contract | eval | security   # business — прежнее имя acceptance (standard/09 §9.5)
negative: boolean                     # true для парного негативного TC

# Scope
level: system | subsystem | module
scope: { system: "<system-id>", subsystem: "<subsystem-id>", module: "<module-id>" }

# TC-норма собственного статуса не имеет (standard/10 §10.7); состояния реализации — automation.* / last-run.* (§10.9)

# Verification mapping (≥1)
verifies:
  - { id: "<requirement-id>", statement: 5, ref: "<ссылка>" }   # statement — адрес утверждения <id>#n (standard/15 §15.1.1), обязателен при >1 утверждении; версия описания — один раз, automation.set-version (standard/10 §10.5.4)

# Pair link (mandatory если negative=false и существует парный)
paired-with: ["<TC-id>"]

# Привязка к задаче (optional; standard/09 §9.19.7)
verifies-tr: "TR-NN"                  # задача, к объёму которой сужен тест
verifies-claims: []                   # подмножество утверждений родительского SR в объёме этой TR

# Стенд и данные (mandatory; standard/09 §9.3, standard/08 §8.5.10)
environment-ref: "SPEC-TEST-NN"       # conditional: mandatory если automation.kind: dynamic.
                                      # Для static (док-линт §9.8, структурный TC §9.8.1) не применяется:
                                      # анализатор работает по артефактам, а не против стенда

# Automation
automation:
  status: automated | manual-pending
  set-version: "N.M"                  # версия комплекта, против которой написана реализация (QG-1)
  kind: dynamic | static                    # mandatory; static — runner является статическим анализатором
  location: "<path-to-implementation>"      # mandatory если automated
  runner: pytest | jest | go-test | playwright | vlm-judge | ragas | pact | other
  manual-pending-until: "<ISO date>"        # mandatory если manual-pending
  manual-pending-reason: "<why>"

# Красная история (standard/09 §9.18.2) — условие засчёта TC как доказательства
red-history:
  fixing-run:                               # фиксирующий прогон до реализации
    date: "<ISO timestamp>"
    result: fail                            # обязан быть красным; pass → сигнал разбора, не успех
    run-ref: "<ссылка>"
  green-transition:                         # записанный переход красный → зелёный
    date: "<ISO timestamp>"
    run-ref: "<ссылка>"
  inherited-from: "TC-NN"                   # conditional: наследование у задач без изменения поведения
  not-applicable-reason: implementation-originated   # conditional: только для этого класса; тогда обязателен убитый мутант
  mutation-check: { mutants-killed: integer, report-ref: "<ссылка>" }   # mandatory при not-applicable-reason

# Execution (mandatory если tc-type=ux | eval; judge.vendor ≠ production model vendor — см. §6.2 SPEC-AI, §9)
judge: { vendor: "<provider>", model: "<model-id>", prompt-template: "<template-path>@<version>" }
baseline: { artifact: "<pointer>", perceptual-diff-threshold: float, metric-thresholds: {} }

# Last run (auto-managed; bot-only)
last-run:
  date: "<ISO timestamp>"
  result: pass | fail | skipped | n/a
  runner-id: "<runner@version>"
  run-ref: "<ссылка>"
  set-version: "N.M"                    # версия комплекта, на которой выполнен прогон (V5)
  judge-report: "<for ux/eval>"

# Замена / obsolescence
obsolete-pending: boolean             # true при detected delta-ТЗ инвалидации
replaces: "<old-id>"
replaced-by: "<new-id>"
obsoleted-date: "<ISO date>"

# Inherited
ai-provenance: { ... }                # см. §1
```

`tc-type: business` — каноническое имя; прежнее `acceptance` в v1.0 не используется. Приёмка против итогового ТЗ выполняется не через TC, а через AT (§8.1).

### 8.1 AT schema — приёмочный тест контрактного контура

AT ([`standard/09 §9.19`](../standard/09-test-cases.md#9.19)) выводится **исключительно** из итогового ТЗ ([standard/07 §7.14](../standard/07-adapt.md#7.14)): начальное ТЗ с приложениями плюс все подписанные ACTZ (§7.2). AT проверяет соответствие контракту, а не интерпретации, и создаётся изолированным агентом, которому доступ к внутреннему контуру (ADAPT, BR / SR / SPEC, TC, код) запрещён.

```yaml
# Identity
id: AT-NN                             # неизменяем
title: "<краткое описательное название>"
type: AT
negative: boolean                     # обязательно; pos/neg-парность — как у TC (standard/09 §9.7)

# Verification target (обязательно; закрытый список ссылок)
verifies:
  - "TZ §N"                           # раздел итогового ТЗ
  - "ACTZ-NNN §M"                     # пункт подписанного протокола
# Ссылка на внутренний артефакт (BR / SR / SPEC / TC) — fatal (standard/09 §9.19.2)

tz-version: "<редакция итогового ТЗ>" # обязательно; редакция, из которой AT выведен

# Провенанс изолированного агента (обязательно)
generator:
  vendor: "<provider>"                # обязан отличаться от модели основного агента (зеркально P7)
  model: "<model-id>"
  internal-contour-access: false      # обязательно; подтверждение отсутствия доступа к внутреннему контуру
  generated-at: "<ISO-8601>"

# Lifecycle
status: draft | ready | passing | failing | obsolete

# Стенд и датасет приёмочных испытаний (обязательно; standard/09 §9.19.3, §8.5.10).
# Поле заполняет НЕ генератор: изолированный агент внутренних артефактов не видит —
# привязку проставляет архитектор или runner ПОСЛЕ генерации, не нарушая изоляцию.
environment-ref: SPEC-TEST-NN

# Automation (обязательно; прогон только автоматическим runner'ом)
automation:
  status: automated | manual-pending
  kind: dynamic | static
  location: "<path-to-implementation>"
  runner: "<runner>"

# Last run (ведёт runner; bot-only)
last-run:
  date: "<ISO timestamp>"
  result: pass | fail | skipped | n/a
  runner-id: "<runner@version>"
  run-ref: "<ссылка>"
  tz-version: "<редакция итогового ТЗ на момент прогона>"
```

Тело AT обязательно содержит раздел с **дословной цитатой** проверяемого пункта итогового ТЗ (`tz_text`) рядом с шагами проверки ([standard/09 §9.19.3](../standard/09-test-cases.md#9.19)).

| Правило | Уровень |
|---|---|
| `verifies[]` содержит только `TZ §N` и `ACTZ-NNN §M`; ссылка на внутренний артефакт — **fatal** | hook-enforced |
| `generator.internal-contour-access: true` — **fatal**: изоляция есть суть механизма, а не гигиена | hook-enforced |
| `generator.model` ≠ модель основного агента | hook-enforced |
| AT перегенерируются перед каждыми испытаниями; `tz-version` обязана совпадать с действующей редакцией итогового ТЗ, иначе испытания блокируются | hook-enforced |
| Продукт не предъявляется к сдаче, пока не все AT в статусе `passing` ([standard/10 §10.4.3](../standard/10-lifecycle-qg.md#10.4)) | normative |

---

### 8.2 Запись версии комплекта описания (DescriptionSet)

Артефакт носителя, выпускаемый QG-0 комплекта ([standard/10 §10.5.4](../standard/10-lifecycle-qg.md#10.5.4)). Единственный объект, несущий версию описания; BR / SR / SPEC / TC-норма собственных `status` и `version` не имеют.

```yaml
set-version: "N.M"                  # идентификатор; неизменяем после выпуска (V1)
major: boolean                      # true — предъявляемая версия (полный аудит обязателен)
approved-by: "<участник>"           # Архитектор (V6)
approved-at: "<ISO-8601>"
supersedes: "N.M-1"                 # ровно один предшественник; отсутствует у первой версии
resolves-to: "<указатель>"          # нативный для носителя способ разрешить версию в содержимое
                                    # (снимок / тег / перечень пар (artifact-id, version-id) по V5)
changes:
  added:   []                       # идентификаторы артефактов, впервые вошедших в версию
  changed: []                       # изменённых относительно N.M-1
  removed: [{ id: "<artifact-id>", replaced-by: "<artifact-id>" }]
audit:                              # обязателен при major: true
  full: boolean
  findings-ref: "<указатель>"
architecture-signoff:               # conditional: QG-3 объявлен и применён к версии
  signed-by: "<участник>"
  signed-at: "<ISO-8601>"
verification:                       # bot-managed; пишет только runner (QG-2)
  - product-version: "<указатель>"
    date: "<ISO-8601>"
    result: pass | fail
    runner-id: "<runner-name@version>"
    evidence-refs: []
accepted-outcomes:                  # conditional: QG-4 объявлен
  - br: "BR-NN"
    accepted-by: "<участник>"
    accepted-at: "<ISO-8601>"
```

Состояния записи: `draft` (единственный черновик комплекта) → `approved` → `superseded` ([standard/10 §10.5.1](../standard/10-lifecycle-qg.md#10.5.1)). Разделы `changes`, `resolves-to`, `audit`, `approved-*` неизменяемы после выпуска; `verification` и `accepted-outcomes` — дополняемые свидетельства.

---

### 8.3 MW — запись ручного прохода

Класс свидетельств ([standard/09 §9.20](../standard/09-test-cases.md#9.20)): не узел графа требований, не расширяет закрытых списков.

```yaml
id: MW-NN
type: MW
scenario: SPEC-UC-NN | SPEC-UI-NN
set-version: "N.M"
product-version: "<указатель>"
walked-by: "<участник>"              # V6; ≠ автору реализации (P8)
walked-at: "<ISO-8601>"
findings:
  - step: 3
    observed: "<что увидено>"
    expected-ref: SR-NN | SPEC-<TYPE>-NN   # отсутствует, если поведение не описано
    route: implementation-originated | contractual
evidence-refs: []
```

| Правило | Проверка |
|---|---|
| MW не входит в `verified-by` ни одного артефакта и в `evidence-refs` записи верификации | hook-enforced (standard/14 §14.7) |
| Каждая находка несёт `route` | hook-enforced |
| `walked-by` ≠ автору реализации проверяемого поведения | V6 |

---

## 9. Validation rules (cross-field)

Правила, не выражаемые в чистой JSON Schema; требуют custom validator. Колонка «Формальная проверка» даёт исполнимый предикат или ссылку на готовый KG-запрос ([reference/05 §5/§6](05-knowledge-graph-schema.md)).

| Правило | Описание | Формальная проверка |
|---|---|---|
| **ID неизменяем** | При изменении файла поле `id` не меняется. | `diff(prev.id, curr.id) == ∅` |
| **`parent` exists** | Для SR — parent BR входит в ту же версию комплекта. | `BR[SR.parent.id] ∈ set.members`; orphans — [05 §6.1](05-knowledge-graph-schema.md#61-orphan-approved-requirements) |
| **`source.adapt` approved** | Для BR/SR/SPEC — ADAPT в `source.adapt` в статусе `approved`/`frozen`. | `status(ADAPT[art.source.adapt]) ∈ {approved, frozen}` |
| **`verified-by` consistency** | TC в `verified-by` имеют `verifies[].id` = этот артефакт. | `∀ tc ∈ art.verified-by: art.id ∈ tc.verifies[].id` |
| **Состав версии** | Каждый BR / SR / SPEC / TC-норма версии `N.M` разрешается через `resolves-to`; `changes` согласован с diff содержимого `N.M−1 → N.M`; `supersedes` образует цепочку без ветвлений. | `members(N.M) = resolve(set.resolves-to)`; `changes == diff(N.M−1, N.M)`; `∀ s: count(s.supersedes) ≤ 1 ∧ count(successors(s)) ≤ 1` |
| **`uses`: версия и подтверждение** | Ребро `uses` указывает утверждённую версию комплекта компонента; каждое `A-NN` этой версии подтверждено; граф `uses` ацикличен; `consumer`-утверждения подключённых компонентов совместимы (иначе — остановка до решения человека, standard/06 §6.14.4 п. 7). | `∀ u ∈ BR.uses: approved(u.set-version) ∧ confirmed(u) = assumptions(u.component, u.set-version) ∧ acyclic(uses)` |
| **Покрытие версии (`coverage-presence`)** | На каждое нормативное утверждение BR / SR / SPEC версии — пара TC-норм в той же версии ([standard/13 §13.3.5](../standard/13-conformance.md#13.3.5)). | `∀ a ∈ assertions(N.M): ∃ tc⁺, tc⁻ ∈ N.M: a ∈ tc.verifies` |
| **Покрытие перечня экранов** | Каждый экран перечня SPEC-ARCH версии покрыт хотя бы одним SPEC-UI той же версии; `screens[]` каждого SPEC-UI не выходит за перечень ([standard/08 §8.5.6.1](../standard/08-specifications.md#8.5.6.1)); предусловие утверждения версии (§10.7.2). | `∀ s ∈ screens(ARCH, N.M): ∃ ui ∈ N.M: s ∈ ui.screens ∧ ∀ ui: ui.screens ⊆ screens(ARCH, N.M)` |
| **`set-version` lock** | `TC.last-run.set-version` = верифицируемая версия комплекта; `TC.automation.set-version` = версия, в которой норма не изменена с момента написания реализации ([standard/10 §10.9.4](../standard/10-lifecycle-qg.md#10.9.4)). | `tc.last-run.set-version == set.version ∧ tc.automation.set-version ≥ last-change(tc)`; stale — [05 §4.4](05-knowledge-graph-schema.md#44-stale-tc-criteria-version-drift) |
| **`source.adapt` для BR/SR/SPEC (conditional)** | Канонический источник ADAPT при наличии findings; при вердикте «no findings» — `source.adversarial-review-ref` ([standard/07 §7.4.1](../standard/07-adapt.md#7.4.1)). TR — через parent SR ([standard/06 §6.6.2](../standard/06-requirements-hierarchy.md#6.6.2)). | `art.type ∈ {BR, SR, SPEC-*} ⇒ art.source.adapt ≠ null ∨ art.source.adversarial-review-ref ≠ null` |
| **SPEC-AI requires ai-act** | Для AI-артефакта `ai-act.risk-class` обязательно. | `art.type == SPEC-AI ⇒ art.ai-act.risk-class ≠ null` |
| **Data residency consistency** | RU в `SR.data-classification.data-residency` ⇒ то же в parent BR. | `'RU' ∈ SR.…data-residency ⇒ 'RU' ∈ BR[SR.parent].…data-residency` |
| **Compliance hierarchy** | `SR.compliance ⊆ parent BR.compliance` (или явный justification). | `SR.compliance ⊆ BR[parent].compliance ∨ exists(extension-justification)` |
| **TC `automated` requires location** | `automation.status: automated` ⇒ `automation.location` непустое. | `tc.automation.status == 'automated' ⇒ tc.automation.location ≠ ''` |
| **Negative TC обязателен** | На каждое нормативное утверждение — TC с `negative: true`. | `∀ assertion ∈ art: ∃ tc(negative: true)` ([standard/09 §9.7](../standard/09-test-cases.md#9.7)) |
| **ADAPT open-questions == 0 for approved** | Approval блокируется при `open` / `asked-to-client` / `answered` / `revised` backward: все находки обязаны быть в `resolved` ([standard/07 §7.4.5](../standard/07-adapt.md#7.4.5), [standard/10 §10.8.2](../standard/10-lifecycle-qg.md#10.8)). | `adapt.status == approved ⇒ count(backward[status ∈ {open, asked-to-client, answered, revised}]) == 0` ([05 §4.7](05-knowledge-graph-schema.md#47-adapt-с-open-backward-findings)) |
| **Дезавуирование корректно** | `supersedes` ⇒ непустой `supersession-rationale` и симметричный `superseded-by` на цели; нет висячих `source.adapt` на `superseded` ([standard/07 §7.6.4](../standard/07-adapt.md#7.6), [standard/10 §10.8.5](../standard/10-lifecycle-qg.md#10.8)). | `adapt.supersedes ≠ null ⇒ adapt.supersession-rationale ≠ '' ∧ ADAPT[adapt.supersedes].superseded-by == adapt.id`; `∄ art: art.source.adapt = X ∧ status(ADAPT[X]) == superseded` |
| **Judge isolation (SPEC-AI)** | `judge.vendor` ≠ `production-model.vendor`. | `tc.judge.vendor ≠ SPEC-AI[tc.verifies].production-model.vendor` |
| **SPEC depends-on acyclic** | Граф `depends-on` между SPEC — DAG. | cypher cycle-detection ([05 §4.6](05-knowledge-graph-schema.md#46-spec-dependency-cycle-detection)): rows ≠ ∅ ⇒ нарушение |
| **Находка `resolved` решена подписанным ACTZ** | Обратная находка в статусе `resolved` обязана нести `decided-in: ACTZ-NNN §M`, и целевой ACTZ обязан быть в статусе `signed` ([standard/07 §7.13.2](../standard/07-adapt.md#7.13)). | `b.status == resolved ⇒ b.decided-in ≠ null ∧ status(ACTZ[b.decided-in]) == signed ∧ b.decided-in.§M ∈ ACTZ.decisions[].number` |
| **ACTZ `signed` двусторонне подписан** | Оба поля подписи заполнены; подписанный протокол неизменяем. | `actz.status == signed ⇒ actz.client-signature ≠ null ∧ actz.vendor-signature ≠ null` |
| **AT ссылается только на контракт** | `AT.verifies[]` содержит только `TZ §N` / `ACTZ-NNN §M`; ссылка на внутренний артефакт — fatal ([standard/09 §9.19.2](../standard/09-test-cases.md#9.19)). | `∀ v ∈ at.verifies: v ~ /^(TZ §|ACTZ-\d{3} §)/`; `at.generator.internal-contour-access == false` |
| **AT свеж относительно итогового ТЗ** | `AT.tz-version` = действующая редакция итогового ТЗ; расхождение блокирует испытания ([standard/09 §9.19.4](../standard/09-test-cases.md#9.19)). | `at.tz-version == effective-tz.version` |
| **Красная история — условие засчёта TC** | TC засчитывается как доказательство при QG-2 только с записанным переходом красный → зелёный; исключение — класс `implementation-originated` с убитым мутантом ([standard/09 §9.18.2](../standard/09-test-cases.md#9.18)). | `tc.red-history.green-transition ≠ null ∨ tc.red-history.inherited-from ≠ null ∨ (tc.red-history.not-applicable-reason == implementation-originated ∧ tc.red-history.mutation-check.mutants-killed ≥ 1)` |
| **`implementation-originated` не наблюдаем клиентом** | Класс легализует внутреннюю деталь; наблюдаемое клиентом поведение через него запрещено ([standard/06 §6.13.2](../standard/06-requirements-hierarchy.md#6.13)). | `art.source.implementation-originated ≠ null ⇒ observable-by-client == false ∧ human-approval ≠ null ∧ mutation-check.mutants-killed ≥ 1` |
| **Динамический TC привязан к стенду** | TC с `automation.kind: dynamic` несёт `environment-ref` на существующий SPEC-TEST: «тест пройден» имеет смысл только против конкретного стенда и конкретных данных ([standard/09 §9.3](../standard/09-test-cases.md#9.3)). Отсутствие поля у динамического TC — **fatal**. Статические проверки (док-линт, структурный TC) стенда не требуют. | `tc.automation.kind == 'dynamic' ⇒ tc.environment-ref ≠ null ∧ type(SPEC[tc.environment-ref]) == 'SPEC-TEST'` |
| **Реальные данные клиента подписаны клиентом** | SPEC-TEST, где хотя бы один датасет несёт реальные или персональные данные клиента, обязан нести `client-signature`; объём и анонимизация — решение клиента, не исполнителя ([standard/08 §8.5.10](../standard/08-specifications.md#8.5.10)). Отсутствие подписи — **fatal**. | `∃ d ∈ spec.datasets: d.contains-real-client-data == true ∨ d.anonymized == false ⇒ spec.client-signature ≠ null` |
| **SPEC-DOC покрыт док-линтом** | У SPEC-DOC существует минимум один TC-док-линт (`automation.kind: static`); без него SPEC-DOC неверифицируем и **не проходит QG-2** ([standard/09 §9.8](../standard/09-test-cases.md#9.8)). | `spec.type == 'SPEC-DOC' ⇒ ∃ tc ∈ spec.verified-by: tc.automation.kind == 'static'` |

---

## 10. Изоморфизм носителя

Отображение для git (YAML frontmatter) ↔ document-oriented store (JSON document):

| Поле (canonical) | git (YAML frontmatter) | Document store (JSON doc) |
|---|---|---|
| `id` | `id` | `_id = <project>:<doc-type>:<slug>`, поле `slug` |
| `type: BR` | `type: BR` | `level: "business"` |
| `type: SPEC-API` | `type: SPEC-API` | `doc_type: "spec_api"` |
| `parent.id` | `parent.id` | `parent` |
| `children` | (auto-derived) | `children` (auto-derived) |
| `status` | `status` | `status` |
| `priority` | `priority` | `priority` |
| `source.adapt` | `source.adapt` | `created_from_adapt` |
| `constrained-by[]` | `constrained-by` | `constrained_by` (subdoc array) |
| `verified-by[]` | (auto-derived) | `linked_tests` |
| `ai-provenance.*` | nested object | nested subdocument |
| `compliance` | `compliance` array | `compliance` subdoc |
| `data-classification` | nested object | nested subdoc |
| `business-outcome` | nested object | nested subdoc |
| `replaces` / `replaced-by` | string ID | `replaces` / `replaced_by` |

Нативные для носителя имена полей могут отличаться, но **семантика и invariants сохраняются** через capabilities V1-V6 ([01-glossary.md §2.7](01-glossary.md#2.7)).

---

## 11. Schema versioning

Каждый артефакт имеет поле `schema-version` (semver). При несовпадении версии файла и текущей схемы validator предлагает migration.

| Изменение | Bump |
|---|---|
| Новое необязательное поле | minor (1.0 → 1.1) |
| Новое обязательное поле | major (1.0 → 2.0) + migration script |
| Удаление поля | major + migration script |
| Изменение enum | minor если добавление, major если удаление |
| Переименование поля | major + migration script |

**Текущая версия schemas:** 1.0.

---

## 12. JSON Schema fragment example (BR)

Ключевые patterns (полная BR-схема — `reference/schemas/br.json`, планируется):

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://renar.tech/schemas/br.json",
  "type": "object",
  "required": ["id", "title", "type", "status", "priority", "source", "ai-provenance"],
  "properties": {
    "id":     { "type": "string", "pattern": "^BR-[0-9]{2}(\\.[0-9]+)?$" },
    "title":  { "type": "string", "minLength": 5, "maxLength": 100 },
    "type":   { "const": "BR" },
    "status": { "enum": ["draft", "approved", "verified", "accepted", "deprecated"] },
    "priority": { "enum": ["must", "should", "could"] },
    "source": { "type": "object", "required": ["tz-section"], "properties": { "adapt": { "pattern": "^ADAPT-[0-9]{3}(-delta-[0-9]+)?$" } } },
    "ai-provenance": { "required": ["generated-by", "generated-at"], "properties": { "generated-by": { "pattern": "^[a-z]+-[a-z0-9-]+@[0-9]{4}-[0-9]{2}-[0-9]{2}$" } } }
  }
}
```

Аналогичные JSON-схемы для SR/TR/SPEC-*/TC/ADAPT — в `reference/schemas/` (планируется).

---

*Schemas reference RENAR 1.1 — см. также [01-glossary.md](01-glossary.md), `standard/06`-`09` для нормативных определений.*
