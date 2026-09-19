---
title: "Соответствие"
description: "Mapping RENAR ↔ ISO 27001 / GDPR / ФЗ-152 / EU AI Act / NIST AI RMF / ISO/IEC 23894 / ISO/IEC 5338 / PCI-DSS, чек-листы самооценки и auto-generated audit artifacts."
order: 6
lang: ru
version: "1.0"
---

# 06. Соответствие

> RENAR — стандарт инженерии требований; не compliance-стандарт сам по себе. Но RENAR предоставляет **инфраструктуру traceability**, которая делает соответствие другим стандартам (ISO 27001, GDPR, ФЗ-152, EU AI Act и др.) проверяемым автоматически. Эта глава — mapping артефактов RENAR на 8 ключевых compliance фреймворков + чек-листы самооценки + список auto-generated artifacts для аудитора.
>
> **Предпосылки:** [RENAR Core](../core/renar-core.md), [reference/02-schemas.md](../reference/02-schemas.md), [reference/03-ai-risk-register.md](../reference/03-ai-risk-register.md).

---

## 1. Принципы compliance в RENAR

**1.1 Compliance frontmatter.** Каждое требование с регуляторным обоснованием **обязано** содержать поле `compliance` во `frontmatter` — трассировать соответствие во внешней таблице без связи с артефактом нельзя. Поле повторяемое (одно требование может закрывать controls нескольких стандартов):

```yaml
compliance:
  - { standard: "ISO 27001:2022", control: "A.5.34", rationale: "Privacy and protection of PII" }
  - { standard: "GDPR", article: "Art.32", rationale: "Security of processing" }
  - { standard: "ФЗ-152", article: "ст.19", rationale: "Меры по обеспечению безопасности ПДн" }
```

**1.2 Data classification.** Каждое BR/SR, оперирующее данными, обязано декларировать классификацию. При `contains-pii: true` автоматически становятся обязательными SR-encryption + SR-audit-log + SR-erasure.

```yaml
data-classification:
  contains-pii: true              # GDPR / ФЗ-152 trigger
  contains-financial: false       # PCI-DSS trigger
  contains-health: false          # HIPAA / ФЗ-152 спец. категории
  contains-children-data: false   # COPPA trigger
  retention-days: 1095
  data-residency: ["RU", "EU"]
```

**1.3 Traceability как audit foundation.** Цепочка `BR → SR → SPEC → TC → реализация → CI run` — это и есть журнал аудита. Аудитор открывает coverage report и видит: какие требования закрывают control; какие TC верифицируют; `last-run.date`; `last-run.set-version` (версия комплекта, на которой прошёл прогон). Время аудита: 1-2 дня вместо 2-3 недель. Reconciliation hook (drift detection) гарантирует, что evidence не «протух» между аудитами.

---

## 2. ISO/IEC 27001:2022 — Information Security

| ISO 27001:2022 Annex A control | RENAR-артефакт |
|---|---|
| A.5.7 Threat intelligence | SPEC-SEC с threat model; SR с `compliance: A.5.7` |
| A.5.8 Information security in project management | Сам RENAR как process compliance |
| A.5.34 Privacy and protection of PII | SR с шифрованием + `data-classification.contains-pii: true` |
| A.6.3 Information security awareness | Onboarding процесс включает чтение RENAR |
| A.8.1 User endpoint devices | SR с device requirements (если в scope) |
| A.8.5 Secure authentication | SR-AUTH-* + SPEC-SEC с authentication flow |
| A.8.7 Protection against malware | SR + adversarial review (AI-критик) |
| A.8.10 Information deletion | SR с deletion logic + `retention-days` |
| A.8.16 Monitoring activities | SR с logging + SPEC-OPS audit-log |
| A.8.24 Use of cryptography | SR с явным crypto algorithm + ISO 25010 Security |
| A.8.25 Secure development life cycle | SENAR + RENAR как evidence |
| A.8.28 Secure coding | TC с security checks; SPEC-SEC с STRIDE coverage |
| A.12.1.2 Change management (наследие 27001:2013) | Delta-ТЗ + Impact Analysis ([§7.6](../standard/07-adapt.md)) |

**Результат для аудита.** Auto-generates conformance report: scope (BR/SR/TC counts с `compliance.iso27001`) + Coverage by Annex A (control ↔ mapped SR ↔ status). Нормативный механизм — capability V4 reporting from versioned artifacts.

---

## 3. GDPR (Regulation EU 2016/679)

