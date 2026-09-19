# RENAR Changelog

## v1.1 — волна партнёрских предложений: комплект описания, SPEC-UC, язык описания (2026-09-19, GitLab #30–#54, ADR-015…ADR-026)

Minor-версия по [§13.9.3](standard/13-conformance.md#13.9.3); руководство по миграции — [guide/12](guide/12-migration-v11.md); все действующие заявления о соответствии подлежат переоценке ([§13.7.3](standard/13-conformance.md#13.7.3)).

**Комплект описания (ADR-024, #46, #48).** Единица утверждения, версии и верификации — комплект целиком: BR / SR / SPEC / TC-норма без `status` / `version`, `set-version N.M` (минор при всяком изменении, мажор с полным аудитом), линейная история, запись версии (§10.5); QG-0 / QG-1 / QG-2 переопределены по объектам (§10.3); §13.3.5 проверяется при QG-0 версии (`coverage-presence`); манифест — `set-version`, `confirmation`. `automation.status` бинарный (#50); `tc-type: business` вместо `acceptance`.

**Артефакты.** SPEC-UC — двенадцатый тип с `role: human | agent` и ссылкой шага на утверждение (§8.5.12, ADR-018, #35); `ux`-TC на каждое действие через интерфейс (§9.8); запись ручного прохода MW (§9.20, #34, #50). Реестр экранов — перечень в теле SPEC-ARCH, `screens[]` в SPEC-UI, правило покрытия и определение экрана (§8.5.6.1, ADR-023, #42, #45, #54); полнота охвата обязательного тела (§8.4.1); SPEC-ARCH и SPEC-SEC обязательны на уровне `system` (§8.3). Переиспользуемый компонент — система в собственном дереве, `assumptions[]`, `applies-to`, ребро `uses[]` (§6.14, ADR-016, #30); `implements[]` — обязательное положение §13.3.8.

**Область и роли.** Подтверждение первой стороной — концепт на входе, ACTZ / AT беспредметны (§1.4.4, ADR-017, #33); совмещение ролей (§5.5.5); три входа изменения комплекта и исходы разбора (guide/01 фаза 8а); роли модели исполнителя ↔ §5 (guide/11 §1.1).

**Норма и процесс (ADR-027, #55).** По замечанию партнёра при утверждении: стандарт нормирует артефакты, их жизненные циклы и правила (предусловия переходов, связи), не порядок работ — §1.2.1, §1.3 п. 7, §0.2; агентские редакции: раздел «Рабочий процесс» заменён «Предусловиями артефактов»; §7.6.1 условиями, §11.11 — состав уровня; оговорки в Core и guide/00/01/11.

**Адрес утверждения (ревью v1.1).** Утверждение адресуется как `<id>#n` по порядку предложений нормативных разделов (§15.1.1); TC-норма указывает `verifies[].statement`, шаг сценария — `ref` в форме адреса (§8.5.12.1), проверка `coverage-presence` считает пары по адресам (§10.11.1).

**Язык описания (ADR-026, #51).** Новая глава 15: закрытый список из пяти операторов (строка 17 §1.7.5), глоссарная дисциплина, десять запрещённых конструкций, атомарность «ровно одна пара TC», формы по типу артефакта; обязательна с RENAR-2. §6.6.3.1 — шесть форм (ADR-022, #37), охват SPEC.

**Прочее.** Калибровка порогов метрик по условию достаточности данных (ADR-020); измеритель невырожденности контроля (§13.9.4, ADR-021, #40); AR фиксирует модель основного агента (#38); `ai-provenance` по канону (#44); C-04 — Утверждение 2 главы 02 не обязательное положение (#49). Гейты: `check-rfc-modals` с эталоном, `check-statement-form` (форма утверждения, прописные в русской прозе), `validate-schema-examples` (`verifies-legacy-version`, `uses-edge-unconfirmed`, `--self-test`), `check-agent-edition-parity`. Стандарт — 16 глав (00–15), 13 руководств.

## v1.0 — опубликованная поверхность доведена до корпуса (2026-07-26, GitLab #21)

Снятие draft 17.07 дошло до человеческой части сайта, но не до машинной. Девять дней renar.tech предъявлял AI-агентам отменённую норму: `llms.txt`, `llms-full.txt`, `.well-known/agent.json` и `openapi.json` объявляли версию `1.0-draft` и закрытый список из девяти типов SPEC; агентская редакция `/renar-agent-ru.md` и `/renar-agent-en.md` — флагманская ссылка лендинга «стандарт для агента одним файлом» — отдавалась в редакции 1.3-draft от 7 июня, то есть без ACTZ, без AT и с прежним списком типов. Корневые `RENAR-AGENT-RU.md` и `RENAR-AGENT-EN.md` были верны; в `site/public/` их просто никто не скопировал, и сверять было нечем.

Всё перечисленное приведено к корпусу; в `llms.txt` заодно исправлены две содержательные записи, пережившие ADR-011: «двойная подпись» у ADAPT и определение ADAPT как двусторонней интерпретации. Порождающий заголовок `scripts/build-llms-full.js` нёс число типов константой — правка в генераторе, иначе она откатывалась бы каждой перегенерацией.

`check-site-parity.js` расширен тремя проверками, которых не было: версия и число типов SPEC в машинных артефактах, побайтовое совпадение опубликованных агентских редакций с корневыми. Прежний гейт сверял только счётчики руководств и справочников на лендингах — ровно тот случай, когда проверка слабее нормы и снаружи видна только проверка.

## v1.0 — финализация корпуса: снятие draft (2026-07-17, GitLab #21, #28)

Версия стандарта переведена с `1.0-draft` на `1.0` во всех живых метках корпуса (frontmatter, заголовки, баннеры, PDF, лендинги, agent-файлы); датированные записи истории изменений сохранены как есть. Операционная редакция для агента и Core, ранее носившие собственный номер `1.3-draft`, приведены к версии стандарта `1.0`.

**Информативные приложения (#28):** «Рекомендованная форма ТЗ» (reference/13) и «Рекомендованная форма программы приёмочных испытаний» (reference/14) — форма выводится из типологии обратных находок; механизм коридоров качества дан без числовых порогов и без привязки к вендорскому регламенту.

**Тщательный ревью корпуса** перед релизом (двухволновой, RU+EN): доведены следы пакетов ADR-011..014 (ACT → ACTZ, 9 → 11 типов SPEC, реактивность ADAPT, tc-type `acceptance` → `business`, подпись ADAPT только архитектором); исправлены ~300 расхождений, включая мёртвые слаг-якоря и лживые §-метки; добавлен гейт `check-anchor-slugs`.

**Открытые правки нормативки, вынесенные при финализации** (требуют согласования партнёра): идентификатор `at-release` в журнале гейтов; поле `quality-characteristic` как условно-обязательное (§6.6.2); трактовка релизного гейта AT как не-Quality-Gate; отсутствие статуса `obsolete` у ADAPT.

## SPEC-TEST и SPEC-DOC — закрытый список 9 → 11 типов (2026-07-14, ADR-013, GitLab #24, #25)

**SPEC-TEST** — тестовые стенды и данные. Прежнее решение (§8.3.1: «тестовые окружения — раздел SPEC-OPS») **отменено**, и отменённая строка осталась в таблице зачёркнутой: история решений — часть стандарта, и читатель вправе видеть, что и почему пересмотрено.

Решающий довод — механический, и он мой недосмотр. §10.5.4 инвалидирует `verified` при **любом** инкременте версии артефакта, автоматически и без разбора причины. При привязке TC к стенду через SPEC-OPS правка **процедуры деплоя** — к стенду отношения не имеющая — обрушивала бы в `approved` **все** TC, ссылающиеся на этот SPEC-OPS. Деплой правят чаще стенда, значит доказательная база обесценивалась бы регулярно и ложно. Отдельный тип делает инвалидацию **точной**: TC инвалидируются тогда и только тогда, когда изменилось то, против чего они валидны.

Второй довод — жанр: стенд интеграционно-тяжёлой системы не инсталляция, а **конструкция доказательства** (эмуляторы и sandbox-инстансы внешних систем, датасеты по обе стороны каждой интеграции, правила сверки — ожидаемый результат живёт **не в нашей системе**). Стандарт это уже чувствовал: §9.6.3 требовал прогон против sandbox-counterparty, но носителя для её описания не давал.

**SPEC-DOC** — поставляемая документация: состав, структура, проверяемые требования. Обязателен **док-линт** (§9.8): неверифицируемый SPEC не проходит QG-2, и без машинной проверки такой тип либо заблокировал бы поставку, либо вынудил проделать дыру в MVR-5. Содержательное соответствие реализованному поведению — опционально judge-агентом через существующий механизм P7 / `eval`, без новых сущностей. Граница с SPEC-OPS — по **составу поставки**: руководство администратора всегда SPEC-DOC (заказчик получает его как часть результата), внутренний runbook эксплуатирующей команды — всегда SPEC-OPS.

Спецификация описывает теперь **три предмета** (§8.1, §8.2.1): устройство системы, конструкцию доказательства её соответствия и состав поставляемого результата.

**Обвязка:** обязательное `TC.environment-ref` → SPEC-TEST (условно — только при `automation.kind: dynamic`; статические проверки стенда не требуют); подпись клиента на SPEC-TEST при реальных или персональных данных; MVR-4, §13.3.4, §1.7.5, схемы, шаблоны, глоссарий, чек-лист самооценки, EN-паритет.

**Три дефекта в собственной норме**, вскрытые ревью пакета:

- **Док-линт был необязателен де-факто.** Требования к TC для SPEC-DOC и SPEC-TEST стояли в **прозе под** закрытой таблицей §9.8 — а именно таблицу и реализует хук. Норма в тексте была, гейт её не видел: SPEC-DOC без док-линта прошёл бы. Обе строки добавлены в саму таблицу.
- **`environment-ref` был безусловным** — и каждый SPEC-DOC вынужденно тянул бы за собой SPEC-TEST: док-линт и структурный TC гоняются статическим анализатором **по артефактам**, а не против стенда. Поле стало условным.
- **У AT поле ломало бы изоляцию:** генератор AT внутренних артефактов не видит и видеть не должен. Разрешено явно — привязку к стенду проставляет архитектор или runner **после** генерации, поэтому испытания остаются воспроизводимыми, а изоляция не нарушается.

Плюс: нормативный YAML-enum в §8.4 был девятизначным и противоречил §8.3; схема хранения не знала подпапок `test/` и `doc/`; MVR-4 говорил «11 типов», но перечислял девять имён; YAML-манифест соответствия предлагал декларировать девять.

## Пакет ADAPT/ACTZ + AT — расщепление контуров и приёмка от контракта (2026-07-14, ADR-011 + ADR-012, GitLab #26, #27)

Крупнейшая правка корпуса с момента появления стандарта. Вносится **в v1.0 до заморозки** — отдельного v2.0 не будет: пока стандарт не выпущен, расширение закрытых списков и правка MVR суть дописывание невыпущенного стандарта, а не изменение выпущенного. Поэтому процедура §13.9, руководство по миграции и повторная оценка соответствия (§13.7.3) **не применяются**. Пакет вносится целиком — связка ADR-011 + ADR-012 (условие партнёра): порознь они делают корпус хуже, чем был.

### Расщепление ADAPT / ACTZ (ADR-011)

Клиентская подпись под ADAPT была **фикцией**: клиент подписывал инженерный документ, который не читал и не мог оценить. Стандарт это чувствовал и лечил полумерами (§7.8.2 — «краткое содержание для одностраничного чтения клиентом»). Теперь граница проводится **по аудитории**, а не по содержанию записи:

> Всё, что показано клиенту и утверждено, — обязательство. Всё, что не показано, — интерпретация.

- **ADAPT** (§7.5) — внутренний артефакт интерпретации; подписывает **только Архитектор**; клиент его не видит.
- **ACTZ** (§7.13) — новый артефакт контрактного контура: протокол уточнения ТЗ, двусторонняя подпись, контрактный вес, жизненный цикл `draft → sent → signed → superseded`. Кардинальность: 0..N на ТЗ, 1..N на ADAPT. Поздний протокол (после демонстрации) — **штатный** случай. Договорное имя — «Протокол уточнения ТЗ № N»; слово «акт» в корпусе запрещено и зарезервировано за документами сдачи-приёмки.
- **Итоговое ТЗ** (§7.14) — эталон сдачи-приёмки: начальное ТЗ с приложениями **плюс все подписанные ACTZ**; приоритет у более позднего подписанного документа. ADAPT в эталон приёмки не входит — приёмка стала юридически чистой.
- Каждая находка обязана нести `decided-in` на пункт **подписанного** ACTZ (§7.4.5). Интерпретация не может опираться на решение, которого клиент не принимал; подписанное решение, не отражённое ни в одном ADAPT, — обязательство вне требований, **fatal**.
- Состояние `client-ready` изъято из машины состояний ADAPT: вынесение вопросов клиенту — предмет ACTZ, а не состояние интерпретации.

### AT, изоляция авторства, красная история (ADR-012)

Прослеживаемость TC замкнута через интерпретацию (`TC → SR → ADAPT → ТЗ`). Отсюда класс дефектов, который TC не ловит **в принципе**: при неверной интерпретации все TC зелёные — система идеально соответствует **неверному** толкованию — и проваливает приёмку у заказчика.

- **AT** (§9.19) — приёмочный тест контрактного контура: выводится **изолированным агентом** исключительно из итогового ТЗ, без доступа к ADAPT / BR / SR / SPEC / TC / коду. **Перегенерируется перед каждыми испытаниями** от действующей редакции: итоговое ТЗ живёт инкрементно, и AT, выведенные при планировании, к испытаниям устаревают — иначе система в конце длинного заказа проверяется против контракта годичной давности. Обязательный `tz_text` — дословная цитата пункта контракта. Матрица маршрутизации: **«AT красный при зелёных TC = ошибка интерпретации»**.
- **Релизный гейт** (§10.4.3): продукт не предъявляется к сдаче, пока не все AT зелёные и не выведены из действующей редакции. Не смешивается с QG-4 (тот опционален и меряет бизнес-результат).
- **P8, изоляция авторства** (§9.18.1): тест заморожен до реализации; пишется не тем агентом, что код; правка критериев — только через `[test-spec-change]`.
- **P9, красная история** (§9.18.2): **тест, ни разу не наблюдавшийся красным, не является доказательством.** P8 гарантирует, что тест написан до кода, но не что он **что-то проверяет**: пустой тест, честно написанный изолированным агентом, зелен с рождения и проходит все три оси P8. Красная история закрывает ровно этот остаток.
- **Машинный сигнал ослабления нормы** (§9.18.3): если после правки теста падает ранее зелёная система — норма изменилась по факту, как бы правку ни классифицировали.
- **`automation.kind: static | dynamic`** (§9.8.1) вместо нового типа TC: проверка zoning/dependency уже была обязательна в §9.8, недоставало лишь признания, что её runner — статический анализатор. У структурного TC нормативная часть **вырождена**: эталон живёт в SPEC, поэтому провал чинится изменением SPEC, а не правкой теста.
- **`tc-type: acceptance` → `business`** (§9.5): приёмок две, и одно имя на два разных предмета путало.
- Спот-чек (§9.14.2) из случайной выборки стал **направляемым**: отбор наводят машинные сигналы — тесты без красной истории, пережившие мутанта, `implementation-originated`.

### Люфт агента: `implementation-originated` (ADR-014, §6.13)

Запрет обратной фильтрации (MVR-1) **не ослаблен**. Но требовать delta-ADAPT с подписью клиента на каждую защитную проверку — абсурдная церемония, и именно она толкает к молчаливой подгонке требований задним числом. Введён **узкий** легальный класс: только внутренние технические детали **без наблюдаемого клиентом поведения**, с провенансом единицы изменения, утверждением человека-супервайзера, TC до слияния и счётчиком в метриках дрейфа. Красной истории у такого TC быть не может по построению (он пишется после кода и рождается зелёным) — компенсация обязательна: **убитый мутант**.

### Обвязка

- **Метрика §12.3.11 Implementation-originated Rate** — список REQ-метрик стал из одиннадцати. Рост доли означает, что процесс требований протекает: функциональность рождается в коде и легализуется задним числом. Норма требует, чтобы этот дрейф был **виден**.
- **Три хука** (§10.11.1): `actz-integrity`, `at-isolation`, `red-history` — с fatal-условиями.
- **Гейт `scripts/check-actz-at.js`** — 15 фикстур на четыре класса нарушений.
- Схемы (`reference/02`), шаблоны (`reference/12`), глоссарий (`reference/01`), agent-файлы, руководства, `core/` — приведены в соответствие. EN-паритет по всему корпусу.

### Adversarial-ревью пакета

Пакет прошёл многоагентное состязательное ревью (82 находки, 69 подтверждено независимыми скептиками). Оно вскрыло класс дефектов, который не ловил ни один гейт: **невычищенные остатки прежней нормы**. Глава обязательных положений (§13.3.3) одновременно требовала подпись клиента на дезавуирующем ADAPT и объявляла требование клиентской подписи избыточным; §10.4.1 требовал двойную подпись в предусловии и подпись архитектора в триггере — через две строки; frontmatter ADAPT (§7.8.1) сохранял `client-signature: mandatory`. Корпус был нормативно противоречив ровно там, где решается, соответствует ли проект стандарту. Все подтверждённые находки устранены.


## AR — запись состязательного обзора (2026-07-13, ADR-009, GitLab #20)

Вердикт состязательного обзора получил носителя. До этой правки вердикт был обязателен и проверялся хуком, но сам артефакт-носитель был безымянен: `source.adversarial-review-ref` указывал на «нативную ссылку носителя» без нормированной цели — ни идентичности, ни жизненного цикла, ни шаблона. Это и есть разрыв из GitLab #20.

**AR (Adversarial-Review Record)** введена как **запись-свидетельство**, а не как тип требования: у неё нет родителей и потомков, она не декомпозируется и не покрывается TC. Поэтому **ни один закрытый список не расширен** ([§1.7.5](standard/01-scope.md#1.7.5)) и формальная процедура §13.9 не потребовалась — доопределено уже существующее поле. Классификацию (Q1) и остальные четыре вопроса партнёр подтвердил в GitLab #20 2026-06-08.

- **Нормативно:** `standard/07 §7.4.6` (идентичность `AR-NNN` в рамках ТЗ, обязательные поля, жизненный цикл `draft → issued → superseded`, инвариант провенанса узла), `§7.4.1.2`/`§7.4.1.3` (AR — единственный носитель вердикта в обоих исходах), `§7.11` (подпапка `ar/`), `standard/04 §4.10.4` (термин — в терминах происхождения), `§6.5.2`/`§6.6.2`/`§6.10.3` и `§8.5` (`source.adversarial-review-ref` → `AR-NNN`), MVR-3 в `§0.5`, `standard/13 §13.3.3` (обязательные положения + четыре негативных сценария).
- **Гейт:** `check-adapt-applicability.js` переведён на AR как носитель вердикта и расширен проверками целостности — изоляция модели рецензента (§7.10.2), непустой `produces-adapt[]` при `findings-present`, подпись V6 у `issued`, `superseded-by` у `superseded`, висячие ссылки и ссылки на `draft`/`superseded` — fatal. Отдельный QG **не** вводится (прецедент ADR-007 Q5). 17 фикстур, включая 14 негативных.
- **Справочник:** схема AR (`reference/02 §7.1`), шаблон AR (`reference/12 §12.6`), EN-паритет по всем файлам, agent-файлы RU/EN.

## Языковое разделение поиска на /docs/ (2026-07-13, GitLab #10)

Поиск на русском сайте выдавал английские статьи. Причина: корпус `/docs/` двуязычный — EN-издания лежат **внутри** секций (`standard/en/*.md`, …), и monorepo-плагин собирает их как страницы, хотя в навигации их нет. Они попадали в общий индекс рядом с русскими: из 2015 проиндексированных документов 1003 были английскими. Комментарий в `mkdocs.yml` при этом утверждал, что корпус русскоязычный, — и это было неправдой.

Настройками задача не решается: бандл mkdocs-material тянет **один** индекс на сайт, по фиксированному пути от корня. Поэтому индекс делится после сборки:

- `site-docs/hooks.py` (`on_post_build`) разрезает индекс на русский (`search_index.json`, 1019 док.) и английский (`search_index_en.json`, 1010 док., `lang: en` — английский стеммер вместо русского);
- EN-страницы переписываются (`on_post_page`): перед бандлом темы вставляется шим, который перенаправляет загрузку индекса на английский. URL не меняются — 44 EN-адреса в sitemap остаются живыми;
- пустой EN-индекс роняет сборку: EN-читатель не должен молча получить пустой поиск вместо результатов.

Шим держится на допущении, что тема грузит индекс по пути `search/search_index.json`. Обновление mkdocs-material могло бы сломать это молча, поэтому `scripts/check-search-index.js` проверяет допущение **против самого бандла** и падает, если путь изменился, а заодно ловит утечку языков в оба конца и неверную позицию шима. Гейт в `check:all`, 5 фикстур.

## Внутренние противоречия стандарта (2026-07-13)

Два дефекта, найденные при сверке партнёрских исследований (GitLab #26–#28) с действующим текстом. Оба — расхождения стандарта с самим собой, не связанные с предложениями партнёра.

- **«Ровно один корневой ADAPT» против MVR-3.** `standard/04 §4.3.2` («обязательный мостовой артефакт… каждое ТЗ обязано иметь **ровно один** корневой ADAPT») и `standard/06 §6.3.1` («один корневой ADAPT») сохраняли формулировку до ADR-006/007 и прямо противоречили MVR-3 (ADAPT реактивен, кардинальность 0..N). Причём §4.3.2 ссылался на §13.3.3, который утверждает обратное — то есть стандарт противоречил сам себе в определении центрального артефакта. Приведено к MVR-3 в RU и EN; попутно исправлен чек-лист соответствия `guide/06` («на каждое ТЗ ровно один approved ADAPT»).
- **Норма покрытия строже собственной проверки.** `§9.7` / P5 / MVR-5 / `§13.3.5` требуют pos/neg-парность **по каждому нормативному утверждению** (BR/SR/SPEC), но предусловие перехода `approved → verified` в `§10.5.2` ослабляло это до «хотя бы один TC с `negative: true`» **на артефакт** — то есть QG-2 пропустил бы артефакт, где второе утверждение покрыто только положительным тестом. `§10.5.2` приведён к §9.7; в `§9.15.2` явно разведены две метрики покрытия (`coverage-percent` считается от артефактов и меряет продвижение; парность считается от утверждений и всегда 100%).

## EN edition (2026-06-06) — epic `en-translation`

Полное второе языковое издание стандарта на английском, добавлено рядом с первичным RU-корпусом (RU остаётся primary). Корпус стал bilingual.

- **EN-фундамент:** EN Style Guide (`reference/en/06-en-style-guide.md` — зеркало RU-стайл-гайда с инвертированной полярностью RFC-2119: EN UPPERCASE canonical, RU lowercase только в цитировании); EN-глоссарий (`reference/en/01-glossary.md`); конвенция директории `<секция>/en/<имя>.md` (ADR/decision #80); «ТЗ» → `TZ` (латиница, совпадает с ID `TZ-YYYY-NNN`).
- **Перевод:** `standard/en/` (16 файлов: 00–14 + README), `reference/en/` (12), `core/en/` (2), `guide/en/` (12). Faithful native-English; канон-идентификаторы (BR/SR/SPEC-*/TC/ADAPT/QG/MVR/V1–V6/статусы/поля) латиницей в обоих изданиях; §1.15-термины — нативный английский (manifest/conformance/provenance/adversarial review/lifecycle/…); RFC-2119 RU lowercase → EN UPPERCASE с сохранением уровня.
- **Гейты EN:** RU-scoped проверки (`check-substrate-term`, `check-process-vocab`, bucket-a/-e в `style-guide-check`) исключают `lang:en`; `check-rfc-modals` — двойной инвентарь RU/EN; новый `check-en-parity.js` — двусторонний паритет (наличие counterpart + совпадение множества §-номеров), в `check:all`.
- **Интеграция:** EN PDF (`build-pdf-en.js` + `pdf-manifest-en.json`, npm `pdf:en`, 4 документа); bilingual site (Astro i18n: `/en/` маршруты, переключатель RU↔EN, hreflang, RU default); cross-section ссылки EN-издания перенацелены на EN-аналоги (`scripts/repoint-en-crosslinks.js`).
- **Версия-веха:** EN-перевод выполнен → снят блокер для bump v1.0 (остаётся согласование партнёров).

## v1.0 (pending release; release date set at tag-v1)

> Финальная нормативная форма стандарта. Замораживает schemas, closed lists, lifecycle и conformance procedures. После релиза изменения — только через явный RFC с bump major version (v2.0+) или patch updates (v1.0.x для editorial corrections).

### Standard (15 normative chapters)

Полный 15-главный нормативный текст в `standard/`:

- **§00 Introduction** — MVR + drift taxonomy.
- **§01 Scope** — closed lists + negative scope.
- **§02 Normative references** — ISO/IEC, GDPR, AI Act, SAFe 6.0, и др.
- **§03 Terms and definitions** — глоссарий + canonical-only принцип.
- **§04 Roles** — RACI + dual-signature ADAPT.
- **§05 Methodology positioning** — три принципа (SoT inversion, waterfall-form, substrate-agnostic).
- **§06 Requirements hierarchy** — ТЗ → ADAPT → BR → SR → TR + frontmatter schemas.
- **§07 ADAPT** — bridge artefact, forward + backward, double signature.
- **§08 Specifications** — closed list 9 SPEC типов (ARCH/API/DATA/INT/PROC/UI/AI/SEC/OPS); specifications as parallel axis через `constrained-by[]` / `implements-spec[]`.
- **§09 Test cases** — first-class TC, closed list `tc-type` (acceptance/ux/system/contract/eval/security), pos/neg парность, VLM-judge, eval judge isolation.
- **§10 Lifecycle и Quality Gates** — состояния и переходы; QG-0 / QG-1 / QG-2 / QG-3 / QG-4.
- **§11 Substrate versioning** — capability V1-V6 (substrate-agnostic).
- **§12 Maturity model** — closed list уровней RENAR-1 (Ad-hoc) → RENAR-5 (Optimized).
- **§13 Metrics** — RDLT, Coverage Velocity, Hallucination Rate, Acceptance Coverage Rate, Multi-Model Disagreement Rate, и др.
- **§14 Conformance** — manifest schema, self-assessment, third-party assessment, re-assessment cadence.

### Core

- `core/renar-core.md` (~426 lines) — gentle single-doc введение: 5 правил + ADAPT + 2 QG + walkthrough.

### Reference (8 appendices)

- `reference/01-glossary.md` — компактный глоссарий.
- `reference/02-schemas.md` — frontmatter schemas.
- `reference/03-ai-risk-register.md` — AI risk register (14 AIR).
- `reference/04-ai-style-guide.md` — AI provenance + style.
- `reference/05-knowledge-graph-schema.md` — KG schema.
- `reference/06-ru-style-guide.md` — RU editorial rules.
- `reference/07-iso29148-trace-matrix.md` — ISO 29148 mapping.
- `reference/08-conformance-self-assessment.md` — printable conformance kit.

### Guide (11 practical chapters)

- `guide/00-quickstart.md` — 30-минутный hands-on.
- `guide/01-walkthrough.md` — полный example (Login Flow для AcmeCorp).
- `guide/02-transition-guide.md` — поэтапная миграция RENAR-1 → RENAR-5.
- `guide/03-tool-guide-git.md` — substrate-specific guide на git.
- `guide/04-document-store-substrate.md` — informative обзор V1–V6 на document-oriented store.
- `guide/05-safe-comparison.md` — mapping RENAR ↔ SAFe 6.0.
- `guide/06-compliance.md` — mapping на compliance фреймворки + printable checklists.
- `guide/07-failure-modes.md` — 8 классов дрифта + 14 AI-рисков.
- `guide/08-developer-guide.md` — practical guide для разработчиков.
- `guide/09-worked-examples.md` — библиотека E2E-примеров.
- `guide/10-migration-v1.md` — миграция deprecated типов на v1.0-draft.

### v1.0-draft hardening P1 (2026-05-22)

- Agent adversarial review: `research/ru-agent-adversarial-review.md`, findings `research/internal/agent-review-2026-05-22.md`.
- `guide/09` — E4 (webhook/API), E5 (SPEC-AI eval), E6 (delta-ТЗ dispute).
- `guide/06 §11.1` — GDPR/ФЗ-152 evidence pack templates.
- Decision trees (informative): `standard/09 §9.1.1`, `standard/10 §10.1.1`.

### v1.0-draft hardening (2026-05-22)

- `scripts/validate-schema-examples.js` — YAML example drift gate vs reference/02.
- `scripts/check-site-parity.js` — hero counts vs mkdocs nav.
- Anchor fixes: `standard/03 §3.11.5`, `guide/02`, `reference/04`, `reference/07`.
- `research/ru-external-review-kit.md` + `.gitlab/issue_templates/renar-external-review.md`.
- Site: hero 11 guides / 9 reference; llms.txt updated.

### Site infrastructure

- Astro static site (`site/`) — renar.tech, RU primary, EN deferred.
- Docker dev environment (`docker-compose.yml`) — local dev на порту 4321.
- `scripts/sync-site-content.js` — синхронизирует `{standard,guide,reference,core}/*.md` → `site/src/content/`.
- `scripts/add-chapter-frontmatter.js` — backfill helper для chapter frontmatter.
- Astro content collection schema валидирует chapter frontmatter (title / description / order / lang / version).

### Migration notes от v0.1-draft → v1.0

Несовместимые изменения терминологии (см. [standard/03 §3.14](standard/03-terms.md)):

| Deprecated (v0.1-draft) | Canonical (v1.0) | Notes |
|---|---|---|
| `INT-SR` (Integration SR) | `SR` с `constrained-by: [SPEC-INT-N]` | Интеграционные требования — обычный SR с ссылкой на SPEC-INT |
| `INT-TC` (Integration TC) | `TC` с `tc-type: contract` | Контрактные тесты получили явный `tc-type` |
| `AIC` (AI Concept) | `SPEC-AI` (§8.5.7) | AI use cases — один из 9 SPEC типов |
| `UIC` (UI Concept) | `SPEC-UI` | UI/UX baselines — отдельный SPEC тип |
| `TS` (Technical Specification) | `SPEC-ARCH` или `SPEC-OPS` | По содержанию (архитектурная vs операционная) |
| `TM` (Module/Submodule SR) | `SR` с `level: module` | Уровень указывается полем, не отдельным типом |

Существующие проекты на v0.1-draft могут оставаться там — RENAR Standard поддерживает major version concurrency. Миграция на v1.0:

1. Заменить deprecated имена файлов / папок на canonical (мaintain ID stability через `legacy-id` поле во frontmatter если требуется).
2. Обновить `constrained-by[]` / `verifies[]` cross-references.
3. Bump frontmatter `version: "1.0-draft"` → `version: "1.0"`.
4. Перепубликовать conformance manifest с новой версией.

### Closed list changes

Закрытые списки финализированы:
- 9 SPEC типов (`SPEC-{ARCH,API,DATA,INT,PROC,UI,AI,SEC,OPS}`).
- 6 `tc-type` значений (`acceptance / ux / system / contract / eval / security`).
- 5 maturity levels (`RENAR-1` через `RENAR-5`).
- 5 base статусов (`draft / approved / verified / deprecated / obsolete`).
- 5 QG (`QG-0 / QG-1 / QG-2 / QG-3 / QG-4`).

Project-local создание новых типов запрещено (`§14.3.4`). Расширения только через RFC + новый major version стандарта.

### Contributors

- **Vadim Soglaev** — author, normative GAP analysis and partner review.
- **Andrey Yumashev** — author, RENAR Standard editor, project lead.
- **AI collaborators** — Claude Opus / Sonnet (Anthropic) для генерации drafts, normative chapters, и review.

Спасибо ревьюерам черновиков и членам Andersen Stack team за feedback на партнёрском workflow patterns (research drafts 00, 06, 13).

### License

CC BY-SA 4.0 (наследовано из SENAR). Используется для всех текстов стандарта, guide, reference и core.

---

## v1.0-draft (in progress, 2026-05-21)

### Foundation
- 19 research drafts in `research/` covering vision, positioning vs world standards, agent-driven principles, maturity, metrics, glossary, multi-perspective review, AI style guide, SAFe/compliance mappings, AI risk register, elicitation, solution evaluation, worked example, requirement schema, lifecycle, knowledge graph, methodology positioning, specifications, ADAPT
- Skeleton structure for `standard/` (15 chapters), `guide/` (8 guides), `reference/` (5 appendices), `core/` (1 doc) created
- Scope v1.0 = 15 chapters confirmed by partner 2026-05-13
- LICENSE CC BY-SA 4.0 adopted (inherited from SENAR)

### Approved drafts (по итогам ревью партнёра 2026-05-13)
- **Draft 17** — Specification Schema and Templates: closed list of 9 SPEC types (ARCH/API/DATA/INT/PROC/UI/AI/SEC/OPS); specifications as parallel axis to requirements via `constrained-by[]` graph edges
- **Draft 18** — ADAPT: intermediate artifact between immutable TZ and BR/SR/SPEC; forward interpretation + backward findings with lifecycle (7 categories); client + architect double signature for approval
- **Draft 19** — Methodology Positioning: three principles — (1) SoT inversion (requirements, not code; Spec-Driven Development); (2) waterfall-form ≠ classical waterfall (4 differentiations); (3) substrate-agnostic versioning (V1-V6 capabilities)

### Renamed (earlier)
- Standard renamed REQ → RENAR (Requirements Engineering & Normative Adaptive Regulation)

### Phase 5 cleanup (2026-05-13)
- **E4 legacy cleanup:** удалены 4 legacy normative-документа из корня — содержание уже зафиксировано в `standard/00-14`:
  - `requirements-methodology.md` → перенесено в `standard/03` (terms), `standard/04` (roles), `standard/05` (methodology positioning), `standard/06` (hierarchy)
  - `requirements-storage-standard.md` → перенесено в `standard/06` (hierarchy), `standard/08` (specifications), `standard/10` (lifecycle/QG)
  - `testing-methodology.md` → перенесено в `standard/09` (test-cases)
  - `developer-guide-requirements.md` → мигрирован в `guide/08-developer-guide.md` (practical guide, не normative)

### TODO (Phase 5 onwards)
- Phase 5: senar-parity (site/, docs/, README.ru.md, RENAR-SUMMARY*.md, Dockerfile/CI) — epic `phase-5-senar-parity`
- EN translation parallel to RU (deferred)
- Site at renar.tech (E7, planned)
- PDF generation pipeline
- Generalization guide/08-developer-guide.md от Kibertum/andersen-specific examples к generic placeholders (follow-up)
