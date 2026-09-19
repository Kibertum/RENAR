---
title: "Руководство по RU-стилю"
description: "Canonical RU style для RENAR Standard — terminology (§1) + RFC-2119 wording (§2) + formatting (§3). Тонкий public guideline; operational enforcement — scripts/style-guide-check.js."
order: 6
lang: ru
version: "1.2.1"
status: approved
---

# RENAR RU Style Guide

> **Нормативный артефакт.** Этот документ — canonical Style Guide для RU нормативного корпуса RENAR Standard: терминология ([§1](#1-терминология)), RFC-2119 RU wording ([§2](#2-rfc-2119-ru-normative-wording)), форматирование ([§3](#3-formatting-conventions)).
>
> **Применение:** любая editorial правка RU контента в `standard/`, `reference/`, `core/` (и с soft-application — `guide/`).
>
> **Operational enforcement:** `scripts/style-guide-check.js` + сопутствующие гейты (`scripts/check-rfc-modals.js`, `scripts/check-substrate-term.js`, `scripts/check-literary-headings.js`). Style Guide задаёт **правила**, скрипты — **enforcement**.
>
> **История версий:** [CHANGELOG.md](https://github.com/Kibertum/RENAR/blob/main/CHANGELOG.md).

---

## §0 Introduction

### §0.1 Назначение

Style Guide фиксирует **форму** RU нормативного контента:

- какие термины остаются латиницей, какие переводятся в prose, какие — кальки (§1);
- regime для RFC-2119 keywords (RU lowercase canonical, явный carve-out per RFC 8174) (§2);
- canonical форматирование citations / code-fences / headings / tables / typography / frontmatter (§3).

Style Guide **не** dictates per-sentence wording — задаёт constraints, внутри которых editor применяет judgement.

### §0.2 Normative status

Style Guide — **normative companion** RENAR Standard.

- Любая editorial правка RU нормативного контента **обязана** соответствовать §1–§3.
- При расхождении формулировок — побеждает [`standard/04-terms.md`](../standard/04-terms.md) (canonical terminology); §1 этого Style Guide отражает результат.

### §0.3 Scope

**In scope:** RU prose, headings, tables, lists, code-fences, frontmatter в `standard/`, `reference/`, `core/`, `guide/`; cross-file links; нормативная семантика (no MUST → SHOULD downgrade); RU typography.

**Out of scope:** EN-переводы (`*/en/` — отдельный [EN Style Guide](en/06-en-style-guide.md), активен с EN translation epic); код в `scripts/`, `site/`, `.tausik/` (governed by tech-stack conventions); imagery / SVG (deferred); `research/` drafts pre-publish (author discretion).

### §0.4 Hierarchy of authority

При конфликте источников побеждает первый match:

1. [`standard/04-terms.md`](../standard/04-terms.md) — canonical terminology RENAR.
2. [`standard/01-scope.md §1.7`](../standard/01-scope.md#1.7) — closed-list policy.
3. Style Guide §1–§3 — operational normative form.
4. SENAR (родительский стандарт) — для общеинженерной терминологии.
5. ISO/IEC/IEEE 29148:2018 — для requirements engineering терминов.

### §0.5 Change procedure

Изменения Style Guide — formal procedure (mirrors closed-list policy [`standard/01 §1.7`](../standard/01-scope.md#1.7)):

1. Propose — `research/ru-style-amendment-NNN-<topic>.md` с rationale.
2. Review — Style Guide editor (или project owner).
3. Pilot — accepted amendment проходит pilot validation на 1 главе.
4. Lock-in — merged + повышение версии.
5. Retroactive — уже пройденные главы пересканируются на compliance.

### §0.6 Versioning

| Version | Status | Дата |
|---|---|---|
| v1.0 | approved | 2026-05-17 (Phase 3 pilot validated) |
| v1.1.x | maintenance | 2026-05-19..20 (patches) |
| v1.2 | approved | 2026-05-27 (compression pass — historical content в [CHANGELOG.md](https://github.com/Kibertum/RENAR/blob/main/CHANGELOG.md)) |
| **v1.2.1** | **approved** | **2026-06-06 (EN epic sync: §0.3 + §3.6 — EN Style Guide активен)** |

---

## §1 Терминология

### §1.1 Принципы

Шесть нормативных принципов, обязательные для применения Style Guide:

1. **No mass find-replace** — каждое term-decision контекстуальное. Слепая поиск-замена ломает нормативный смысл. Editor работает per-occurrence.
2. **Preserved normative semantic** — правка, меняющая RFC-2119 level (MUST/SHOULD/MAY discrimination) — это content change, не editorial. См. §2.5.
3. **AI-bias guard** — LLM trained on web English вписывает anglicism («имплементация», «эссенциальный») и tolerate их в editorial review. Применяется explicit checklist + human spot-check, не «звучит нормально».
4. **Closed list canonical terms** — §1.9 фиксирует closed list canonical RENAR terms (mirrors [`standard/04`](../standard/04-terms.md)). Локальное создание новых на уровне проекта — запрещено.
5. **Single source of truth** — canonical normative источник: [`standard/04-terms.md`](../standard/04-terms.md). [`01-glossary.md`](01-glossary.md) — informative companion.
6. **Domain-context preservation** — технические термины носителя / VCS / SE (`commit`, `merge`, `hook`, `pipeline`, `runner`, `linter`) остаются латиницей: они конвенции отрасли, RU-эквиваленты теряют точность.

### §1.2 Шесть buckets

Все термины RU нормативного корпуса классифицируются в одну из шести buckets:

| Bucket | Категория | Policy | Section |
|---|---|---|---|
| A | Technical identifiers (носитель / VCS / SE domain) | Keep latin | §1.3 |
| B | Status vocabulary (lifecycle статусы) | Keep latin (matches YAML schema) | §1.4 |
| C | Organisational vocabulary | Rewrite в RU (prose); keep в YAML/code | §1.5 |
| D | Accepted кальки | Keep, density-cap 1% per chapter | §1.6 |
| E | Rewrite кальки | Per-term replacement table | §1.7 |
| F | Translit | Triage: accepted / borderline / rewrite | §1.8 |

### §1.3 Bucket A — technical identifiers (keep latin)

**Правило:** остаются латиницей в prose, code-fence content, frontmatter, section labels (когда technical).

| Term | Domain | RU UI projection (только в `guide/`) |
|---|---|---|
| `commit` | VCS | фиксация |
| `merge` / `diff` / `branch` / `push` / `pull` | VCS | слияние / различие / ветка / отправка / получение |
| `hook` / `patch` | VCS | перехватчик / патч |
| `frontmatter` | Markdown | заголовочный блок |
| `manifest` | RENAR | манифест |
| `slug` / `hash` | Markdown / crypto | слаг / хеш |
| `trigger` | RENAR enforcement | триггер |
| `runner` | CI / testing | прогонщик |
| `pipeline` | CI | конвейер |
| `release` / `deploy` / `build` | DevOps | релиз / развёртывание / сборка |
| `workflow` | RENAR / VCS | рабочий поток |
| `linter` / `parser` / `compiler` | tooling | линтер / парсер / компилятор |
| `tracker` | RENAR | трекер |
| `substrate` (только поля/код/имена файлов) | RENAR | **носитель** (prose canonical) |
| `provenance` | RENAR | происхождение |
| `fallback` | SE / RENAR ops | запасной вариант |

**RU UI projection** — допустима в multilingual UI ([`standard/04 §4.13.3`](../standard/04-terms.md#4.13.3)); в `standard/`, `reference/`, `core/` — всегда canonical латиница.

> **`substrate` → «носитель» в prose.** Канонический термин в RU prose — **«носитель»** (склоняется). Латиницей `substrate` остаётся только в именах полей (`substrate-capabilities`), коде/YAML и именах файлов (`03-substrate-versioning.md`). Канонический источник — [`standard/04 §4.8`](../standard/04-terms.md#4.8).

**Cross-bucket extension:** Bucket A whitelist открытый — новые VCS / SE / CI / observability domain-terms добавляются proactively при необходимости. Brand names (`GitHub`, `Astro`, `Markdown`) — proper nouns, не Bucket A.

### §1.4 Bucket B — status vocabulary (keep latin)

**Правило:** lifecycle status identifiers (`draft`, `proposed`, `approved`, `verified`, `accepted`, `done`, `obsolete`, `deprecated`, `frozen`, `active`, `planning`, `blocked`, `review`, `ready`, `passing`, `failing`, `client-ready`, `answered`, `resolved`, `revised`) остаются латиницей в prose, frontmatter, code-fences.

**Rationale:** status — system-readable identifier, появляется в YAML (`status: approved`); state machines [`standard/10`](../standard/10-lifecycle-qg.md) определены через эти identifiers; перевод в prose разрывает референциальную цепь.

**RU UI projection** (допустима только в `guide/`): `draft` → «черновик», `approved` → «утверждённый», `verified` → «проверенный», `accepted` → «принятый», `done` → «выполненный», `deprecated` → «удалённый», `frozen` → «замороженный», и т.д. См. полный список в [`01-glossary.md §2.6`](01-glossary.md#2.6).

**Case consistency:** всегда lowercase. `Proposed` / `ПРЕДЛОЖЕННЫЙ` — paste-error.

### §1.5 Bucket C — organisational vocabulary (rewrite в prose)

**Правило:** в **prose** заменить на RU; в **YAML field names, code, fence content** — keep latin.

| Latin (prose) | RU canonical | Notes |
|---|---|---|
| `scope` | область (применения) / охват | YAML key `scope:` keeps latin |
| `audit` | ревизия / проверка / контроль | прозовый `audit trail` / `audit log` → «журнал аудита» (§1.14); идентификатор `audit-trail` / путь — keep latin |
| `policy` | правило / политика | «closed list policy» — keep latin (RENAR canonical phrase) |
| `ownership` | владение / ответственность | — |
| `default` | по умолчанию | YAML `default:` keeps latin |
| `bypass` / `rollback` / `override` / `stub` | обход / откат / переопределение / заглушка | — |
| `guide` (noun) | руководство | директория `guide/` — keep latin (path) |
| `walkthrough` | прохождение / пошаговый разбор | — |
| `quickstart` | быстрый старт | — |

**Context discrimination:** перед rewrite — проверить:
- Pure prose («scope нормирует X») → replace.
- YAML / code field name (`scope:`) → keep.
- Идентификатор / путь домена носителя (`audit-trail`, `commit`) → keep latin; прозовый `audit trail` / `audit log` → «журнал аудита» (§1.14).
- Cross-ref label («closed list policy» как RENAR canonical phrase) → keep latin.

### §1.6 Bucket D — accepted кальки (keep, density-cap 1%)

**Правило:** accepted кальки остаются как есть; density-cap **1% prose-words per chapter**. При превышении — реструктуризация / synonyms / pronominals.

**Accepted list:** `нормативный`, `реализация`, `спецификация`, `формальный`, `опциональный`, `атомарный`, `декомпозиция`, `композиция`, `верификация`, `валидация`, `идентификация`, `идентификатор`, `интеграция`, `миграция`, `авторизация`, `аутентификация`, `итерация`.

**Density measurement:** density = (term occurrences) / (prose words after strip YAML/fences/inline-code) × 100. Cap: 1.0% per chapter. Enforcement — `scripts/style-guide-check.js` density mode.

### §1.7 Bucket E — rewrite кальки

**Правило:** заменить на RU canonical per replacement table. Manual per-occurrence (no mass-replace per §1.1).

| Latin (calque) | RU canonical | Notes |
|---|---|---|
| `имплементация` | реализация (общее) / исполнение (active-voice) | — |
| `конформация` | соответствие | **Exception:** `conformance` — ISO 29148 term, keep latin в контексте носителя ([`standard/13`](../standard/13-conformance.md)) |
| `эссенциальный` | существенный / обязательный | context-dependent |
| `дрифт` (translit) | дрейф / расхождение | **Exception:** `drift` (latin) — RENAR canonical class name; keep `Schema drift / Lifecycle drift` латиницей |
| `трейс*` (in prose) | трасс* / отслеживание | `traceability` — RENAR canonical, keep latin |
| `аннотация` (AI-bias overuse) | пояснение / примечание | context-dependent |
| `регуляция` | регулирование | rare, AI-bias residue |

**Exceptions:** frozen quote / citation — keep verbatim; field name в YAML schema — never touch; cross-ref label как canonical phrase — keep latin per §1.3.

### §1.8 Bucket F — translit triage

Транслит-термины классифицируются:

- **Accepted (keep):** `артефакт`, `автор`, `шаблон`, `репозиторий` — давно accepted в RU IT.
- **Borderline (case-by-case):** `ревью` (keep после «adversarial»; rewrite stand-alone → «рецензия»), `кейс` (keep в «edge case» / «test case»; rewrite общий → «случай»), `стейкхолдер` (keep formal; «заинтересованная сторона» longer-formal для `standard/`), `чекпойнт`, `гейт`, `онбординг`.
- **Rewrite (low-count):** `фронтенд` / `бэкенд` → keep latin form (`frontend` / `backend`); `пайплайн` → keep `pipeline`; `фолбэк` → keep `fallback`; `оверхед` → «накладные расходы»; `апдейт` → «обновление».

**Negative scenario:** не делать translit того, что Bucket A keeps latin. `pipeline` (latin) > `пайплайн` (translit).

### §1.9 Closed list canonical RENAR terms

Канонический список — [`standard/04-terms.md §4.3–§4.11`](../standard/04-terms.md#4.3) (артефакты, SPEC family, TC types, Quality Gates, lifecycle статусы — §4.3–§4.7; V1–V6 capabilities — §4.8.2; drift classes — §4.11). Style Guide отражает этот canonical; не дублирует.

### §1.10 Accepted technical loanwords

Cross-bucket whitelist — accepted technical loanwords (не калька, не translit):

| Term | Domain |
|---|---|
| `state machine` | distributed systems |
| `immutable history` | RENAR / V1 (gloss возможности) |
| `atomic change unit` | RENAR / V2 |
| `diff & review` | RENAR / V3 |
| `author + timestamp` | RENAR / V6 (gloss возможности) |
| `adversarial review` | RENAR / AI |
| `eval` / `evaluation` | AI testing |
| `runner` | CI / RENAR |
| `branching` | VCS / V4 |
| `version pin` | RENAR / носитель / V5 |
| `traceability` | RE general |
| `provenance` | RENAR AI |
| `closed list` | RENAR meta-policy |

**Правило:** не подлежат rewrite — canonical в RENAR domain и accepted в международной literature.

### §1.11 Добавление новых терминов

1. **Identify need** — term появляется в нормативной главе ≥3 раз без §1.9 listing.
2. **Bucket classification** — определить A–F по §1.2 critera.
3. **Rationale draft** — 3–5 предложений: why needed, why this bucket, what alternatives rejected.
4. **Review** — Style Guide editor (или project owner), same procedure как для [`standard/04`](../standard/04-terms.md) formal change.
5. **Retroactive** — после утверждения, существующие occurrences нормализуются.

В interim period — допустим в `research/` drafts; в `standard/` / `guide/` / `reference/` / `core/` — только после approval.

### §1.12 AI-bias guard — failure modes

LLM trained on multilingual web с English dominance склонна:

- **English-bias** — inserting anglicism («корпоративный admin»), preferring «имплементация» over «реализация», rewriting RU в English-flavored RU.
- **Confidence over precision** — confidently rewriting «должен» → «следует» «для читаемости» (semantic downgrade per §2.5).
- **Pattern-completion drift** — completing 51-й example с тем же pattern, даже если §1 требует RU rewrite.
- **Compression anglicism creep** (failure mode введён v1.3): при сжатии bullet-списков в inline-параграфы LLM сохраняет английские структурные метки (`**Actor:**`, `**Default:**`, `**Override:**`) и одиночные английские слова мид-prose (`actor`, `evidence`, `cadence`, `result`, `denial`). Это **anti-pattern** — после компрессии плотность анг. токенов в prose растёт сверх Bucket A/D, нарушая §1.1 принципы.

**Mitigation:** Bucket E replacement table (§1.7) — hard-coded substitutions; semantic preservation (§1.1, §2.5) — explicit check; human spot-check на random 10% правок — обязателен (не на «typical» examples — на random); compression-pass §1.13 synonym map (для prose-cleanup сразу при сжатии).

### §1.13 Compression synonym map — prose-cleanup при сжатии bullet-списков

При замене bullet-списка / multi-paragraph block на inline-параграф структурные метки и connectives **обязаны** переводиться. Canonical synonym map (closed list для частых compression-failure-modes):

| English label (prose) | RU canonical | Контекст применения |
|---|---|---|
| `**Actor:**` / `actor` | `**Участник:**` / «участник» | RACI, gates, assessments |
| `**Methodology:**` | `**Методология:**` | procedures |
| `**Evidence:**` / `evidence` | `**Доказательная база:**` / «свидетельства» | conformance audit |
| `**Default:**` / `default` (mid-prose) | `**По умолчанию:**` / «по умолчанию» | cadence, settings |
| `**Override:**` / `override` (mid-prose) | `**Переопределение:**` / «переопределение» | manifest declared-stricter |
| `**Cadence:**` / `cadence` | `**Периодичность:**` / «периодичность» | re-assessment, review |
| `**Result:**` / `result` (mid-prose) | `**Итог:**` / **Результат:** / «итог» | assessment outcome |
| `**Denial:**` / `denial` | `**Отказ:**` / «отказ» | assessment, gate result |
| `**Joint:**` / `Joint` | `**Совместный:**` / «совместный» | ownership type |
| `Single` (в roles table) | «одиночный» / «единоличный» | ownership type |
| `Split` (в roles table) | «разделённый» | ownership type |
| `**Recovery plan:**` | `**План восстановления:**` | потеря соответствия |
| `**Trigger:**` / `trigger` (mid-prose) | `**Триггер:**` (русифицировано, accepted) / «поводом для» | event-driven flows |
| `**Signal:**` / `signal` (mid-prose) | `**Сигнал:**` / «сигнал» | observable signals |
| `Review` (как noun mid-prose) | «обзор», «ревью» (если canonical RENAR-термин) | adversarial review остаётся как canonical |
| `Audit log` | «Журнал аудита» (mid-prose) или `audit-log` (как identifier) | event tracking |
| `Independent verification` | «Независимая проверка» | third-party assessment |
| `Notification` (mid-prose) | «Уведомление» | public communications |
| `Application` (mid-prose, как «Применимость») | «Применимость» | gate scope |

**Carve-out (НЕ переводится):**

- Backtick-код, YAML-значения, JSON properties — `actor:`, `result: pass`, `last-run:`, `default: ...` остаются как есть (это API / schema fields).
- Закрытые RENAR-термины ([§1.9](#1.9)): `manifest`, `ADAPT`, `BR`, `SR`, `SPEC-*`, `TC`, `ai-provenance`, `audit-trail`, `declared-stricter`, `assessment-mode`, `mandatory clauses`, `verifies[]`, `conformance` остаются.
- Bilingual H3 headers формата «### §X.Y RU-название (canonical-EN-term)» — допустимы как terminology mapping: «### 13.5.1 Участник (actor)».

**Hard rule:** новый bold-label paragraph header `**English Word:**` мид-prose, где English Word **не** входит в §1.9 / §1.10 closed lists и **не** имеет canonical RENAR-семантики — нарушение Style Guide v1.3+.

### §1.14 Process-vocabulary — нормализация процедурной лексики

Лексика процедуры изменения стандарта и оценки соответствия — **не** canonical-идентификаторы и **не** входит в accepted loanwords (§1.10). Это описательные обороты; латиницей в прозе они нарушают §1.1 (сначала смысл) и §2.2 камертона голоса (один автор, не комитет). Переводятся всегда, грамматически по падежам:

| English (prose) | RU canonical | Контекст |
|---|---|---|
| `formal change procedure` | формальная процедура изменения (стандарта) | §13.9 — изменение closed lists |
| `research draft` | исследовательский черновик | этап процедуры изменения |
| `public review` | публичное обсуждение | этап процедуры изменения |
| `minor-version bump` / `major-version bump` / `version bump` | повышение minor-/major-версии / повышение версии | выпуск версии стандарта |
| `migration guidance` | руководство по миграции | сопровождение conformant-проектов |
| `loss-of-conformance` / `loss of conformance` | потеря соответствия | §13.8 |
| `self-assessment checklist` | чек-лист самооценки | §13.5, reference/08 |
| `corresponding alignment` | согласующая правка | синхронизация с SENAR |
| `project-local` (adj/adv) | локальный для проекта / локально на уровне проекта | закрытые списки |
| `audit trail` / `audit log` (прозовый space-form) | журнал аудита | §10.13, §4.8.5; идентификатор `audit-trail` / путь — keep latin |
| `baseline` (bare prose) | базовый уровень / базовые значения / эталон / опорное состояние (по контексту) | метрики §12; eval/UX §8–9; идентификаторы `baseline-*` / `baselines/*.png` — keep latin |
| `immutable` (свободное прилаг.) | неизменяемый (по роду/падежу) | V1-gloss `immutable history` (§1.10) — keep; код-фенс комментарии — keep |
| `approval` (сущ.) | утверждение | имя гейта QG-0 `Approval Gate`, статус `approved`, поля `approval-*`, ISO-поле «Approval authority» — keep |
| `findings` (проза) | находки | вердикт-метки `no findings` / `findings present`, §7 `backward findings`, метрика `Reconciliation Findings` — keep |

**Carve-out (НЕ переводится):**

- Имена файлов (`08-conformance-self-assessment.md`), HTML-анкеры (`<a id="...self-assessment...">`), URL-фрагменты ссылок — referential integrity.
- Идентификаторы и пути (`loss-event`, `audit-trail/CFM-…/loss-events/…`).
- Билингв-заголовки формата «### RU-название (canonical-EN-term)»: «### 13.5 Процедура самооценки (Self-assessment)» — допустимы как terminology mapping (§1.13 carve-out).
- Версионные квалификаторы `minor`/`major` рядом с «версия» — accepted hybrid (`minor-версия`), переводится только `bump`.

**Enforcement:** `scripts/check-process-vocab.js` флагует в прозе (вне carve-out) обороты `formal change procedure`…`baseline`; в `check:all`. Три нижних термина (`immutable` / `approval` / `findings`) **не гейтируются** — они переплетены с каноническими именами (QG-0 `Approval Gate`, V1-gloss `immutable history`, метрика `Reconciliation Findings`), поэтому blocklist-гейт даёт ложные срабатывания; прозовые формы нормализуются вручную по этой таблице.

### §1.15 Поправка v1.3-draft — расширение русификации прозы

Решением владельца проекта (2026-06) политика русификации прозы расширена: перечисленные ниже термины, ранее остававшиеся латиницей по §1.3 (Bucket A) / §1.7 / §1.10, **переводятся в прозе и табличных лейблах**. В backtick-коде, YAML-полях/значениях, именах файлов, RFC-2119-цитатах и идентификаторах — остаются латиницей. Поправка имеет приоритет над §1.3/§1.7/§1.10 для этих конкретных терминов (§0.4: первый match — но эта таблица фиксирует исход для перечисленного).

| Latin (проза) | RU canonical | Latin сохраняется в |
|---|---|---|
| `manifest` | манифест; `conformance manifest` → манифест соответствия | имя файла `RENAR-CONFORMANCE.yaml`, поля |
| `conformance` | соответствие (склоняется); `conformant` → соответствующий; `non-conformant` → несоответствующий | `RENAR-CONFORMANCE.yaml`, `senar-version`/`renar-version`, уровни `RENAR-1..5` |
| `provenance` | происхождение | поле `ai-provenance`, `*-provenance` в backtick |
| `adversarial review` | состязательный обзор; `adversarial reviewer` → состязательный рецензент; `adversarial critic` → состязательный критик | поле `adversarial-review-ref` |
| **Имена ролей** | `Architect` → Архитектор; `Engineer` → Инженер; `Reviewer` (роль) → Рецензент; `Supervisor` → Супервизор; `Stakeholder` → Заинтересованная сторона; `Product Owner` → Владелец продукта | нормативные имена SENAR §4 в манифесте и значения `role:` (`authorized-role-holder` и т.п.); внешние термины `Stakeholder Requirement` (BABOK), `Product Owner` в цитате Scrum/SAFe |

**Средний слой (2026-06, решение владельца — «мягкие слова-понятия»):** дополнительно переводятся в прозе и лейблах, при сохранении истинных идентификаторов латиницей:

| Latin (проза) | RU canonical | Latin сохраняется в |
|---|---|---|
| `lifecycle` | жизненный цикл; «lifecycle-состояние/статус» → состояние/статус жизненного цикла | drift-класс `Lifecycle drift`, статусы-значения, имена файлов |
| `mandatory clauses` / `mandatory clause` | обязательные положения / обязательное положение; `mandatory` (прил.) → обязательный/обязательно | schema-аннотации `mandatory` в YAML-примерах |
| `enforcement` | обеспечение соблюдения; `enforcement-точка` → точка контроля | поля/идентификаторы |
| `SoT inversion` / `SoT` | инверсия источника истины / источник истины | — |
| `canonical` (прил.) | канонический | bilingual-заголовки `English (canonical)`, comparison-заголовки `RENAR canonical` |
| `closed list` | закрытый список | — |
| `state machine` | машина состояний | §1.10-gloss где как V-описание |
| `data model` → модель данных; `multilingual` → многоязычный; `spot-check` → выборочная проверка; `normative` → нормативный; `trace chain` → цепочка прослеживаемости; `full-completeness` → полнота | | drift-классы (`Order / provenance drift`), внешние термины |

Сохраняются латиницей (истинные идентификаторы, §0.7 машиночитаемый слой): **frontmatter**; lifecycle-**статусы** (`draft`/`approved`/`verified`/`frozen`/`superseded`/…) — §1.4 референциальная цепь; имена полей и backtick-код; ID/типы (`BR`/`SR`/`SPEC-*`/`TC`/`ADAPT`/`QG`/`MVR`/`V1–V6`); `Quality Gate(s)` как канон-имя; §1.10 V-glosses `immutable history`/`atomic change unit`/`diff & review`/`version pin`/`traceability`/`branching`/`author + timestamp`; Bucket A VCS (`commit`/`hook`/`trigger`/`runner`/`pipeline`/…); карв-аут `backward findings`/`no findings`/`findings present`; имена собственные и внешние термины (BABOK/ISO/KG node-types).

---

## §2 RFC-2119 RU normative wording

### §2.1 Принципы

1. **Semantic-fidelity первичен** — RFC-2119 ([RFC 2119](https://www.rfc-editor.org/rfc/rfc2119)) фиксирует строгое discrimination между MUST / SHOULD / MAY. RU wording должен сохранить эту дискриминацию.
2. **No semantic downgrade** — Phase 5 editorial pass **не имеет права** менять modal verb level. «должен» → «следует» — это **content change**, требует отдельной задачи.
3. **RFC 8174 carve-out для RU** — RU lowercase modal verbs (`должен`, `следует`, `может`, `рекомендуется`, `допускается`, `требуется`, `обязательно`, `опционально`, `обязан`, `запрещено`) в нормативных clauses RENAR Standard **являются canonical эквивалентом** RFC-2119 UPPERCASE keywords. Их normative weight равен соответствующему UPPERCASE.
4. **No UPPERCASE convention в RU prose** — `ОБЯЗАН / ДОЛЖЕН / СЛЕДУЕТ / МОЖЕТ` — **запрещено**. Capitalization только когда начинают предложение (orthographic). Визуальный emphasis — через `**bold**`.
5. **Imperative-mood для нормативных положений** — нормативные положения используют imperative («X должен Y», «X обязан Y»). Indicative («X выполняет Y», «X is Y») — только для descriptive prose.
6. **EN translation convention** — EN-перевод (`*/en/`) использует UPPERCASE RFC-2119 keywords; RU carve-out на EN **не распространяется**.

### §2.2 Regime decision (Option C — RU lowercase canonical)

RENAR Standard RU corpus использует **RU lowercase modal verbs** как canonical normative wording. Решение зафиксировано: de-facto majority уже соответствует; natural RU normative tradition (ГОСТ Р, ISO 29148 RU translations); RFC 8174 carve-out явно устраняет ambiguity.

### §2.3 Canonical mapping table

11 RFC-2119 / RFC-8174 keywords ↔ RU canonical equivalents. **Bidirectional usage:** RU pass — EN UPPERCASE → RU lowercase; EN translation — RU lowercase → EN UPPERCASE.

#### Mandatory levels

| RFC-2119 | RU canonical (primary) | RU canonical (synonyms) | Semantics |
|---|---|---|---|
| `MUST` | должен | обязан / необходимо / требуется | Absolute requirement |
| `MUST NOT` | не должен | запрещено / недопустимо | Absolute prohibition |
| `SHALL` | должен | обязан | Equivalent к MUST per RFC 2119 §2 |
| `SHALL NOT` | не должен | запрещено | Equivalent к MUST NOT |
| `REQUIRED` | обязателен / обязательно | необходим | Equivalent к MUST (adjective form) |

#### Strong recommendation level

| RFC-2119 | RU canonical (primary) | RU canonical (synonyms) | Semantics |
|---|---|---|---|
| `SHOULD` | следует | рекомендуется | Strong recommendation; exceptions требуют explicit rationale |
| `SHOULD NOT` | не следует | не рекомендуется | Strong negative recommendation |
| `RECOMMENDED` | рекомендуется | предпочтительно | Equivalent к SHOULD |
| `NOT RECOMMENDED` | не рекомендуется | нежелательно | Equivalent к SHOULD NOT |

#### Optional level

| RFC-2119 | RU canonical (primary) | RU canonical (synonyms) | Semantics |
|---|---|---|---|
| `MAY` | может | допускается | Truly optional; no preference |
| `OPTIONAL` | опционален / опционально | необязателен / по выбору | Equivalent к MAY |

#### Negation table — canonical patterns

| Form | RU canonical | Discrimination |
|---|---|---|
| MUST NOT / SHALL NOT | не должен / запрещено / недопустимо | Hardcoded prohibition |
| SHOULD NOT / NOT RECOMMENDED | не следует / не рекомендуется / нежелательно | Strong negative recommendation |
| (MAY NOT — non-canonical RFC) | не требуется | RFC 2119 не содержит MAY NOT; «не требуется» = «MAY omit» |

**Hard rule:** «не должен» (MUST NOT) ≠ «не следует» (SHOULD NOT). Editor никогда не подменяет одну форму другой «для читаемости».

### §2.4 Tense / mood normative rules

#### §2.4.1 Normative clauses → imperative-mood

```markdown
# Canonical
SR должен прослеживаться до родительского BR через `parent:` link.

# Anti-pattern (indicative — теряется normative weight)
SR прослеживается до родительского BR через `parent:` link.
```

#### §2.4.2 Definitions → predicative form

Canonical form: `X — Y, нормированное Z`.

```markdown
ADAPT — двусторонняя адаптация ТЗ, нормированная процессом согласования с заказчиком.
```

Acceptable variants: `X — это Y, …` (с подчёркивающим частицей «это»); `X — Y` (без «это»).

**Anti-pattern:** `X является Y` (formal-overhead); `X представляет собой Y` (anglicism-style копи verb chains).

#### §2.4.3 Future tense — scope-limited

Future-tense (`будет / будут`) **запрещён** в нормативных clauses. Допустим только в: roadmap sections, forward-looking limitations, conditional consequences.

```markdown
# Wrong (semantic ambiguous — это требование или прогноз?)
SR будет содержать parent link.

# Correct
SR должен содержать parent link.
```

#### §2.4.4 Conditional clauses

Canonical pattern: `Если X, то Y должен Z`. Variants: `При условии X — Y должен Z`; `Когда X, Y обязан Z`.

**Anti-pattern:** conditional + indicative («Если X, то Y делает Z») — теряется normative weight.

### §2.5 Strict semantic preservation rule

**Hard rule:** Phase 5 editorial pass **не имеет права** менять RFC-2119 level modal verb.

**Permitted (semantic-preserving):**

| From | To | Reason |
|---|---|---|
| `должен` ↔ `обязан` | — | Same level (MUST); synonym choice editorial |
| `следует` ↔ `рекомендуется` | — | Same level (SHOULD) |
| `может` ↔ `допускается` | — | Same level (MAY) |
| `необходимо` ↔ `требуется` | — | Same level (MUST); passive-voice register |
| `не должен` ↔ `запрещено` | — | Same level (MUST NOT); emphatic synonym |

**Forbidden (content change, не editorial):**

| From | To | Reason |
|---|---|---|
| `должен` | `следует` | MUST → SHOULD downgrade |
| `следует` | `может` | SHOULD → MAY downgrade |
| `не должен` | `не следует` | MUST NOT → SHOULD NOT downgrade |
| Любое modal | indicative («X делает Y») | Loss of normative weight |

**Verification protocol:** pre-pass scan modal verbs in chapter (count per level); post-pass re-scan; sum-per-level не должна меняться. Если меняется — flag as semantic drift, revert, surface для content-review задачи. Enforcement — `scripts/check-rfc-modals.js`.

### §2.5bis Разграничение с языком описания артефактов

Настоящий раздел нормирует текст **самого стандарта**: таблица §2.3 допускает синонимы ради верности перевода. Текст **артефактов проекта** нормирует [standard/15](../standard/15-description-language.md): закрытый список из пяти операторов без синонимов, строчными — принцип 4 §2.1 действует и там.

### §2.6 reference/04 descriptive carve-out

[`04-ai-style-guide.md`](04-ai-style-guide.md) содержит EN RFC-2119 keywords в **descriptive** context (mentions RFC 2119 vocabulary, не uses normatively). Descriptive mention RFC-2119 keywords в reference / guide / core — **разрешено**, при условии:

1. Контекст явно descriptive: «RFC 2119 определяет `MUST` как absolute requirement».
2. Keyword обёрнут в `code-fence` или blockquote / EN-citation.

Phase 5 chapter pass distinguishes — descriptive mention НЕ migrates на «должен», keeps EN form.

---

## §3 Formatting conventions

### §3.1 Принципы

1. **Читаемость носителем первична** — сохранять git-blame readability; не ломать syntax highlighting; быть стабильным при нативной для носителя обработке (markdown lint, frontmatter validator).
2. **Scan-readable вторичен** — numbered sections для cross-ref, consistent bullets, typographic quotes.
3. **No tool dependency** — convention applicable manual editor (любой text editor); не требует specific IDE plugin.
4. **Accessibility** — semantic heading hierarchy (no skip H1 → H3); typed code-fences для syntax-highlighting; alt-text (deferred).
5. **De-facto convention endorsement** — bullet `-`-only (100% базовый уровень), table alignment-less `---` (100% базовый уровень) — endorsed.
6. **RU-типографика первого класса** — `«»`, `—`, NBSP — базовый уровень нормативной RU-типографики.

### §3.2 Citations & cross-refs

Три класса:

| Class | Pattern | Когда |
|---|---|---|
| (A) Bare intra-file | `§3.5` | Reference внутри того же файла |
| (B) Markdown link cross-file | `[§4.5](../standard/04-terms.md#4.5)` | Reference на другой файл |
| (C) Anchor-only intra-file | `[§3.5](#35-tables--lists)` | Clickable intra-file reference |

**Hard rule:** cross-file reference **обязан** быть markdown link (Class B). Bare `§X.Y` для cross-file — anti-pattern (не clickable, no PDF outline integration).

**Inline «глав* X»:** «в [главе 4](../standard/04-terms.md)» — canonical для cross-file narrative reference. «глава 4» без link — anti-pattern, если cross-file.

**«См.» (RU) — endorsed.** «see» (EN) — banned.

**Anchor format:** lowercase, hyphens вместо spaces / dots, RU keywords lowercase Cyrillic. Verified post-edit via cross-ref dead-link scan (`scripts/check-md-links.js`).

### §3.3 Code-fences

**Hard rule:** все fences обязаны иметь language tag. No-lang fences — anti-pattern.

**Language tag whitelist** (closed list):

| Tag | Purpose |
|---|---|
| `yaml` | frontmatter / config examples |
| `bash` | CLI commands / shell snippets |
| `cypher` | Knowledge graph queries (`reference/05`) |
| `markdown` | Markdown examples внутри docs |
| `json` | JSON schema / API payloads |
| `python` | Python script examples |
| `sql` | SQL queries |
| `text` | Plain text / tree fragments / generic output |

**Inline code (backticks):** identifiers (`` `SR-001` ``), file paths (`` `standard/04-terms.md` ``), type / field names (`` `parent: BR-001` ``), short CLI fragment.

**Anti-pattern:** backticks вокруг whole sentences — reserve backticks для technical identifiers.

### §3.4 Headings

#### §3.4.1 Numbering convention — split

| Directory | Convention | Rationale |
|---|---|---|
| `standard/` | Dotted-number (`## 3.5 Tables / lists`) | Normative — numbered для cross-ref precision |
| `reference/` | Dotted-number | Same as standard/ — reference is normative companion |
| `core/` | Plain (`## Что вы получите`) | Pedagogical narrative |
| `guide/` | Plain или numbered «Phase 0..9» | Narrative + numbered phase walkthroughs |
| `research/` | Author choice | Drafts |

#### §3.4.2 Depth rule

- Max depth normative — **H3**.
- H4 — exception (substructure внутри large H3), ≤ 5 per chapter.
- H5 / H6 — **banned** (restructure parent instead).
- Depth jumps (H1 → H3 без intermediate H2) — **banned**.

#### §3.4.3 Casing & form

Headings — sentence-case RU (capitalize only first word + proper nouns). Not title-case English («## Tables And Lists»). **Anti-pattern:** sentence-as-heading («## Этот раздел описывает основные принципы»). Heading — short label, not sentence.

### §3.5 Tables / lists

**Hard rules:**

- Bullet style — `-` only. `*` / `+` — banned. Mixed bullets within file — banned.
- Table alignment — `---` only. `:---` / `---:` / `:---:` — banned (вводят rendering variance per renderer).
- Nested unordered — 2-space indent (CommonMark default).
- Nested ordered — 3-space indent.
- Ordered list — manual `1.`, `2.`, `3.` preferred (explicit count visible в source).

### §3.6 frontmatter YAML

Canonical field order:

```yaml
---
title: "Document title"
status: draft  # | proposed | approved | verified | obsolete | frozen
phase: "Phase N description"  # optional
epic: epic-slug  # optional
task: task-slug  # optional
draft-date: 2026-05-16  # ISO 8601
scan-date: 2026-05-16  # для audit artifacts
target-final-path: "reference/06-...md"  # для drafts с known lock-in target
lang: ru  # primary language
---
```

**Quoting policy:**

| Value type | Quoting |
|---|---|
| Pure ASCII identifier (`status: draft`, `lang: ru`) | No quotes |
| String с пробелами / special chars (`title: "..."`) | Double quotes |
| ISO date (`2026-05-16`) | No quotes |
| Numeric / Boolean | No quotes |

**`lang:` closed list** — `ru`, `en`. EN-переводы (`*/en/`) активны с EN translation epic; конвенция директории и frontmatter — [EN Style Guide §0.3](en/06-en-style-guide.md).

**Validation:** `node scripts/validate-frontmatter.js` (required-field presence + type correctness + closed-value-list).

### §3.7 RU typography

#### §3.7.1 Кавычки — «»

**Canonical:** ёлочки `«»` для outer quotes. ASCII `"…"` в prose — anti-pattern.

**Exceptions:** ASCII quotes допустимы внутри code-fences (verbatim), в URL / file paths, в EN citation внутри RU prose.

#### §3.7.2 Em-dash — `—`

Em-dash `—` (U+2014) для: парных separators («Term X — described as Y — applies …»), definition predicate («X — Y»), list-item separator («item A — description»).

**Anti-pattern:** `-` (hyphen) или `--` (double-hyphen) instead of `—`.

**Exceptions:** hyphen `-` correct в compound words («closed-list», «source-of-truth»), file paths, identifiers. En-dash `–` (U+2013) для number ranges («§3.5–3.7») — rare, acceptable.

#### §3.7.3 Non-breaking space

Required:
- Между числом и единицей: `100 ms`.
- Между initials и фамилией: «И. М. Сеченов».
- Перед em-dash в pair: «слово — описание».

Markdown source может содержать literal NBSP (U+00A0) или HTML entity `&nbsp;`. Soft rule — author discretion.

#### §3.7.4 Числительные форматы

| Form | Convention |
|---|---|
| Whole numbers ≤ 999 | No separator: `42`, `999` |
| Whole numbers ≥ 1000 | Space separator: `1 000`, `50 649` |
| Percentages | `33%` (no space) |
| Decimals | English period `0.5` (corpus default) |
| Ordinals | `1-й`, `2-й` (RU suffix) |

#### §3.7.5 Math operators

`≥` / `≤` / `≠` / `±` / `→` / `↔` — Unicode preferred в prose. ASCII `>=` / `<=` / `!=` / `+/-` / `->` — anti-pattern в prose; acceptable inside code-fences.

### §3.8 Cross-file link conventions

- **Relative paths only** (from linking file's directory). Absolute paths — break при site build.
- **Anchor case:** lowercase, hyphens, RU keywords Cyrillic.
- **Dead-link prevention:** Astro build / `scripts/check-md-links.js` flag broken links.

```markdown
# from reference/06-ru-style-guide.md
[§4.5 standard/04](../standard/04-terms.md#4.5)
[reference sibling](01-glossary.md)
```

### §3.9 Inline emphasis (bold / italic)

**Bold (`**text**`)** — для term introduction («**Closed list policy** — формальный механизм …»), emphatic normative («**Hard rule:** …»), negative scenario flag («**Anti-pattern:** …»).

**Hard rule:** bold **не используется** как replacement для UPPERCASE (§2.1, принцип 4). Bold — additive emphasis, не shouting.

**Italic (`*text*`)** — restricted: foreign-language phrase («*ad hoc*», «*de facto*»), title of external work («*Software Requirements* (Wiegers)»). Не для general emphasis (use bold).

---

## §4 Integration & enforcement

### §4.1 Canonical sources

| Источник | Назначение |
|---|---|
| [`standard/04-terms.md`](../standard/04-terms.md) | Canonical RENAR terminology (closed list canonical из §1.9) |
| [`standard/01-scope.md §1.7`](../standard/01-scope.md#1.7) | Master index 16 closed lists |
| [`01-glossary.md`](01-glossary.md) | Informative glossary с примерами и mapping на ISO/BABOK/SAFe |
| [`02-schemas.md`](02-schemas.md) | Canonical frontmatter schemas |

### §4.2 Automated enforcement (scripts/)

Style Guide задаёт правила; следующие скрипты enforce их:

| Скрипт | Что проверяет | Style Guide § |
|---|---|---|
| `scripts/validate-frontmatter.js` | required fields, type correctness, closed value lists | §3.6 |
| `scripts/check-rfc-modals.js` | EN UPPERCASE / RU UPPERCASE modals; modal-verb level integrity | §2.1, §2.5 |
| `scripts/check-substrate-term.js` | `substrate` в prose → «носитель» | §1.3 |
| `scripts/check-substrate-leakage.js` | git-specifics в normative-каталогах | §1.3 |
| `scripts/check-literary-headings.js` | sentence-as-heading anti-pattern | §3.4 |
| `scripts/check-md-links.js` | dead links, anchor validity | §3.2, §3.8 |
| `scripts/check-site-russian-prose.js` | site-build RU prose checks | §1, §2 |
| `scripts/style-guide-check.js` | aggregate Style Guide enforcement | §1, §2, §3 |

**npm scripts:** `npm run check:all` запускает полный gate sweep.

### §4.3 Change procedure (cross-ref)

См. [§0.5 Change procedure](#05-change-procedure).

---

## §5 Limitations & follow-ups

- **EN Style Guide** — отложен до начала EN translation epic. Будет mirror этого RU Style Guide с swapped polarity (UPPERCASE EN canonical, lowercase RU только в citation context).
- **Imagery / SVG alt-text policy** — отложено до введения images в корпус.
- **Inner quotes** (`„…"` — German low-9 + high-9) — defer to author discretion case-by-case; формального правила пока нет.
- **NBSP insertion** — soft rule; не gated check. Возможен дальнейший lock-in.

## §6 Источники

- **RENAR Standard** — [`standard/`](../standard/).
- **SENAR (родительский стандарт)** — методологическая база.
- **RFC 2119** — [Key words for use in RFCs](https://www.rfc-editor.org/rfc/rfc2119) (1997).
- **RFC 8174** — [Ambiguity of Uppercase vs Lowercase](https://www.rfc-editor.org/rfc/rfc8174) (2017).
- **ISO/IEC/IEEE 29148:2018** — Requirements engineering.
- **ГОСТ Р, ISO/IEC** в RU translations — natural RU normative tradition (carve-out per §2.1, RFC 8174).

---

*RENAR RU Style Guide v1.2.1 — EN epic sync 2026-06-06 (compression pass v1.2 — 2026-05-27). Полная история (v1.0, v1.0.1, v1.1, v1.1.1, v1.1.2 lock-in details, F1/F2 reconciliation history, Phase 5 migration plans, MVR migration plan, EN UPPERCASE migration log) — [CHANGELOG.md](https://github.com/Kibertum/RENAR/blob/main/CHANGELOG.md).*
