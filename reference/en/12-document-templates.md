---
title: "Document templates BR / SR / TR / TC / AR / ACTZ / AT"
description: "Copy-paste skeletons for RENAR artifacts (frontmatter + body) — a possible implementation of the normative schemas in chapters 6, 7 and 9."
order: 12
lang: en
version: "1.0"
---

# Document templates BR / SR / TR / TC / AR / ACTZ / AT

> **Status:** informative. This is a **possible implementation** of the normative schemas — organizations may adapt the skeletons to their substrate and editorial conventions. The normative text these templates merely illustrate: [`standard/06`](../../standard/en/06-requirements-hierarchy.md) (BR / SR / TR), [`standard/07 §7.4.6`](../../standard/en/07-adapt.md#7.4.6) (AR), [`standard/07 §7.13`](../../standard/en/07-adapt.md#7.13) (ACTZ) and [`standard/09`](../../standard/en/09-test-cases.md) (TC, AT). Where a skeleton and a chapter disagree, **the chapter prevails**.
>
> **The closed list is untouched:** this appendix provides skeletons for already-normative types; new artifact or SPEC types are added only through the formal standard-change procedure ([chapter 13](../../standard/en/13-conformance.md)). AR is an evidence record, not a requirement type, and belongs to no closed list ([§1.7.5](../../standard/en/01-scope.md#1.7.5)).

---

## 12.1 Status and how to use it

Each section below carries two skeletons: a `yaml` block with the frontmatter (with per-line comments on field obligation) and a `markdown` block with the body skeleton. To get a ready artifact file, concatenate both parts into a single substrate document: frontmatter on top, body next.

How the skeletons map to the normative schemas:

| Artifact | frontmatter (normative) | Body sections (normative) |
|---|---|---|
| BR | [§6.5.2](../../standard/en/06-requirements-hierarchy.md#6.5.2) | [§6.5.3](../../standard/en/06-requirements-hierarchy.md#6.5.3) |
| SR | [§6.6.2](../../standard/en/06-requirements-hierarchy.md#6.6.2) | [§6.6.3](../../standard/en/06-requirements-hierarchy.md#6.6.3) |
| TR | [§6.7.2](../../standard/en/06-requirements-hierarchy.md#6.7.2) | [§6.7.3](../../standard/en/06-requirements-hierarchy.md#6.7.3) |
| TC | [§9.3](../../standard/en/09-test-cases.md#9.3) | [§9.4](../../standard/en/09-test-cases.md#9.4) |
| AR | [§7.4.6.2](../../standard/en/07-adapt.md#7.4.6) | [§7.4.6.2](../../standard/en/07-adapt.md#7.4.6) |
| ACTZ | [§7.13.3](../../standard/en/07-adapt.md#7.13) | [§7.13.3](../../standard/en/07-adapt.md#7.13), [§7.13.5](../../standard/en/07-adapt.md#7.13) |
| AT | [§9.19.3](../../standard/en/09-test-cases.md#9.19) | [§9.19.3](../../standard/en/09-test-cases.md#9.19) |

Conventions used in the skeletons: `<...>` is a placeholder to replace; `NN` is a sequential number within the scope; the comment `# conditional` marks a field whose obligation depends on a condition (explained inline); `# auto` marks a field maintained by the substrate/runner, not filled in by hand.

---

## 12.2 BR template

A BR captures a business need at the system or subsystem level; technical detail is prohibited in a BR ([§6.5.1](../../standard/en/06-requirements-hierarchy.md#6.5.1)). Frontmatter per [§6.5.2](../../standard/en/06-requirements-hierarchy.md#6.5.2); body per [§6.5.3](../../standard/en/06-requirements-hierarchy.md#6.5.3).

```yaml
---
id: BR-NN                            # immutable; NN sequential within the scope
title: "<short, descriptive>"
type: BR
slug: "<kebab-case>"                 # auto-derived

# === Scope (mandatory) ===
level: system | subsystem            # a BR at module level is prohibited (§6.4)
scope:
  system: "<system-id>"
  subsystem: "<subsystem-id>"        # null if level=system

# === Lifecycle (mandatory) ===
# status and version are absent: the artifact is approved as part of a set version (standard/10 §10.7)
owner: "<role / responsible person>"

# === Source: provenance (see §7.4.1) ===
source:
  tz-section: "§N.N"                 # always mandatory — primary provenance from the TZ
  adapt: ADAPT-NNN                   # conditional: present if an ADAPT was created
  adapt-section: "Forward §N"        # mandatory if adapt is set
  adversarial-review-ref: AR-NNN     # conditional: if adapt is absent — the AR carrying the "no findings" verdict (§7.4.1.2)

# === Cross-level link subsystem BR → system BR (see §6.8.2) ===
implements:                          # array; not a parent-edge, a separate edge type
  - id: BR-NN                        # id of the parent system BR
    scope:
      system: "<system-id>"
    rationale: "<short>"             # optional; reference to an ADAPT section if present

# === Relationship graph (substrate-managed) ===
children: []                         # auto: SR whose parent.id = this BR
implemented-by: []                   # auto: subsystem BR referencing it via implements[]
verified-by: []                      # auto: TC verifying through SR

# === AI provenance (mandatory on RENAR-4+; schema — §4.10.1) ===
ai-provenance:
  generated-by: "<vendor>-<model>-<version>@<date>"
  generated-at: "<ISO-8601>"
  human-edits: boolean

# === Replacement (mandatory if applicable) ===
replaces: "<old-id>"
replaced-by: "<new-id>"
deprecated-date: "<ISO date>"
---
```

```markdown
## Need

Who (role), what (action), why (business goal) — one sentence.

## Success criteria

1. <Measurable outcome, independently verifiable.>
2. <…> (3–7 items in total.)

## Context

Where the requirement came from (with a reference to an ADAPT section if present);
what alternatives were considered.

## Constraints

<Optional: business constraints — budget, deadlines, regulation. Technical
constraints do not belong here — the SPEC types and SR exist for those.>
```

The `source.tz-section` field is always present; `source.adapt` is omitted when the adversarial review returns a "no findings" verdict — then `source.adversarial-review-ref` is mandatory ([§7.4.1](../../standard/en/07-adapt.md#7.4.1)).

> **Where the "requirements" live in a BR.** The BR template deliberately has no section with "the system shall …" statements: in RENAR those statements are SR ([§6.6](../../standard/en/06-requirements-hierarchy.md#6.6)), derived from this BR. Mixing them into the BR blurs the "business need ↔ system requirement" boundary and produces "technical BRs" indistinguishable from SR ([§6.4](../../standard/en/06-requirements-hierarchy.md#6.4), [§6.5.1](../../standard/en/06-requirements-hierarchy.md#6.5.1)). The BR's own verifiable, enumerable content is carried by the **Success criteria** section: 3–7 measurable, independently checkable outcomes — these are the business requirements in verifiable form. Decomposing BR → SR turns each criterion into one or more normative SR ("the system shall …" form per [§6.6.3](../../standard/en/06-requirements-hierarchy.md#6.6.3)).

---

## 12.3 SR template

An SR captures what the system does (observable behavior and constraints); table, framework, and data-structure names are the responsibility of SPEC ([§6.6.1](../../standard/en/06-requirements-hierarchy.md#6.6.1)). Frontmatter per [§6.6.2](../../standard/en/06-requirements-hierarchy.md#6.6.2); body per [§6.6.3](../../standard/en/06-requirements-hierarchy.md#6.6.3).

```yaml
---
id: SR-NN                            # immutable
title: "<short, descriptive>"
type: SR
slug: "<kebab-case>"

# === Scope (mandatory) ===
level: system | subsystem | module
scope:
  system: "<system-id>"
  subsystem: "<subsystem-id>"        # null if level=system
  module: "<module-id>"              # null if level ≠ module

# === Lifecycle (mandatory) ===
# status and version are absent: the artifact is approved as part of a set version (standard/10 §10.7)
owner: "<role / responsible person>"

# === Parent (mandatory) ===
parent:
  id: BR-NN                          # single parent

# === Source: provenance (see §7.4.1; same rules as BR) ===
source:
  tz-section: "§N.N"                 # always mandatory
  adapt: ADAPT-NNN                   # conditional
  adapt-section: "Forward §N"        # mandatory if adapt is set
  adversarial-review-ref: AR-NNN     # mandatory if adapt is omitted — the AR (§7.4.6)

# === Quality characteristic (conditional; §6.6.2) ===
# mandatory for a non-functional SR; the value comes from the ISO/IEC 25010:2023 list.
# Omitted for a functional SR.
quality-characteristic: functional-suitability | performance-efficiency | compatibility |
                        interaction-capability | reliability | security | maintainability |
                        flexibility | safety

# === Relationship graph ===
constrained-by:                      # typed edges to SPEC (chapter 8)
  - SPEC-UI-NN
  - SPEC-API-NN
  - SPEC-DATA-NN
children: []                         # auto: TR whose parent.id = this SR
verified-by: []                      # auto: TC verifying the SR

# === AI provenance (mandatory on RENAR-4+) ===
ai-provenance:
  generated-by: "<vendor>-<model>-<version>@<date>"
  human-edits: boolean

# === Replacement (mandatory if applicable) ===
replaces: "<old-id>"
replaced-by: "<new-id>"
deprecated-date: "<ISO date>"
---
```

```markdown
## Requirement

One sentence in one of the six controlled forms (standard/06 §6.6.3.1):
"The system MUST ‹response›" or with a condition marker as the first word —
WHILE ‹state›, WHEN ‹trigger›, IF ‹condition› THEN, WHERE ‹feature is present›,
WHILE ‹state›, WHEN ‹trigger›. Operators, terms, prohibited constructions,
atomicity — standard/15.

## Behavior

A detailed description of observable behavior; functional scenarios.

## Constraints

<Mandatory if applicable: non-functional constraints — performance, security.
Full constraints are pushed into SPEC via constrained-by[].>

## Link to SPEC

<Mandatory if constrained-by[] is present: which aspects of behavior are governed
by which SPEC.>
```

`parent.id` is the single BR (a parent tree); `constrained-by[]` is a graph of references to SPEC of any type and in any number ([§6.6.2](../../standard/en/06-requirements-hierarchy.md#6.6.2)).

---

## 12.4 TR template

A TR is the atomic unit of implementer work: exactly what to build within a single SR ([§6.7.1](../../standard/en/06-requirements-hierarchy.md#6.7.1)). Frontmatter per [§6.7.2](../../standard/en/06-requirements-hierarchy.md#6.7.2); body per [§6.7.3](../../standard/en/06-requirements-hierarchy.md#6.7.3).

```yaml
---
id: TR-NN                            # immutable
title: "<short, descriptive>"
type: TR
slug: "<kebab-case>"

# === Scope (mandatory) ===
level: system | subsystem | module   # system — rare, cross-subsystem tasks
scope:
  system: "<system-id>"
  subsystem: "<subsystem-id>"        # null if level=system
  module: "<module-id>"              # null if level ≠ module

# === Lifecycle (mandatory) ===
status: draft | approved | done | obsolete
owner: "<assignee role / agent>"

# === Parent (mandatory) ===
parent:
  id: SR-NN                          # single parent

# === Source: traceability chain (inherited from parent SR, §6.7.5) ===
source:
  adapt: ADAPT-NNN                   # auto: inherited from parent SR; may be absent
  sr-version: "<version-ref>"        # pinning to the SR version (substrate capability V5)

# === Relationship graph ===
implements-spec:                     # typed edges to SPEC
  - SPEC-API-NN
  - SPEC-UI-NN
verified-by: []                      # auto: TC verifying through SR

# === Goal and acceptance criteria ===
goal: "<one-sentence outcome>"
acceptance-criteria:
  - "<numbered, falsifiable, unambiguous>"
  - "<…>"

# === AI provenance (mandatory on RENAR-4+) ===
ai-provenance:
  generated-by: "<vendor>-<model>-<version>@<date>"
  human-edits: boolean
---
```

```markdown
## Goal

One paragraph; the outcome the TR makes observable.

## Acceptance Criteria

1. <Falsifiable criterion; covers a positive scenario.>
2. <Falsifiable criterion; covers a negative scenario / boundary.>

## Scope

What is in and what is **not** in the TR (per SENAR Rule 2).

## References

<Mandatory if applicable: to the SPEC in implements-spec[] and the sections of the
parent SR.>
```

The section names `Goal`, `Acceptance Criteria`, `Scope` are canonical per [§6.7.3](../../standard/en/06-requirements-hierarchy.md#6.7.3). The TR implementer works within the SR / SPEC and does not reach the ADAPT directly ([§6.7.5](../../standard/en/06-requirements-hierarchy.md#6.7.5)).

---

## 12.5 TC template

A TC verifies a normative assertion of a BR / SR / SPEC; the common frontmatter is per [§9.3](../../standard/en/09-test-cases.md#9.3), the body per [§9.4](../../standard/en/09-test-cases.md#9.4). Type-specific fields (`judge`, `baseline` for `ux` / `eval`) are added on top per [§9.6](../../standard/en/09-test-cases.md#9.6).

```yaml
---
# === Identity (mandatory) ===
id: TC-NN                            # immutable; NN sequential within the scope
title: "<short, descriptive>"
type: TC
slug: "<kebab-case>"

# === Classification (mandatory) ===
tc-type: business | ux | system | contract | eval | security   # business — the canonical name (§9.5)
negative: boolean                    # true for the paired negative TC

# === Scope (mandatory) ===
level: system | subsystem | module
scope:
  system: "<system-id>"
  subsystem: "<subsystem-id>"        # null if level=system
  module: "<module-id>"              # null if level ≠ module

# === Lifecycle (mandatory) ===
# a TC norm has no status (standard/10 §10.7); the implementation — automation.* / last-run.* (§10.9)

# === Verification target (mandatory; at least one) ===
verifies:
  - id: SR-NN | BR-NN | SPEC-<TYPE>-NN

# === Pair link (mandatory if negative=false and a paired TC exists) ===
paired-with:
  - TC-NN

# === Task binding (optional; §9.19.7) ===
verifies-tr: TR-NN                   # the task to whose scope the test is narrowed
verifies-claims: []                  # the subset of the parent SR's claims covered within that TR

# === Bench and data (conditional; §9.3) ===
environment-ref: SPEC-TEST-NN        # mandatory if automation.kind: dynamic — the bench and dataset
                                     # the TC is valid against. Does not apply to static checks
                                     # (doc-lint, structural TC): the analyzer works over artifacts

# === Automation (mandatory) ===
automation:
  status: automated | manual-pending
  kind: dynamic | static                             # mandatory; static — the runner is a static analyser
  location: "<substrate pointer to implementation>"  # mandatory if automated
  manual-pending-until: "<ISO date>"                 # mandatory if manual-pending
  manual-pending-reason: "<text>"                    # mandatory if manual-pending

# === Red history (§9.18.2; the condition for counting a TC as evidence) ===
red-history:                         # auto: maintained by the substrate from run facts
  fixing-run:                        # the fixing run before implementation; MUST be red
    date: "<ISO-datetime>"
    result: fail
  green-transition:                  # the recorded red → green transition
    date: "<ISO-datetime>"
  inherited-from: null               # conditional: TC-NN when behaviour did not change (refactoring, migration)
  not-applicable-reason: null        # conditional: implementation-originated — then a killed mutant is mandatory
  mutation-check:                    # mandatory when not-applicable-reason is set
    mutants-killed: 0                # >= 1, otherwise the TC is not evidence

# === Execution (mandatory for tc-type: ux | eval) ===
judge:
  vendor: "<provider>"               # mandatory; judge isolation — P7
  model: "<model-id>"
baseline:                            # mandatory for ux | eval
  artifact: "<substrate pointer>"
  perceptual-diff-threshold: float   # for ux
  metric-thresholds: {}              # for eval

# === Last run (runner-managed; not filled by the author) ===
last-run:                            # auto
  date: "<ISO-datetime>"
  result: pass | fail | skipped | n/a
  runner-id: "<runner-name@version>"

# === AI provenance (mandatory on RENAR-4+) ===
ai-provenance:
  generated-by: "<vendor>-<model>-<version>@<date>"
  human-edits: boolean
---
```

```markdown
## Context

Which clause of the verified artifact the TC references; a quote or paraphrase of
the assertion.

## Preconditions

The system and data state required for the run; provided by the seed mechanism.

## Steps

Runner actions. For tc-type: ux — intentions, not selectors (§9.6.1).

## Pass criterion

Binary, observable, reproducible (§9.11).

## Fail criterion

A list of observable signs of a violation (not the negation of the Pass criterion):
leaks, side effects, race conditions.

## Postconditions

The state expected after the run; the cleanup mechanism.

## Out of scope

What is **deliberately** not checked, naming the paired TC where it is covered.
```

The `environment-ref` field is mandatory for dynamic TCs (`automation.kind: dynamic`): "the test passed" only means something against a specific bench and specific data ([§9.3](../../standard/en/09-test-cases.md#9.3)). It does not apply to static checks (the doc-lint, the structural TC) — the analyzer works over artifacts. The reference points to a SPEC-TEST ([§8.5.10](../../standard/en/08-specifications.md#8.5.10)); changing the bench or the dataset increments its version and invalidates `verified` on the dependent TCs — precisely, without touching anything else.

The headings `## Pass criterion` and `## Fail criterion` are fixed: the change-of-criteria control hook detects them ([§10.11.3](../../standard/en/10-lifecycle-qg.md#10.11.3)) — they may not be renamed locally. The "Out of scope" section is mandatory: its absence blocks the TC transition to `ready` ([§9.4](../../standard/en/09-test-cases.md#9.4)).

---

## 12.6 AR template — the adversarial-review record

AR captures the fact and the outcome of the mandatory adversarial review ([§7.4.6](../../standard/en/07-adapt.md#7.4.6)). It is an **evidence record**, not a requirements artifact: it has no parents, no children and no test cases. It is issued in both outcomes of the review — whether an ADAPT is created or not.

```yaml
---
id: AR-NNN                           # immutable; NNN is sequential within the parent TZ
type: AR
tz-ref: TZ-YYYY-NNN                  # mandatory; the (delta-)TZ under review
trigger-stage: import-tz             # mandatory: import-tz | decompose-br | decompose-sr | spec | tc

# === Reviewer (mandatory; isolation §7.10.2) ===
reviewer:
  vendor: "<provider>"
  model: "<model-id>"                # MUST differ from the primary agent's model
primary:                             # mandatory: the second operand of the §7.10.2 condition
  vendor: "<provider>"
  model: "<model-id>"                # the primary agent's model at the moment the AR was issued

# === Verdict (mandatory) ===
verdict: no-findings                 # no-findings | findings-present
produces-adapt: []                   # conditional: non-empty when findings-present; empty when no-findings

# === Lifecycle (mandatory) ===
status: issued                       # draft | issued | superseded
superseded-by: null                  # conditional: AR-NNN when status=superseded

# === Signature (mandatory for issued; substrate capability V6) ===
signature:
  author: "<reviewer-id>"
  timestamp: "<ISO-8601>"
---
```

Body on `verdict: no-findings`:

```markdown
## Justification of unambiguity

Why converting the reviewed TZ sections into requirements produces no gap: how each risk
(terms, scope boundary, tacit assumptions) is closed.

## Review coverage

The TZ sections included in the review, and the derivation stage at which it was conducted.
```

Body on `verdict: findings-present`:

```markdown
## Review coverage

The TZ sections included in the review, and the derivation stage.

## Findings produced

Pointers to the `B-NNN` records in the ADAPT that was produced — **without duplicating** their
content: the findings themselves live in the ADAPT (§7.4.4).
```

An issued AR is immutable. If a repeat review at the same stage issues a new verdict, a new AR is issued and the previous one moves to `superseded` with `superseded-by` filled in. Referencing an AR in status `draft` or `superseded` from `source.adversarial-review-ref` is prohibited — the gate reports a fatal error ([§10.11.1](../../standard/en/10-lifecycle-qg.md#10.11.1)).

---

## 12.7 ACTZ template — the TZ clarification protocol

ACTZ is an artifact of the contractual contour: a batch of questions, proposals and decisions put to the client and signed by both parties ([§7.13](../../standard/en/07-adapt.md#7.13)). Decisions are worded in the language of obligations ("the button is named X"), not of interpretation. The frontmatter is per [§7.13.3](../../standard/en/07-adapt.md#7.13); TZ annexes are per [§7.13.5](../../standard/en/07-adapt.md#7.13).

```yaml
---
id: ACTZ-NNN                         # immutable; sequential within the parent TZ
title: "TZ Clarification Protocol No. N"
type: ACTZ
tz-ref: TZ-YYYY-NNN                  # mandatory; the TZ the protocol belongs to

# === Findings closed (conditional) ===
resolves:                            # B-NNN entries in an ADAPT that the protocol closes
  - id: B-NNN                        # absent on a client-initiated protocol (§7.13.2)
    adapt: ADAPT-NNN

# === Decisions (mandatory; non-empty list) ===
decisions:
  - number: "§M"                     # stable clause number; the target of decided-in
    statement: "<decision in the language of obligations>"
    tz-section: "§N.N"               # the TZ section being clarified

# === TZ annexes (conditional; §7.13.5) ===
annexes:
  - name: "<annex>"
    version: "<new version>"
    document-ref: "<substrate link>"

# === Lifecycle (mandatory) ===
status: draft                        # draft | sent | signed | superseded
superseded-by: null                  # conditional: ACTZ-NNN when status=superseded

# === Signatures (mandatory for signed; substrate capability V6) ===
client-signature:
  signed-by: "<name>"
  role: "<role of the authorised representative>"
  organization: "<client organization>"
  signed-at: "<ISO-datetime>"
vendor-signature:
  signed-by: "<name>"
  role: "<role>"
  signed-at: "<ISO-datetime>"
---
```

```markdown
## Subject of the clarification

Which TZ sections are clarified and why the question arose — with a reference to the TZ
section (and to the `B-NNN` finding in the ADAPT where one exists).

## Decisions

1. **§1.** <The decision in the language of obligations; a checkable wording.>
2. **§2.** <…>

Clause numbering is stable: `B-NNN.decided-in` and `AT.verifies[]` point at it.

## Annexes

<Mandatory where applicable: the new versions of the TZ annexes approved by this protocol
(§7.13.5). The previous version remains immutable.>

## Signatures

A two-sided signature: the client (or an authorised representative) and the vendor.
```

A signed ACTZ **MUST NOT** be edited: a correction is made only by a new protocol that supersedes the earlier decision (`superseded-by`). Referencing an ACTZ in status `draft` from `decided-in` is prohibited ([§7.13.4](../../standard/en/07-adapt.md#7.13)). The effective TZ = the initial TZ with its annexes plus every signed ACTZ ([§7.14](../../standard/en/07-adapt.md#7.14)).

---

## 12.8 AT template — the acceptance test of the contractual contour

An AT is derived **exclusively** from the effective TZ and checks conformance to the contract, not to the interpretation ([§9.19](../../standard/en/09-test-cases.md#9.19)). An AT is created by an isolated agent: only the effective TZ is given as input; access to ADAPT, BR / SR / SPEC, TC and code is prohibited, and a breach of isolation is fatal. The frontmatter and body are per [§9.19.3](../../standard/en/09-test-cases.md#9.19).

```yaml
---
id: AT-NN                            # immutable
title: "<short, descriptive>"
type: AT
negative: boolean                    # mandatory; pos/neg pairing — as for TC (§9.7)

# === Verification target (mandatory; contractual references only) ===
verifies:
  - "TZ §N"                          # a section of the effective TZ
  - "ACTZ-NNN §M"                    # a clause of a signed protocol
  # a reference to BR / SR / SPEC / TC is fatal (§9.19.2)

tz-version: "<effective TZ revision>"  # mandatory; the revision the AT was derived from

# === Provenance of the isolated agent (mandatory) ===
generator:
  vendor: "<provider>"
  model: "<model-id>"                # MUST differ from the primary agent's model
  internal-contour-access: false     # mandatory; confirmation of no access to the internal contour
  generated-at: "<ISO-8601>"

# === Lifecycle (mandatory) ===
status: draft                        # draft | ready | passing | failing | obsolete

# === Trial environment and dataset (mandatory; §9.19.3) ===
environment-ref: SPEC-TEST-NN        # set by the Architect or the runner AFTER generation:
                                     # the isolated agent does not see internal artifacts (§9.19.2)

# === Automation (mandatory) ===
automation:
  status: automated | manual-pending
  kind: dynamic | static
  location: "<substrate pointer to implementation>"

# === Last run (runner-managed; not filled by the author) ===
last-run:                            # auto
  date: "<ISO-datetime>"
  result: pass | fail | skipped | n/a
  runner-id: "<runner-name@version>"
  tz-version: "<effective TZ revision at the time of the run>"
---
```

```markdown
## The effective-TZ clause

> "<The verbatim quotation of the clause under test — a TZ section or a clause of a
> signed ACTZ.>"

This section is mandatory (`tz_text`, §9.19.3): at acceptance the clause of the contract
itself is produced, not a paraphrase.

## Preconditions

The system and data state required for the run.

## Steps

Runner actions — worded from the client's standpoint, without relying on the internal
construction of the system.

## Pass criterion

Binary, observable, reproducible; derived from the quoted clause.

## Fail criterion

A list of observable signs of non-conformance to the contract.

## Out of scope

What is **deliberately** not checked, naming the paired AT where it is covered.
```

ATs **are regenerated before every trial** from the current revision of the effective TZ: an outdated trial programme MUST NOT be admitted to the run ([§9.19.4](../../standard/en/09-test-cases.md#9.19)). The product is not submitted for hand-over until every AT is in status `passing` ([§10.4.3](../../standard/en/10-lifecycle-qg.md#10.4)).

---

## 12.9 SPEC templates — deferred

Skeletons for the SPEC types ([chapter 8](../../standard/en/08-specifications.md)) are **deliberately not included** in this appendix. The partner review flagged the question of SPEC skeletons as open: the set of mandatory fields differs across SPEC types (UI, API, DATA, AI, SEC, INT) far more than across BR / SR / TR / TC, and fixing a skeleton prematurely risks reading as normative.

Draft sketches of the SPEC skeletons are kept in the repository — `research/17-specification-schema-and-templates.md` (§5; an internal draft, not published to the site) — pending a separate decision. When that decision is made, the SPEC skeletons are added here as a new section — without changing the closed list of SPEC types, which is already normative in [`standard/08 §8.2.2`](../../standard/en/08-specifications.md#8.2.2) and [§8.3](../../standard/en/08-specifications.md#8.3).

The closed list now holds **twelve** types: the nine structural ones plus `SPEC-TEST` (benches and data — how conformance is proven), `SPEC-UC` (use case — an end-to-end path with an executor role, v1.1) and `SPEC-DOC` (the composition of the delivered documentation). The deferral of skeletons covers them too: the field schemas of both types are given in [`reference/02`](02-schemas.md) (sections 6.3 and 6.4); document skeletons are not.

---

## 12.10 Filling placeholders and common mistakes

| Placeholder | What to replace it with | Common mistake |
|---|---|---|
| `BR-NN` / `SR-NN` / `TR-NN` / `TC-NN` / `AT-NN` | A sequential ID within the scope (the substrate assigns it on creation) | Changing the ID after publication — it is immutable |
| `ACTZ-NNN` / `decisions[].number` | The protocol's sequential number and the stable clause number of a decision | Renumbering the clauses of a signed protocol — `decided-in` and `AT.verifies[]` point at them |
| `AT.verifies[]` | Only `TZ §N` and `ACTZ-NNN §M` | Referencing an SR / SPEC / TC — a breach of isolation; the gate reports a fatal error |
| `<system-id>` / `<subsystem-id>` / `<module-id>` | Identifiers from the project's system registry | Filling `subsystem` when `level=system` (it must be `null`) |
| `source.tz-section` | The section of the source TZ — always present | Omitting it on the assumption that a reference to the ADAPT is enough |
| `constrained-by[]` / `implements-spec[]` | IDs of existing SPEC | Naming a SPEC type outside the closed list |
| `# auto` fields (`children`, `verified-by`, `last-run`) | Nothing — the substrate / runner maintains them | Filling them by hand and diverging from the relationship graph |

Placeholders like `BR-NN` and empty `<...>` are an unfilled skeleton, not a valid artifact: run such files through checks only after substituting real values, otherwise the substrate validators will rightly reject the template IDs.

---

[← Back to the reference overview](README.md)
