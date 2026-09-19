---
title: "Conformance Self-Assessment"
description: "Printable checklist, MVR↔§13.3 bijection, RENAR-CONFORMANCE.yaml template for self-assessment."
order: 8
lang: en
version: "1.0"
---

# Conformance Self-Assessment

> **Purpose:** a practical kit for the Tech Lead / Architect before releasing [RENAR-CONFORMANCE.yaml](../../standard/en/13-conformance.md#13.4). The normative basis is [standard/13](../../standard/en/13-conformance.md). This document is **informative**; on conflict, standard/13 wins.

---

<a id="1-mvr-mandatory-clauses-133-bijection"></a>

## 1. MVR ↔ mandatory clauses §13.3 bijection

| MVR ([§0.5](../../standard/en/00-introduction.md#0.5)) | §13.3 clause | `mandatory-clauses-confirmed` field |
|---|---|---|
| MVR-1 Source-of-Truth inversion | §13.3.1 | `sot-inversion: true` |
| MVR-2 V1–V6 | §13.3.2 | `substrate-v1-v6: { v1..v6: true }` |
| MVR-3 ADAPT per TZ | §13.3.3 | `adapt-per-tz: true` |
| MVR-4 12 SPEC types | §13.3.4 | `spec-types-closed-list: true` |
| MVR-5 TC pos/neg | §13.3.5 | `tc-pos-neg-pairing: true` |
| MVR-6 QG — closed list | §13.3.6 | `quality-gates-closed-list: true` |
| MVR-7 conformance manifest | §13.4 (artifact) | manifest exists + signed |
| — (closed-list policy) | §13.3.7 | `closed-lists-backward-findings: true` |
| — (subsystem cross-level traceability) | §13.3.8 | `implements-edge-subsystem: true` |

All seven MVR + §13.3.7 + §13.3.8 are mandatory for **any** RENAR-1..5 level.

**On §13.3.8.** On v1.0 the clause was recommended; from v1.1 it is **mandatory** and is confirmed by the `implements-edge-subsystem` field of the manifest. The effective modality is recorded in [`reference/normative-index.yaml`](../normative-index.yaml), entry `MC-13.3.8`; the `check-implements-edge` gate reads it from there and checks the **presence** of the edge when the condition fires, not merely the validity of an existing one. Projects that conformed to v1.0 without the edge bring their descriptions in line via the migration guide.

---

<a id="2-self-assessment-checklist-mandatory-clauses"></a>

## 2. Self-assessment checklist (mandatory clauses)

Check off after verifying the evidence in the substrate:

### §13.3.1 Source-of-Truth inversion

- [ ] The BR/SR/SPEC/TC hierarchy is the authoritative source of behavior
- [ ] No SR reconstructed from code without a defect-fix justification
- [ ] Drift-hooks / review policy block silent SR←code adaptation

### §13.3.2 V1–V6 capabilities (substrate)

- [ ] V1 immutable history — enabled
- [ ] V2 atomic change unit — enabled
- [ ] V3 diff & review — enabled
- [ ] V4 branching / change-set — enabled
- [ ] V5 end-to-end version pinning across substrates — enabled (`automation.set-version`, `last-run.set-version`, the manifest `set-version`)
- [ ] V6 author + timestamp — enabled

### §13.3.3 Reactive ADAPT

- [ ] Every TZ has passed the adversarial review; the outcome is issued as an AR in status `issued`
- [ ] On a "findings present" verdict: the ADAPT is in status `approved` with the Architect's signature
- [ ] On a "no findings" verdict: no ADAPT is created; BR/SR/SPEC carry `source.tz-section` + `source.adversarial-review-ref`
- [ ] Every finding in `resolved` carries `decided-in` on a clause of a signed ACTZ
- [ ] Signed ACTZ carry the dual signature (client + contractor)
- [ ] A client signature on an ADAPT is not declared mandatory (admissible only as `declared-stricter`)
- [ ] delta-TZ: the delta-ADAPT is created on the fact of findings from the delta-TZ review, not automatically
- [ ] Superseded ADAPT are retained in the terminal `superseded`; no dangling `source.adapt` remain

### §13.3.4 SPEC types

- [ ] All SPEC ∈ {ARCH, API, DATA, INT, PROC, UI, AI, SEC, OPS, TEST, DOC, UC} — the \g<1>12\g<2>
- [ ] No local `SPEC-CUSTOM-*`
- [ ] Test benches and data are described as `SPEC-TEST`, not as a section of `SPEC-OPS`
- [ ] Delivered documentation is described as `SPEC-DOC`; the internal runbook stays in `SPEC-OPS`
- [ ] Every `SPEC-TEST` in which at least one dataset carries real or personal client data has a `client-signature`

### §13.3.5 TC pos/neg

- [ ] Every normative statement of the set version is covered by a pos + neg pair of TC norms in the same version (or a negative-invariant exception); the presence of coverage, not only pairing, is checked at QG-0 of the set (`coverage-presence`, §10.11.1)
- [ ] QG-0 of the set blocks version approval on an uncovered or unpaired statement
- [ ] Every TC with `automation.kind: dynamic` carries an `environment-ref` to an existing `SPEC-TEST` (a missing field is fatal; static checks require no bench)
- [ ] Every `SPEC-DOC` is covered by the doc-lint (a TC with `automation.kind: static`); without it the SPEC-DOC does not pass QG-2

### §13.3.6 Quality Gates

- [ ] QG-0, QG-1, QG-2 implemented as `required`
- [ ] QG-3, QG-4 declared `required` | `declared` | `absent` in the manifest
- [ ] No local custom gates

### §13.3.7 Closed lists

- [ ] Backward finding types — closed list §7.4.4 only
- [ ] SPEC decomposition types — closed list §8 only

### §13.3.8 Subsystem cross-level traceability

Checked only when the condition holds: `BR.level = subsystem` AND the parent system has ≥ 1 approved BR.

- [ ] When the condition holds, `implements[]` is non-empty. Omitting it is admissible **only** when the parent system is a container without BRs of its own, and then the justification is recorded in the “Context” section with a reference to ADAPT
- [ ] The `implements`-edge validation control point is implemented by the substrate — this duty applies **already on v1.0** and does not depend on the modality of the clause itself ([§13.3.8](../../standard/en/13-conformance.md#13.3.8))

Failing the first item is **non-conformance** ([§13.8.1](../../standard/en/13-conformance.md#13.8.1)).

**Rule:** if at least one is unchecked → **do not release** the manifest ([§13.5.1](../../standard/en/13-conformance.md#13.5.1)).

---

## 3. Level checklist (choose the target RENAR-N)

The minimum for claiming a level is [standard/11 §§11.4–11.8](../../standard/en/11-maturity-model.md). Brief summary:

| Level | Key additional criteria |
|---|---|
| RENAR-1 | Mandatory clauses only; frontmatter minimal |
| RENAR-2 | Basic frontmatter (no strict schema validation); TZ and delta-TZ as explicit immutable artifacts; lifecycle statuses not yet mandatory |
| RENAR-3 | Full frontmatter schema + lifecycle statuses; hooks enforce QG-0 and QG-1, QG-2 — partially |
| RENAR-4 | ai-provenance mandatory; pos/neg pairing for every normative assertion; QG-2 enforced natively |
| RENAR-5 | Adversarial review as a gate; multi-model consensus for `priority: must`; knowledge graph as primary lookup; continuous evaluation of SPEC-AI |

- [ ] The chosen `level` in the manifest is **no higher** than the checklist actually passed
- [ ] `declared-stricter` (if present) is documented separately

---

## 4. Manifest template (minimal)

Save as `RENAR-CONFORMANCE.yaml` at the root of the requirements substrate:

```yaml
manifest-version: 1
manifest-id: "CFM-YYYY-NNN"
renar-version: "1.1"
senar-version: "1.0"
level: RENAR-2
assessment-date: "2026-05-22"
assessment-mode: self          # self | third-party (§13.4.2)
next-assessment-due: "2026-08-22"

mandatory-clauses-confirmed:
  sot-inversion: true
  substrate-v1-v6: { v1: true, v2: true, v3: true, v4: true, v5: true, v6: true }
  adapt-per-tz: true
  spec-types-closed-list: true
  tc-pos-neg-pairing: true
  quality-gates-closed-list: true
  closed-lists-backward-findings: true

quality-gates:
  qg-0: required
  qg-1: required
  qg-2: required
  qg-3: declared
  qg-4: absent

external-claims:
  - standard: "ISO/IEC/IEEE 29148:2018"
    clause-ref: "§14.4.2"
    claim: "partial"           # full | partial | aligned

substrate-capabilities:
  v1-immutable-history: declared
  v2-atomic-change-unit: declared
  v3-diff-review: declared
  v4-branching: declared
  v5-version-pin: declared
  v6-author-timestamp: declared
  substrate-id: "<substrate-native pointer>"

spec-types-supported: ["SPEC-ARCH", "SPEC-API", "SPEC-DATA", "SPEC-INT",
                       "SPEC-PROC", "SPEC-UI", "SPEC-AI", "SPEC-SEC", "SPEC-OPS",
                       "SPEC-TEST", "SPEC-DOC"]

assessor:
  id: "<V6 author identifier>"
  role: architect              # architect | authorized-role-holder | external-assessor
  signature-ref: "<substrate-native pointer to the signature event>"
```

The full field list is [§13.4.2](../../standard/en/13-conformance.md#13.4.2).

---

## 5. Cadence

- Self-assessment: **quarterly** (default)
- After a delta-TZ that affects mandatory clauses — **out of cycle**
- Conformance-loss triggers — [§13.8](../../standard/en/13-conformance.md#13.8)

---

*Reference RENAR 1.1 — renar.tech*
