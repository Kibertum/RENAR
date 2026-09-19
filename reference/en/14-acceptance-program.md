---
title: "Recommended form of the acceptance-test programme"
description: "Informative appendix: the human-readable acceptance programme as the client-facing projection of the ATs — fields, coverage completeness, verdict by defect class."
order: 14
lang: en
version: "1.0"
---

# Recommended form of the acceptance-test programme

> **Status:** informative. The recommendation concerns the **human-readable document produced for the client**. The structure of the AT artifact itself is normative and is set by [`standard/09 §9.19`](../../standard/en/09-test-cases.md#9.19); where a skeleton and a chapter disagree, **the chapter prevails**.
>
> This appendix creates no obligations and extends no closed list ([§1.7.5](../../standard/en/01-scope.md#1.7.5)).

---

## 14.1 What this document is

The acceptance-test programme is the scenario document produced for the client at handover. It is the **client-facing projection of the set of ATs**: the machine executes the acceptance tests, the human reads the programme. That split continues a general principle of the standard: the client sees the human-readable, the machine works with the canonical.

The programme is **derived from the effective TZ** ([§7.14](../../standard/en/07-adapt.md#7.14)) — the initial TZ with its annexes plus every signed ACTZ — and not from the initial TZ. The contract lives incrementally, and a system cannot be produced against a stale revision: ATs are regenerated before every trial from the revision in force ([§9.19.4](../../standard/en/09-test-cases.md#9.19)), and the programme is regenerated after them.

The programme is generated, not hand-written: everything in it already exists in the ATs.

---

## 14.2 Fields of the programme

| Field | Content | Source in the standard |
|---|---|---|
| `id` | The stable identifier of the contract clause (`UC-NNN`, `FR-NNN`) or of an ACTZ clause. Not invented — taken from the document | [`reference/13 §13.3`](13-tz-form.md#13.3) |
| `source` | Where the clause comes from: `TZ §N` or `ACTZ-NNN §M`, including subsystem TZs | `verifies[]`, [§9.19.3](../../standard/en/09-test-cases.md#9.19) |
| `tz_text` | The **verbatim quotation** of the effective-TZ or ACTZ clause | a mandatory body section of an AT, [§9.19.3](../../standard/en/09-test-cases.md#9.19) |
| `steps` | Verification steps as active commands: "open…", "click…", "confirm…" | AT body |
| `expected` | The expected result in one sentence | AT body |

An example of a single programme entry:

```yaml
id: FR-014
source: "TZ §5.14"
tz_text: >
  The system shall reject posting of a document when the sum of the
  line items diverges from the sum stated in the header.
steps:
  - "Open a document whose header sum diverges from its line items."
  - "Click «Post»."
  - "Confirm the document is not posted and the reason for refusal is shown."
expected: "The document remains a draft, and the reason for refusal is stated explicitly."
```

**The verbatim quotation is the key field.** The client sees the text of the contract and the verification side by side: what is produced is not a paraphrase of the clause but the clause itself. There is nothing left to argue about regarding "what was meant" — which is why the quotation was taken into the normative part of the AT rather than left as a recommendation.

---

## 14.3 Coverage completeness

**Every** contract clause enters the programme — with no omissions and with no merging of several clauses into one. Completeness is counted over the sections of the effective TZ and the clauses of the signed ACTZ; the machine-side counterpart of this requirement is the `ACCEPTANCE.md` report ([§9.19.6](../../standard/en/09-test-cases.md#9.19)).

Merging clauses looks like a saving, but it removes addressability: the failure of a merged check cannot be attributed to a specific contract clause, and the verdict once again becomes a matter of discussion.

The programme may be agreed with the client through a further ACTZ ([§7.13](../../standard/en/07-adapt.md#7.13)) — the programme then becomes an approved commitment in its own right, and the order of handover is known to both parties in advance.

---

## 14.4 The verdict by defect class

The acceptance verdict is best computed **by defect class rather than as a binary**. The mechanism: every failed programme entry is classified, and the decision to sign the acceptance certificate is taken according to quality corridors declared in advance.

A typical gradation has four levels:

| Level | Name | Description |
|---|---|---|
| 1 | Critical | The system is inoperable, data is lost or corrupted, security is breached |
| 2 | Major | Key functionality is unavailable or works incorrectly, with no workaround |
| 3 | Moderate | Secondary functionality is incorrect, a workaround exists |
| 4 | Minor | Visual, textual, and other defects that do not affect operation |

> **The gradation is given as an example, not as a norm.** The standard establishes **neither** numeric thresholds **nor** the number of levels itself. The quality corridors — which levels block signature of the certificate, how many defects of each level may be carried over — are declared by the **vendor** in the contract or in the manifest, based on their internal quality regulations. A bank and an in-house department will have different thresholds, and both will be right: this is commercial policy, not a property of requirements engineering.

What is recommended regardless of the thresholds chosen: a defect that blocks signature is recorded with a reference to the contract clause (`id`, `tz_text`), a description of the divergence, and the expected result; non-blocking defects are recorded, and the plan for fixing them is agreed with the client before the certificate is signed.

The value of the mechanism lies in the rules of the game being declared before the trials. Without it, every defect found at acceptance becomes a matter of bargaining.

---

## 14.5 The internal gate and contractual acceptance are not the same thing

Quality corridors describe the behaviour of the parties **at acceptance** and do **not** weaken the vendor's internal gate.

| Contour | Rule | Who finds the defects |
|---|---|---|
| Internal, before handover | Every AT in `passing` status — strictly, with no corridors ([§10.4.3](../../standard/en/10-lifecycle-qg.md#10.4)) | The vendor |
| Contractual acceptance | The verdict follows the vendor's quality corridors | The client |

A vendor does not produce a system with known failures — otherwise the corridors turn into permission to hand over unfinished work. But at a real acceptance the client finds their own defects, and the classification supplies rules agreed in advance for that situation.

---

## 14.6 The defect level as diagnostics

The defect level correlates with the routing of AT and TC failures ([§9.19.5](../../standard/en/09-test-cases.md#9.19)):

- a level 1–2 defect with green TCs is almost always an **interpretation error**: the system conforms to ADAPT but not to the contract. It is fixed in ADAPT and ACTZ, not in the code;
- level 3–4 defects more often point to under-specified detail — candidates for a further ACTZ.

---

## 14.7 Relation to other documents

| Document | Relation |
|---|---|
| [`standard/09 §9.19`](../../standard/en/09-test-cases.md#9.19) | AT — the normative structure of the artifact the programme is derived from |
| [`standard/07 §7.13`](../../standard/en/07-adapt.md#7.13) | ACTZ — the protocol for clarifying the TZ and agreeing the programme |
| [`standard/07 §7.14`](../../standard/en/07-adapt.md#7.14) | The effective TZ — the benchmark the trials run against |
| [`standard/10 §10.4`](../../standard/en/10-lifecycle-qg.md#10.4) | The internal release gate |
| [`reference/13`](13-tz-form.md) | Recommended TZ form — the source of stable clause identifiers |

[← Back to the reference](README.md)
