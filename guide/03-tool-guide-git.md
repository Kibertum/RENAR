---
title: "Носитель: VCS (git)"
description: "Реализация V1-V6 на git: layout .req репо, submodule pinning, PR/MR workflow, pre-commit hooks, delta-ТЗ flow."
order: 3
lang: ru
version: "1.0"
---

# 03. Носитель: VCS — git

> Конкретная реализация RENAR на git. Структура `.req` репозитория, submodule-pinning между `.req` и `.src`, PR/MR ревью workflow, pre-commit hooks для capability V1-V6, delta-ТЗ workflow. Этот guide — informative; нормативное содержание (capability требования, schemas, lifecycle) — в `standard/`. Альтернатива на document-oriented store — [guide/04-document-store-substrate](04-document-store-substrate.md).
>
> **Предпосылки:** [standard/03-substrate-versioning](../standard/03-substrate-versioning.md) (нормативные capability V1-V6), [reference/02-schemas](../reference/02-schemas.md) (frontmatter schemas).

---

## 1. Когда выбрать git как носитель

Git — носитель по умолчанию для:
- Открытых стандартов и проектов (нет зависимости от внутренней инфры).
- Внешних клиентов без выделенной document-store инфраструктуры.
- Команд с устоявшимся PR-workflow.
- Проектов, где требования и код в одной экосистеме (один и тот же VCS provider).

Git **не** оптимален, если:
- Нужны частые конкурентные правки одного артефакта несколькими авторами без merge conflicts.
- Нужен built-in полнотекстовый поиск без внешних инструментов.
- Требуется UI для non-technical stakeholder (PM, юристов) без git CLI.

В таких случаях рассмотрите document-oriented store — [guide/04](04-document-store-substrate.md).

---

## 2. Layout двух репозиториев

Каноническая структура — два связанных репо.

```text
<project>/
├── <project>.req/                ← репозиторий ТРЕБОВАНИЙ (отдельный VCS repo)
│   ├── tz/                       ← TZ-YYYY-NNN.md (immutable после регистрации)
│   ├── actz/                     ← ACTZ-NNN.md (протоколы уточнения ТЗ, двусторонняя подпись)
│   ├── adapt/                    ← ADAPT-NN.md (bridge artefacts)
│   ├── ar/                       ← AR-NNN.md (записи состязательного обзора, §7.4.6)
│   ├── br/                       ← BR-NN.md
│   ├── sr/                       ← SR-NN.md
│   ├── specs/                    ← SPEC-* по подпапкам (arch/, api/, data/, int/, …)
│   │   ├── arch/  api/  data/  ui/  ai/  int/  proc/  sec/  ops/  test/  doc/
│   ├── tr/                       ← TR-NN.md (task requirements)
│   ├── tests/                    ← TC-NN.md (контрольные примеры, `TC` — самостоятельные артефакты)
│   ├── at/                       ← AT-NN.md (приёмочные тесты от итогового ТЗ)
│   ├── dpia/                     ← DPIA-NN.md (опционально для regulated)
│   ├── library/                  ← templates, patterns
│   ├── docs/                     ← AI-generated документация
│   ├── COVERAGE.md               ← auto-generated (`[coverage]` commits)
│   ├── REQUIREMENTS.md           ← auto-generated index
│   └── TEST-PLAN.md              ← auto-generated
└── <project>.src/                ← репозиторий РЕАЛИЗАЦИИ
    ├── src/                      ← код
    ├── tests/                    ← реализации TC (адресуются `automation.location`)
    ├── requirements/             ← submodule → <project>.req @ <commit>
    ├── .gitmodules
    └── README.md
```

В `<project>.req/.gitattributes` для bot-generated артефактов:

```text
COVERAGE.md      linguist-generated=true
REQUIREMENTS.md  linguist-generated=true
TEST-PLAN.md     linguist-generated=true
docs/**          linguist-generated=true
```

Это исключает их из статистики кода и сильно сжимает PR diff.

---

## 3. Capability mapping V1-V6 на git

