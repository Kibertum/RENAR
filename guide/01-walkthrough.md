---
title: "Сквозной пример"
description: "Полноразмерный сквозной пример RENAR на проекте Login Flow для AcmeCorp."
order: 1
lang: ru
version: "1.0"
---

# 01. Сквозной пример: Login Flow для AcmeCorp

> Один полный цикл RENAR от подписанного ТЗ до accepted release. Пример — внутренний инструмент с регистрацией через корпоративный email и 2FA. Цель — показать **все этапы** на одном среднем по размеру проекте.
>
> **Контекст:** AcmeCorp, ~1 спринт работы команды, стек Next.js + FastAPI + PostgreSQL. RENAR-зрелость уровня RENAR-3+ (полный ADAPT + TC + adversarial). Пример **независим от вида хранилища**: операции через capabilities V1–V6; конкретная раскладка каталогов — [03-tool-guide-git](03-tool-guide-git.md) или [04-document-store-substrate](04-document-store-substrate.md).
>
> **Предпосылки:** [00-quickstart](00-quickstart.md), [core/renar-core](../core/renar-core.md), [reference/01-glossary](../reference/01-glossary.md).

**Фазы — иллюстрация, не норма.** Порядок фаз 0–9 — один из возможных порядков работы; стандарт нормирует предусловия и правила, а не последовательность ([standard/01 §1.2.1](../standard/01-scope.md#1.2.1)). Организация с иным процессом соответствует стандарту, пока предусловия каждого артефакта выполнены.

**Маршрут читателя.** Фазы 0–2 — сбор контекста, подписание ТЗ, ADAPT и протокол уточнения ТЗ (ACTZ). Фазы 3–4 — декомпозиция в BR/SR/SPEC и генерация пар pos/neg для TC. Фаза 5 — канонические шлюзы QG-0 (утверждение требований) и QG-1 (только переход TC `draft → ready`). Фазы 6–7 — реализация TR и верификация (QG-2). Фазы 8–9 — дельта-ТЗ при изменениях и приёмка: AT против итогового ТЗ плюс опциональный QG-4 по бизнес-результату.

---

## Фаза 0 — Сбор требований (elicitation)

До подписания ТЗ. AI-агент проводит 2-3 интервью со stakeholder (Sales Director, IT Manager) и собирает контекст в структурированном виде.

Артефакты фазы 0 (справочные, не нормативные для RENAR Core): `elicitation/{domain-context.md, sales-director.yaml, it-manager.yaml, findings-clustered.md, critic-review.md, multi-model-diff.md}`. Фаза 0 не закреплена в Core — область методологии elicitation, вне scope RENAR v1.0 ([standard/01 §1.3](../standard/01-scope.md#1.3)).

---

## Фаза 1 — Импорт ТЗ

После итераций elicitation клиент подписывает `TZ-2026-042`:

```markdown
# TZ-2026-042 — Login Flow для AcmeCorp Internal Tool
Дата подписания: 2026-05-03 · Стороны: AcmeCorp + VendorCorp

## §1. Цели
Сократить время входа сотрудников AcmeCorp в инструмент до <2 минут от первого захода до полного доступа.

## §2. Функциональные требования
### ФТ-001. Регистрация по корпоративному email
Сотрудник регистрируется через email из домена @acmecorp.com. Email вне домена — отказ с пояснением.

### ФТ-002. Двухфакторная аутентификация (TOTP)
После регистрации обязательная настройка 2FA через TOTP.

### ФТ-003. Восстановление доступа через корпоративного администратора
При потере 2FA устройства — recovery через ticket в IT support.

## §3. Нефункциональные требования
### НФТ-001. Производительность: Login <2 секунд (p95).
### НФТ-002. Безопасность: bcrypt cost-factor ≥ 12; логи входов — 1 год; блокировка после 5 неудачных попыток за 15 минут.
### НФТ-003. Юрисдикция: все данные в РФ (гос-контракты).
```

ТЗ подписан → **неизменяемо**. Любые правки идут через ADAPT (фаза 2) или delta-TZ (фаза 8). Runtime **обязан** зарегистрировать неизменяемое ТЗ как ревизию (V1+V2) с AI-provenance (V6).

---

## Фаза 2 — ADAPT и ACTZ (интерпретация и протокол уточнения)

Здесь расходятся два контура. **ADAPT** — внутренняя интерпретация: её аудитория инженер, агент и рецензент, подписывает её архитектор ([standard/07 §7.5](../standard/07-adapt.md#7.5)). **ACTZ** — протокол уточнения ТЗ: то, что вынесено клиенту и подписано обеими сторонами, то есть обязательство ([§7.13](../standard/07-adapt.md#7.13)). Граница проходит по аудитории: что показано клиенту и утверждено — обязательство; что не показано — интерпретация.

**2.1 Primary agent генерирует draft ADAPT.** Input: TZ-2026-042 (неизменяемо). Output: draft ADAPT-001 + Forward sections (по §2 ФТ + §3 НФТ) + Backward findings (6 candidates) + V6 provenance.

**2.2 Adversarial review.** Отдельный critic-agent (другая модель) проверяет backward findings; блокирует adapt-approve пока критические находки открыты. Примеры:

```text
[HIGH] B-001 reclassify gap → hidden-assumption
[HIGH] missed backward: case-sensitivity email in ФТ-001
[MEDIUM] B-004 terminology: define "сотрудник" via User.role
[MEDIUM] B-006 feasibility: rate-limit scope (IP vs email vs session)
```

**2.3 Iterative resolution.** Архитектор корректирует Forward и Backward, AI re-генерирует. После 2 циклов adversarial: 7 backward записей (B-001..B-007), все reclassified либо подготовлены к выносу клиенту; Forward охватывает §2 + §3 ТЗ полностью.

**2.4 ACTZ-001 — протокол уточнения ТЗ № 1.** Пять находок из семи требуют решения клиента. Архитектор не пересылает сырой вывод агента: он агрегирует вопросы, переформулирует их на языке обязательств («домен сравнивается без учёта регистра»), прикладывает предложения — и выносит одним протоколом. Клиент подписывает; подписывает и исполнитель.

```yaml
---
id: ACTZ-001
title: "Протокол уточнения ТЗ № 1 к TZ-2026-042"
type: ACTZ
tz-ref: TZ-2026-042
resolves: [B-001, B-002, B-004, B-006, B-007]
status: signed
client-signature: { signed-by: "Иванова А.А.", role: "Product Lead", organization: "AcmeCorp", signed-at: "2026-05-04T11:30:00Z" }
vendor-signature: { signed-by: "Петров П.П.", role: architect, organization: "VendorCorp", signed-at: "2026-05-04T11:50:00Z" }
---

## §1. Регистр email
Адрес `@AcmeCorp.com` и `@acmecorp.com` — один и тот же сотрудник: домен сравнивается без учёта регистра.

## §2. Термин «сотрудник»
Сотрудник — учётная запись с ролью `employee` в корпоративном каталоге.

## §3. Граница блокировки
5 неудачных попыток за 15 минут считаются по паре «IP + email», не по сессии.
```

Каждая находка, требующая решения клиента, получает `decided-in: ACTZ-001 §M` — ссылку на пункт **подписанного** протокола. Находка не может стать `resolved` без такой ссылки, а ADAPT не может быть утверждён при незакрытых находках ([§7.4.5](../standard/07-adapt.md#7.4.5)).

**2.5 ADAPT в статусе `approved`.** Подпись — только архитектора: клиент этот документ не видел и не подписывал, его решения живут в ACTZ-001.

```yaml
---
id: ADAPT-001
title: "Адаптация TZ-2026-042 — Login Flow AcmeCorp"
type: ADAPT
trigger-stage: import-tz
source-tz: { id: TZ-2026-042, signed-date: "2026-05-03", signed-by-client: "AcmeCorp PM" }
status: approved
approval:
  # client-signature отсутствует по §7.5 — ADAPT не выносится клиенту
  architect-signature: { signed-by: "Петров П.П.", role: architect, signed-at: "2026-05-04T12:00:00Z" }
generates-requirements: [BR-01, BR-02, SR-01, SR-02, SR-03, SR-04, SR-05, SR-06, SR-07]
generates-specs: [SPEC-UI-01, SPEC-API-01, SPEC-DATA-01, SPEC-SEC-01]
open-questions-count: 0
resolved-questions-count: 7
ai-provenance: { generated-by: "anthropic-claude-opus-4-7@2026-05-04", prompt-template: "prompts/adapt-from-tz.md@v2.1", human-edits: true }
---
```

После утверждения ADAPT-001 — **неизменяем**.

**Итоговое ТЗ** ([§7.14](../standard/07-adapt.md#7.14)) на этот момент: `TZ-2026-042` + `ACTZ-001`. Это и есть эталон, против которого система будет сдаваться в фазе 9. ADAPT в эталон приёмки не входит — клиент отвечает только за то, что подписывал.

---

## Фаза 3 — Декомпозиция в BR / SR / SPEC

**3.1 Декомпозиция.** Operation: `decompose`. Input: approved ADAPT-001. Output: draft BR (2), SR (7), SPEC (4) + adversarial-review артефактов.

**3.2 Adversarial-находки:**

```text
[HIGH] BR-01 stakeholder поле пустое — кто owner business goal?
[HIGH] SR-05 говорит "bcrypt cost-factor 12" — это deployment detail, должно быть в SPEC-SEC-01, не в SR.
[MEDIUM] НФТ-003 (юрисдикция) не отражено в data-classification SR-01.
[MEDIUM] SPEC-UI-01 не имеет accessibility-level — WCAG-AA минимум для корпоративного инструмента.
→ 4 находки → fix → re-generate.
```

**3.3 Финальный набор артефактов:**

```text
acmecorp-requirements/
├── br/
│   ├── BR-01-self-service-registration.md
│   └── BR-02-secure-mfa.md
├── sr/
│   ├── SR-01-email-domain-validation.md       (ФТ-001)
│   ├── SR-02-totp-enrollment.md               (ФТ-002 setup)
│   ├── SR-03-totp-verification.md             (ФТ-002 verify)
│   ├── SR-04-password-recovery-via-admin.md   (ФТ-003)
│   ├── SR-05-rate-limiting-failed-logins.md   (НФТ-002)
│   ├── SR-06-audit-logging.md                 (НФТ-002 audit)
│   └── SR-07-data-residency-ru.md             (НФТ-003)
├── specs/
│   ├── ui/SPEC-UI-01-login-flow.md
│   ├── api/SPEC-API-01-auth.md
│   ├── data/SPEC-DATA-01-user-model.md
│   └── sec/SPEC-SEC-01-auth-policy.md
└── tz/TZ-2026-042.md
```

**3.4 Пример: SR-01 (frontmatter + body):**

```yaml
---
id: SR-01
title: "Валидация домена email при регистрации"
type: SR
parent: { id: BR-01 }
source: { adapt: "ADAPT-001", adapt-section: "Forward §2.1", tz-section: "§2 ФТ-001" }
constrained-by: ["SPEC-API-01", "SPEC-DATA-01", "SPEC-SEC-01"]
data-classification: { contains-pii: true, data-residency: ["RU"], retention-days: 365 }
compliance: [{ standard: "ФЗ-152", article: "ст.13.1" }]
ai-provenance: { generated-by: "anthropic-claude-opus-4-7@2026-05-04", prompt-template: "prompts/decompose-adapt.md@v2.1", context-tokens: 12450, output-tokens: 320, human-edits: true }
---

## Описание
Регистрация разрешена только если email принадлежит домену `@acmecorp.com`. Остальные домены отклоняются с пояснением.

## Поведение
- email НЕ из `@acmecorp.com` → 422 с `{"error":"email-domain-not-allowed", "allowed-domain":"acmecorp.com"}`. [ADAPT-001 Forward §2.1; TZ-2026-042 §2 ФТ-001]
- email из `@acmecorp.com` → стандартная регистрация (SPEC-API-01).
- Whitelist хранится в `SPEC-SEC-01.allowed-domains`, расширяется без релиза.
- Сравнение домена — case-insensitive (ADAPT-001 Forward §2.1).

## Ограничения
- `*.acmecorp.com` subdomain — отдельное решение архитектора (не входит).
```

**3.5 SPEC-API-01 (фрагмент):**

```yaml
---
id: SPEC-API-01
title: "REST API аутентификации"
type: SPEC-API
source: { adapt: "ADAPT-001", adapt-section: "Forward §2" }
api-style: rest
api-version: "v1.0.0"
versioning-strategy: url-path
authentication: bearer-jwt
rate-limits: [{ endpoint: "POST /auth/login", limit: "5/15min/ip+email" }]
contract-file: { format: openapi-3.1, location: "contracts/auth-api.yaml" }
depends-on: ["SPEC-DATA-01", "SPEC-SEC-01"]
---

## Endpoints

### POST /auth/register
- body: `{"email": "<corp-email>", "password": "<strong>"}`
- 201 → `{"user_id": "<uuid>", "verified": false, "totp_setup": false}` · 422 → invalid · 409 → email exists

### POST /auth/login
- body: `{"email", "password", "totp": "<6digits>"}`
- 200 → `{"access_token": "<jwt>", "expires_in": 3600}` · 401 → invalid · 429 → rate limit

### POST /auth/totp-setup — см. SR-02.

## Error model
Единая структура: `{"error": "<code>", "details": {...}}`.
```

---

## Фаза 4 — Генерация TC (пары pos/neg)

Operation: `tc-generate` SR-01 → pos/neg TC pairs per testable assertion ([standard/09 §9.7](../standard/09-test-cases.md#9.7): на каждое утверждение SR — минимум одна пара positive + negative TC).

**TC-001 (позитивный):**

```yaml
---
id: TC-001
title: "Регистрация с разрешённым доменом — happy path"
type: TC
tc-type: system
verifies: [{ id: SR-01 }]
negative: false
automation: { status: automated, location: "tests/auth/test_registration.py::test_allowed_domain_succeeds", runner: pytest }
---

## Контекст
SR-01, раздел «Требование»: регистрация с email из whitelist-домена создаёт учётную запись.

## Предусловия
- БД пуста; email alice@acmecorp.com не зарегистрирован.

## Шаги
POST /auth/register {email: "alice@acmecorp.com", password: "ValidPass123!"}

## Pass-критерий
- status 201; body содержит {"user_id": "<uuid>", "verified": false, "totp_setup": false}; User в БД создан; verification email отправлен (mock SES).

## Fail-критерий
- status ≠ 201; plaintext password в body/логах; User с другим email (case mismatch); email верификации не отправлен.

## Постусловия
- User удалён seed-механизмом; очередь mock SES очищена.

## Out of scope
- TOTP setup → TC-005 (SR-02); rate limiting → TC-009 (SR-05).
```

**TC-004 (негативный):**

```yaml
---
id: TC-004
title: "Регистрация с неразрешённым доменом — отказ с пояснением"
type: TC
tc-type: system
verifies: [{ id: SR-01 }]
negative: true
automation: { status: automated, location: "tests/auth/test_registration.py::test_disallowed_domain_rejected", runner: pytest }
---

## Контекст
SR-01, раздел «Поведение»: email вне whitelist-домена отклоняется с объяснимой причиной.

## Предусловия
- email "bob@gmail.com" (вне whitelist).

## Шаги
POST /auth/register {email: "bob@gmail.com", password: "ValidPass123!"}

## Pass-критерий
- status 422; body == {"error": "email-domain-not-allowed", "allowed-domain": "acmecorp.com"}; User в БД НЕ создан; email НЕ отправлен; audit-запись о rejected attempt (для SR-06).

## Fail-критерий
- status ≠ 422; User создан; email отправлен (security leak); audit-запись отсутствует.

## Постусловия
- Audit-запись удалена seed-механизмом.

## Out of scope
- Формат audit-записи → TC-021 (SR-06).
```

---

## Фаза 5 — Шлюзы качества перед кодом (QG-0 и QG-1)

После генерации TC для всех 7 SR — суммарно 26 записей контрольных примеров (пары pos/neg + дополнительные негативы для SR-05, SR-06).

**5.1 QG-0 — утверждение версии комплекта 1.0.** BR-01, семь SR, SPEC и 26 TC-норм утверждаются одной версией ([standard/10 §10.5.2](../standard/10-lifecycle-qg.md#10.5.2)). Предусловия: `source.adapt = ADAPT-001 (approved)`; дерево полно (у каждого SR — `parent`, у каждого SPEC — источник); adversarial-review успешен по каждому артефакту; на каждое утверждение — пара TC-норм; утверждения и связи cite разделы ADAPT-001. Постусловие: запись версии `1.0` с подписью Архитектора; отдельных статусов у BR/SR/SPEC нет.

**5.2 QG-1 — допуск реализаций TC.** По [§10.3.2](../standard/10-lifecycle-qg.md#1032-qg-1--гейт-реализации-проверки-только-tc) QG-1 применим только к реализации TC и отделяет допущенный к прогону код проверки от ещё не готового. Предусловия: `automation.set-version: "1.0"` указывает на утверждённую версию (V5); `automation.status` + `location` валидны; статические проверки и сухой прогон пройдены; фиксирующий красный прогон записан; обязательные секции body заполнены. Постусловие: реализация допущена.

После **QG-0** версии 1.0 и **QG-1** на каждой реализации TC можно открывать работу по TR (фаза 6).

---

## Фаза 6 — Реализация

> **Порядок не случаен.** TC заморожены (`ready`) в фазе 5 — **до** первой строки кода, и писал их не тот агент, который сейчас пишет реализацию ([standard/09 §9.18](../standard/09-test-cases.md#9.18)). Тест, рождённый рядом с кодом, проверяет код, а не требование. Отсюда же требование красной истории: перед реализацией TC прогоняется и обязан упасть — тест, ни разу не бывший красным, ничего не доказывает.

**6.1 Создание задач (TR).** Operation `sync-tasks`: input — verified SR/SPEC set; output — 7 TR in implementation tracker (parent SR, implements-spec[], QG-0 ready с Goal + AC).

**6.2 Разработчик берёт TR-101.** QG-0 checks: Goal from SR-01; AC list (4 items); parent.id resolves (approved); implements-spec present; negative scenario in AC → work session allowed.

**6.3 Реализация (фрагмент):**

```python
# acmecorp-login.src/src/auth/registration.py
from fastapi import HTTPException
from config import settings   # allowed-domains из SPEC-SEC-01

def validate_email_domain(email: str) -> None:
    domain = email.split("@", 1)[-1].lower()
    if domain not in settings.AUTH_ALLOWED_DOMAINS:
        raise HTTPException(status_code=422, detail={
            "error": "email-domain-not-allowed",
            "allowed-domain": settings.AUTH_ALLOWED_DOMAINS[0],
        })
```

```python
# acmecorp-login.src/tests/auth/test_registration.py
def test_allowed_domain_succeeds(client, db, mock_ses):
    r = client.post("/auth/register", json={"email": "alice@acmecorp.com", "password": "ValidPass123!"})
    assert r.status_code == 201
    assert "user_id" in r.json()
    user = db.query(User).filter_by(email="alice@acmecorp.com").one()
    assert user.verified is False
    mock_ses.send_email.assert_called_once_with(template_id="verification-email", to_email="alice@acmecorp.com")

def test_disallowed_domain_rejected(client, db):
    r = client.post("/auth/register", json={"email": "bob@gmail.com", "password": "ValidPass123!"})
    assert r.status_code == 422
    assert r.json() == {"error": "email-domain-not-allowed", "allowed-domain": "acmecorp.com"}
    assert db.query(User).count() == 0
```

**6.4 Хук валидации на стороне носителя:**

```text
[hook] Проверка связей TR-101: parent.id SR-01 (approved); implements-spec [SPEC-API-01, SPEC-SEC-01].
[hook] Негативные TC: SR-01.verified-by включает TC-002, TC-004 (negative).
✓ Изменение разрешено.
```

---

## Фаза 7 — QG-2 (шлюз верификации)

**7.1 CI запускает TC.** `pytest acmecorp-login.src/tests/auth/test_registration.py` → 4 TC PASSED → Bot обновляет `last-run.result = pass`, `last-run.set-version = 1.0` в TC файлах. В истории прогонов у каждого TC виден переход «красный → зелёный»: фиксирующий прогон до реализации был красным — это условие, при котором TC засчитывается как доказательство на QG-2 ([§9.18.2](../standard/09-test-cases.md#9.18)).

**7.2 Выборочная проверка ([standard/09 §9.14](../standard/09-test-cases.md#9.14)).** Раз в спринт инженер вручную запускает 5 passing TC, отобранных по машинным сигналам (приоритет — TC без красной истории, затем пережившие мутанта, затем класса `implementation-originated`; остаток — случайным добором), и сверяет фактический результат с SR. Selected: TC-001, TC-008, TC-012, TC-019, TC-024 → 5/5 совпадают.

**7.3 Запись верификации версии 1.0.** QG-2 предусловия: все TC версии прошли при `last-run.set-version = 1.0`; обе TC каждой пары; красная история; выборочная проверка пройдена. Постусловие: runner дописывает `verification` в запись версии 1.0 ([§10.5.4](../standard/10-lifecycle-qg.md#10.5.4)) — SR-01 верифицирован как часть пары (версия 1.0, сборка), собственного статуса у него нет; обновляется индекс покрытия.

---

## Фаза 8 — Дельта-ТЗ

**8.1 Клиент через неделю:**

```markdown
# TZ-2026-051 — Дополнение к TZ-2026-042
Базовый: TZ-2026-042

## §2 (изменение) ФТ-001 (расширение)
Дополнительно разрешить @subsidiary.acmecorp.com (дочерняя компания). Whitelist расширяется до 2 доменов.
```

**8.2 Delta-ADAPT + ACTZ-002.** Operation `adapt-from-tz (delta)`: input — TZ-2026-051 + parent ADAPT-001; output — draft ADAPT-001-delta-1 + delta Forward + backward findings (e.g. B-008 scope). Находка B-008 («считать ли `@sub.subsidiary.acmecorp.com` разрешённым?») выносится клиенту протоколом `ACTZ-002` — «Протокол уточнения ТЗ № 2»; после подписания B-008 получает `decided-in: ACTZ-002 §1`, и delta-ADAPT утверждается подписью архитектора. Итоговое ТЗ становится: `TZ-2026-042` + `TZ-2026-051` + `ACTZ-001` + `ACTZ-002`.

**8.3 Анализ влияния.** Operation `impact-analysis --delta TZ-2026-051`:

```text
Affected:
  BR-01 (расширение охвата)
  SR-01: изменён в черновике → войдёт в версию 1.1
  TC-001..004: нормы изменены → реализации устареют (automation.set-version 1.0 ≠ 1.1); +2 new TC (subsidiary domain)
  TR-115: new implementation task
  SPEC-SEC-01: allowed-domains extended
```

**8.4 Apply delta.** Архитектор открывает изменения с маркером `[delta:TZ-2026-051]`. AI обновляет SR-01 (расширяет whitelist), генерирует 2 новых TC. Реализация в TR-115. CI прогоняет TC, бот обновляет last-run. Черновик утверждается версией 1.1 (QG-0 комплекта); реализации пересоздаются против новой нормы (`automation.set-version: "1.1"`). После spot-check — запись верификации версии 1.1.

> **Note (simple delta).** Если adversarial reviewer выносит вердикт «no findings, no clarifications» ([§7.4.1.2](../standard/07-adapt.md#7.4.1)), **delta-ADAPT не создаётся** — BR/SR/SPEC получают `source.tz-section` напрямую с зафиксированным `adversarial-review-ref`. Тривиальное изменение (переименование поля) не порождает ни интерпретации, ни протокола: спрашивать клиента не о чем.

---

## Фаза 8а — Три входа изменения комплекта

Дельта-ТЗ — не единственный путь, которым комплект описания получает новую версию. Изменение приходит тремя входами; различаются они моментом и тем, что уже известно, а не ценой: каждый даёт минорную версию сразу ([standard/10 §10.5.1](../standard/10-lifecycle-qg.md#10.5.1)), задачи растут из изменённого комплекта, а не из текста входа.

| Вход | Когда | Что известно | Провенанс в BR / SR / SPEC | Что производит |
|---|---|---|---|---|
| **Дельта-ТЗ** (фаза 8) | клиент изменил обещание | новый текст контракта | `source.tz-section` на дельту; при находках — ADAPT / ACTZ | версия комплекта; переделка с аннулированием верификации |
| **Концепт на изменение** | инженер видит доработку раньше, чем есть задача — до QG-0 задачи | целевое устройство: как должно быть | `source.adversarial-review-ref` + ссылка на принятый концепт в записи версии | версия комплекта (минор) |
| **Обратная находка / проблема** | в ходе задачи или после факта: провал TC, дефект, замечание клиента | только «что не так» | зависит от исхода разбора (таблица ниже) | диагноз → дефект реализации либо версия комплекта |

**Концепт на изменение** описывает целевое устройство — без сравнений с текущим, без вариантов и без открытых вопросов; он не входит в комплект и клиентом не подписывается. Принятый Архитектором концепт **вносится в комплект** и даёт минор; дальше он — история версии, не источник истины. Отложенная реализация — запись в очереди без собственной версии комплекта. Устное поручение или пожелание входом не является: его оформляют концептом. Для решений, которые проверяются поведением экрана, а не текстом, концепт дополняет или заменяет кликабельный прототип на условных данных — в комплект входит только то, что из него внесено в SPEC-UI.

**Исходы разбора проблемы.** Разбор устанавливает, кто неправ, и только затем появляется дефект. Пять исходов и их место в RENAR:

| Исход | Кто неправ | В RENAR | Комплект | Контракт |
|---|---|---|---|---|
| **К** — код | реализация | дрейф источника истины ([standard/04 §4.11](../standard/04-terms.md#4.11), класс 4.11.3) → TR против текущей версии | не тронут | не тронут |
| **Т** — тест | реализация TC (норма верна) | реализация TC пересоздаётся против той же нормы ([standard/09 §9.9](../standard/09-test-cases.md#9.9)); красная история сохраняется | не тронут | не тронут |
| **Э** — описание | комплект | обратная находка внутреннего контура ([standard/06 §6.13.2](../standard/06-requirements-hierarchy.md#6.13.2)): `gap`, `hidden-assumption`, `contradiction`, `terminology`, `feasibility` без наблюдаемых клиентом последствий | минор сразу либо запись в очереди | не тронут |
| **И** — обещание нужно поменять | контракт | обратная находка с контрактным исходом: `scope`, `contradiction`, `regulatory` или любая категория с наблюдаемым клиентом поведением → проект ACTZ, двусторонняя подпись ([standard/07 §7.13](../standard/07-adapt.md#7.13)) | версия после подписи ACTZ; аннулирование верификации затронутых TC | изменён |
| **Н** — нужно то, чего не обещали | вне заказа | `scope` (не входит) → дельта-ТЗ либо новый ТЗ; в текущий комплект не попадает | не тронут | новый |

Отсев — «требование вне объёма реализации» — проблемой не становится и записи не оставляет. Граница, которую агент не переходит: исходы **Э, И, Н объявляет только человек** — Архитектор для Э, уполномоченное лицо исполнителя вместе с Архитектором для И / Н ([standard/05 §5.4](../standard/05-roles.md#5.4)); агент предлагает диагноз и готовит проект ACTZ, но версию не утверждает и подписи не ставит. К и Т агент разбирает и чинит сам: комплект не тронут, правка идёт обычной задачей.

---

## Фаза 9 — Приёмка: AT против итогового ТЗ + QG-4

**9.1 Перегенерация AT.** Перед испытаниями изолированный агент заново выводит приёмочные тесты **AT** из действующей редакции итогового ТЗ ([standard/09 §9.19](../standard/09-test-cases.md#9.19)). На вход ему подаётся только `TZ-2026-042` + `TZ-2026-051` + `ACTZ-001` + `ACTZ-002`. Доступа к ADAPT, BR/SR/SPEC, TC и коду у него нет, и модель его отличается от модели основного агента — иначе он воспроизведёт ту же интерпретацию, и приёмка выродится в повторную верификацию.

```yaml
---
id: AT-03
title: "Регистрация из домена дочерней компании"
type: AT
verifies: [{ tz: "TZ-2026-051 §2" }, { actz: "ACTZ-002 §1" }]
tz-version: "effective@2026-05-14"
generator: { vendor: "<vendor-B>", model: "<model-B>", internal-context-access: false }
negative: true
status: passing
environment-ref: SPEC-TEST-01
automation: { kind: dynamic, location: "at/test_subsidiary_domain.py", runner: pytest }
---

## tz_text
«Дополнительно разрешить @subsidiary.acmecorp.com (дочерняя компания). Whitelist расширяется до 2 доменов.»
```

Дословная цитата пункта контракта рядом с шагами проверки — обязательная часть тела AT: на испытаниях предъявляется сам пункт договора, а не его пересказ.

**9.2 Релизный гейт.** Продукт не предъявляется к сдаче, пока не все AT в статусе `passing` ([§10.4.3](../standard/10-lifecycle-qg.md#10.4)). Прогон даёт 11/12 зелёных: `AT-07` («восстановление доступа через администратора») красный при всех зелёных TC. Это самая ценная строка таблицы маршрутизации ([§9.19.5](../standard/09-test-cases.md#9.19)): AT ✗ / TC ✓ — ошибка **интерпретации**, а не кода. Разбор подтверждает: ADAPT свёл recovery к сбросу пароля, тогда как ТЗ требует ticket в IT support. Чинится ADAPT (errata) и производные SR/TC, после чего AT-07 зеленеет. Отчёт `ACCEPTANCE.md` — покрытие AT по разделам итогового ТЗ — актуализируется автоматически.

**9.3 QG-4 (опционально) — бизнес-результат.** Через 4 недели после релиза измеряется бизнес-эффект. Гейт не подменяет приёмку по контракту и с ней не смешивается: цель может быть достигнута при несоответствии договору, и наоборот.

```text
BR-01 KPI: Time-to-first-login — target <2min P95, actual 1.4min (143%)
BR-02 KPI: 2FA adoption — target ≥95%, actual 97%
```

**9.4 Sign-off.** Клиент принимает результат: BR версии комплекта принят (QG-4), отметка — в записи версии. Архив: `ACCEPTANCE.md` + `QG-4-REPORT-v1.0.md` + lessons learned `lessons/2026-Q2.md`.

---

## Финальные артефакты

```text
acmecorp-requirements/
├── adapt/                 ADAPT-001-main.md (frozen) + ADAPT-001-delta-1.md (frozen)
├── actz/                  ACTZ-001.md + ACTZ-002.md (signed, immutable)
├── br/                    BR-01 + BR-02 (status: accepted)
├── sr/                    SR-01 (v1.1, verified) + SR-02..SR-07 (v1.0, verified)
├── specs/                 SPEC-UI-01 + SPEC-API-01 + SPEC-DATA-01 + SPEC-SEC-01 (verified; SPEC-SEC-01 v1.1)
├── tests/                 TC-001..TC-028 (28 TC, 100% passing)
├── at/                    AT-01..AT-12 (перегенерированы перед испытаниями, 100% passing)
├── tz/                    TZ-2026-042.md + TZ-2026-051.md (delta, immutable)
├── elicitation/           # артефакты фазы 0
├── lessons/2026-Q2.md     # уроки фазы 9
├── ACCEPTANCE.md          # покрытие AT по итоговому ТЗ
└── QG-4-REPORT-v1.0.md    # отчёт по бизнес-результату
```

Итоговое ТЗ здесь — не файл, а вычисляемая сумма: `TZ-2026-042` + `TZ-2026-051` + `ACTZ-001` + `ACTZ-002`. Именно она подавалась на вход генератору AT.

---

## Метрики проекта

| Метрика | Значение |
|---|---|
| RDLT (TZ signed → all SR verified) | 11 days |
| Coverage Velocity | 100% за 2 спринта |
| Hallucination Rate (детектированных) | 0% |
| Найдено adversarial-находок (цикл 1) | 4 high + 2 medium |
| Test-spec drift на delta-ТЗ | 0% |
| Подписанных ACTZ | 2 (протоколы уточнения ТЗ № 1 и № 2) |
| AT на испытаниях | 12 (1 красный → ошибка интерпретации, исправлена) |
| Споры на приёмке | 0 (1 находка, закрыта до подписания) |
| Cost per BR | $0.46 (gen) + $0.18 (critic) = $0.64 |
| Total AI cost | ~$8.50 |
| BRs accepted | 2/2 |
| Дней до accept | 35 |

---

## Что показывает этот пример

1. **Прозрачность** — каждый артефакт имеет provenance, каждый переход — шлюз с явными условиями.
2. **Скорость** — декомпозиция approved ADAPT — десятки секунд + 2 цикла adversarial.
3. **Трассировка** — от строки в ТЗ до passing TC за несколько операций запроса к носителю.
4. **Дельта-ТЗ** — затронутые SR/SPEC/TC/TR вычисляются автоматически.
5. **Два контура вместо одного** — клиент подписывает ACTZ (обязательства), архитектор подписывает ADAPT (интерпретация). Спор «уточнение это или уже изменение» не возникает: достаточно спросить, выносилось ли решение клиенту.
6. **Приёмка против контракта** — AT, выведенные изолированным агентом из итогового ТЗ, поймали ошибку интерпретации, которую все 28 зелёных TC пропустили в принципе.
7. **Замыкание контура** — QG-4 связывает результат с бизнес-метриками (KPI achievement).
8. **AI-нативность** — критик и генератор — разные модели (изоляция); выборочная проверка находит расхождения, которые может пропустить только автоматический прогон.

---

## Что дальше

- [02-transition-guide.md](02-transition-guide.md) — переход с legacy подхода.
- [03-tool-guide-git.md](03-tool-guide-git.md) — git как носитель.
- [04-document-store-substrate.md](04-document-store-substrate.md) — документо-ориентированный носитель.
- [05-safe-comparison.md](05-safe-comparison.md) — сравнение с SAFe / BABOK / ISO 29148.
- [06-compliance.md](06-compliance.md) — compliance mapping (GDPR / ФЗ-152 / AI Act).
- [07-failure-modes.md](07-failure-modes.md) — failure modes.

---

*Сквозной пример RENAR 1.1 — renar.tech*
