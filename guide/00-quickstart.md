---
title: "Быстрый старт"
description: "30-минутный практический старт RENAR: ТЗ → ADAPT → SR → SPEC → TC → версия комплекта → верификация."
order: 0
lang: ru
version: "1.0"
---

# 00. Быстрый старт

> Сквозной пример: маленький проект «email/password sign-up». Полный цикл RENAR с минимальным набором артефактов: ТЗ → ADAPT → BR → SR → SPEC → TC.
> Время: ~30 минут чтения + проб на бумаге; ~2-3 часа если делать вживую на носителе.
> Предпосылки: [core/renar-core.md](../core/renar-core.md) (≤ 10 мин). Полноразмерный пример с 2FA — [01-walkthrough.md](01-walkthrough.md). Язык RU-корпуса — [standard/00 §0.7](../standard/00-introduction.md#0.7).

Шаги ниже — один из возможных порядков работы, выбранный для маленького примера; нормативны только предусловия и правила стандарта ([standard/01 §1.2.1](../standard/01-scope.md#1.2.1)) — порядок ваша организация вправе выстроить по-своему.

После этого старта у вас будет: понимание полного цикла RENAR на одном маленьком примере; готовые YAML-шаблоны для каждого типа артефакта; опыт прохождения двух шлюзов (QG-ADAPT-approve ≡ **QG-3 Architecture Gate** [§10.4.1](../standard/10-lifecycle-qg.md#10.4.1) + **QG-2 Verification Gate**); точка для масштабирования.

---

## Предпосылки

**Носитель** (`substrate`) — система хранения и версионирования артефактов. RENAR независим от вида хранилища; для быстрого старта подойдёт любой носитель с capabilities V1-V6 ([reference/01 §2.7](../reference/01-glossary.md#2.7)): git с PR-ревью; документо-ориентированное хранилище; БД с историей и подписями.

**Структура папок (соглашение по умолчанию для git):**

```text
my-project.req/
  tz/                   # immutable ТЗ от клиента
  actz/                 # ACTZ — протоколы уточнения ТЗ (двусторонняя подпись)
  adapt/                # ADAPT артефакты
  br/                   # Business Requirements
  sr/                   # System Requirements
  specs/{arch,api,data,ui,sec,ai,proc,int,ops,test,doc}/
  tests/                # TC (tc-type incl. contract)
  at/                   # AT — приёмочные тесты от итогового ТЗ
```

Для других носителей — эквивалентная организация по типам документов или namespace.

---

## Шаг 1. ТЗ (5 мин)

Клиент даёт небольшое ТЗ. Это **договорной неизменяемый** документ — после подписания не редактируется.

**`tz/TZ-2026-001.md`** (фрагмент):

```markdown
---
id: TZ-2026-001
title: "Регистрация пользователей через email"
signed-date: "2026-05-15"
signed-by-client: "Иванов И.И., PM, ClientCo"
---

# ТЗ-2026-001

## §1. Контекст
Создать систему регистрации пользователей по email.

## §2. Требования
- Пользователь может зарегистрироваться через email и пароль.
- После регистрации пользователь подтверждает email.
- После подтверждения — доступ в личный кабинет.

## §3. Ограничения
- Только web-приложение.
- Хранение в РФ (ФЗ-152).
```

ТЗ подписано клиентом → неизменяемо. Все правки и интерпретации идут через ADAPT.

---

## Шаг 2. ADAPT `draft` (10 мин)

Создаём `adapt/ADAPT-001-main.md`. AI-агент или инженер заполняет **Forward** (как поняли) и **Backward** (что неясно).

```yaml
---
id: ADAPT-001
title: "Адаптация ТЗ-2026-001 — Регистрация через email"
type: ADAPT
source-tz: { id: TZ-2026-001, signed-date: "2026-05-15", signed-by-client: "Иванов И.И., PM, ClientCo", document-version-ref: "<идентификатор версии>" }
status: draft
created: "2026-05-16"
ai-provenance: { generated-by: "anthropic-claude-opus-4-7@2026-05-16", prompt-template: "prompts/adapt-from-tz.md@v1.0", context-tokens: 1024, output-tokens: 2048, human-edits: false }
---
```

**Forward §2 Требования:**

```markdown
**Цитата:** «Пользователь может зарегистрироваться через email и пароль. После регистрации пользователь подтверждает email. После подтверждения — доступ в личный кабинет.»

**Интерпретация:** POST /auth/sign-up с {email, password}. Создаётся User status `unverified`. Отправляется email с verification link. Клик → status `verified`. Только `verified` могут войти.

**Достроенные сценарии:** sign-up; email verification; первый вход.
**Охват:** входит email/password + verification + доступ в ЛК. НЕ входит: OAuth, SMS, 2FA, сброс пароля.
**Forward links** (auto-populated после утверждения): BR-01, SR-01, SR-02, SR-03.
```

**Backward (3 записи):**

```markdown
### B-001: gap — попытка входа неверифицированного
Статус: open. Описание: ТЗ не описывает поведение системы при попытке входа до верификации email.
Вопрос клиенту: показывать сообщение «подтвердите email» с кнопкой повторной отправки — или 401 без объяснения?

### B-002: regulatory — ФЗ-152 хранение в РФ
Статус: open. Описание: ТЗ §3 требует «хранение в РФ». Что конкретно: дата-центр, юрисдикция provider, отдельная инсталляция?
Вопрос клиенту: уточнить scope ФЗ-152.

### B-003: gap — повторная отправка email
Статус: open. Описание: что если verification email потерян? rate limit?
Вопрос клиенту: policy на повторную отправку.
```

---

## Шаг 3. ACTZ и ADAPT `approved` (5 мин)

Вопросы к клиенту уходят не «в переписку»: архитектор собирает их в **ACTZ** — протокол уточнения ТЗ (в договорном обороте — «Протокол уточнения ТЗ № 1»). ACTZ подписывают обе стороны, и именно он несёт контрактный вес ([standard/07 §7.13](../standard/07-adapt.md#7.13)). Lifecycle backward: `open → asked-to-client → answered → resolved → frozen (после approval)`; lifecycle протокола: `draft → sent → signed`.

`actz/ACTZ-001.md` — решения клиента на языке обязательств:

```yaml
---
id: ACTZ-001
title: "Протокол уточнения ТЗ № 1 к TZ-2026-001"
type: ACTZ
tz-ref: TZ-2026-001
resolves: [B-001, B-002, B-003]
status: signed
client-signature: { signed-by: "Иванов И.И.", role: PM, organization: "ClientCo", signed-at: "2026-05-18T15:30:00Z" }
vendor-signature: { signed-by: "Петров П.П.", role: architect, organization: "VendorCorp", signed-at: "2026-05-18T15:40:00Z" }
---

## §1. Вход до подтверждения email
Система показывает сообщение «подтвердите email» и кнопку повторной отправки. Отказ без объяснения не допускается.

## §2. Хранение данных
Данные хранятся в дата-центре на территории РФ; юрисдикция — РФ. Выбор provider остаётся за исполнителем.

## §3. Повторная отправка письма
Не чаще 1 раза в 5 минут и не более 5 раз в сутки.
```

Подписанный протокол возвращается в ADAPT: каждая находка получает `decided-in` — ссылку на пункт **подписанного** ACTZ ([§7.4.5](../standard/07-adapt.md#7.4.5)). Без такой ссылки находка не может стать `resolved`.

```markdown
### B-001: resolved (decided-in: ACTZ-001 §1)
→ добавлен сценарий в Forward §2; создан SR-04.

### B-002: resolved (decided-in: ACTZ-001 §2)
→ data-residency: ["RU"] в Forward §2.

### B-003: resolved (decided-in: ACTZ-001 §3)
→ rate-limit правила в Forward §2; SR-04 уточнён.
```

Все backward → `resolved`, `open-questions-count = 0`. **QG-ADAPT-approve** (≡ QG-3) — все предусловия [§10.8.3](../standard/10-lifecycle-qg.md#10.8.3) выполнены. ADAPT — внутренний документ интерпретации, поэтому его подписывает **только архитектор**; клиентская подпись живёт в ACTZ ([§7.5](../standard/07-adapt.md#7.5)):

```yaml
status: approved
approval:
  # client-signature отсутствует: ADAPT — интерпретация, а не обязательство
  architect-signature: { signed-by: "Петров П.П.", role: architect, signed-at: "2026-05-18T16:00:00Z" }
ai-provenance: { human-edits: true }    # архитектор отредактировал AI-draft
open-questions-count: 0
resolved-questions-count: 3
```

После утверждения ADAPT **неизменяем** (frozen). Все BR/SR/SPEC ссылаются на approved ADAPT, не на ТЗ напрямую.

**Итоговое ТЗ** = начальное ТЗ + все подписанные ACTZ ([§7.14](../standard/07-adapt.md#7.14)). Именно против него потом сдаётся система — не против ADAPT.

---

## Шаг 4. BR-01 (3 мин)

`br/BR-01-user-registration.md`:

```yaml
---
id: BR-01
title: "Регистрация пользователей через email"
type: BR
priority: must
source: { adapt: "ADAPT-001", adapt-section: "Forward §2", tz-section: "§2" }
business-context:
  stakeholder: "Иванов И.И. (PM, ClientCo)"
  business-goal: "Дать пользователям возможность создать аккаунт и получить доступ к продукту"
business-outcome:
  measurement-type: kpi
  kpi-name: "registration-conversion-rate"
  measurement-method: "registered / visited_signup * 100%"
  baseline-value: 0
  target-value: 60
  target-met-by: "2026-09-01"
data-classification: { contains-pii: true, retention-days: 2555, data-residency: ["RU"] }   # 7 лет по ФЗ-152
compliance: [{ standard: "ФЗ-152", article: "ст.6,12" }]
ai-provenance: { generated-by: "anthropic-claude-opus-4-7@2026-05-18", human-edits: true }
---

# BR-01: Регистрация пользователей через email

Пользователь должен мочь самостоятельно создать аккаунт через email/password
и получить доступ к личному кабинету после подтверждения email.
```

---

## Шаг 5. SR-01..SR-04 (3 мин)

Декомпозируем BR на verifiable SR. Каждый SR ссылается на approved ADAPT.

`sr/SR-01-sign-up.md` (frontmatter + body):

```yaml
---
id: SR-01
title: "Sign-up через email/password"
type: SR
parent: { id: BR-01 }
source: { adapt: "ADAPT-001", adapt-section: "Forward §2", tz-section: "§2" }
constrained-by: ["SPEC-API-01", "SPEC-DATA-01", "SPEC-SEC-01"]
quality-characteristic: [functional-suitability, security]
---

## Описание
POST /auth/sign-up принимает {email, password}.
- Валидные данные → User status `unverified`, отправляется verification email, 201.
- Невалидный email (regex/format) → 422 с указанием поля.
- Уже занятый email → 409.
- Слабый пароль (< 8 chars или blacklist) → 422.
```

Аналогично — SR-02 (email verification), SR-03 (вход для verified), SR-04 (повторная отправка email с rate limit из B-003).

---

## Шаг 6. SPEC-* (3 мин)

`specs/api/SPEC-API-01-auth.md` (фрагмент):

```yaml
---
id: SPEC-API-01
title: "REST API аутентификации"
type: SPEC-API
source: { adapt: "ADAPT-001", adapt-section: "Forward §2", tz-section: "§2" }
api-style: rest
api-version: "v1.0.0"
versioning-strategy: url-path
authentication: bearer-jwt
rate-limits: [{ endpoint: "POST /auth/resend-email", limit: "1/5min/user; 5/24h/user" }]
contract-file: { format: openapi-3.1, location: "contracts/auth-api.yaml" }
depends-on: ["SPEC-DATA-01", "SPEC-SEC-01"]
referenced-by: ["SR-01", "SR-02", "SR-03", "SR-04"]   # auto-derived
---
```

Аналогично — SPEC-DATA-01 (схема User), SPEC-SEC-01 (auth model + ФЗ-152 controls).

---

## Шаг 7. TC pos/neg парность (5 мин)

Каждый SR — минимум 1 позитивный + 1 негативный TC. Каноничная схема — [reference/02 §8](../reference/02-schemas.md#8-tc--test-case).

`tests/TC-01-signup-success.md` (позитивный для SR-01):

```yaml
---
id: TC-01
title: "Sign-up: успешная регистрация"
type: TC
tc-type: system
verifies: [{ id: SR-01 }]
negative: false
automation: { status: automated, set-version: "1.0", location: "tests/auth/test_signup.py::test_signup_success", runner: pytest }
---

## Given
- новый email (uniquely-generated@test.com); валидный password (длина ≥ 8, не в blacklist).

## When
POST /auth/sign-up {email, password}

## Then
- status 201; body {"user_id": "<uuid>", "status": "unverified"}; User в БД с status `unverified`; verification email отправлен (mock).
```

`tests/TC-02-signup-invalid-email.md` (негативный для SR-01):

```yaml
---
id: TC-02
title: "Sign-up: отклонить невалидный email"
type: TC
tc-type: system
verifies: [{ id: SR-01 }]
negative: true
automation: { status: automated, set-version: "1.0", location: "tests/auth/test_signup.py::test_signup_invalid_email", runner: pytest }
---

## Given
- email "not-an-email" (без `@`); любой валидный password.

## When
POST /auth/sign-up {email, password}

## Then
- status 422; body {"field": "email", "error": "invalid format"}; User в БД НЕ создан; email НЕ отправлен.
```

Аналогично пары для SR-02, SR-03, SR-04. Для SR-04 (с rate limit) — дополнительный TC, проверяющий блок после 5-й попытки за 24 часа.

---

## Шаг 8. Утвердить версию комплекта, запустить TC, записать верификацию (2 мин)

**8.1 QG-0 комплекта.** BR-01, SR-01..04, три SPEC и все TC-нормы лежат в черновике комплекта. Собственного статуса у них нет ([standard/10 §10.7](../standard/10-lifecycle-qg.md#10.7)) — утверждается комплект целиком: на каждое утверждение есть пара TC-норм, состязательный обзор пройден, Архитектор подписывает. Выпускается запись версии ([§10.5.4](../standard/10-lifecycle-qg.md#10.5.4)):

```yaml
# renar/VERSIONS/1.0.md
set-version: "1.0"
major: false
approved-by: "Иванов И.И."
approved-at: "2026-05-19T09:00:00Z"
resolves-to: "<нативный для носителя указатель на снимок>"
changes: { added: [BR-01, SR-01, SR-02, SR-03, SR-04, SPEC-API-01, SPEC-DATA-01, SPEC-SEC-01, TC-01, TC-02, "…"] }
```

**8.2 QG-1 и прогон.** Реализации TC написаны против версии `1.0` (`automation.set-version: "1.0"`), допущены к прогону; test runner на носителе запускает их и обновляет `last-run`:

```yaml
# tests/TC-01-signup-success.md (после прогона):
last-run: { date: "2026-05-19T10:00:00Z", result: pass, runner-id: "test-runner@1.0", run-ref: "<ссылка>", set-version: "1.0" }
```

**8.3 QG-2 версии.** Когда **все** TC версии `1.0` зелёные при `last-run.set-version = 1.0` и у каждого записан переход «красный → зелёный» ([standard/09 §9.18.2](../standard/09-test-cases.md#9.18)) → спот-чек инженера ([§9.14](../standard/09-test-cases.md#9.14)): выборка из 5 прошедших TC, наводимая машинными сигналами (в первую очередь — TC без красной истории), сверяется с SR. Если спот-чек пройден → **QG-2 Verification Gate** пройден → runner дописывает запись верификации в запись версии:

```yaml
# renar/VERSIONS/1.0.md (дополняет только runner):
verification:
  - { product-version: "<указатель на сборку>", date: "2026-05-19T14:00:00Z", result: pass, runner-id: "test-runner@1.0", evidence-refs: ["<прогон>"] }
```

SR-01..04 «верифицированы» не сами по себе, а как часть пары (версия 1.0, сборка) — отдельного статуса у них нет. Версия 1.0 готова к предъявлению.

Соответствие **контракту** проверяет отдельный слой — **AT** ([standard/09 §9.19](../standard/09-test-cases.md#9.19)): приёмочные тесты, которые изолированный агент выводит только из итогового ТЗ, не заглядывая ни в ADAPT, ни в SR, ни в код. TC доказывают, что система соответствует интерпретации; AT — что интерпретация соответствует договору. Пока не все AT зелёные, продукт к сдаче не предъявляется ([§10.4.3](../standard/10-lifecycle-qg.md#10.4)).

---

## Цепочка трассировки

В любой момент восстанавливается происхождение артефакта:

```text
TC-02 (negative)
  └─ verifies SR-01 (sign-up)
       └─ derived from ADAPT-001 §2 Forward (email/password)
             └─ interprets TZ-2026-001 §2 (immutable)
             └─ B-001 decided-in ACTZ-001 §1 (signed: клиент + исполнитель)
       └─ constrained-by SPEC-API-01 (REST contract)
                                 └─ depends-on SPEC-DATA-01, SPEC-SEC-01

AT-01
  └─ verifies TZ-2026-001 §2 + ACTZ-001 §1   # только контракт, без внутренних артефактов
```

Аудит через год: «откуда взялось требование возвращать 422 на невалидный email» — TC-02 → SR-01 → ADAPT-001 §2. Трассировка полная.

---

## Что вы только что сделали

- Создали неизменяемый договорной артефакт (ТЗ).
- Прошли двустороннюю интерпретацию через ADAPT с тремя backward findings → вынесли вопросы клиенту протоколом ACTZ → подписанные решения вернулись в ADAPT через `decided-in` → approved.
- Получили итоговое ТЗ (начальное ТЗ + подписанный ACTZ-001) — эталон будущей сдачи-приёмки.
- Декомпозировали в BR + 4 SR, привязанные к approved ADAPT.
- Описали 3 SPEC (API, DATA, SEC) как параллельную ось структуры.
- Покрыли каждый SR pos+neg TC парой (минимум 8 TC).
- Прошли три шлюза качества: QG-ADAPT-approve (≡ QG-3 Architecture), QG-0 комплекта (версия 1.0) и QG-2 Verification Gate (запись верификации версии 1.0).

Это полный цикл RENAR в минимальной форме. На реальном проекте — больше BR/SR/SPEC, больше backward findings, делегирование на AI-агентов; опционально QG-4 (acceptance).

**В реальной работе шаги 2-7 выполняет AI-агент.** Инженер не пишет frontmatter и body артефактов построчно — он формулирует задачу, читает результат, уточняет и утверждает. Это штатный режим ([standard/00 §0.2.1](../standard/00-introduction.md#0.2.1)). Полный сценарий первичного ТЗ и delta-ТЗ с adversarial reviewer — в [01-walkthrough.md фаза 2 + фаза 8](01-walkthrough.md).

---

## Что дальше

| Хотите... | Документ |
|---|---|
| Детальный сквозной пример (login + 2FA, фазы 0–9) | [01-walkthrough.md](01-walkthrough.md) |
| Переход с legacy подхода на RENAR | [02-transition-guide.md](02-transition-guide.md) |
| Git как носитель (commit-policy, PR-ревью, hooks) | [03-tool-guide-git.md](03-tool-guide-git.md) |
| Документо-ориентированный носитель | [04-document-store-substrate.md](04-document-store-substrate.md) |
| Сравнение RENAR с SAFe / BABOK / ISO 29148 | [05-safe-comparison.md](05-safe-comparison.md) |
| Compliance: GDPR / ФЗ-152 / AI Act | [06-compliance.md](06-compliance.md) |
| Failure modes — типовые провалы и паттерны | [07-failure-modes.md](07-failure-modes.md) |
| Полная нормативная спецификация (16 глав) | [`standard/`](../standard/README.md) |
| Схемы артефактов и validation rules | [`reference/02-schemas.md`](../reference/02-schemas.md) |
| Глоссарий и mapping на отраслевые стандарты | [`reference/01-glossary.md`](../reference/01-glossary.md) |

---

*Быстрый старт RENAR 1.1 — renar.tech*