| Возможность ([standard/03 §3.3](../standard/03-substrate-versioning.md#3.3)) | Норматив | Git-механизм |
|---|---|---|
| V1 — неизменяемая история | Прошлые состояния артефактов нельзя переписать задним числом | Неизменяемая история коммитов; protected branch + запрет force-push на `main`; `id:` во frontmatter стабилен |
| V2 — атомарная единица изменения | Изменение применяется целиком либо не применяется | Атомарный commit / squash-merge PR как одна единица; delta-ADAPT = один PR |
| V3 — сравнение различий и рецензирование | Предложенное изменение рецензируется до утверждения | `git diff` + PR/MR review с обязательным approve до merge |
| V4 — ветвление / набор изменений | Черновик отделён от утверждённой правды | Ветка черновика комплекта против `main` (утверждённая версия); PR — набор изменений, слияние = выпуск версии `N.M` |
| V5 — сквозная фиксация версии | Реализация ссылается на конкретную версию комплекта | Submodule-pin между `.req` и `.src`; тег версии комплекта `renar-vN.M` = `set-version`; `automation.set-version` в TC |
| V6 — автор и отметка времени | Каждая единица изменения регистрирует автора и время | Метаданные коммита: автор + дата; для AI-агента — `ai-provenance` |

> RENAR-проверки на git (валидация схемы, контроль переходов статусов, coverage-отчёты, целостность ссылок, reconciliation / drift detection) — это **enforcement-механизмы** поверх возможностей, а не сами V1–V6. Их раскладка по pre-commit / CI — §3.1 и §8 этого гайда; нормативные классы дрейфа — [standard/04 §4.11](../standard/04-terms.md#4.11).

> **Состояние сценариев.** Приведённые ниже механизмы под `git` — **целевой образец v1.0**. Фактически готов только `scripts/validate-frontmatter.js`; остальные (`validate-lifecycle`, `validate-references`, `generate-coverage`, `detect-drift` и др.) — в **бэклоге фазы 8** (раздел §8 этого гайда). Пока скриптов нет, перечисленные возможности обеспечиваются вручную и код-ревью; автоматическое навязывание уровней RENAR-3+ «из коробки» под `git` пока недостижимо. Замечание относится и к document-store ([guide/04](04-document-store-substrate.md)).

---

## 4. Submodule pinning

`<project>.src` фиксирует **конкретный commit** `<project>.req` через git submodule.

### 4.1 Как работает

- В `<project>.src` директория `requirements/` — submodule на `<project>.req`.
- При сборке / CI код знает: «я реализую требования по состоянию на commit `abc1234`».
- Разработчик в задаче открывает `requirements/sr/SR-05.md` через обычный `cat` или IDE — это файл в worktree.

### 4.2 Bump pattern

При обновлении требований:
1. PR в `<project>.req` с изменениями требований → ревью → merge.
2. **Отдельный** PR в `<project>.src`, который **только** двигает submodule pointer:
   ```bash
   cd requirements
   git pull origin main
   cd ..
   git add requirements
   git commit -m "bump requirements: TZ-2026-042 delta + 3 new SR"
   ```
3. Этот PR явно показывает: «требования обновились до коммита X».

### 4.3 Почему submodule, не subtree / monorepo

- **Provenance:** для любого коммита кода точно известно, какую версию требований он реализовывал.
- **Изоляция ревью:** ревью требований (в `.req`) и ревью кода (в `.src`) не смешиваются.
- **Атомарность delta-ТЗ:** delta — это атомарный PR в `.req` + последующий submodule-bump в `.src`. Нет «частично применённой delta».
- **Совместимость с document store:** при переходе на document-oriented store submodule pinning превращается в revision-token pinning — концепция та же.

Альтернативы (subtree, monorepo): рассматривайте только если submodule не работает по причинам, специфичным для вашего VCS provider; во всех остальных случаях submodule — рекомендация.

---

## 5. PR/MR review workflow

Два уровня ревью, разделённые по репозиториям.

### 5.1 Ревью в `<project>.req`

**Фокус рецензента:**
- Frontmatter schema-valid.
- Citation на ТЗ / ADAPT присутствует и валиден.
- `parent` / `verified-by` / `constrained-by` ссылки существуют.
- Lifecycle transition легитимна.
- Source citation в body для каждого нормативного утверждения (на RENAR-4+).
- Adversarial AI-review prompt пройден (на RENAR-5).

**Утверждение = QG-0** ([standard/10 §10.3.1](../standard/10-lifecycle-qg.md)).

### 5.2 Ревью в `<project>.src`

**Фокус рецензента:**
- Submodule pointer соответствует merged commit в `.req`.
- Реализация ссылается на утверждённую версию комплекта через `automation.set-version` (тег `renar-vN.M`).
- Новые / изменённые TC соответствуют контракту в `.req`.
- Никаких изменений Pass/Fail-критериев TC без `[test-spec-change]` тега.

**Слияние ветки черновика в `main` = QG-0 комплекта** (выпуск версии `N.M`, тег `renar-vN.M`); **QG-2** ([standard/10 §10.3.3](../standard/10-lifecycle-qg.md)) — запись верификации версии после прохождения всех TC на ней.

### 5.3 Запрещённые анти-паттерны

- **PR одновременно в `.req` и `.src`** — нарушает изоляцию ревью; запрещён hook носителя.
- **Изменение submodule pointer без merged commit в `.req`** — pointer указывает в untracked commit; CI блокирует.
- **`[test-spec-change]` без отдельного approval** — Pass/Fail-критерий TC изменён вместе с code-fix; CI блокирует merge.

---

## 6. Pre-commit и pre-merge hooks

Минимальный набор hooks носителя для RENAR-3+ на git.

### 6.1 Pre-commit (в `<project>.req`)

```bash
# Запускается на коммит в .req
# Capability V2: schema validation
yamllint --strict $(git diff --cached --name-only | grep '\.md$')
node scripts/validate-frontmatter.js $(git diff --cached --name-only --diff-filter=AM)

# Capability V3: legal lifecycle transitions
node scripts/validate-lifecycle.js $(git diff --cached --name-only --diff-filter=M)

# Capability V5: reference integrity (быстрая проверка только изменённых)
node scripts/validate-references.js --changed-only $(git diff --cached --name-only)
```

### 6.2 Pre-merge (CI job в `<project>.req`)

Полные проверки, которые медленны для pre-commit:

```yaml
- name: Full reference validation (V5)
  run: node scripts/validate-references.js --all

- name: Coverage report regeneration (V4)
  run: node scripts/generate-coverage.js
  # commits as [coverage] bot user

- name: Drift detection (reconciliation, weekly)
  run: node scripts/detect-drift.js
  if: github.event.schedule == '0 0 * * 0'
```

### 6.3 Pre-merge (CI job в `<project>.src`)

```yaml
- name: Submodule points to merged commit
  run: |
    cd requirements
    git fetch origin
    git merge-base --is-ancestor HEAD origin/main

- name: TC implementations pinned to the current set-version
  run: node scripts/validate-tc-version-pinning.js

- name: No Pass/Fail change without [test-spec-change]
  run: node scripts/validate-test-spec-changes.js
```

---

## 7. ТЗ workflow на git

### 7.1 Forward-workflow (проект с нуля)

Первичное создание оси требований из нового ТЗ:

1. **branch в `.req`:** `git checkout -b init/TZ-2026-001` в `<project>.req`.
2. **Регистрация ТЗ:** `tz/TZ-2026-001.md` (immutable после регистрации; зафиксировать подпись клиента и дату).
3. **Состязательный обзор ТЗ и — при находках — ADAPT:** обзор обязателен всегда, его вердикт фиксируется записью `ar/AR-001.md` ([§7.4.1.2](../standard/07-adapt.md#7.4.1)). При вердикте «findings present» создаётся `adapt/ADAPT-001.md` — Forward (интерпретация по каждому § ТЗ) + Backward (находки), [standard/07](../standard/07-adapt.md); ADAPT — внутренний документ, клиенту он не выносится. При вердикте «no findings» ADAPT не создаётся, а BR / SR / SPEC несут `source.tz-section` + `source.adversarial-review-ref: AR-001` ([§7.4.1.1](../standard/07-adapt.md#7.4.1)).
4. **ACTZ:** находки, требующие решения клиента, выносятся протоколом `actz/ACTZ-001.md` — «Протокол уточнения ТЗ № 1» ([§7.13](../standard/07-adapt.md#7.13)). `status: draft → sent → signed`; подписи клиента и исполнителя фиксируются автором и отметкой времени (V6). После подписания каждая находка получает `decided-in: ACTZ-001 §M`.
5. **Подпись архитектора → QG-ADAPT-approve:** ADAPT в `approved` (подпись только архитектора, [§7.5](../standard/07-adapt.md#7.5)), `open-questions-count == 0`.
6. **Декомпозиция:** AI-агент выводит BR из approved ADAPT, затем SR (`source.adapt`) и SPEC (`source.adapt`; SR ссылается на них через `constrained-by[]`, [§8.6.1](../standard/08-specifications.md#8.6)), а также парные pos/neg TC.
7. **QG-0 approval** каждого артефакта (`draft → approved`); CI dry-run новых TC.
8. **PR в `.req` → merge;** bot regenerates REQUIREMENTS.md / COVERAGE.md / TEST-PLAN.md.
9. **Setup `.src`:** добавить submodule `requirements/ → <project>.req @ <commit>`, создать TR, реализовать, QG-2.
10. **AT перед испытаниями:** изолированный агент (другая модель, без доступа к `.src` и к внутренним артефактам `.req`) выводит `at/AT-NN.md` из итогового ТЗ — `tz/` + подписанные `actz/` ([§7.14](../standard/07-adapt.md#7.14)). Прогон обновляет `ACCEPTANCE.md`; релизный гейт требует все AT зелёными ([§10.4.3](../standard/10-lifecycle-qg.md#10.4)).

### 7.2 Delta-ТЗ workflow

Полная последовательность для применения нового delta-ТЗ.

1. **branch в `.req`:** `git checkout -b change/TZ-2026-042` в `<project>.req`.
2. **Создание delta-ТЗ, состязательный обзор, при находках — delta-ADAPT:** `tz/TZ-2026-042-delta.md` (текст delta); состязательный рецензент выносит вердикт, который фиксируется записью `ar/AR-NNN.md`. При вердикте «findings present» создаётся `adapt/ADAPT-NNN-delta.md` (forward интерпретация + backward findings, [standard/07 §7.6](../standard/07-adapt.md#7.6)); при вердикте «no findings» delta-ADAPT не создаётся, и затрагиваемые BR / SR / SPEC ссылаются на `source.tz-section: TZ-2026-042-delta` напрямую ([§7.6.1](../standard/07-adapt.md#7.6)). Решения клиента по находкам delta-ТЗ фиксируются новым `actz/ACTZ-NNN.md` (двусторонняя подпись); delta-ADAPT утверждается подписью архитектора прежде чем затронутые требования будут модифицированы. Итоговое ТЗ пересчитывается — от него же перегенерируются AT.
3. **Impact analysis:** AI-агент находит затронутые BR / SR / SPEC / TC; помечает TC как `obsolete-pending`.
4. **Update artefacts:** AI-агент обновляет / создаёт BR / SR / SPEC и парные TC (pos+neg) в той же ветке.
5. **Adversarial critic review:** на RENAR-5 — обязательно (другая AI-модель).
6. **CI dry-run:** новые TC должны запускаться без ошибок инфры.
7. **Finalize:** слияние черновика в `main` = версия комплекта `N.M+1` (тег `renar-vN.M+1`, запись версии), regenerate REQUIREMENTS.md / COVERAGE.md / TEST-PLAN.md (bot).
8. **PR в `.req` → QG-0 approval → merge.**
9. **branch в `.src`:** `git checkout -b change/TZ-2026-042` в `<project>.src`.
10. **Bump submodule:** `cd requirements && git pull && cd ..`.
11. **Create TR / tasks:** для новых / изменённых SR (через `/task` или специфичный для носителя tooling).
12. **PR в `.src` «bump requirements + tests + new tasks» → ревью → merge.**
13. **Development:** взять TR → реализовать → CI → бот заполняет `last-run` в TC.
14. **QG-2:** при зелёных TC на версии — runner дописывает запись верификации в запись версии; отдельного статуса у требования нет.

Каждый шаг кроме (1) и (9) автоматизирован нативно для носителя через скрипты или CI.

---

## 8. Migration notes: scripts/ модернизация

Существующие [scripts/](../scripts/) — bash + node helpers из ранней эпохи проекта. Состояние модернизации на v1.0:

- ✅ **Legacy terms вычищены.** `req-branch.sh`, `req-finalize.sh`, `req-ai-instructions.md`, `req-use-template.sh` больше не используют устаревшие `INT-SR`, `AIC`, `UIC`, `tech-specs`, `ai-concepts`, `ui-concepts`. Closed list актуальных типов — [standard/04 §4.4](../standard/04-terms.md#4.4) (BR/SR/TR + 11 SPEC). Остаточные упоминания в [reference/01-glossary.md](../reference/01-glossary.md) и [reference/02-schemas.md](../reference/02-schemas.md) являются legacy-mapping для проектов, мигрирующих с предыдущих версий.
- ✅ **Schema validator создан.** `scripts/validate-frontmatter.js` — Node ES-module, проверяет frontmatter всех `.md` в `standard/`, `guide/`, `reference/`, `core/`: required fields `title` / `order` / `lang ∈ {ru, en}`. Запуск: `node scripts/validate-frontmatter.js [--quiet]`; exit 0 если все валидны, exit 1 при первой ошибке. Источник схемы — [reference/02-schemas](../reference/02-schemas.md).
- ✅ **ADAPT в forward-workflow.** Начальное создание (ТЗ → состязательный обзор → при находках ADAPT → BR) описано в §7.1 этого гайда; delta-workflow с delta-ADAPT — в §7.2 согласно [standard/07 §7.6](../standard/07-adapt.md#7.6).
- ⏳ **Pre-commit hooks** (§6.1) пока частично разбросаны по `req-finalize.sh`. Целевое состояние — выделить в отдельные `scripts/validate-*.js`; `validate-frontmatter.js` — первый такой модуль.

Глава описывает **целевой набор скриптов** на выпуске v1.0; строки без отметки ✅ — работа для **фазы 8** (продолжение бэклога).

---

## 9. Common operations

Шорткаты для частых операций.

| Операция | Команда |
|---|---|
| Найти SR по ID | `grep -r "^id: SR-12" sr/` |
| Найти TC, верифицирующий SR-12 | `grep -rl "id: SR-12" tests/` |
| Найти orphan SR (без TC) | `node scripts/find-orphans.js sr` |
| Создать новый SR из шаблона | `cp library/templates/sr.md sr/SR-NN.md && $EDITOR sr/SR-NN.md` |
| Diff frontmatter за период | `git log --all --since=...  -- sr/` |
| Текущая версия комплекта | `git describe --tags --match 'renar-v*'` |
| Список stale TC (last-run < current version) | `node scripts/list-stale-tc.js` |

---

## 10. CI/CD integration patterns

### 10.1 Bot-commit conventions

Auto-generated артефакты коммитятся hooks носителя с conventional commit-message тегами для машинного парсинга:

| Тег | Когда | Что коммитится |
|---|---|---|
| `[coverage]` | Post-merge в `.req` | Регенерация COVERAGE.md / REQUIREMENTS.md / TEST-PLAN.md |
| `[baseline-update]` | После approval PR с изменением эталона | Перегенерация PNG-эталонов для SPEC-UI |
| `[bump-req]` | Submodule bump в `.src` | Только submodule pointer + minimal metadata |
| `[reconcile]` | Reconciliation hook находит drift | Авто-fix очевидных рассогласований; flag для остальных |
| `[test-spec-change]` | Pass/Fail-критерий TC изменён | Manual; требует отдельный approval от reviewer |

Hook носителя парсит commit message и применяет различные validation rules в зависимости от тега. `[coverage]` / `[bump-req]` / `[reconcile]` коммиты — от bot user; `[baseline-update]` / `[test-spec-change]` — от human + bot signature.

### 10.2 Bot-user setup

- Отдельный bot account (например `renar-bot@<org>`) с **write** permission в `<project>.req` и `<project>.src`.
- Bot commits signed (GPG / SSH commit signature).
- Bot user **не** может approve PR (separation of duties — утверждение всегда human).

### 10.3 branch protection

В обоих репо:
- `main` (или `master`) — protected. Push требует merged PR.
- Required status checks: schema validation (V2), reference integrity (V5), lifecycle validation (V3).
- Reviewers required: ≥ 1 для большинства; ≥ 2 для priority=must BR / SR / SPEC.

---

## 11. Перекрёстные ссылки

- [standard/03-substrate-versioning](../standard/03-substrate-versioning.md) — нормативные требования к носителю (capability V1-V6).
- [reference/02-schemas](../reference/02-schemas.md) — frontmatter schemas для validation hooks.
- [02-transition-guide](02-transition-guide.md) — где этот guide вписывается в путь от pre-RENAR к RENAR-N.
- [04-document-store-substrate](04-document-store-substrate.md) — informative обзор document-oriented store (альтернатива git).
- [07-failure-modes](07-failure-modes.md) — что может пойти не так на git как носителе (схема обхода hooks, etc.).
- [standard/10-lifecycle-qg](../standard/10-lifecycle-qg.md) — нормативные lifecycle переходы, которые validate-lifecycle hook должен enforce.

---

## 12. Open questions

- Стандартизировать ли минимальные реализации перехватов как **единое** спецификационное описание, на которое опираются и VCS, и document store?
- Submodule vs subtree: какие conditions делают subtree приемлемой альтернативой? Сейчас гайд однозначно за submodule.
- `[coverage]` bot user: best practice для signing / permission в multi-org проектах?
- Периодичность детекции дрейфа: weekly — хороший вариант по умолчанию, но что для high-velocity teams (> 50 PR/неделя)?

