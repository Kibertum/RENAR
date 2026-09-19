---
title: "Руководство разработчика"
order: 8
lang: ru
---
# 08. Руководство разработчика

> **Пример только для среды `git`.** Эта глава иллюстрирует **один** возможный сценарий на примере GitHub и разнесённых по репозиториям подсистем (каталоги `.req` и `.src`). **RENAR к конкретной реализации среды не привязан**; ваши перехватчики и пайплайны могут быть устроены иначе. Общезначимые главы — [§6 Иерархия требований](../standard/06-requirements-hierarchy.md), [§8 Спецификации](../standard/08-specifications.md), [§10 Жизненный цикл и контрольные точки качества](../standard/10-lifecycle-qg.md). Альтернатива без VCS как файловой базы — [04-document-store-substrate.md](04-document-store-substrate.md).
>
> **Выдуманная компания AcmeCorp:** платформа `acme-platform` и четыре подсистемы — `acme-portal`, `acme-ai`, `acme-orchestrator`, `acme-site`. Имена в примерах замените на свои; ссылки на нормативные главы этого не затрагивают.

---

## 1. Что нужно знать перед стартом

**Два типа репозиториев.** Каждая подсистема имеет два отдельных репозитория:

| Репозиторий | Суффикс | Что внутри | Кто пишет |
|---|---|---|---|
| Требования | `.req` | BR, SR — что система должна делать | Архитектор, Tech Lead |
| Исходный код | `.src` | Код, тесты, CI/CD | Разработчик |

Разделение намеренное: требования версионируются независимо от кода, имеют другой цикл рецензирования и другие права доступа.

**Иерархия:** Система (`acme-platform`) → Подсистема (`acme-portal`, `acme-ai`, `acme-orchestrator`, `acme-site`) → Модуль (если есть). Каждый уровень имеет свой `.req` репозиторий; требования верхнего уровня — родители для требований ниже.

**Три уровня требований:**

```text
BR  — Бизнес-требование  (зачем это нужно бизнесу)
 └── SR  — Системное требование  (что делает система)
      └── TR  — Требование к задаче  (конкретика для реализации)
                  ↑ это поля вашей задачи в трекере, не файл
```

**TR не является файлом** — это Goal + Acceptance Criteria в задаче трекера.

---

## 2. Первоначальная настройка локального окружения

**Шаг 1.** Уточните у Tech Lead, с какой подсистемой работаете: `acme-portal` (клиентский портал), `acme-ai` (AI-pipeline), `acme-orchestrator` (оркестратор), `acme-site` (публичный сайт).

**Шаг 2-3.** Создайте структуру и клонируйте репозитории:

```bash
mkdir -p ~/projects/acmecorp/acme-platform && cd ~/projects/acmecorp/acme-platform

# Обязательно для всех — родительские требования системы (только чтение):
git clone git@github.com:acmecorp/acme-platform/acme-platform.req

# Обязательно — репозитории вашей подсистемы:
SUBSYSTEM=acme-portal   # замените на свою
mkdir -p $SUBSYSTEM
git clone git@github.com:acmecorp/acme-platform/$SUBSYSTEM/$SUBSYSTEM.req $SUBSYSTEM/$SUBSYSTEM.req
git clone git@github.com:acmecorp/acme-platform/$SUBSYSTEM/$SUBSYSTEM.src $SUBSYSTEM/$SUBSYSTEM.src

# По необходимости — требования смежных подсистем (только чтение):
mkdir -p acme-ai && git clone git@github.com:acmecorp/acme-platform/acme-ai/acme-ai.req acme-ai/acme-ai.req
```

**Шаг 4.** Проверка: `find ~/projects/acmecorp -maxdepth 4 -name ".git" -type d | sed 's|/.git||' | sort`. Ожидаемый результат для разработчика Acme Portal:

```text
~/projects/acmecorp/acme-platform/acme-platform.req
~/projects/acmecorp/acme-platform/acme-portal/acme-portal.req
~/projects/acmecorp/acme-platform/acme-portal/acme-portal.src
```

---

## 3. Структура папок на локальной машине

