---
title: "Description language"
order: 15
lang: en
---
# 15. Description language

> **Part of the RENAR Standard v1.1** · [← Table of contents](README.md)

> The normative sections of description artifacts — BR, SR, SPEC, TC norms, ADAPT, ACTZ — are written in a controlled dialect of natural language: a closed list of modal operators, glossary discipline, prohibited constructions, atomicity and forms by artifact type. The chapter governs the text of **project artifacts**; the text of the standard itself is governed by the editorial policy of [reference/06](../../reference/en/06-en-style-guide.md). Basis — ADR-026; the sources of the techniques are ASD-STE100, EARS, RFC 2119 / 8174, the INCOSE Guide for Writing Requirements, Attempto Controlled English, RuleSpeak, Gherkin ([reference/11](../../reference/en/11-external-standards-mapping.md)).

## 15.1 Why

The standard governs which artifacts are created and how they are linked ([chapter 6](06-requirements-hierarchy.md), [chapter 8](08-specifications.md), [chapter 9](09-test-cases.md)); the statement inside an artifact was until now governed by one form ([§6.6.3.1](06-requirements-hierarchy.md#6.6.3.1)). Free prose produces four classes of defects that are discovered late — at QG-2 or at the client:

- **breadth of reading** — a phrase admits several readings, the agent picks one with no signal that a choice was made;
- **unverifiability** — "the system MUST be convenient" does not translate into a Pass criterion;
- **terminological drift** — one meaning is named by different words along the chain BR → SR → SPEC → TC;
- **accumulation of ambiguity** along the same chain through `constrained-by[]` and `source.*`.

The description language is neither a formal language (no parser, readable by a person) nor free prose: a set of disciplines checked by reading — by a person in the adversarial review and by the agent at generation.

### 15.1.1 Normative section

The rules of the chapter act in the **normative sections** of artifact bodies: "Need", "Success criteria" and "Constraints" of a BR ([§6.5.3](06-requirements-hierarchy.md#6.5.3)); "Requirement", "Behaviour", "Constraints" of an SR ([§6.6.3](06-requirements-hierarchy.md#6.6.3)); the type-specific sections of a SPEC ([§8.5](08-specifications.md#8.5)); the Pass and Fail criteria, preconditions and steps of a TC ([§9.4](09-test-cases.md#9.4)); the clauses of an ACTZ ([§7.13](07-adapt.md#7.13)); the forward interpretation of an ADAPT ([§7.4.3](07-adapt.md#7.4.3)). Free sections — "Context", rationales, version history — are subject only to [§15.2](#152-modal-operators-closed-list) (operators) and [§15.3](#153-glossary-discipline) (terms); correspondence inside an ACTZ (the client's text) is not subject to the chapter's rules.

**The address of a statement.** A statement is a sentence of a normative section. The statements of one artifact are numbered through its normative sections in order of appearance; the address of a statement is `<id>#<n>` (for example, `SR-10#5`). The numbering is derived from the text and is not stored in it; inserting a sentence shifts the numbers — that is an edit of statements, visible in the version record ([§10.5.4](10-lifecycle-qg.md#10.5.4)). The address is used by `verifies[].statement` of a TC norm ([§9.3](09-test-cases.md#9.3)), by the `ref` of a scenario step ([§8.5.12.1](08-specifications.md#8.5.12.1)) and by the version coverage check ([§10.11.1](10-lifecycle-qg.md#10.11.1)).

## 15.2 Modal operators (closed list)

| Operator | RFC 2119 | Semantics |
|---|---|---|
| **MUST** | MUST / SHALL | An absolute requirement; non-fulfilment is a defect |
| **MUST NOT** | MUST NOT / SHALL NOT | An absolute prohibition |
| **SHOULD** | SHOULD | A default requirement; deviation is permitted with an explicit rationale in the same artifact |
| **SHOULD NOT** | SHOULD NOT | A default prohibition; an exception is justified in the same place |
| **MAY** | MAY | Truly optional behaviour; no rationale required |

The list is closed ([§1.7.5](01-scope.md#1.7.5), row 17). Three rules:

1. **Uniqueness of form.** In a normative section, obligation is expressed only by an operator from the list; synonyms ("shall", "needs to", "is required to", "it is important that", "it is desirable", "can") are prohibited. The operators are written in upper case per RFC 8174 — the convention the tooling understands ([reference/06](../../reference/en/06-en-style-guide.md)); the Russian edition of an artifact writes its operators in ordinary orthography, and unambiguity there comes from the closedness of the list and the position, as with the markers of the forms of [§6.6.3.1](06-requirements-hierarchy.md#6.6.3.1).
2. **One operator per statement.** In one normative sentence — exactly one operator. "MUST, but MAY" is two statements that require separation ([§15.5](#155-atomicity-of-a-statement)).
3. **"MAY", not "can".** The word "can" is ambiguous between capability and permission ("the system can process 1000 requests" — a capability or a right?) and is not used in normative sections.

The Russian edition of an artifact uses **должен / не должен / следует / не следует / допускается**; the correspondence between the editions is by the table above.

## 15.3 Glossary discipline

1. **One term — one definition — one form.** Every term that matters for reading a statement (a role, a state, a unit of measure, an entity of the system) is defined once — in the term-mapping section of the ADAPT ([§7.4](07-adapt.md#7.4)) or at its first use in the set — and is used only in that form thereafter.
2. **Synonyms of one concept are prohibited** along the chain BR → SR → SPEC → TC. Two artifacts naming one entity differently ("check" and "validation") are not a stylistic variant but a backward finding of the `terminology` category ([§7.4.4](07-adapt.md#7.4.4)); in artifacts originating from the TZ it passes the contractual contour by the general rules.
3. **Units of measure** are mandatory with every numeric value and identical throughout the artifact (always "ms", not "ms" in one place and "seconds" in another).
4. **An abbreviation** is expanded at its first use in the artifact and used only in the short form thereafter; the abbreviations of the standard's closed lists (`BR`, `SR`, `SPEC`, `TC`, `ADAPT`, `ACTZ`, `QG`) require no expansion.

The terms of the standard itself are subject to [§4.2](04-terms.md#4.2); their drift in an artifact is class 4.11.5 ([§4.11](04-terms.md#4.11)).

## 15.4 Prohibited constructions

Every occurrence is reformulated before the set version is approved. The list is closed within the chapter; additions — through a change of the standard.

| # | Prohibited | Why | Bad | Good |
|---|---|---|---|---|
| 1 | An evaluative word without a criterion: "convenient", "fast", "sufficient", "modern", "intuitive" | Unverifiable | The system MUST work fast | The system MUST return a response within 300 ms |
| 2 | An escape clause: "where possible", "as needed", "within reason", "where applicable" | A documented excuse for non-fulfilment | Errors are logged where possible | The system MUST write every error to the `errors.log` journal |
| 3 | The "/" sign as an "and / or" connector | Unclear whether it is a conjunction or an alternative | The field is mandatory/optional | The user MUST fill the field. IF the value is absent, THEN the system MUST substitute `null` |
| 4 | An unattainable absolute without a tolerance: "always", "never", "100 %", "completely" | Literally unverifiable | The system never loses data | The system MUST keep data with a reliability of no less than 99.99 % over a 30-day interval |
| 5 | "All / any / both" instead of an element-wise quantifier | Unclear whether the statement is collective or per element | All fields MUST pass validation | Each form field MUST pass validation by its own rule |
| 6 | A pronoun without an unambiguous antecedent in the same sentence: "this", "it", "they" | The statement loses self-sufficiency — the agent reads in parts | The user submits the form. It MUST be validated | The system MUST validate the form before submission |
| 7 | A negative requirement without a positive measurable criterion: "MUST NOT hang" | Sets no verifiable behaviour | The interface MUST NOT hang | The system MUST display a waiting indicator IF no response is received within 200 ms |
| 8 | A connector "and / or / then / unless" joining different conditions or actions | Two requirements glued into one | The system checks permissions and logs the attempt if access is denied | (1) The system MUST check access permissions. (2) IF access is denied, THEN the system MUST write the attempt to the journal |
| 9 | Passive voice without the acting subject | Unclear who acts — responsibility is not assigned | The request MUST be approved | The manager MUST approve the request |
| 10 | A subordinate clause attached to a word other than the one it refers to | Ambiguous attachment | The user receives a notification about the request that was rejected | IF the request is rejected, THEN the user MUST receive a rejection notification |

The informative word list of [reference/04 §3.2](../../reference/en/04-ai-style-guide.md) illustrates rows 1 and 2; the norm is here.

## 15.5 Atomicity of a statement

One normative sentence — one verifiable thought.

**The criterion.** A statement is not atomic and MUST be split if more than one pair of TC norms — a positive and a negative one ([§13.3.5](13-conformance.md#13.3.5)) — is needed to verify it; for a negative invariant — more than one TC norm. Signs: more than one operator in the sentence; "and" joins two actions or two conditions rather than homogeneous objects of one action; the sentence answers two "what must happen" questions at once.

**Length.** A statement SHOULD fit into 25 words. Exceeding that is a sign of gluing, not a reason to shorten the text at the expense of meaning.

## 15.6 Forms by artifact type

The forms are a recommendation in v1.1 (SHOULD), with a review of the modality by practice (ADR-022, ADR-026); the rules of [§15.2](#152-modal-operators-closed-list)–[§15.5](#155-atomicity-of-a-statement) act regardless of the form.

### 15.6.1 SR and SPEC

Statements about the behaviour of the system in an SR and in the type-specific sections of a SPEC take one of the six forms of [§6.6.3.1](06-requirements-hierarchy.md#6.6.3.1): ubiquitous, state-driven (WHILE …), event-driven (WHEN …), unwanted behaviour (IF …, THEN …), optional feature (WHERE …), complex (WHILE …, WHEN …). The word "system" is replaced by the exact name of the component when the statement is addressed to it rather than to the system as a whole ([§15.4](#154-prohibited-constructions), row 9).

### 15.6.2 BR

"Need" takes the form "who, what, why" ([§6.5.3](06-requirements-hierarchy.md#6.5.3)). The "Constraints" section of a BR holds business rules in four forms:

| Form | Template | Example |
|---|---|---|
| Obligation | ‹Subject› MUST ‹action› | The client MUST confirm payment before work begins |
| Prohibition | ‹Subject› MUST NOT ‹action› | The contractor MUST NOT engage a subcontractor without written approval |
| Restricted permission | ‹Subject› MAY ‹action› only if ‹condition› | The client MAY request a refund only if the request is filed within 14 days |
| Conditional obligation | IF ‹condition›, THEN ‹subject› MUST ‹action› | IF the amount exceeds 500 000 ₽, THEN the manager MUST escalate the request |

"Only if" is mandatory in a restricted permission: without it, it is unclear whether the condition is mandatory or one of the admissible ones. "Success criteria" — 3–7 measurable atomic statements.

### 15.6.3 TC

The body of a TC is governed by the seven sections of [§9.4](09-test-cases.md#9.4); the triple "Given — When — Then" reads as three of them: **Given** → Preconditions, **When** → Steps, **Then** → Pass criterion. The Fail criterion is a list of observable signs of violation, not a negation of Pass ([§9.11](09-test-cases.md#9.11)). For `system` and `contract` the steps SHOULD contain one action or one event; several actions — several TCs. Journey TCs (`ux`, SPEC-UC) are multi-step by construction — the restriction does not extend to them.

### 15.6.4 ADAPT and ACTZ

The forward interpretation of an ADAPT is prose under [§15.2](#152-modal-operators-closed-list)–[§15.5](#155-atomicity-of-a-statement); a form on behalf of the analysis ("is interpreted as …") is permitted. A clause of an ACTZ is an obligation to the client: the form of an obligation ("the button is named …", "the deadline is 72 hours") without interpretive hedges; a question requiring the client's decision is put as a question, not as an assumption ([§7.13](07-adapt.md#7.13)).

## 15.7 Application and checking

- The rules of [§15.2](#152-modal-operators-closed-list)–[§15.5](#155-atomicity-of-a-statement) are mandatory from the `RENAR-2` level ([§11.5](11-maturity-model.md#11.5)); the forms of [§15.6](#156-forms-by-artifact-type) are a recommendation.
- The check is part of the adversarial review ([§7.10.2](07-adapt.md#7.10.2)) and a precondition of the approval of the set version: a violation is recorded as a remark on the wording and removed before approval ([§10.7.2](10-lifecycle-qg.md#10.7.2)). A language violation is by itself not a backward finding — that records a divergence of meaning; the exception is a synonym that produced a terminological conflict ([§15.3](#153-glossary-discipline), item 2).
- A static check of the rules (the operator list, the constructions of rows 2–5 and 8 of [§15.4](#154-prohibited-constructions), the length) is recommended as `automation.kind: static` ([§9.8.1](09-test-cases.md#9.8.1)); it is not a gate of the standard.
- A change of the operator list or of the forms is a change of the standard ([§13.9](13-conformance.md#13.9)); previously approved set versions are not reworded after the fact.

## 15.8 Language of writing

An artifact in a language other than English is written in that language in full, except: the identifiers and abbreviations of the standard's closed lists (`BR`, `SR`, `SPEC-*`, `TC`, `ADAPT`, `ACTZ`, `QG-N`, `V1`–`V6`); schema field names (`source.tz-section`, `constrained-by[]`). The markers of the forms of [§6.6.3.1](06-requirements-hierarchy.md#6.6.3.1) are written in the language of the artifact, in its ordinary orthography. The rule coincides with the policy of the corpus — [reference/06 §1](../../reference/en/06-en-style-guide.md).

## 15.9 Example: before and after

**Before:**

> The system must process requests fast. If something goes wrong, it must correctly inform the user about it and/or log the error. All requests are handled by a manager, who should reply within a reasonable time where possible.

**After:**

> WHEN the user submits a request, the system MUST return an acknowledgement within 2 seconds.
> IF the processing of a request ends in an error, THEN the system MUST display an error message to the user.
> IF the processing of a request ends in an error, THEN the system MUST write the error to the `errors.log` journal.
> The manager MUST reply to each request no later than 24 hours after it is received.

One "before" sentence became four statements: three forms of [§6.6.3.1](06-requirements-hierarchy.md#6.6.3.1) and one business rule of [§15.6.2](#1562-br); each has exactly one pair of TCs.

## 15.10 Links to other chapters

| Chapter | Link |
|---|---|
| [01 Scope](01-scope.md) | The closed list of operators — [§1.7.5](01-scope.md#1.7.5), row 17 |
| [04 Terms](04-terms.md) | The terms of the standard — §4.2; terminological drift — class 4.11.5 |
| [06 Requirements hierarchy](06-requirements-hierarchy.md) | The SR statement forms — [§6.6.3.1](06-requirements-hierarchy.md#6.6.3.1); the BR body — [§6.5.3](06-requirements-hierarchy.md#6.5.3) |
| [07 ADAPT](07-adapt.md) | Term mapping; the `terminology` category; the form of ACTZ clauses — [§7.13](07-adapt.md#7.13) |
| [09 Test cases](09-test-cases.md) | The TC body sections — [§9.4](09-test-cases.md#9.4); Pass / Fail — [§9.11](09-test-cases.md#9.11) |
| [10 Lifecycle and QG](10-lifecycle-qg.md) | The language check as a precondition of version approval, [§10.7.2](10-lifecycle-qg.md#10.7.2) |
| [11 Maturity model](11-maturity-model.md) | Mandatory from `RENAR-2` — [§11.5](11-maturity-model.md#11.5) |
| [13 Conformance](13-conformance.md) | A pair of TCs per statement — [§13.3.5](13-conformance.md#13.3.5); changing lists — [§13.9](13-conformance.md#13.9) |
| [reference/04](../../reference/en/04-ai-style-guide.md) | The informative lexicon and templates for the AI generator |
| [reference/06](../../reference/en/06-en-style-guide.md) | The editorial policy of the standard's text (not of artifacts) |
