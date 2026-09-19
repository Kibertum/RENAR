---
title: "Справочник RENAR"
order: 1
lang: ru
---
# Справочник RENAR

## Зачем справочник

**Стандарт** — для чтения последовательно и для assessor-а. **Справочник** — для точечного lookup: один термин, одна схема поля, один чек-лист перед audit, одна строка трассировки ISO 29148.

Сюда не нужно заходить «с нуля». Начните с [Core](../core/renar-core.md) и [guide/00-quickstart](../guide/00-quickstart.md); возвращайтесь сюда, когда нужна таблица, schema или printable kit.

---

## Оглавление

| # | Документ | Ссылка | Когда открывать |
|---|---|---|---|
| 01 | Глоссарий | [01-glossary.md](01-glossary.md) | Нужен термин с примером и mapping на ISO 29148 / SENAR |
| 02 | Схемы (formal) | [02-schemas.md](02-schemas.md) | Валидация frontmatter, cross-field rules, TC schema |
| 03 | Реестр AI-рисков | [03-ai-risk-register.md](03-ai-risk-register.md) | SPEC-AI, eval, происхождение — 14 рисков и mitigation |
| 04 | AI Style Guide | [04-ai-style-guide.md](04-ai-style-guide.md) | System prompt и citation rules для AI-генератора артефактов |
| 05 | Knowledge Graph Schema | [05-knowledge-graph-schema.md](05-knowledge-graph-schema.md) | Типы узлов/рёбер KG, federated queries |
| 06 | RU Style Guide | [06-ru-style-guide.md](06-ru-style-guide.md) | Редакционные правила RU-корпуса (для maintainers) |
| 07 | ISO 29148 — матрица | [07-iso29148-trace-matrix.md](07-iso29148-trace-matrix.md) | External claim ISO 29148; audit evidence |
| 08 | Самооценка соответствия | [08-conformance-self-assessment.md](08-conformance-self-assessment.md) | Printable checklist + шаблон `RENAR-CONFORMANCE.yaml` |
| 09 | Педагогическая плотность | [09-pedagogical-density.md](09-pedagogical-density.md) | Какие главы стандарта плотные — куда идти за quickstart |
| 10 | Профиль реализации для агента | [10-agent-implementation-profile.md](10-agent-implementation-profile.md) | Abstract contract для runtime / agent implementer |
| 11 | Каталог внешних стандартов | [11-external-standards-mapping.md](11-external-standards-mapping.md) | Расширенный mapping SAFe, BDD, CMMI и др. (overflow из §02) |
| 12 | Шаблоны документов | [12-document-templates.md](12-document-templates.md) | Copy-paste заготовки BR / SR / TR / TC (frontmatter + тело) |
| 13 | Рекомендованная форма ТЗ | [13-tz-form.md](13-tz-form.md) | Клиент пишет ТЗ — 11 разделов, выведенных из типологии находок (informative) |
| 14 | Форма программы приёмки | [14-acceptance-program.md](14-acceptance-program.md) | Программа испытаний для заказчика: поля, полнота, вердикт по классам дефектов (informative) |

---

## Три частых сценария

| Сценарий | Документ |
|---|---|
| «Что значит этот термин?» | [01-glossary](01-glossary.md) |
| «Как заполнить frontmatter TC?» | [02-schemas §8](02-schemas.md#8-tc--test-case) |
| «Готовим манифест перед release» | [08-conformance-self-assessment](08-conformance-self-assessment.md) |

[← К корневому README](https://github.com/Kibertum/RENAR/blob/main/README.ru.md)
