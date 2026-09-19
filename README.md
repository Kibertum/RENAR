# RENAR — Requirements Engineering & Normative Adaptive Regulation

![Version](https://img.shields.io/badge/version-1.1-blue)
![License](https://img.shields.io/badge/license-CC%20BY--SA%204.0-green)

**AI-native standalone standard and methodology for requirements engineering. Complements SENAR; works independently.**

[renar.tech](https://renar.tech) · [Читать на русском](README.ru.md)

## What is RENAR

RENAR is a **standalone normative standard and methodology** for managing requirements (BR / SR / TR), specifications (12 SPEC types), test cases, and adaptation artifacts (ADAPT) in projects where AI agents produce the implementation. RENAR is **substrate-agnostic**: the same normative rules apply on distributed VCS, document-oriented stores, or any versioned backend — substrate capabilities V1–V6 define what each backend must support.

RENAR can be adopted **independently** in any organization that needs structured requirements engineering for AI-native development. It is **interoperable with [SENAR](https://senar.tech)** (Supervised Engineering & Normative AI Regulation): the two share quality-gate vocabulary and the five core values, so an organization using both gets a coherent end-to-end picture — but each standard works on its own.

## Start Here

The standard lives in `standard/` (16 chapters, 00–15). Start with `core/renar-core.md`, then `guide/00-quickstart.md`. Role-based paths: `standard/00-introduction.md` §0.6.4 and `guide/README.md`.

**[Standard (RU)](standard/README.md)** · **[Standard (EN)](standard/en/README.md)** — 16 normative chapters  
**[Guide (RU)](guide/README.md)** · **[Guide (EN)](guide/en/README.md)** — 13 practical guides  
**[Reference (RU)](reference/README.md)** · **[Reference (EN)](reference/en/README.md)** — appendices  
**[Core (RU)](core/README.md)** · **[Core (EN)](core/en/README.md)** — gentle single-doc intro

The standard is bilingual: the RU edition is primary; the EN edition mirrors it under `<section>/en/` (same chapter numbers and §-structure, enforced by `scripts/check-en-parity.js`).

## Summary

For a 60-line overview of RENAR see **[RENAR-SUMMARY.md](RENAR-SUMMARY.md)** (English) or **[RENAR-SUMMARY-RU.md](RENAR-SUMMARY-RU.md)** (Russian).

## PDF Downloads

- [RENAR v1.1 — Standard, Russian (PDF)](https://renar.tech/renar-v1.1-ru-standard.pdf) — normative core (core + 16 chapters); primary read
- [RENAR v1.1 — Practical Guide, Russian (PDF)](https://renar.tech/renar-v1.1-ru-guide.pdf)
- [RENAR v1.1 — Reference, Russian (PDF)](https://renar.tech/renar-v1.1-ru-reference.pdf)
- [RENAR v1.1 — Full Archive, Russian (PDF)](https://renar.tech/renar-v1.1-ru.pdf) — everything + author meta-docs
- [RENAR v1.1 — Standard, English (PDF)](https://renar.tech/renar-v1.1-en-standard.pdf) — normative core (core + 16 chapters)
- [RENAR v1.1 — Practical Guide, English (PDF)](https://renar.tech/renar-v1.1-en-guide.pdf)
- [RENAR v1.1 — Reference, English (PDF)](https://renar.tech/renar-v1.1-en-reference.pdf)
- [RENAR v1.1 — Full Archive, English (PDF)](https://renar.tech/renar-v1.1-en.pdf) — everything + author meta-docs
- [Standard for the agent (MD, EN)](RENAR-AGENT-EN.md) · [(MD, RU)](RENAR-AGENT-RU.md) — a self-sufficient operational edition of the whole standard in one file: download it, place it beside an AI agent, work at 100% RENAR

## Online Reading

- [renar.tech/docs/](https://renar.tech/docs/) — MkDocs Material (search, navigation); the site is built from the sibling repository `renar`, which pins this corpus as the `content/` submodule
- Markdown source — `standard/`, `guide/`, `reference/`, `core/` in this repository

## Documentation

| Document | Description | Link |
|----------|-------------|------|
| **Standard v1.1** | Normative specification: 16 chapters covering hierarchy, ADAPT, specifications, test cases, lifecycle, substrate versioning, maturity, metrics, conformance | [standard/](standard/README.md) |
| **Guide** | Practical guide: quickstart, walkthrough, transition, substrate guides (VCS / document-store), SAFe comparison, compliance, failure modes, developer guide, worked examples, v1 migration | [guide/](guide/README.md) |
| **Reference** | Glossary, schemas, AI risk register, style guides, ISO 29148 trace matrix, conformance self-assessment, pedagogical density | [reference/](reference/README.md) |
| **Core v1.1** | Gentle introduction — single document | [core/](core/README.md) |

## Key Concepts

- **BR / SR / TR hierarchy** — Business / System / Task-level requirements; TR lives in tracker (Goal + Acceptance Criteria), not as a separate file
- **ADAPT artifact** — intermediate bidirectional adaptation between immutable TZ (statement of work) and BR/SR/SPEC; forward interpretation + backward findings with dual signature (client + architect)
- **12 SPEC types (closed list)** — ARCH / API / DATA / INT / PROC / UI / AI / SEC / OPS / TEST / DOC / UC — parallel axis to requirements via `constrained-by[]` graph edges
- **Test cases (TC) as first-class artifacts** — pos/neg pair coverage, VLM-judge for UX, spec-specific TC types
- **Substrate-agnostic V1–V6** — normative language applies regardless of backend (git, document stores, Mercurial, SVN map to the same capabilities)
- **5 RENAR maturity levels** — RENAR-1 (Initial) → RENAR-5 (Optimizing); one axis of overall SENAR maturity

## Who Is This For

- Engineering teams adopting SENAR who need normative rules for the requirements domain
- Architects and Tech Leads designing requirements lifecycle on any versioned substrate
- AI-engineers building agents that produce or modify requirements artifacts
- PM, legal/compliance, and auditors needing traceability and conformance evidence

## Status

- **v1.0-draft (2026-05-22)** — RU public corpus; 15 normative chapters + 11 guides + 10 reference appendices
- **EN edition (2026-06-06)** — full English translation under `<section>/en/` (standard, guide, reference, core) + bilingual site (renar.tech) + EN PDFs; second language alongside the primary RU corpus
- **v1.0 (2026-07-17)** — corpus finalized after partner approval (EN translation done)
- **v1.1 (2026-09-19)** — partner-proposal wave: the description set as the unit of approval/version, SPEC-UC (12 SPEC types), MW records, screen list, reusable component, first-party confirmation, chapter 15 «Description language»; 16 chapters + 13 guides; migration — [guide/12](guide/12-migration-v11.md)

See [CHANGELOG.md](CHANGELOG.md) for detailed history.

## License

[CC BY-SA 4.0](LICENSE) — free use with attribution.

**Authors:** Vadim Soglaev, Andrey Yumashev
