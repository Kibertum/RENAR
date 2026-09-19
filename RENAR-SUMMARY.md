# RENAR — Requirements Engineering & Normative Adaptive Regulation

AI-native standalone standard and methodology for requirements engineering. Complements SENAR; works independently.
**Version v1.1** | 19.09.2026 | Authors: Vadim Soglaev, Andrey Yumashev | [renar.tech](https://renar.tech)

## In One Sentence

RENAR is a standalone normative standard defining how to manage requirements (BR/SR/TR), specifications (12 SPEC types), test cases and adaptation artifacts (ADAPT) in projects where AI agents produce the implementation. Substrate-agnostic: the normative language is written in terms of capabilities V1–V6; the concrete backend (VCS, document store) is an implementation choice.

## The Shift

In AI-native development the implementation is produced by agents from inputs. The Source of Truth shifts from code to requirements: requirements define behavior, agents emit the implementation, tests verify against requirements — not the reverse. RENAR formalizes this Spec-Driven Development inversion as substrate-agnostic normative rules.

## 5 Values (inherited from SENAR)

1. **Context over Code** — the quality of an AI result is set by the quality of its input context
2. **Verification over Speed** — correctness is the constraint, not velocity
3. **Knowledge over Experience** — what is not documented does not exist for AI
4. **Enforcement over Agreement** — quality gates as automated control, not meetings
5. **Judgment over Keystrokes** — human attention on decisions, not on typing

## RENAR Core Structure

16 normative chapters (00–15): introduction, scope, methodology positioning, substrate versioning, terms, roles, requirements hierarchy, ADAPT, specifications, test cases, lifecycle and quality gates, maturity model, metrics, conformance, normative references, description language.

## Structure (Standard)

- **3-level hierarchy:** BR (Business — who, what, why) → SR (System — what the system does) → TR (Task — Goal + AC in the tracker, not a file)
- **ADAPT artifact:** bidirectional adaptation between the immutable TZ and BR/SR/SPEC; forward interpretation + backward findings (7 categories); client + architect double signature
- **12 SPEC types (closed list):** ARCH / API / DATA / INT / PROC / UI / AI / SEC / OPS / TEST / DOC / UC — a parallel axis to requirements via `constrained-by[]` graph edges
- **The description set (v1.1):** BR / SR / SPEC / TC norms are approved, versioned and verified together — as a set version, not per artifact; every statement has a pair of TC norms (positive + negative) already at version approval
- **Test cases (TC):** a first-class artifact; pos/neg pairing; VLM judge for UX; spec-specific TC types; the `[test-spec-change]` tag protects against test tampering
- **Quality gates (closed list):** QG-0 (Approval) → QG-1 (Implementation) → QG-2 (Verification); QG-3 (Architecture) and QG-4 (Acceptance) — optional, declared
- **Substrate capabilities V1–V6:** immutable history, atomic change unit, diff & review, branching, cross-substrate version pin, author + timestamp
- **5 RENAR maturity levels:** RENAR-1 (Initial) → RENAR-5 (Optimizing) — one axis of overall SENAR maturity

## What Sets RENAR Apart

The individual ideas have precedents (the inversion of the source of truth is the Spec-Driven Development paradigm of 2024–2026; requirements traceability is ISO 29148 / BABOK canon; versioning capabilities taken one by one are ISO 12207 configuration management). What sets RENAR apart is not these ideas as such but their **normative assembly and enforcement** ([§2.3.4](standard/en/02-methodology-positioning.md#234-positioning-in-the-industry-typology) states this openly):

- **Enforcement of the source-of-truth inversion** — four checkable normative consequences ([§2.3.3](standard/en/02-methodology-positioning.md#233-contract-mandatory-consequences)): a ban on recovering behavior from code into an SR, code review and specification review separated into two different gates, a drift hook of the substrate, a ban on silently adapting an SR to the code. Turns the SDD paradigm from a principle into blocking requirements — no SDD tool with such formalized enforcement was found.
- **V1–V6 as a closed capability contract of the substrate** — a substrate lacking any of the six capabilities normatively **does not implement** the standard; mapping onto git / SVN / Perforce / document store + a declaration in the manifest. An example on a non-git substrate — [guide/04](guide/en/04-document-store-substrate.md).
- **ADAPT (reactive)** — a bidirectional artifact between the client's immutable TZ and BR/SR/SPEC, materialized **only** when backward findings exist; the "no findings" verdict is a recorded assertion of the adversarial reviewer, not the architect's silence.
- **Loss of conformance as a discipline** — not only reaching a level is normed but also **losing** it (downgrade), with a mandatory audit log and a ban on hiding the downgrade.
- **An AI-native loop inside an RE standard** — the AI agent as a regular performer, `ai-provenance` in the trace chain, an AI risk register and adversarial multi-model agreement as normative maturity criteria.
- **12 SPEC types + the test case as an artifact in its own right** — closed lists with their own lifecycle and provenance; a type is added only through an amendment of the standard.

## Document Set

| Document | Purpose |
|----------|---------|
| RENAR Standard | Normative specification (MUST / SHOULD / MAY), 16 chapters (00–15) |
| RENAR Guide | Practical guide: quickstart, walkthrough, transition, substrate guides, SAFe, compliance, failure modes, migration to v1.1 |
| RENAR Reference | Glossary, schemas, conformance kit, agent implementation profile |
| RENAR Core | Gentle single-document introduction |
| Standard for the agent | Self-sufficient operational edition in one file for an AI agent — [RENAR-AGENT-EN.md](RENAR-AGENT-EN.md) (EN) · [RENAR-AGENT-RU.md](RENAR-AGENT-RU.md) (RU) |

## Read and Download

- **Markdown (source):** `standard/`, `guide/`, `reference/`, `core/` — RU primary, EN under `<section>/en/`
- **Site:** [renar.tech/docs/](https://renar.tech/docs/) (MkDocs Material + Astro landing)
- **PDF:** [Standard](https://renar.tech/renar-v1.1-en-standard.pdf) · [Guide](https://renar.tech/renar-v1.1-en-guide.pdf) · [Reference](https://renar.tech/renar-v1.1-en-reference.pdf) · [full archive](https://renar.tech/renar-v1.1-en.pdf) (EN); [Стандарт](https://renar.tech/renar-v1.1-ru-standard.pdf) · [полный архив](https://renar.tech/renar-v1.1-ru.pdf) (RU)
- **Standard for the agent (md):** [RENAR-AGENT-EN.md](RENAR-AGENT-EN.md) — a self-sufficient operational edition of the whole standard in one file, to be loaded into an AI agent