Локальная структура **зеркалит** иерархию репозиториев в хостинге — относительные пути `../..` между репозиториями становятся предсказуемыми.

> С раскладкой по каталогам (раскладка носителя) см. [guide/03 §2 и §4](03-tool-guide-git.md): каноническая схема репозитория + закрепление `.src` → `.req` через submodule (возможность V5). Здесь — типичное локальное размещение для IDE: несколько рядом лежащих клонов без обязательного submodule в рабочей папке.

```text
~/projects/acmecorp/acme-platform/
  acme-platform.req/          # требования системы (read-only)
  acme-portal/
    acme-portal.req/          # требования вашей подсистемы
    acme-portal.src/          # код вашей подсистемы — здесь вы работаете
  acme-ai/{acme-ai.req,acme-ai.src}/             # клонируется по необходимости
  acme-orchestrator/{acme-orchestrator.req,acme-orchestrator.src}/
  acme-site/{acme-site.req,acme-site.src}/
```

> **Правило:** не меняйте имена папок — они совпадают с именами проектов в git-хостинге. Это позволяет скриптам и CI работать с одними путями везде.

---

## 4. Настройка рабочего пространства в IDE

**VS Code Workspace.** Создайте `<subsystem>.code-workspace` в папке подсистемы:

```bash
cat > ~/projects/acmecorp/acme-platform/acme-portal/acme-portal.code-workspace << 'EOF'
{
  "folders": [
    { "path": "acme-portal.src", "name": "Code — Acme Portal" },
    { "path": "acme-portal.req", "name": "Requirements — Acme Portal" },
    { "path": "../acme-platform.req", "name": "Requirements — System (read only)" }
  ],
  "settings": { "files.exclude": { "**/.git": true } }
}
EOF
```

Открыть: `code ~/projects/acmecorp/acme-platform/acme-portal/acme-portal.code-workspace`. В боковой панели — три репозитория одновременно.

**JetBrains (IntelliJ, WebStorm, PyCharm).** Откройте каждый репозиторий как отдельный модуль через **File → Attach Project** или **Project Structure → Modules**.

---

## 5. Работа с требованиями

**5.1 Как читать.** Перед началом задачи прочитайте SR (`acme-portal.req/sr/SR-NN-*.md`). В frontmatter SR найдите поле `parent` — ссылка на родительский BR:

```yaml
parent: { id: BR-01, repo: "acmecorp/acme-platform/acme-platform.req", file: "br/BR-01-order-ai-dev.md" }
```

Откройте родительский BR — это «зачем существует функциональность».

**5.2 Трассировка.** Цепочка: `Задача TASK-42 → SR-01 (acme-portal.req/sr/...) → BR-01 (acme-platform.req/br/...)`. Если что-то непонятно — поднимайтесь вверх.

**5.3 Изменить требование.** Разработчик создаёт Issue в `.req` репозитории с описанием несоответствия SR ↔ реальное поведение. Tech Lead / Архитектор вносит изменение:

```bash
cd acme-portal.req
git checkout -b change/TZ-2026-002-totp
# Редактируете файл SR; обновляете version + updated в frontmatter; обновляете source.tz-section на раздел delta-ТЗ
git add sr/SR-01-auth.md
git commit -m "[delta:TZ-2026-002] SR-01 v1.2: добавить TOTP"
# Создаёте MR/PR в git-хостинге
```

> **Запрещено:** коммитить изменения требований напрямую в `main` без MR/PR и ревью.

**5.4 Что нельзя делать.** Менять `acme-platform.req` без согласования с архитектором; менять требования смежных подсистем (`acme-ai.req`, `acme-orchestrator.req`); удалять файлы требований (только переводить в `status: deprecated`); создавать файлы версий (`SR-01-v1.md`, `SR-01-v2.md`) — история в `git log`.

---

## 6. Работа с задачами

**6.1 Перед тем как взять задачу** — все пункты QG-0 Approval Gate выполнены ([reference/01 §2.4](../reference/01-glossary.md); pre-v1.0 legacy «Context Gate»):