| GDPR Article | RENAR-артефакт |
|---|---|
| Art.5 Principles | BR явно указывает lawful basis |
| Art.6 Lawfulness | BR с `gdpr.lawful-basis: consent / contract / legal-obligation / vital-interests / public-task / legitimate-interests` |
| Art.7 Conditions for consent | SR с consent management workflow |
| Art.13 Information to data subject | SPEC-UI с privacy notice экраном |
| Art.15 Right of access | SR с export-user-data |
| Art.16 Right to rectification | SR с edit-user-profile |
| Art.17 Right to erasure | SR с delete-user-data + propagation на linked entities |
| Art.18 Right to restriction | SR с suspend-processing |
| Art.20 Right to data portability | SR с export в machine-readable формате |
| Art.25 Data protection by design and by default | `data-classification` обязателен на BR-уровне |
| Art.30 Records of processing activities | Auto-generated из BR с `contains-pii` |
| Art.32 Security of processing | SR с encryption + access control |
| Art.33 Notification of personal data breach | SR с breach notification workflow + 72h timer |
| Art.35 DPIA | Для high-risk — отдельный `dpia/<feature>.md` |

**GDPR frontmatter:**

```yaml
gdpr:
  data-categories: ["identification: email, name", "contact: phone, address", "behavioral: usage logs"]
  lawful-basis: contract              # Art.6
  retention-period-days: 1095
  cross-border-transfer: false        # true → обязательны SCCs или adequacy decision
  dpia-required: false                # true → link на DPIA
  data-subject-rights: [access, rectification, erasure, portability]   # Art.15/16/17/20
```

**DPIA.** Для high-risk processing — `<system>.req/dpia/DPIA-NN-<slug>.md` с frontmatter (`id`, `type: DPIA`, `gdpr-article: 35`, `related-br[]`, `risks-identified`, `mitigations`) и секциями: процесс обработки, необходимость, риски, меры снижения, согласование с DPO.

---

## 4. ФЗ-152 «О персональных данных» (РФ)

| ФЗ-152 Статья | RENAR-артефакт |
|---|---|
| ст.5 Принципы обработки | BR с целевым назначением (`business-context.business-goal`) |
| ст.6 Условия обработки | BR с `lawful-basis` |
| ст.9 Согласие на обработку | SR с consent management |
| ст.13.1 Хранение в РФ | `data-classification.data-residency: ["RU"]` для российских клиентов |
| ст.14 Доступ к информации | SR с export-user-data |
| ст.15 Право на уточнение | SR с edit-user-profile |
| ст.16 Удаление | SR с delete-user-data |
| ст.18 Обязанности оператора | Журнал аудита через RENAR traceability |
| ст.18.1 Локализация ПДн граждан РФ | `data-residency` обязательно |
| ст.19 Меры защиты | SR с encryption + access control + ISO 25010 Security |
| ст.21 Уведомление о нарушении | SR с breach notification workflow |
| ст.22 Уведомление РКН | operational, вне scope RENAR |

**Контроль резидентности данных.** Для проектов с российскими клиентами `data-residency` обязательно. Hook носителя проверяет: при `data-residency: ["RU"]` все upstream SR (хранение, бэкапы, репликация) должны иметь evidence хранения в РФ; добавление зарубежной юрисдикции для RU-резидентного BR → блок на QG-0.

---

## 5. EU AI Act (Regulation EU 2024/1689)

AI Act EU классифицирует AI-системы по уровню риска. RENAR обязывает явно указывать класс:

```yaml
ai-act:
  risk-class: limited                 # prohibited | high | limited | minimal
  rationale: "Generative AI for document drafting; no autonomous decisions affecting users' legal rights"
  high-risk-domain: false             # true → требуется conformity assessment
  general-purpose-ai: true            # GPAI — Claude / GPT и т.д.
```

**High-risk AI requirements (Art.9-15)** — для класса `high` (financial scoring, hiring, public services, медицинская диагностика):

| AI Act требование | RENAR-артефакт |
|---|---|
| Art.9 Risk management system | SPEC-AI + AI risk register ([reference/03](../reference/03-ai-risk-register.md)) |
| Art.10 Data and data governance | `eval-datasets/` с provenance + spot-check |
| Art.11 Technical documentation | SPEC-AI + ISO/IEC 5338 conformance (§7) |
| Art.12 Record-keeping | tool_event audit + `ai-provenance` |
| Art.13 Transparency to users | SPEC-UI с обозначением AI-обработки |
| Art.14 Human oversight | One-click утверждение + spot-check на QG-0 / QG-2 |
| Art.15 Accuracy, robustness, cybersecurity | Eval-tests (`tc-type: eval`) + adversarial review |

