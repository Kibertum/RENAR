---
title: "Recommended TZ form"
description: "Informative appendix: a recommended 11-section structure for a TZ, derived from the typology of backward findings in chapter 7."
order: 13
lang: en
version: "1.0"
---

# Recommended TZ form

> **Status:** informative. This is a **recommendation**, not a norm. RENAR conformance does not depend on the form of the incoming TZ in any clause: the standard accepts the TZ **as given** — including a poorly structured one — and normalizes not the writing of the TZ but the work with it ([`standard/01 §1.3`](../../standard/en/01-scope.md#1.3): RENAR does not normalize the TZ authoring process on the client's side). The only normative mechanism for working with a TZ of any quality is the ADAPT contour ([`standard/07`](../../standard/en/07-adapt.md)).
>
> This appendix creates no obligations and extends no closed list ([§1.7.5](../../standard/en/01-scope.md#1.7.5)).

---

## 13.1 Status and boundaries

The TZ is a document external to the standard: the client writes it or brings it, it lives in the contractual contour, and it follows the vendor's business practice. The whole machinery of the standard — forward adaptation, backward findings, ACTZ — is built precisely on the assumption that an incoming TZ may be anything at all. Were the standard to require a TZ form, it would stop accepting other parties' TZs and would lose its own input condition.

Hence three hard boundaries, upheld across the corpus:

1. **Conformance does not depend on the TZ form.** The conformance chapter ([`standard/13`](../../standard/en/13-conformance.md)) does not mention the form of the incoming TZ in any clause. An organization whose TZ has an arbitrary structure may claim full conformance.
2. **Substrate checks do not touch the TZ structure.** No automated check parses a TZ into sections or requires that any be present. Requiring a TZ structure through an automated check would violate the boundary of [§1.3](../../standard/en/01-scope.md#1.3).
3. **The TZ does not become a requirements registry.** Duplicating BR / SR into the TZ is an explicit anti-recommendation ([§13.5](#13.5)).

The only admissible argument in favour of the form is an observation, not an obligation: a TZ written to this form produces fewer backward findings, shortens the clarification rounds, and makes the derivation of acceptance tests reliable ([§13.6](#13.6)). The choice remains with the vendor and the client.

---

## 13.2 The basis of the form: an inversion of the finding typology

The form is recommended not out of one vendor's habit but on a basis internal to the standard. The seven categories of backward findings ([§7.4.4](../../standard/en/07-adapt.md#7.4.4)) are, in essence, a catalogue of typical TZ defects. Therefore the **recommended form is an inversion of the finding typology**: each section exists not for its own sake but as prophylaxis against a specific category of defect.

| Section of the form | Category of findings it forecloses |
|---|---|
| Out of scope | `scope` — an unclear boundary of work |
| User roles (explicit authorization) | `hidden-assumption` — an assumption that may be wrong |
| Integrations (constraints, risks) | `feasibility`, `regulatory` |
| Definitions | `terminology` — direct prophylaxis |
| Key data entities | `terminology` (entities), `hidden-assumption` |
| "Given — when — then" criteria on every functional requirement | `gap` — the TZ is silent about something without which implementation is impossible |
| Use-case scenarios with pre- and postconditions | `contradiction` — scenarios collide requirements against one another |
| Accompanying artifacts (a table) | addressability of annexes: each annex becomes a unit that can be referenced |

The table also works in reverse — as an instrument for finding holes in the form itself. It is precisely this table that exposed the fact that the `terminology` category, until the "Definitions" section appeared, was the only one without a prophylactic section.

---

## 13.3 Sections of the form

Eleven sections. The numbering is internal to the TZ; the stable identifiers of clauses (`UC-NNN`, `FR-NNN`, `NFR-NNN`) are assigned by the client and do not change afterwards: acceptance tests reference them ([§9.19.3](../../standard/en/09-test-cases.md#9.19)).

| # | Section | Content |
|---|---|---|
| 1 | General description | One paragraph: what this is, for whom, how it works |
| 2 | **Definitions** | The client's terms with fixed meanings ([§13.4](#13.4)) |
| 3 | User roles | A table of roles; authorization is stated explicitly — including an explicit "not provided" |
| 4 | Use-case scenarios | `UC-NNN`: participant, precondition, steps, postcondition |
| 5 | Functional requirements | `FR-NNN`: priority and "given — when — then" acceptance criteria, including at least one negative variant with invalid data |
| 6 | Non-functional requirements | `NFR-NNN`: only what is explicitly constrained or significant |
| 7 | Integrations | A table per external system: address, type, constraints, data in and out, risks |
| 8 | Key data entities | A vocabulary of the problem domain — not a database schema |
| 9 | Accompanying artifacts | A table of annexes: type, coverage, location, status |
| 10 | Out of scope | An explicit list of what is not included |
| 11 | Project acceptance criteria | Conditions of acceptance, the reference scenario |

Sections 4 and 5 are what the acceptance-test programme is derived from ([`reference/14`](14-acceptance-program.md)). Section 11 is its embryo: from the outset the client sees how the handover will proceed.

---

## 13.4 The "Definitions" section — second, and why exactly there

The section is placed **second**, immediately after the general description and before the first use of terms in scenarios and requirements. This follows GOST and ISO practice, where terms and definitions come at the start of a document.

The content is the client's terms that admit a second reading: roles and their synonyms, action verbs ("post", "approve", "export"), statuses, units of measure, the organization's abbreviations. The form is a table: "term — meaning — synonyms, or what the term is not".

| Term | Meaning | Synonyms / what it is not |
|---|---|---|
| Post a document | Commit a document to the ledger such that it affects balances | Not "save": a draft does not change balances |

Two recommendations for filling it in:

- include only terms with a possible second reading — the section is not a substitute for a dictionary of ordinary words;
- define a term with a self-contained formulation. Definition by usage ("as in section 4") is inadmissible: it does not remove the ambiguity, it hides it.

The section is a direct input for term mapping in ADAPT: a term fixed by the client produces no finding of the `terminology` category, and the whole terminological layer need not be reconstructed through rounds of clarification, when the client could have fixed their own terms up front.

---

## 13.5 What a TZ is not

The TZ remains a document in the language of the client and their problem domain. The form structures the client's language — it does not turn the TZ into a requirements registry.

Duplicating BR / SR into the TZ is **not recommended**. The requirements of the standard live in the internal contour and are derived from the TZ through interpretation ([§7.12](../../standard/en/07-adapt.md#7.12)); copied into the TZ, they diverge from the original at the first edit and produce two sources of truth instead of one. Decoupling the contours is not a formality but a condition for traceability to work.

The distance between the language of the TZ and the language of the standard is variable and normal ([§7.12.1](../../standard/en/07-adapt.md#7.12)). The form shortens that distance; it does not abolish it.

---

## 13.6 The observed effect

The effect is recorded as an observation — neither a promise nor an obligation:

- fewer backward findings — each section of the form forecloses its own category in advance ([§13.2](#13.2));
- a shorter path to requirements — fewer ACTZ clarification rounds ([§7.13](../../standard/en/07-adapt.md#7.13));
- a more reliable derivation of acceptance tests: stable `UC-NNN` and `FR-NNN` identifiers give the `verifies` field its addressing without interpretation, while the mandatory negative variant in the acceptance criteria yields positive/negative pairing of acceptance tests almost for free ([§9.19](../../standard/en/09-test-cases.md#9.19)).

A vendor with their own TZ loses only speed, not conformance.

---

## 13.7 Relation to other documents

| Document | Relation |
|---|---|
| [`standard/07 §7.4.4`](../../standard/en/07-adapt.md#7.4.4) | The seven categories of backward findings — the basis of the form |
| [`standard/07 §7.13`](../../standard/en/07-adapt.md#7.13) | ACTZ — the TZ clarification protocol |
| [`standard/07 §7.14`](../../standard/en/07-adapt.md#7.14) | The effective TZ — the benchmark for acceptance |
| [`standard/09 §9.19`](../../standard/en/09-test-cases.md#9.19) | AT — the acceptance test of the contractual contour |
| [`reference/14`](14-acceptance-program.md) | Recommended form of the acceptance-test programme |
| [`reference/12`](12-document-templates.md) | Templates for internal artifacts (BR / SR / TR / TC / AR / ACTZ / AT) |

[← Back to the reference](README.md)