- Сформулирована цель (Goal); есть Acceptance Criteria — конкретные, тестируемые, независимые.
- Есть хотя бы один негативный сценарий в AC.
- Задача ссылается на SR (поле в трекере); назначен тип работы.
- Если затрагивает безопасность — `threat-surface` задекларирован.

Если чего-то нет — **не берите задачу в работу**, вернитесь к Супервизору / Tech Lead.

**6.2 Acceptance Criteria.** Каждый AC — отдельный тест. Хорошие AC: `POST /auth/login возвращает 200 и JWT-токен при верных credentials`; `POST /auth/login возвращает 401 при неверном пароле` (негативный сценарий); `POST /auth/login возвращает 422, если поле email отсутствует`; `JWT-токен истекает через 24 часа`.

Плохие AC (задачу брать нельзя): «Логин должен работать корректно»; «Обработка ошибок должна быть надёжной»; «Система должна быть безопасной».

**6.3 Если AC непонятен или противоречит SR:** прочитайте SR + родительский BR; создайте комментарий в задаче с конкретным вопросом; не интерпретируйте молча — AI интерпретирует молча, именно поэтому и нужны чёткие требования.

---

## 7. Git-процесс

**7.1 Работа с кодом (`.src`).** Стандартный feature-branch workflow:

```bash
cd acme-portal/acme-portal.src
git checkout main && git pull
git checkout -b feat/TASK-42-totp-auth
# ... работа ...
git add src/auth/totp.py
git commit -m "feat(auth): реализовать TOTP-аутентификацию (TASK-42)"
# Создать MR/PR в git-хостинге
```

Формат коммита: `<type>(<scope>): <описание> (<TASK-ID>)`.

**7.2 Работа с требованиями (`.req`).** Ветка для изменения требования (см. §5.3 выше).

**7.3 Привязка MR/PR к задаче.** В описании MR/PR указывайте: `Closes #42` + `Related SR: SR-01 (acme-portal.req)`. Если изменение затрагивает несколько `.req` репозиториев — `Related: acmecorp/acme-platform/acme-platform.req#15`.

**7.4 Именование веток:**

| Что делаете | Ветка |
|---|---|
| Новая функциональность | `feat/TASK-NN-slug` |
| Исправление бага | `fix/TASK-NN-slug` |
| Изменение требования | `change/TZ-YYYY-NNN-slug` |
| Новое требование | `feat/BR-NN-slug` или `feat/SR-NN-slug` |

---

## 8. Частые сценарии

