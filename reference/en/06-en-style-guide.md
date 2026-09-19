---
title: "EN Style Guide"
description: "Canonical EN style for the RENAR Standard — terminology (§1) + RFC-2119 wording (§2) + formatting (§3). Mirror of the RU Style Guide with inverted RFC-2119 polarity. Thin public guideline; operational enforcement — scripts/style-guide-check.js."
order: 6
lang: en
version: "1.0"
status: draft
---

# RENAR EN Style Guide

> **Normative artifact.** This document is the canonical Style Guide for the EN normative corpus of the RENAR Standard: terminology ([§1](#1-terminology)), RFC-2119 EN wording ([§2](#2-rfc-2119-en-normative-wording)), formatting ([§3](#3-formatting-conventions)).
>
> **Application:** any editorial change to EN content under `standard/en/`, `reference/en/`, `core/en/` (and, with soft application, `guide/en/`).
>
> **Relationship to the RU Style Guide.** This guide is a mirror of [`reference/06-ru-style-guide.md`](../06-ru-style-guide.md) with **inverted RFC-2119 polarity**: EN canonical normative wording is UPPERCASE (`MUST` / `SHOULD` / `MAY`), and the RU lowercase carve-out (RFC 8174) does **not** apply to the EN corpus. Terminology that the RU corpus renders in Russian prose (RU Style Guide §1.15) is rendered here in native English.
>
> **Operational enforcement:** `scripts/style-guide-check.js` and companion gates. RU-targeted gates (`check-substrate-term.js`, `check-process-vocab.js`, the RU-prose checks inside `style-guide-check.js`) skip `lang: en` files; EN files are checked for structural integrity (fence-lang, links, section-refs, parity). The Style Guide sets the **rules**; the scripts perform **enforcement**.
>
> **Version history:** [CHANGELOG.md](https://github.com/Kibertum/RENAR/blob/main/CHANGELOG.md).

---

## §0 Introduction

### §0.1 Purpose

The Style Guide fixes the **form** of EN normative content:

- which terms stay latin (canonical identifiers, shared with the RU corpus), which are rendered in native English prose, and which loanwords are accepted (§1);
- the regime for RFC-2119 keywords (EN UPPERCASE canonical, per RFC 2119 and RFC 8174) (§2);
- canonical formatting of citations / code-fences / headings / tables / typography / frontmatter (§3).

The Style Guide does **not** dictate per-sentence wording — it sets constraints within which the editor applies judgement.

### §0.2 Normative status

The Style Guide is a **normative companion** to the RENAR Standard.

- Any editorial change to EN normative content MUST conform to §1–§3.
- On a terminology conflict, [`standard/04-terms.md`](../../standard/en/04-terms.md) (canonical terminology) wins; §1 of this guide reflects that result.

### §0.3 Scope

**In scope:** EN prose, headings, tables, lists, code-fences, and frontmatter under `standard/en/`, `reference/en/`, `core/en/`, `guide/en/`; cross-file links; normative semantics (no `MUST` → `SHOULD` downgrade); EN typography.

**Out of scope:** the RU corpus (`standard/`, `reference/`, `core/`, `guide/` proper — governed by the [RU Style Guide](../06-ru-style-guide.md)); code under `scripts/`, `site/`, `.tausik/` (governed by tech-stack conventions); imagery / SVG (deferred); `research/` drafts pre-publish (author discretion).

**EN directory convention (decision #80, task `en-dir-frontmatter`):** each EN file mirrors its RU original at `<section>/en/<name>.md` with the **same file name** — e.g. `standard/en/00-introduction.md` mirrors `standard/00-introduction.md`, `reference/en/01-glossary.md` mirrors `reference/01-glossary.md`. The alternative `en/<section>/...` (a duplicated tree at the repository root) is rejected: it would break relative cross-link depth and per-section site navigation. Structural EN↔RU parity is enforced by `scripts/check-en-parity.js`.

### §0.4 Hierarchy of authority

On a source conflict the first match wins:

1. [`standard/04-terms.md`](../../standard/en/04-terms.md) — canonical RENAR terminology.
2. [`standard/01-scope.md §1.7`](../../standard/en/01-scope.md#1.7) — closed-list policy.
3. EN Style Guide §1–§3 — operational normative form for the EN corpus.
4. RU Style Guide [`reference/06`](../06-ru-style-guide.md) — for shared structural conventions (formatting, headings, code-fences) that are language-agnostic.
5. SENAR (the parent standard) — for general engineering terminology.
6. ISO/IEC/IEEE 29148:2018 — for requirements-engineering terms.

### §0.5 Change procedure

Style Guide changes follow a formal procedure (mirrors the closed-list policy [`standard/01 §1.7`](../../standard/en/01-scope.md#1.7)):

1. Propose — `research/en-style-amendment-NNN-<topic>.md` with rationale.
2. Review — Style Guide editor (or project owner).
3. Pilot — an accepted amendment is pilot-validated on one chapter.
4. Lock-in — merged, with a version bump.
5. Retroactive — already-translated chapters are re-scanned for compliance.

### §0.6 Versioning

| Version | Status | Date |
|---|---|---|
| **v1.0** | **draft** | **2026-06-06 (EN translation epic — foundation)** |

---

## §1 Terminology

### §1.1 Principles

Six normative principles govern EN terminology:

1. **No mass find-replace** — each term decision is contextual. Blind search-replace breaks normative meaning. The translator works per-occurrence.
2. **Preserved normative semantic** — a change that alters the RFC-2119 level (`MUST` / `SHOULD` / `MAY` discrimination) is a content change, not editorial. See §2.5.
3. **Reverse-calque guard** — a translator working from RU MUST NOT produce Russian-flavored English (literal calques of RU syntax, transliterated Russian terms, or word-order artifacts). Native idiomatic English is required. Conversely, the translator MUST NOT *re-translate* canonical identifiers that are latin in both languages (§1.3) — `BR` stays `BR`, never an English expansion used as the identifier.
4. **Closed-list canonical terms** — §1.9 fixes the closed list of canonical RENAR terms (mirrors [`standard/04`](../../standard/en/04-terms.md)). Coining new project-local terms is forbidden.
5. **Single source of truth** — the canonical normative source is [`standard/04-terms.md`](../../standard/en/04-terms.md). [`reference/01-glossary.md`](../01-glossary.md) (and its EN counterpart) is an informative companion.
6. **Domain-context preservation** — substrate / VCS / SE technical terms (`commit`, `merge`, `hook`, `pipeline`, `runner`, `linter`) stay latin: they are industry conventions in English too.

### §1.2 Five buckets

Every term in the EN normative corpus is classified into one of five buckets:

| Bucket | Category | Policy | Section |
|---|---|---|---|
| A | Canonical identifiers (IDs, types, statuses, field names) | Keep latin verbatim — shared with RU | §1.3 |
| B | RENAR conceptual terms (RU Style Guide §1.15 reversal) | Render in native English | §1.4 |
| C | Technical loanwords / SE vocabulary | Keep native English form | §1.5 |
| D | Role names | Canonical English role names | §1.6 |
| E | External-standard terms | Keep the source standard's spelling | §1.7 |

> The RU Style Guide has six buckets because its central concern is *which Russian-vs-latin form to use*. The EN corpus is natively English, so its buckets instead govern *what must stay latin* (Bucket A) and *what must read as native English* (Buckets B–E). The reverse-calque guard (§1.1.3) replaces the RU corpus's anglicism guard.

### §1.3 Bucket A — canonical identifiers (keep latin verbatim)

**Rule:** these stay latin, identical to the RU corpus, in prose, code-fence content, frontmatter, and section labels. They are machine-readable identifiers; translating them breaks the referential chain across both language editions.

| Class | Members |
|---|---|
| Artifact / requirement types | `BR`, `SR`, `SPEC-*` (`SPEC-UI`, `SPEC-AI`, `SPEC-INT`, `SPEC-ARCH`, `SPEC-OPS`, `SPEC-DATA`, `SPEC-API`, `SPEC-PROC`, `SPEC-SEC`), `TC` |
| ADAPT family | `ADAPT`, `delta-TZ`, `trigger-stage` |
| TZ | `TZ` and the ID format `TZ-YYYY-NNN` (see §1.10) |
| Quality Gates | `QG-0`, `QG-1`, `QG-2`, `QG-3`, `QG-4` |
| Maturity / capabilities | `RENAR-1`..`RENAR-5`, `V1`–`V6`, `MVR-*` |
| Lifecycle statuses | `draft`, `proposed`, `approved`, `verified`, `accepted`, `done`, `obsolete`, `deprecated`, `frozen`, `active`, `planning`, `blocked`, `review`, `ready`, `passing`, `failing`, `superseded`, `answered`, `resolved`, `revised` (§1.3.1) |
| Field names / YAML keys | `parent:`, `verifies[]`, `source.tz-section`, `source.adapt`, `adversarial-review-ref`, `ai-provenance`, `audit-trail`, `declared-stricter`, `assessment-mode`, `supersedes`, `superseded-by`, `role:`, `status:`, `lang:` |
| Proper nouns | `RENAR`, `SENAR`, `ISO`, `IEC`, `IEEE`, `RFC`, `GDPR`, `SAFe`, `BABOK`, `GitHub`, `Astro`, `Markdown` |
| Substrate / VCS / SE | `commit`, `merge`, `diff`, `branch`, `push`, `pull`, `hook`, `patch`, `frontmatter`, `slug`, `hash`, `runner`, `pipeline`, `release`, `deploy`, `build`, `workflow`, `linter`, `parser`, `compiler`, `tracker` |

#### §1.3.1 Status case consistency

Lifecycle statuses are always lowercase, even at sentence start where possible (reword to avoid leading a sentence with a status). `Approved` / `APPROVED` as a status token is a paste-error; the value is `approved`.

### §1.4 Bucket B — RENAR conceptual terms (native English)

These terms are rendered in **Russian prose** in the RU corpus (RU Style Guide §1.15). In the EN corpus they revert to their **native English** form. The latin identifiers in the right column stay latin in both languages.

| EN canonical (prose) | RU prose (reference only) | Stays latin in |
|---|---|---|
| manifest; conformance manifest | манифест; манифест соответствия | file name `RENAR-CONFORMANCE.yaml`, field names |
| conformance; conformant; non-conformant | соответствие; соответствующий; несоответствующий | `RENAR-CONFORMANCE.yaml`, `senar-version`/`renar-version`, levels `RENAR-1..5` |
| provenance | происхождение | field `ai-provenance`, `*-provenance` |
| adversarial review; adversarial reviewer; adversarial critic | состязательный обзор; состязательный рецензент; состязательный критик | field `adversarial-review-ref` |
| lifecycle; lifecycle state / status | жизненный цикл; состояние/статус жизненного цикла | drift class `Lifecycle drift`, status values, file names |
| mandatory clauses / mandatory clause; mandatory (adj.) | обязательные положения / обязательное положение; обязательный | schema annotation `mandatory` in YAML |
| enforcement; enforcement point | обеспечение соблюдения; точка контроля | field / identifier names |
| Source-of-Truth inversion (SoT inversion); Source of Truth (SoT) | инверсия источника истины; источник истины | — |
| canonical (adj.) | канонический | bilingual headers `English (canonical)` |
| closed list | закрытый список | — |
| state machine | машина состояний | — |
| data model; multilingual; spot-check; normative; trace chain; full-completeness | модель данных; многоязычный; выборочная проверка; нормативный; цепочка прослеживаемости; полнота | drift classes (`Order / provenance drift`), external terms |

**Rule:** prefer the established English term over a literal calque of the Russian prose. `Source of Truth` (not "source of verity"), `provenance` (not "origin-tracking"), `state machine` (not "states automaton").

### §1.5 Bucket C — technical loanwords / SE vocabulary (native English)

Standard requirements-engineering and software-engineering vocabulary is used in its ordinary English form — no special treatment, no quoting:

`scope`, `audit`, `policy`, `ownership`, `default`, `bypass`, `rollback`, `override`, `stub`, `walkthrough`, `quickstart`, `baseline`, `traceability`, `immutable`, `approval`, `findings`, `eval` / `evaluation`, `version pin`, `branching`.

**Rule:** these read as plain English. The RU corpus debates whether to keep them latin or rewrite them; in EN there is no debate. Hyphenated identifier forms (`audit-trail`, `baseline-dataset`) stay latin per Bucket A.

### §1.6 Bucket D — role names (canonical English)

SENAR §4 role names are canonical in English; the RU corpus translates them (RU Style Guide §1.15). The EN canonical forms:

| EN canonical | RU prose (reference only) | Notes |
|---|---|---|
| Architect | Архитектор | — |
| Engineer | Инженер | — |
| Reviewer | Рецензент | the role; lowercase `review` is the lifecycle status |
| Supervisor | Супервизор | — |
| Stakeholder | Заинтересованная сторона | `Stakeholder Requirement` is the BABOK term |
| Product Owner | Владелец продукта | Scrum/SAFe term |

The `role:` field value (`authorized-role-holder`, etc.) stays latin in both languages.

### §1.7 Bucket E — external-standard terms

Terms owned by an external standard keep that standard's spelling and casing: `Stakeholder Requirement` (BABOK), `Statement of Need` (ISO 29148), `Definition of Done` (Scrum), `Product Backlog` (Scrum/SAFe). Do not paraphrase them into RENAR vocabulary.

### §1.8 Reverse-calque failure modes

A translator working from RU is prone to:

- **Literal syntax calque** — preserving RU word order or predicate chains (`X is being represented by Y` for `X — это Y`). Use idiomatic English: `X is Y`.
- **Transliteration leakage** — leaving a transliterated Russian term (`adapt-irovanie`) instead of the English (`adaptation`).
- **Over-translation of identifiers** — expanding `BR` to "Business Requirement" *as the identifier*. The identifier is `BR`; "Business Requirement" is its gloss, used once at definition.
- **Modal flattening** — collapsing RU lowercase modals to indicative English and losing the RFC-2119 level (see §2). The EN form MUST be an UPPERCASE keyword.

**Mitigation:** the §1.4 reversal table is the hard-coded substitution set; semantic preservation (§2.5) is an explicit check; a human spot-check on a random 10% of edits is required.

### §1.9 Closed-list canonical RENAR terms

The canonical list lives in [`standard/04-terms.md §4.3–§4.11`](../../standard/en/04-terms.md#4.3) (artifacts, the SPEC family, TC types, Quality Gates, lifecycle statuses — §4.3–§4.7; V1–V6 capabilities — §4.8.2; drift classes — §4.11). The Style Guide reflects this canonical list; it does not duplicate it.

### §1.10 TZ — the source requirements artifact

**Decision #80.** Russian `ТЗ` is rendered in EN as **`TZ`** (latin), not translated to "ToR" / "SoW" / "Technical Assignment". Rationale: `TZ` is already a latin canonical identifier in both language editions — the artifact ID format is `TZ-YYYY-NNN` ([`standard/04 §4`](../../standard/en/04-terms.md)), the field is `source.tz-section`, the directory is `tz/`, and the pointer format is `[TZ-XXX §Y]`. Translating the prose mention while keeping the latin identifier would split a single concept across two spellings.

**Gloss (used once, at first mention in the EN glossary):** *TZ — the immutable contractual requirements artifact (ID `TZ-YYYY-NNN`); the client's source-of-record requirements document, adapted into RENAR artifacts via ADAPT.*

### §1.11 Adding new terms

1. **Identify need** — a term appears in an EN normative chapter ≥3 times without a §1.9 listing.
2. **Bucket classification** — assign A–E per §1.2.
3. **Rationale draft** — 3–5 sentences: why needed, why this bucket, what alternatives were rejected.
4. **Review** — Style Guide editor (or project owner), same procedure as for a [`standard/04`](../../standard/en/04-terms.md) formal change.
5. **Retroactive** — after approval, existing occurrences are normalized.

---

## §2 RFC-2119 EN normative wording

### §2.1 Principles

1. **Semantic-fidelity first** — RFC-2119 ([RFC 2119](https://www.rfc-editor.org/rfc/rfc2119)) fixes strict discrimination between `MUST` / `SHOULD` / `MAY`. EN wording MUST preserve this discrimination.
2. **No semantic downgrade** — an editorial pass MUST NOT change a modal-verb level. `MUST` → `SHOULD` is a **content change** that requires a separate task.
3. **EN UPPERCASE canonical** — RFC-2119 keywords in EN normative clauses are written in **UPPERCASE** (`MUST`, `MUST NOT`, `SHOULD`, `SHOULD NOT`, `MAY`, `REQUIRED`, `RECOMMENDED`, `NOT RECOMMENDED`, `OPTIONAL`, `SHALL`, `SHALL NOT`). This is the canonical normative weight per RFC 2119 §1 and RFC 8174.
4. **Polarity inversion vs RU** — the RU corpus uses **lowercase** modals as canonical (RU Style Guide §2.2, Option C, under the RFC 8174 carve-out). That carve-out is RU-only and **does not** extend to EN. The EN corpus uses UPPERCASE; lowercase English modals (`must`, `should`) in prose carry **no** normative weight and are reserved for descriptive sentences.
5. **Imperative mood for normative clauses** — normative clauses use the active/imperative form (`An SR MUST trace to its parent BR`). Indicative descriptive prose uses lowercase verbs.
6. **RU lowercase only in citation** — RU modal verbs (`должен`, `следует`, `может`, …) appear in the EN corpus **only** inside an explicit citation or a bilingual mapping (e.g. quoting the RU canonical wording). They are never used as the EN corpus's own normative wording.

### §2.2 Regime decision (EN UPPERCASE canonical)

The RENAR Standard EN corpus uses **UPPERCASE RFC-2119 keywords** as canonical normative wording. This is the international default for normative specifications (IETF, W3C, ISO-adjacent practice) and removes ambiguity per RFC 8174.

### §2.3 Canonical mapping table

11 RFC-2119 / RFC-8174 keywords ↔ EN canonical (UPPERCASE) ↔ RU canonical (lowercase, citation only). **Bidirectional usage:** EN translation — RU lowercase → EN UPPERCASE; RU pass — EN UPPERCASE → RU lowercase.

#### Mandatory levels

| RFC-2119 (canonical EN) | EN synonyms | RU equivalent (citation only) | Semantics |
|---|---|---|---|
| `MUST` | `REQUIRED`, `SHALL` | должен / обязан | Absolute requirement |
| `MUST NOT` | `SHALL NOT` | не должен / запрещено | Absolute prohibition |
| `REQUIRED` | `MUST` | обязателен / требуется | Equivalent to MUST (adjective form) |

#### Strong-recommendation level

| RFC-2119 (canonical EN) | EN synonyms | RU equivalent (citation only) | Semantics |
|---|---|---|---|
| `SHOULD` | `RECOMMENDED` | следует / рекомендуется | Strong recommendation; exceptions require explicit rationale |
| `SHOULD NOT` | `NOT RECOMMENDED` | не следует / не рекомендуется | Strong negative recommendation |

#### Optional level

| RFC-2119 (canonical EN) | EN synonyms | RU equivalent (citation only) | Semantics |
|---|---|---|---|
| `MAY` | `OPTIONAL` | может / допускается | Truly optional; no preference |

#### Negation discrimination

| Form | EN canonical | Discrimination |
|---|---|---|
| MUST NOT / SHALL NOT | `MUST NOT` | Absolute prohibition |
| SHOULD NOT / NOT RECOMMENDED | `SHOULD NOT` | Strong negative recommendation |
| (no canonical "MAY NOT") | "need not" / "is OPTIONAL" | RFC 2119 has no MAY NOT; "need not" = "MAY omit" |

**Hard rule:** `MUST NOT` ≠ `SHOULD NOT`. The editor never substitutes one for the other "for readability".

### §2.4 Tense / mood normative rules

#### §2.4.1 Normative clauses → imperative / active

```markdown
# Canonical
An SR MUST trace to its parent BR through the `parent:` link.

# Anti-pattern (indicative — normative weight lost)
An SR traces to its parent BR through the `parent:` link.
```

#### §2.4.2 Definitions → predicative form

Canonical form: `X is Y, governed by Z` or, in a glossary, `X — Y`.

```markdown
ADAPT is the two-way adaptation of a TZ, governed by the client-agreement process.
```

**Anti-pattern:** `X represents/constitutes Y` chains (calque overhead); `X is being defined as Y`.

#### §2.4.3 Future tense — scope-limited

Future tense (`will`) is **not** used in normative clauses. It is permitted only in roadmap sections, forward-looking limitations, and conditional consequences.

```markdown
# Wrong (ambiguous — requirement or forecast?)
An SR will contain a parent link.

# Correct
An SR MUST contain a parent link.
```

#### §2.4.4 Conditional clauses

Canonical pattern: `If X, then Y MUST Z`. Variant: `When X, Y MUST Z`.

**Anti-pattern:** conditional + indicative (`If X, Y does Z`) — normative weight lost.

### §2.5 Strict semantic-preservation rule

**Hard rule:** an editorial pass MUST NOT change an RFC-2119 modal-verb level.

**Permitted (semantic-preserving):**

| From | To | Reason |
|---|---|---|
| `MUST` ↔ `REQUIRED` | — | Same level (verb vs adjective form) |
| `MUST` ↔ `SHALL` | — | Equivalent per RFC 2119 §1 |
| `SHOULD` ↔ `RECOMMENDED` | — | Same level |
| `MAY` ↔ `OPTIONAL` | — | Same level |
| `MUST NOT` ↔ `SHALL NOT` | — | Same level |

**Forbidden (content change, not editorial):**

| From | To | Reason |
|---|---|---|
| `MUST` | `SHOULD` | MUST → SHOULD downgrade |
| `SHOULD` | `MAY` | SHOULD → MAY downgrade |
| `MUST NOT` | `SHOULD NOT` | MUST NOT → SHOULD NOT downgrade |
| Any modal | indicative (`X does Y`) | Loss of normative weight |

**Verification protocol:** pre-pass, count modal keywords per level in the chapter; post-pass, re-scan; the per-level sum MUST NOT change. If it changes, flag as semantic drift, revert, and raise a content-review task. The RU↔EN modal counts are reported by `scripts/check-rfc-modals.js` (dual RU-lowercase / EN-UPPERCASE inventory).

### §2.5bis Boundary with the artifact description language

This section governs the text of **the standard itself**. The text of **project artifacts** is governed by [standard/15](../../standard/en/15-description-language.md): a closed list of five operators without synonyms, in upper case per RFC 8174.

### §2.6 Descriptive carve-out

When a passage *describes* RFC-2119 vocabulary rather than *using* it normatively (e.g. "RFC 2119 defines `MUST` as an absolute requirement"), the keyword is a descriptive mention. Such mentions are permitted in any section, provided the keyword is wrapped in a code-fence, backticks, or a blockquote / citation. A descriptive mention does not carry normative weight.

---

## §3 Formatting conventions

These conventions are largely shared with the RU Style Guide (§3); EN-specific deltas are typography (§3.7). Structural rules (headings, code-fences, tables) are language-agnostic.

### §3.1 Principles

1. **Substrate readability first** — preserve git-blame readability; do not break syntax highlighting; stay stable under native substrate processing (markdown lint, frontmatter validator).
2. **Scan-readable second** — numbered sections for cross-ref, consistent bullets, typographic punctuation.
3. **No tool dependency** — conventions are applicable in any text editor.
4. **Accessibility** — semantic heading hierarchy (no H1 → H3 skip); typed code-fences; alt-text (deferred).
5. **Structural parity with RU** — the EN file mirrors the RU original's section numbering and anchor structure so cross-references and parity checks line up.

### §3.2 Citations & cross-refs

Three classes:

| Class | Pattern | When |
|---|---|---|
| (A) Bare intra-file | `§3.5` | Reference within the same file |
| (B) Markdown link cross-file | `[§4.5](../../standard/en/04-terms.md#4.5)` | Reference to another file |
| (C) Anchor-only intra-file | `[§3.5](#35-tables--lists)` | Clickable intra-file reference |

**Hard rule:** a cross-file reference MUST be a markdown link (Class B). A bare `§X.Y` for a cross-file target is an anti-pattern (not clickable, no PDF outline integration).

**Cross-edition links.** An EN file SHOULD link to the EN counterpart of its target where that counterpart exists (`../../standard/en/04-terms.md`). Until the EN counterpart exists, link to the canonical RU source (`../../standard/04-terms.md`), which is the source of truth; the `en-crosslinks-changelog` pass re-points these once the EN corpus is complete.

**"See" (EN) is used; "См." (RU) is not used in EN files.**

**Anchor format:** lowercase, hyphens instead of spaces / dots. Verified post-edit by `scripts/check-md-links.js`.

### §3.3 Code-fences

**Hard rule:** every fence MUST have a language tag. No-lang fences are an anti-pattern.

**Language-tag whitelist** (closed list): `yaml`, `bash`, `cypher`, `markdown`, `json`, `python`, `sql`, `text`.

**Inline code (backticks):** identifiers (`` `SR-001` ``), file paths (`` `standard/04-terms.md` ``), type / field names (`` `parent: BR-001` ``), short CLI fragments.

**Anti-pattern:** backticks around whole sentences — reserve backticks for technical identifiers.

### §3.4 Headings

#### §3.4.1 Numbering convention

| Directory | Convention |
|---|---|
| `standard/en/` | Dotted-number (`## 3.5 Tables / lists`) — mirrors RU |
| `reference/en/` | Dotted-number |
| `core/en/` | Plain (pedagogical narrative) |
| `guide/en/` | Plain or numbered phase walkthroughs |

#### §3.4.2 Depth rule

- Max normative depth — **H3**.
- H4 — exception (substructure inside a large H3), ≤ 5 per chapter.
- H5 / H6 — **banned** (restructure the parent instead).
- Depth jumps (H1 → H3 without an intermediate H2) — **banned**.

#### §3.4.3 Casing & form

Headings are **sentence-case** (capitalize the first word and proper nouns only), not Title Case. A heading is a short label, not a sentence: `## Tables and lists`, not `## This Section Describes Tables`.

### §3.5 Tables / lists

**Hard rules:**

- Bullet style — `-` only. `*` / `+` — banned. Mixed bullets within a file — banned.
- Table alignment — `---` only. `:---` / `---:` / `:---:` — banned (renderer-dependent variance).
- Nested unordered — 2-space indent (CommonMark default).
- Nested ordered — 3-space indent.
- Ordered list — manual `1.`, `2.`, `3.` preferred (explicit count visible in source).

### §3.6 frontmatter YAML

Canonical field order:

```yaml
---
title: "Document title"
description: "Optional one-line summary"
order: 6
lang: en
version: "1.0"
status: draft  # | proposed | approved | verified | obsolete | frozen
---
```

**Quoting policy:** strings with spaces / special chars use double quotes; pure ASCII identifiers (`status: draft`, `lang: en`), ISO dates, numerics, and booleans are unquoted.

**`lang:` closed list** — `ru`, `en`. Every EN file MUST declare `lang: en`. This is the single signal RU-targeted gates use to scope themselves out (§4.2).

**Validation:** `scripts/validate-frontmatter.js`.

### §3.7 EN typography

#### §3.7.1 Quotes — straight double quotes

**Canonical:** straight ASCII double quotes `"..."` for outer quotes in EN prose. (The RU corpus uses guillemets `«»`; the EN corpus does not.) Curly quotes are not required; ASCII is the corpus default and is git-blame-stable. The RU-only ASCII-quote check does not apply to EN files.

#### §3.7.2 Em-dash — `—`

Em-dash `—` (U+2014) for parenthetical separators and definition predicates (`X — Y`). Hyphen `-` for compound words (`closed-list`, `source-of-truth`), file paths, and identifiers. En-dash `–` (U+2013) for number ranges (`§3.5–3.7`).

**Anti-pattern:** `-` or `--` in place of `—`.

#### §3.7.3 Numbers

| Form | Convention |
|---|---|
| Whole numbers ≤ 999 | No separator: `42`, `999` |
| Whole numbers ≥ 1000 | Comma separator (EN convention): `1,000`, `50,649` |
| Percentages | `33%` (no space) |
| Decimals | Period: `0.5` |
| Ordinals | `1st`, `2nd` (EN suffix) |

> **Delta from RU:** the RU corpus uses a space thousands-separator (`1 000`) and RU ordinal suffixes (`1-й`). The EN corpus uses the comma separator (`1,000`) and EN ordinals (`1st`).

#### §3.7.4 Math operators

`≥` / `≤` / `≠` / `±` / `→` / `↔` — Unicode preferred in prose. ASCII `>=` / `<=` / `!=` / `->` are anti-patterns in prose; acceptable inside code-fences.

### §3.8 Cross-file link conventions

- **Relative paths only** (from the linking file's directory). From `reference/en/06-en-style-guide.md`, the repository root is `../../`.
- **Anchor case:** lowercase, hyphens.
- **Dead-link prevention:** the site build and `scripts/check-md-links.js` flag broken links.

```markdown
# from reference/en/06-en-style-guide.md
[§4.5 standard/04](../../standard/en/04-terms.md#4.5)
[reference sibling (RU)](../01-glossary.md)
```

### §3.9 Inline emphasis (bold / italic)

**Bold (`**text**`)** — for term introduction (`**Closed list policy** — the formal mechanism …`), emphatic normative flags (`**Hard rule:** …`), and anti-pattern flags (`**Anti-pattern:** …`).

**Hard rule:** bold is **not** a replacement for UPPERCASE RFC-2119 keywords. In EN, the keyword itself is UPPERCASE; bold is additive emphasis, not normative weight.

**Italic (`*text*`)** — restricted: foreign-language phrases (`*ad hoc*`, `*de facto*`), titles of external works (`*Software Requirements* (Wiegers)`). Not for general emphasis (use bold).

---

## §4 Integration & enforcement

### §4.1 Canonical sources

| Source | Purpose |
|---|---|
| [`standard/04-terms.md`](../../standard/en/04-terms.md) | Canonical RENAR terminology (closed list from §1.9) |
| [`standard/01-scope.md §1.7`](../../standard/en/01-scope.md#1.7) | Master index of closed lists |
| [`reference/01-glossary.md`](../01-glossary.md) | Informative glossary (EN counterpart: `reference/en/01-glossary.md`) |
| [`reference/02-schemas.md`](../02-schemas.md) | Canonical frontmatter schemas |
| [`reference/06-ru-style-guide.md`](../06-ru-style-guide.md) | RU Style Guide — the mirror source of this guide |

### §4.2 Automated enforcement (scripts/)

The Style Guide sets the rules; these scripts enforce them. EN files are scoped via the `lang: en` frontmatter signal.

| Script | Behaviour on EN files | Style Guide § |
|---|---|---|
| `scripts/validate-frontmatter.js` | Validates `lang: en`, required fields, closed value lists | §3.6 |
| `scripts/check-rfc-modals.js` | EN UPPERCASE modal inventory (RU lowercase for RU files) | §2.1, §2.5 |
| `scripts/check-substrate-term.js` | **Skipped** (EN uses native `substrate`) | §1.5 |
| `scripts/check-process-vocab.js` | **Skipped** (EN uses native English process vocab) | §1.5 |
| `scripts/style-guide-check.js` | RU-prose checks (bucket-a, bucket-e, en-uppercase, ascii-quote, anglicism-label) **skipped**; `fence-lang` active | §1, §3.3 |
| `scripts/check-md-links.js` | Active (language-agnostic) | §3.2, §3.8 |
| `scripts/check-section-refs.js` | Active (language-agnostic) | §3.2 |
| `scripts/check-en-parity.js` | EN↔RU structural parity (heading counts, counterpart presence) | §0.3, §3.1 |

**npm scripts:** `npm run check:all` runs the full gate sweep (RU + EN scoped).

### §4.3 Change procedure (cross-ref)

See [§0.5 Change procedure](#05-change-procedure).

---

## §5 Limitations & follow-ups

- **EN glossary** — `reference/en/01-glossary.md` (task `en-glossary`) is the term-level source of truth for translating agents; this guide governs form, the glossary governs term equivalents.
- **Cross-edition links** — until the EN corpus is complete, EN files link to canonical RU sources; the `en-crosslinks-changelog` pass re-points them (§3.2).
- **Imagery / SVG alt-text** — deferred until images enter the corpus.
- **Curly quotes** — ASCII straight quotes are the corpus default; a future amendment may adopt curly quotes if a renderer benefit is established.

## §6 Sources

- **RENAR Standard** — [`standard/`](../../standard/).
- **RU Style Guide** — [`reference/06-ru-style-guide.md`](../06-ru-style-guide.md) (mirror source).
- **SENAR (parent standard)** — methodological base.
- **RFC 2119** — [Key words for use in RFCs](https://www.rfc-editor.org/rfc/rfc2119) (1997).
- **RFC 8174** — [Ambiguity of Uppercase vs Lowercase](https://www.rfc-editor.org/rfc/rfc8174) (2017).
- **ISO/IEC/IEEE 29148:2018** — Requirements engineering.

---

*RENAR EN Style Guide v1.0 — EN translation epic foundation, 2026-06-06. Mirror of the RU Style Guide ([`reference/06`](../06-ru-style-guide.md)) with inverted RFC-2119 polarity (EN UPPERCASE canonical).*
