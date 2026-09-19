---
title: "Agent Implementation Profile"
description: "Abstract RENAR execution contract for implementers. Machine-readable index: normative-index.yaml."
order: 10
lang: en
version: "1.0"
---

# Agent Implementation Profile

> **Purpose:** an informative contract for an agent or substrate-native runtime that **implements** RENAR without guesswork. The normative text is `standard/`; this document is an operational mapping with no ties to any specific vendor tooling.  
> **Machine-readable:** [`normative-index.yaml`](../normative-index.yaml)

---

## 1. How to Read the Table

| Column | Meaning |
|---|---|
| `clause_id` | Stable ID from `normative-index.yaml` |
| `Agent action (abstract)` | What the runtime MUST be able to do **without** a vendor CLI |
| `Inputs / outputs` | Substrate artifacts |
| `Gate` | A quality checkpoint or a check driven by the runner |

---

## 2. MVR ↔ Agent Actions

| clause_id | Agent action (abstract) | Inputs | Outputs | Gate |
|---|---|---|---|---|
| MVR-1 | Block reverse-engineering of SR/SPEC from code without bug-fix justification; the Source of Truth = the requirements hierarchy | code diff, SR/SPEC | audit record / blocked promotion | — |
| MVR-2 | Verify that the substrate declares V1–V6 in the manifest; run capability checks | `RENAR-CONFORMANCE.yaml` | pass/fail report | — |
| MVR-3 | Reactive, stage-independent ADAPT: create an ADAPT (0..N per TZ) only on findings from adversarial review; no findings → source.tz-section + adversarial-review-ref; delta-TZ → the same reactive pattern; supersession via superseded | TZ, adversarial verdict, ADAPT draft | ADAPT approved / verdict evidence | QG-ADAPT-approve |
| MVR-4 | Reject a SPEC whose type ∉ the closed list | SPEC frontmatter | validation error | QG-0 |
| MVR-5 | Ensure a pos/neg pair of TCs for every normative statement | SR/SPEC, TC set | paired TC | QG-2 |
| MVR-6 | Reject custom gate types; require QG-0..2 | project config | manifest | — |
| MVR-7 | Require a signed `RENAR-CONFORMANCE.yaml` with `senar-version` | manifest | conformance claim | — |

---

<a id="3-mandatory-143-bijection"></a>

## 3. MVR ↔ Mandatory Clauses §13.3 Bijection

The full MVR ↔ §13.3 bijection is in [`08-conformance-self-assessment.md §1`](08-conformance-self-assessment.md#1-mvr--mandatory-clauses-133-bijection). The agent MUST NOT consider a project conformant if any `mandatory-clauses-confirmed` field = false.

---

## 4. Gate Runner Contract (abstract)

| Gate | Pre (agent checks) | Post (agent records) |
|---|---|---|
| QG-0 | Set: schema valid for every artifact; parent links within the same version; source resolved: `source.adapt` to an ADAPT in `approved` **or** `source.tz-section` + `source.adversarial-review-ref` to an AR with status `issued` ([§7.4.1](../../standard/en/07-adapt.md#7.4.1)); a pair of TC norms for every statement (`coverage-presence`) | version record `N.M` + audit-trail |
| QG-1 | TC only ([§10.3.2](../../standard/en/10-lifecycle-qg.md#10.3.2)): `automation.status: automated` (valid `automation.location`) or `manual-pending`; implementation pinned to the set version (`automation.set-version`, V5); pos/neg pairing; mandatory TC body sections ([§9.4](../../standard/en/09-test-cases.md#9.4)) | TC `draft` → `ready` + transition-log record |
| QG-2 | All TC norms of the set version with an admitted implementation `pass` with `last-run.set-version = N.M`; both TCs of every pair; red history | verification entry in the version record ([§10.5.4](../../standard/en/10-lifecycle-qg.md#10.5.4)) |

Decision trees: [standard/09 §9.1.1](../../standard/en/09-test-cases.md), [standard/10 §10.1.1](../../standard/en/10-lifecycle-qg.md).

---

## 5. Substrate-Neutrality Rule for Implementers

The runtime MUST map abstract actions onto substrate-native primitives via `RENAR-CONFORMANCE.yaml#substrate-capabilities` ([§3.7](../../standard/en/03-substrate-versioning.md#3.7)). Vendor-specific commands MUST NOT be the only way to satisfy a mandatory clause.

---

## 6. Coverage Status (v1.0)

| Area | Index entries | Profile rows | Note |
|---|---|---|---|
| MVR | 7/7 | 7/7 | complete |
| §13.3 mandatory | 8/8 | MVR-1..MVR-6; §13.3.7 / §13.3.8 are outside any MVR | no bijection: MVR-7 maps to §13.4 |
| QG-0..2 | 3/3 | 3/3 | QG-3/4 deferred optional |
| Mandatory clauses by chapter | partial | — | expand in later pass |