**Сценарий 1. Новая задача:** открыть в трекере → проверить QG-0 → найти ссылку на SR → открыть SR в `acme-portal.req/sr/` → прочитать SR + родительский BR (если нужен контекст) → прогнать привязанные к задаче TC (они уже заморожены и **красные** — это норма: их писали до вас и не вы, [standard/09 §9.18](../standard/09-test-cases.md#9.18)) → создать ветку `feat/TASK-NN-slug` в `.src` → реализовать, пока TC не позеленеют → MR/PR → ревью → merge.

> **Почему тест уже написан, а не пишется вами.** Тест, рождённый рядом с кодом, проверяет код, а не требование. Поэтому TC замораживается до старта задачи и пишется другим агентом или инженером. Если по ходу реализации вы добавили внутреннюю техническую деталь, которой нет в требованиях (защитная проверка, журналирование, обработка граничного случая), — её можно легализовать классом `source: implementation-originated` ([standard/06 §6.13](../standard/06-requirements-hierarchy.md#6.13)): нужны провенанс, явное одобрение Супервизора-человека, покрывающий тест до слияния и **убитый мутант** (у такого теста нет красной истории — он рождается зелёным). Наблюдаемое клиентом поведение этим классом не легализуется: экран, поле, сообщение, срок — только через контрактный контур.

**Сценарий 2. Несоответствие между SR и требованием задачи:** НЕ делать ничего молча → комментарий в задаче («SR-01 §4 описывает X, задача требует Y — противоречие, прошу уточнить») → дождаться ответа Супервизора / Архитектора → не брать задачу в работу до устранения.

**Сценарий 3. Понять откуда взялось требование.** В `.req` репозитории: `git log -- sr/SR-01-auth.md` (история изменений) или `git show <commit-hash>` (детали конкретного изменения). Или в frontmatter файла найдите поле `source` — ссылка на ТЗ и его раздел.

**Сценарий 4. Интеграция (Acme Portal → Acme AI).** Клонируйте требования смежной подсистемы, добавьте в workspace; откройте `acme-platform.req/specs/int/SPEC-INT-01-acme-portal-acme-ai.md` — там описан контракт. Интеграционные контракты canonical-формы — `SPEC-INT-NN` ([standard/08 §8.5.4](../standard/08-specifications.md#8.5.4)), не legacy `INT-SR`. SR подсистемы ссылается через `constrained-by: [SPEC-INT-NN]`. SPEC-INT всегда живёт в `acme-platform.req/specs/int/` — не внутри подсистем.

**Сценарий 5. Дельта-ТЗ.** Работа архитектора/Tech Lead, но разработчику: дождаться merge MR с обновлёнными SR → `git pull` в `.req` → прочитать `tz/TZ-YYYY-NNN-delta-N.md` (текст delta-ТЗ) → проверить, затрагивают ли изменения ваши текущие задачи → если да — обновить или закрыть по согласованию с Супервизором.

**Сценарий 6. Обновить локальные репозитории требований:**

```bash
find ~/projects/acmecorp -name "*.req" -type d | while read repo; do
  echo "Updating $repo..."
  git -C "$repo" pull --ff-only
done
```

---

## 9. Права доступа — кто что может менять

| Репозиторий | Разработчик | Tech Lead | Архитектор |
|---|---|---|---|
| `acme-platform.req` (системные BR/SR) | Только чтение | Только чтение | Запись через MR |
| `acme-portal.req` (SR подсистемы) | Только чтение | Запись через MR | Запись через MR |
| `acme-portal.src` (код) | Запись через MR | Запись + ревью | — |
| `acme-ai.req` (чужая подсистема) | Только чтение | Только чтение | — |

**Если нужно изменить требование** — создайте Issue в `.req` репозитории, не коммитьте напрямую.

---

## 10. Быстрый справочник

**Куда смотреть когда непонятно:**

| Вопрос | Где |
|---|---|
| Что должна делать система? | `acme-portal.req/sr/SR-NN-*.md` |
| Зачем это нужно бизнесу? | `acme-platform.req/br/BR-NN-*.md` |
| Откуда взялось это требование? | frontmatter `source` → ТЗ |
| Как изменилось требование? | `git log -- sr/SR-NN-*.md` |
| Как работает интеграция с Acme AI? | `acme-platform.req/specs/int/SPEC-INT-01-*.md` |
| Что изменилось по последнему ТЗ? | `acme-platform.req/tz/TZ-YYYY-NNN-delta-N.md` |

**Чеклист перед созданием MR:** код покрывает все AC из задачи; есть тесты на негативные сценарии; MR/PR содержит ссылку `Closes #NN`; если изменилось поведение — создан Issue в `.req` для обновления SR.

**Команды Git** (наиболее частые):

```bash
git -C <portal.req> pull                                # обновить требования
git -C <portal.req> log -- sr/SR-01-auth.md             # история конкретного SR
grep -r "id: SR-01" ~/projects/acmecorp/ --include="*.md"   # найти требование по ID
grep -r "SR-01" ~/projects/acmecorp/ --include="*.md"       # все задачи, ссылающиеся на SR-01
```

**Глоссарий:** BR — бизнес-требование; SR — системное требование; TR — Goal + AC в трекере; AC — Acceptance Criteria; QG-0 — Approval Gate (canonical v1.0; pre-v1.0 legacy «Context Gate»); `.req` — git-репозиторий с требованиями подсистемы; `.src` — git-репозиторий с кодом; SPEC-INT — интеграционная спецификация (canonical v1.0; pre-v1.0 `INT-SR`); delta-ТЗ — дополнение к ТЗ при повторном заказе; deprecated — устаревшее требование (не удаляется, только помечается).
