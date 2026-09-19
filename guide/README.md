---
title: "Руководство RENAR"
order: 1
lang: ru
---
# Руководство RENAR

## Зачем это руководство

**Стандарт** говорит «что обязано быть». **Руководство** показывает «как это сделать руками» — на вашем проекте, с вашей командой, без остановки разработки.

Здесь — быстрый старт за 30 минут, полный сквозной пример от ТЗ до release, поэтапный переход с Jira/Notion на RENAR, гайды по носителям (git и document store), compliance-чек-листы и типичные ошибки внедрения.

> Нормативные формулировки и closed lists — только в [standard/](../standard/README.md). Если спор «обязан ли мы» — смотрите стандарт; если спор «с чего начать в понедельник» — вы здесь.

---

## Оглавление

| # | Документ | Ссылка | Что получите |
|---|---|---|---|
| 00 | Быстрый старт | [00-quickstart.md](00-quickstart.md) | За 30 минут: ТЗ → ADAPT → BR/SR → SPEC → TC |
| 01 | Сквозной пример | [01-walkthrough.md](01-walkthrough.md) | Полный цикл «Login Flow для AcmeCorp» — от ADAPT до accepted release |
| 02 | Переход на RENAR | [02-transition-guide.md](02-transition-guide.md) | Пошагово с RENAR-1 до RENAR-5 без big-bang |
| 03 | Носитель: git | [03-tool-guide-git.md](03-tool-guide-git.md) | V1–V6 на git: `.req`-репо, review, pinning |
| 04 | Носитель: document store | [04-document-store-substrate.md](04-document-store-substrate.md) | Обзор V1–V6 на document-oriented store (без vendor names) |
| 05 | Сравнение с SAFe | [05-safe-comparison.md](05-safe-comparison.md) | Где RENAR дополняет PI Planning, WSJF, ART |
| 06 | Соответствие | [06-compliance.md](06-compliance.md) | Чек-листы по ISO 27001, GDPR, ФЗ-152, AI Act, NIST AI RMF |
| 07 | Режимы отказа | [07-failure-modes.md](07-failure-modes.md) | 8 классов дрейфа и типичные ошибки внедрения |
| 08 | Руководство разработчика | [08-developer-guide.md](08-developer-guide.md) | Onboarding: `.req` / `.src`, git-процесс, частые сценарии |
| 09 | Библиотека примеров | [09-worked-examples.md](09-worked-examples.md) | E1–E6: login, GDPR export, webhook, RAG eval, delta-ТЗ |
| 10 | Миграция на v1.0 | [10-migration-v1.md](10-migration-v1.md) | Deprecated типы → canonical; bump manifest |
| 11 | Работа с ИИ-агентом | [11-ai-agent-guide.md](11-ai-agent-guide.md) | Загрузка стандарта в агента, команды, результат, точки утверждения |
| 12 | Миграция на v1.1 | [12-migration-v11.md](12-migration-v11.md) | Карта правок волны v1.1 (ADR-015…026), что делать внедрению по каждой, переоценка заявления по §13.7.3 |

---

## Маршруты по ролям

| Роль | Старт | Затем | Зачем RENAR |
|---|---|---|---|
| **Разработчик** | [Core](../core/renar-core.md) → [00](00-quickstart.md) | [08](08-developer-guide.md) → [03](03-tool-guide-git.md) | TR, TC, hooks на носителе |
| **Architect / Tech Lead** | [00](00-quickstart.md) → [01](01-walkthrough.md) | [standard/02](../standard/02-methodology-positioning.md) → [10](10-migration-v1.md) | SoT, ADAPT, schema migration |
| **PM / RTE** | [Core](../core/renar-core.md) | [05](05-safe-comparison.md) → [09 §E3](09-worked-examples.md#2-e3--экспорт-персональных-данных-gdpr-art-15--фз-152) | BR, приоритеты, SAFe mapping |
| **Legal / Compliance** | [09 §E3](09-worked-examples.md#2-e3--экспорт-персональных-данных-gdpr-art-15--фз-152) | [06](06-compliance.md) → [reference/07](../reference/07-iso29148-trace-matrix.md) | Traceability, GDPR/ФЗ-152 evidence |
| **Regulator / Auditor** | [reference/07](../reference/07-iso29148-trace-matrix.md) | [reference/08](../reference/08-conformance-self-assessment.md) → [standard/13](../standard/13-conformance.md) | ISO 29148 mapping, manifest |
| **Оценщик (third-party)** | [standard/13 §13.3](../standard/13-conformance.md#13.3) | [reference/08](../reference/08-conformance-self-assessment.md) | Чек-лист обязательных пунктов |

---

## Порядок чтения

**Новичку:** 00 → 01 → 02 — и можно работать на RENAR-1.

**Tech Lead:** 02 → 03 или 04 (по носителю) → 07 — до включения hooks.

**PM / RTE:** 05 → 06 — когда нужна связь с PI и compliance.

[← К корневому README](https://github.com/Kibertum/RENAR/blob/main/README.ru.md)