**GPAI обязательства (Art.51-55).** Если используется General-Purpose AI Model (Claude, GPT, Gemini): `ai-provenance` во frontmatter любого AI-сгенерированного артефакта (model id, version, prompt hash); технический документ модели (если GPAI с systemic risk) — внешний документ + reference из SPEC-AI.

---

## 6. NIST AI RMF 1.0 (US-юрисдикция)

| NIST AI RMF Function | RENAR-механизм |
|---|---|
| **Govern** | RENAR + роли SENAR + compliance frontmatter |
| **Map** | BR с `business-context`, `data-classification`, `ai-act.risk-class` |
| **Measure** | Eval-тесты с metric thresholds + RENAR-метрики (Hallucination Rate, Coverage Velocity) |
| **Manage** | Lifecycle с QG + reconciliation hook + AI risk register |

**RMF-специфичные расширения** (опционально для US-проектов; не противоречит ISO/IEC 23894 §7):

```yaml
nist-ai-rmf:
  applicable: true
  govern: { role: "AI Governance Lead", policy-link: "..." }
  map: { use-case-category: "content-generation", affected-stakeholders: ["clients", "internal-team"] }
  measure: { metrics-tracked: ["accuracy", "fairness", "robustness"], baseline-run-id: "eval-2026-04-01" }
  manage: { risk-tolerance: "low", incident-response-plan: "<link>" }
```

---

## 7. ISO/IEC 23894:2023 — AI Risk Management

Guidance по AI risk management. Не сертифицирует, но даёт structured framework для управления рисками AI-систем. Совместим с NIST AI RMF и AI Act EU.

| ISO/IEC 23894 раздел | RENAR-артефакт |
|---|---|
| §6.4.1 Risk identification | AI risk register ([reference/03](../reference/03-ai-risk-register.md)) |
| §6.4.2 Risk analysis | SPEC-AI с risk assessment секцией |
| §6.4.3 Risk evaluation | Threshold для каждого риска в `eval-tests` |
| §6.4.4 Risk treatment | SR-mitigations + post-mitigation evaluation |
| §6.4.5 Communication and consultation | RACI matrix ([05-safe-comparison §9](05-safe-comparison.md)) |
| §6.4.6 Monitoring and review | Reconciliation hook (drift detection) |

**Критерии соответствия:** каждый AI-сгенерированный артефакт имеет `ai-provenance`; для каждого AI use-case в SPEC-AI задокументированы identified risks (hallucination, bias, robustness), mitigations (eval thresholds, adversarial review, human-in-the-loop), residual risks; risk register обновляется при изменении SPEC-AI (reconciliation ловит drift).

---

## 8. ISO/IEC 5338:2023 — AI System Life Cycle Processes

Extension к ISO/IEC/IEEE 12207. Удобный фреймворк для AI Act Art.11 technical documentation.

