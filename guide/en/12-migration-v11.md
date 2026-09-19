---
title: "Migration to v1.1"
description: "Moving an implementation from RENAR v1.0 to v1.1: the description set instead of per-artifact statuses, SPEC-UC, MW, the screen list, the component, the description language; re-assessment of the conformance claim per §13.7.3."
order: 12
lang: en
version: "1.1"
---

# 12. Migration to v1.1

> Step 4 of the procedure of [§13.9.3](../../standard/en/13-conformance.md#13.9.3) for the v1.1 wave (ADR-015…ADR-026, GitLab #30–#54). For implementations that claimed RENAR v1.0 conformance. A minor release is an immediate trigger of re-assessment ([§13.7.3](../../standard/en/13-conformance.md#13.7.3)): the v1.0 claim stands until re-assessed but does not carry over to v1.1 automatically.

**Not to be confused with** [10-migration-v1](10-migration-v1.md) — that is the historical v0 → v1.0 migration; this is the set of changes of one minor wave with identifiers and traceability preserved.

---

## 1. What changed — the map of the wave

| # | Change | Where | Basis | Class for an implementation |
|---|---|---|---|---|
| 1 | **The description set** — the unit of approval, version and verification; BR / SR / SPEC / TC norm without `status` / `version`; `set-version N.M`, linear history, version record | [§10.5](../../standard/en/10-lifecycle-qg.md#10.5), [§6.5.4](../../standard/en/06-requirements-hierarchy.md#6.5.4), [§6.6.4](../../standard/en/06-requirements-hierarchy.md#6.6.4), [§8.8](../../standard/en/08-specifications.md#8.8), [§9.9](../../standard/en/09-test-cases.md#9.9) | ADR-024, #46, #48 | **breaking** (schema) |
| 2 | QG-0 / QG-1 / QG-2 — objects: the set version / the TC implementation / the version on a product version; `automation.set-version`, `last-run.set-version` | [§10.3](../../standard/en/10-lifecycle-qg.md#10.3), [§9.3](../../standard/en/09-test-cases.md#9.3) | ADR-024 | **breaking** (schema) |
| 3 | §13.3.5 — pair coverage is checked at QG-0 of the set version (`coverage-presence`) | [§13.3.5](../../standard/en/13-conformance.md#13.3.5), [§10.11.1](../../standard/en/10-lifecycle-qg.md#10.11.1) | ADR-024, ADR-025 | mandatory clause — re-assessment |
| 4 | Manifest: `set-version`, `confirmation: first-party \| second-party` | [§13.4.2](../../standard/en/13-conformance.md#13.4.2) | ADR-024, ADR-017 | **breaking** (manifest) |
| 5 | **SPEC-UC** — the twelfth type (`role: human \| agent`, `steps[].ref`); the step reference to a statement in SPEC-UI / SPEC-PROC; a `ux` TC per action through the interface | [§8.3](../../standard/en/08-specifications.md#8.3), [§8.5.12](../../standard/en/08-specifications.md#8.5.12), [§9.8](../../standard/en/09-test-cases.md#9.8) | ADR-018, #35, #34 | closed list no. 7; new rules |
| 6 | **MW** — the manual walkthrough record (a class of evidence) | [§9.20](../../standard/en/09-test-cases.md#9.20), [reference/02 §8.3](../../reference/en/02-schemas.md) | ADR-018, #34, #50 | additive |
| 7 | **First-party confirmation** — a concept as the input, ACTZ / AT have no subject; combining roles | [§1.4.4](../../standard/en/01-scope.md#1.4.4), [§5.5.5](../../standard/en/05-roles.md#5.5.5) | ADR-017, #33 | additive (a kind of applicability) |
| 8 | **Reusable component** — a system in its own tree; `assumptions[]`, `applies-to`, the `uses[]` edge | [§6.14](../../standard/en/06-requirements-hierarchy.md#6.14), [§10.11.1](../../standard/en/10-lifecycle-qg.md#10.11.1) | ADR-016, #30 | additive |
| 9 | `implements[]` — mandatory clause §13.3.8 | [§13.3.8](../../standard/en/13-conformance.md#13.3.8) | #30 | mandatory clause — re-assessment |
| 10 | **The screen list** — the list in the SPEC-ARCH body, `screens[]` in SPEC-UI, the coverage rule; the definition of a screen | [§8.5.1](../../standard/en/08-specifications.md#8.5.1), [§8.5.6.1](../../standard/en/08-specifications.md#8.5.6.1), [§10.7.2](../../standard/en/10-lifecycle-qg.md#10.7.2) | ADR-023, #42, #45, #54 | additive (structural completeness) |
| 11 | Coverage completeness of the SPEC mandatory body | [§8.4.1](../../standard/en/08-specifications.md#8.4.1) | ADR-023, #42 | additive |
| 12 | SPEC-ARCH and SPEC-SEC mandatory and non-empty at the `system` level | [§8.3](../../standard/en/08-specifications.md#8.3), [§10.7.3](../../standard/en/10-lifecycle-qg.md#10.7.3) | model 3.3 | additive (structural completeness) |
| 13 | The controlled form of an SR statement — six forms (recommendation) | [§6.6.3.1](../../standard/en/06-requirements-hierarchy.md#6.6.3.1) | ADR-022, ADR-026, #37 | recommendation |
| 14 | **The description language** — chapter 15: operators, glossary discipline, prohibited constructions, atomicity; mandatory from `RENAR-2` | [chapter 15](../../standard/en/15-description-language.md), [§11.5](../../standard/en/11-maturity-model.md#11.5), [§1.7.5](../../standard/en/01-scope.md#1.7.5) row 17 | ADR-026, #51 | mandatory from RENAR-2 |
| 15 | `automation.status` — two values (`automated` / `manual-pending` with a deadline and a reason) | [§9.3](../../standard/en/09-test-cases.md#9.3) | #50 | **breaking** (schema) |
| 16 | AR records the primary agent model (`primary.*`); `ai-provenance` composition per the canon | [§4.10.1](../../standard/en/04-terms.md#4.10.1), [§12.3](../../standard/en/12-metrics.md#12.3) | #38, #44 | schema (ai-provenance) |
| 17 | Metric threshold calibration — by the data-sufficiency condition | [§12.3](../../standard/en/12-metrics.md#12.3) | ADR-020 | additive |
| 18 | The non-degeneracy meter of a new mandatory control | [§13.9.4](../../standard/en/13-conformance.md#13.9.4) | ADR-021, #40 | procedural |
| 21 | The statement address `<id>#n`: `verifies[].statement` in a TC (mandatory when the artifact has more than one statement), a step `ref` in address form, `coverage-presence` by addresses | [§15.1.1](../../standard/en/15-description-language.md#15.1.1), [§9.3](../../standard/en/09-test-cases.md#9.3), [§8.5.12.1](../../standard/en/08-specifications.md#8.5.12.1) | v1.1 review | additive (TC schema) |
| 20 | Norm and process: the standard governs artifacts, lifecycles and rules, not the order of work; agent editions — preconditions instead of steps | [§1.2.1](../../standard/en/01-scope.md#1.2.1), [§1.3](../../standard/en/01-scope.md#1.3) item 7, [§7.6.1](../../standard/en/07-adapt.md#7.6.1), [§11.11](../../standard/en/11-maturity-model.md#11.11) | ADR-027, #55 | editorial (the boundary of the norm) |

## 2. What an implementation does — by class

### 2.1 Breaking schema changes (rows 1, 2, 4, 15)

1. **Remove `status` and `version` from BR / SR / SPEC / TC norms.** The state of an artifact is derived from the set version ([§6.5.4](../../standard/en/06-requirements-hierarchy.md#6.5.4)). Migration script: delete the fields, fix the first set version `1.0` by a version record ([§10.5.4](../../standard/en/10-lifecycle-qg.md#10.5.4)) signed by the Architect, including every artifact that was `approved`+; artifacts in `draft` stay in the set's draft.
2. **TC:** `verifies[].version` / `requirement-version` → remove; add `automation.set-version` and `last-run.set-version` = the set version on which the implementation was created and run; `automation.status` → `automated` or `manual-pending` with `manual-pending-until` / `manual-pending-reason`. Check: `scripts/validate-schema-examples.js` (rules `verifies-legacy-version`, `invalid-tc-type`).
3. **Manifest:** add `set-version` (the current approved set version) and `confirmation` (`second-party` with a stakeholder, `first-party` with a concept as the input, [§1.4.4](../../standard/en/01-scope.md#1.4.4)); `renar-version: "1.1"`.
4. **Substrate hooks:** replace per-artifact state transitions with the set-version enforcement points ([§10.11.1](../../standard/en/10-lifecycle-qg.md#10.11.1)): `coverage-presence`, `implements`-edge and `uses`-edge validation; QG-1 — only the admission of a TC implementation.

### 2.2 Mandatory clauses (rows 3, 9) — re-assessment

The v1.0 claim MUST be re-assessed ([§13.7.3](../../standard/en/13-conformance.md#13.7.3)): §13.3.5 is now checked on the set version as a whole (every statement of the version — a pair of TC norms in the same version), §13.3.8 requires `implements[]` for subsystem BRs. Self-assessment — [reference/08](../../reference/en/08-conformance-self-assessment.md); the `mandatory-clauses-confirmed` keys are not renamed.

### 2.3 Additive changes (rows 5–8, 10–12, 16–18)

- **SPEC-UC:** scenarios that lived as a SPEC-UI user journey or a SPEC-PROC happy path and stitch several SRs / SPECs — move into `SPEC-UC` with `role` and a `ref` on every step; steps without `ref` violate structural completeness. SPEC-UI statements about an action through the interface — cover with `ux` TCs.
- **The screen list:** add a "Screen list" section to the `system`-level SPEC-ARCH from the description (not from code routes); `screens[]` in every SPEC-UI; an uncovered screen blocks version approval. A system without an interface records "no interface".
- **SPEC-ARCH / SPEC-SEC:** create if absent; the silence of the TZ about security is closed by a SPEC-SEC of your own, with client-observable consequences — through an ACTZ.
- **Component:** libraries and platforms described as subsystems or a "fourth level" — convert into a system in its own tree with `uses[]` at the consumers ([§6.14](../../standard/en/06-requirements-hierarchy.md#6.14)); path B or C — by the structure of the organization.
- **First party:** implementations without a stakeholder (the former §1.5.4) with a written concept — the kind of applicability of [§1.4.4](../../standard/en/01-scope.md#1.4.4), `confirmation: first-party`; without a concept — out of scope.
- **MW:** record manual scenario runs as `MW-NN`; no standing manual run of `ux` TCs is introduced.
- **ai-provenance / AR:** bring `generated-by` to the versioned format, add `primary.*` to the AR.

### 2.4 The description language (row 14) — mandatory from RENAR-2

For `RENAR-2`+ claims: the normative sections of artifacts — per [§15.2](../../standard/en/15-description-language.md#15.2)–[§15.5](../../standard/en/15-description-language.md#15.5). Order: (1) operators — replace synonyms ("shall", "needs to", "can") with the five words of the list; (2) terms — reduce to one form along the chain, divergences are `terminology` findings; (3) the constructions of [§15.4](../../standard/en/15-description-language.md#15.4) — reformulate; (4) atomicity — split statements from which exactly one pair of TCs cannot be written. The forms of [§15.6](../../standard/en/15-description-language.md#15.6) are a recommendation; approved versions are not reworded after the fact — the edits enter with the next minor set version.

**Row 20** requires no action: if your production process differs from the scenarios in the guides, it conforms to the standard as long as the preconditions of artifacts hold (agent edition, section 4); artifacts and rules of your own are `declared-stricter`.

## 3. Order of work

| Step | What | Check |
|---|---|---|
| A | Inventory: artifacts with `status` / `version`, TCs with `requirement-version`, SPEC-UIs without `screens[]`, SPEC-ARCHs without a list, missing SPEC-ARCH / SPEC-SEC | a frontmatter script |
| B | The first set version `1.0`: version record, the Architect's signature; field removal | `validate-schema-examples`, structural completeness §10.7.2 |
| C | TC: `set-version`, `automation.status` from two values; QG-1 as implementation admission | a run, `last-run.set-version = 1.0` |
| D | The additive changes of §2.3 — as the next minor set version `1.1` | QG-0 of the version: `coverage-presence`, `screens`, `uses` |
| E | The description language (RENAR-2+) — as artifacts are edited | adversarial review, static check |
| F | Manifest `renar-version: "1.1"`, `set-version`, `confirmation`; self-assessment reference/08; re-assessment of the claim | [§13.7.3](../../standard/en/13-conformance.md#13.7.3) |

## 4. Common pitfalls

- **Set version ≠ product version.** `set-version` is the version of the description; the product version lives in the verification record ([§10.5.4](../../standard/en/10-lifecycle-qg.md#10.5.4)).
- **A minor on every change.** The "large or small" rule is not accepted: every approved change is a minor; a major is a decision at a milestone with a full audit.
- **A list from code.** A screen list from implementation routes violates [§2.3.1](../../standard/en/02-methodology-positioning.md#2.3.1); a screen in the code without an entry is a drift and a backward finding.
- **Upper-case operators.** Prohibited in the Russian edition of artifacts (reference/06 §2.1 item 4); unambiguity comes from the closedness of the list.

## 5. Rollback

The v1.1 changes delete no data: the removed `status` / `version` fields are recoverable from the substrate history (V1); the set version record is additive. Returning to a v1.0 claim — roll back the manifest and repeat the self-assessment against v1.0.

## 6. Related documents

- ADR-024 (the batch model; §9 — the places of edits by task of the wave) and ADR-026 (the description language) — the standard's decision archive, available in the repository
- [CHANGELOG](https://github.com/Kibertum/RENAR/blob/main/CHANGELOG.md) — the v1.1 entry
- [reference/08](../../reference/en/08-conformance-self-assessment.md) — conformance self-assessment

---

*Guide RENAR 1.1 — renar.tech*