| ISO/IEC 5338 process | RENAR-этап |
|---|---|
| Stakeholder needs and requirements definition | ТЗ + ACTZ (протоколы уточнения) + ADAPT + BR |
| Architecture definition | SPEC-ARCH + SPEC-AI |
| Design definition | SPEC-AI (model card, prompt design, RAG) |
| Implementation | TR + реализация |
| Verification | TC (`tc-type: eval` для AI) |
| Validation | AT — приёмочные тесты от итогового ТЗ ([§9.19](../standard/09-test-cases.md#9.19)) + spot-check |
| Operation | Reconciliation hook (drift detection, [§4.11](../standard/04-terms.md#4.11)) |
| Maintenance | Delta-ТЗ + ACTZ + ADAPT iteration |
| Disposal | Retirement workflow (вне scope Core RENAR) |

**Доказательная база соответствия:** SPEC-AI для каждой AI use-case с полной model card; eval-tests привязаны к SPEC-AI через `verifies[]`; `ai-provenance` зафиксирован; drift detection активен (V6).

---

## 9. PCI-DSS v4.0 — Payment Card Industry Data Security Standard

Если проект обрабатывает данные платёжных карт (CHD):

| PCI-DSS v4 requirement | RENAR-артефакт |
|---|---|
| Req.1 Network security controls | SPEC-OPS с network segmentation |
| Req.3 Protect stored CHD | `contains-financial: true` + SR с encryption-at-rest |
| Req.4 Encrypt CHD transmission | SR с TLS + SPEC-API requirements |
| Req.6 Develop secure systems | RENAR + SENAR как process compliance |
| Req.7 Restrict access by need to know | SR с RBAC + SPEC-SEC access control matrix |
| Req.8 Authenticate access | SR-AUTH-* + MFA где обязательно |
| Req.10 Log and monitor access | SR с журналом аудита + SPEC-OPS observability |
| Req.11 Test security regularly | TC `tc-type: security` + pen-test (вне RENAR scope) |
| Req.12 Information security policy | RENAR + SENAR как documented policy |

**Минимизация объёма CHD.** hook носителя проверяет, что новые SR с `contains-financial: true` явно обосновывают необходимость хранения CHD — без обоснования требование останавливается на QG-0.

---

## 10. Чек-листы самооценки

Короткие yes/no чек-листы для compliance-команд. Полный печатный kit для RENAR manifest — [reference/08](../reference/08-conformance-self-assessment.md).

**ISO 27001:2022** — все BR с `contains-pii: true` имеют SR закрывающий A.5.34; SoA задокументирован; каждый in-scope control имеет ≥1 verifying SR; coverage report зелёный; аудитор проходит от control до passing TC за <5 минут.

**GDPR** — все PII в Records of Processing (auto-generated); lawful basis указан per processing activity; data subject rights Art.15-22 verifiable через SR + TC; DPIA выполнен для high-risk; cross-border transfers обоснованы (SCCs/adequacy).

**ФЗ-152** — требования с PII граждан РФ имеют `data-residency: ["RU"]`; hook носителя проверяет upstream SR хранения; согласие реализовано в SR с TC evidence; меры защиты ст.19 покрыты SR с passing TC.

**EU AI Act** — каждый SPEC-AI имеет `ai-act.risk-class`; для `high`: Art.9-15 evidence в носителе; human oversight на QG-0/QG-2 + spot-check; technical documentation auto-generated; `ai-provenance` для GPAI-компонент.

**NIST AI RMF** — все 4 функции (Govern/Map/Measure/Manage) имеют доказательную базу; AI Governance Lead определён в roles; эталон eval зафиксирован; incident response plan связан со SPEC-AI.

**ISO/IEC 23894** — AI risk register заполнен и связан со SPEC-AI; каждый риск имеет mitigation в SR; residual risks accepted владельцем; V6 активна.

**ISO/IEC 5338** — SPEC-AI для каждой AI use-case с model card; eval-tests привязаны через `verifies[]`; `ai-provenance` зафиксирован; V6 активна.

**PCI-DSS** — SR с `contains-financial: true` имеют encryption (at-rest + in-transit); CHD scope минимизирован; RBAC в SPEC-SEC; SR журнала аудита покрывает все CHD-touching flows; security TC прогоняются регулярно.

### 10.9 Самооценка RENAR-conformance (за час)

Проект декларирует **собственный** уровень RENAR-conformance через manifest `RENAR-CONFORMANCE.yaml` ([standard/13 §13.4](../standard/13-conformance.md#13.4)). Быстрый чек-лист (yes/no, один проход):

- Носитель требований обеспечивает все V1–V6 ([§11.4.1](../standard/11-maturity-model.md#11.4.1) — Notion/Google Docs не подходят).
- На каждое ТЗ выпущена AR (запись состязательного обзора); каждый ADAPT, порождённый вердиктом «findings present», — в статусе approved с **подписью архитектора** ([§7.5](../standard/07-adapt.md#7.5)). ADAPT может не быть вовсе (вердикт «no findings») либо быть несколько (находки на разных стадиях) — обе ситуации штатны.
- Обязательная подпись клиента под ADAPT **не объявлена**: клиентские обязательства несёт ACTZ. Требовать подпись клиента на ADAPT можно только как `declared-stricter`, но не как базовое соответствие ([§13.3.3](../standard/13-conformance.md#13.3)).
- Каждая находка в статусе `resolved` несёт `decided-in` на пункт **подписанного** ACTZ; ни один подписанный ACTZ не остался неотражённым в ADAPT ([§7.4.5](../standard/07-adapt.md#7.4.5), [§7.13](../standard/07-adapt.md#7.13)).
- Приёмка идёт против **итогового ТЗ** (начальное ТЗ + все подписанные ACTZ, [§7.14](../standard/07-adapt.md#7.14)), а не против ADAPT.
- AT выведены изолированным агентом: модель отлична от модели основного агента, доступа к ADAPT / BR / SR / SPEC / TC / коду не было; `tz-version` каждого AT совпадает с действующей редакцией итогового ТЗ; все AT в `passing` перед предъявлением ([§9.19](../standard/09-test-cases.md#9.19), [§10.4.3](../standard/10-lifecycle-qg.md#10.4)).
- Ни один TC не засчитан как доказательство без красной истории; исключение — TC класса `implementation-originated`, где компенсацией служит убитый мутант ([§9.18.2](../standard/09-test-cases.md#9.18)).
- Требования класса `implementation-originated` описывают только внутренние технические детали: наблюдаемое клиентом поведение через этот класс не легализовано ([§6.13.2](../standard/06-requirements-hierarchy.md#6.13)).
- Все 12 типов SPEC поддерживаются нативно (declaration «type не используется» допустима, «не поддерживается» — нет).
- Каждое нормативное утверждение, покрытое TC, имеет парный negative TC.
- QG-0 / QG-1 / QG-2 объявлены `required`.
- Манифест содержит `renar-version` + `senar-version` + `level` + подтверждение mandatory clauses ([reference/08 §2](../reference/08-conformance-self-assessment.md#2-чек-лист-самооценки-обязательные-положения)).
- `assessment-date` свежее, `next-assessment-due` не просрочен.

Все 13 — `yes` → проект conformant к заявленному `level`. Хотя бы один `no` → manifest не выпускается ([§13.5.2](../standard/13-conformance.md#13.5.2)).

Четыре пункта списка — 3, 6, 7 и 8 — держат одну мысль: **обязательство перед клиентом не может возникнуть ни из инженерного документа, который клиент не читал, ни из кода, ни из теста, написанного тем же агентом и той же моделью, что и реализация.** Источники у них разные, и стоит их назвать поимённо: подпись клиента под ADAPT не объявлена — [§13.3.3](../standard/13-conformance.md#13.3) и [§7.5](../standard/07-adapt.md#7.5); изоляция генератора AT — [§9.19.2](../standard/09-test-cases.md#9.19) с точкой контроля [§10.11.1](../standard/10-lifecycle-qg.md#10.11); красная история — [§9.18.2](../standard/09-test-cases.md#9.18); границы класса `implementation-originated` — [§6.13.2](../standard/06-requirements-hierarchy.md#6.13). Из четырёх только первый входит в закрытый перечень обязательных положений §13.3; остальные три объявлены обязательными в своих главах.

Заполненный пример (RENAR-2, self-assessment) — полная схема в [§13.4.2](../standard/13-conformance.md#13.4.2):

```yaml
renar-version: "1.1"
senar-version: "1.0"
manifest-version: 1
manifest-id: "CFM-2026-014"
level: "RENAR-2"
level-target: "RENAR-3"
assessment-mode: "self"
assessment-date: "2026-05-20"
assessor: { id: "architect-team-lead", role: "architect", signature-ref: "<pointer>" }
next-assessment-due: "2026-08-20"
mandatory-clauses-confirmed:
  sot-inversion: true
  substrate-v1-v6: { v1: true, v2: true, v3: true, v4: true, v5: true, v6: true }
  adapt-per-tz: true
  spec-types-closed-list: true
  tc-pos-neg-pairing: true
  quality-gates-closed-list: true
  closed-lists-backward-findings: true
quality-gates: { qg-0: required, qg-1: required, qg-2: required, qg-3: absent, qg-4: absent }
spec-types-supported: ["SPEC-ARCH","SPEC-API","SPEC-DATA","SPEC-INT","SPEC-PROC","SPEC-UI","SPEC-AI","SPEC-SEC","SPEC-OPS","SPEC-TEST","SPEC-DOC","SPEC-UC"]
exceptions: []
replaced-by: null
```

---

## 11. Автогенерируемые compliance-артефакты

Генерируемые из существующих требований артефакты для аудиторов:

| Artifact | Источник | Содержание |
|---|---|---|
| Records of Processing (GDPR Art.30) | BR/SR с `contains-pii: true` | Категории данных, lawful basis, retention, recipients |
| Statement of Applicability (ISO 27001) | BR/SR с `compliance: ISO 27001:2022` | Annex A controls в/вне scope, justification |
| Data Inventory | Все `data-classification` записи | Categories, residency, retention, owners |
| AI System Cards | SPEC-AI | Model card по NIST AI RMF / AI Act format |
| Отчёт журнала аудита | Traceability BR → SR → SPEC → TC → last-run | Цепочка от контроля до evidence |
| DPIA Index | `dpia/` папка | Список DPIA + статус согласования с DPO |
| AI Risk Register Snapshot | AI risk register | Все идентифицированные риски + mitigations + residual |

Нативный для носителя reporting: агрегация доказательной базы из versioned artifacts (V4) в compliance report по запросу оценщика.

**Evidence pack — шаблоны (informative; полный E2E — [guide/09 §E3](09-worked-examples.md#2-e3--экспорт-персональных-данных-gdpr-art-15--фз-152)).** GDPR Art.15 trace bundle — таблица `Control | BR/SR | SPEC | TC | last-run` + lawful basis + retention + ссылка на manifest. ФЗ-152 ст.14 — mapping table `Требование ↔ RENAR artifact ↔ Evidence field`. ISO 29148 trace excerpt — [reference/07](../reference/07-iso29148-trace-matrix.md) (заполнить «RENAR frontmatter» для каждого SR в scope). Самооценка перед аудитом — [reference/08 §2](../reference/08-conformance-self-assessment.md#2-чек-лист-самооценки-обязательные-положения).

---

## 12. Что RENAR НЕ покрывает в compliance

RENAR — инфраструктура для compliance, но не **substitute** для: DPO (Data Protection Officer — человеческая роль, юридическая ответственность); legal review договоров и privacy notices; pen-testing реализации; bug bounty / vulnerability management; physical security (датацентры, офис); operational security (key rotation, incident response, monitoring runbook); cyber insurance; regulatory filings (уведомления РКН, GDPR registration с DPA, AI Act conformity assessment).

RENAR помогает делать compliance **в процессе разработки требований**. Operational compliance — отдельные процессы, которые *ссылаются* на RENAR-артефакты как на доказательную базу.

---

## 13. Resolved decisions для v1.0

- **Compliance frontmatter — conditional mandatory.** Default — optional; mandatory при `contains-pii: true` (GDPR/ФЗ-152 trigger) или `contains-phi: true` (HIPAA trigger). Артефакт с PII без compliance frontmatter — non-conformant ([§13.3](../standard/13-conformance.md#13.3) расширения через manifest declared-stricter).
- **DPIA — mixed model.** Формальный DPIA — внешний документ (legal owns); RENAR-артефакт `dpia/<slug>.md` хранит machine-readable summary + pointer на formal document. Separation of concerns при traceability.
- **Data residency — вне scope RENAR.** Per [§1.3 (3)](../standard/01-scope.md#1.3) tech stack/infra out of scope. Enforcement — DevOps-уровневые контролы (network policies, cloud regions). RENAR фиксирует **требование** (`data-classification.data-residency: ["EU"]`), не enforcement.
- **Custom industry frameworks** (ЦБ РФ финтех, HIPAA healthcare и др.) — отдельные документы (industry-specific addenda как `guide/06-compliance-<industry>.md` или внешние документы; **могут** declared-stricter поверх RENAR-conformance).

**Отложено на v1.1 (бэклог фазы 8):** автовыгрузка доказательств по Schrems II и трансферам ЕС ↔ РФ — частично закрывается флагами `cross-border-transfer` и ссылками на SCC, но полная автоматизация недостижима (какие SCC применимы — решают юристы). Ответственные: связка legal-tech / адаптеры.

---

## 14. Связь с другими главами

- [00-quickstart](00-quickstart.md) — базовый цикл RENAR без compliance-надстройки.
- [05-safe-comparison](05-safe-comparison.md) — RACI с SAFe ролями (включая DPO как Consulted).
- [reference/02-schemas](../reference/02-schemas.md) — frontmatter schemas: `compliance`, `data-classification`, `gdpr`, `ai-act`.
- [reference/03-ai-risk-register](../reference/03-ai-risk-register.md) — AI risk register структура.
- [reference/04-ai-style-guide](../reference/04-ai-style-guide.md) — стиль AI-провенанса.
- [standard/04-terms](../standard/04-terms.md) — SPEC-SEC, SPEC-AI, SPEC-OPS терминология.
- [standard/13-conformance](../standard/13-conformance.md) — RENAR-уровни conformance (≠ compliance: RENAR-N оценивает зрелость *процесса*, compliance — соответствие *внешним нормам*).
