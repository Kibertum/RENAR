---
title: "Roles"
order: 5
lang: en
---
# 05. Roles

> **Part of the RENAR Standard v1.1** · [← Table of contents](README.md)

## 5.1 Who is responsible for what

RENAR does not set up its own role hierarchy — it inherits the five base roles from SENAR §4 and adds specializations for working with requirements on top of them: who owns each artifact (ADAPT, BR, SR, SPEC, TR, TC) and who is responsible for each Quality Gate. This is also where the rule for which the chapter largely exists lives: the **ACTZ dual signature** (client + implementer), without which a client decision is not an obligation. ADAPT, by contrast, is signed by the Architect alone — it is an internal interpretation.

The chapter does **not** govern the substrate-native mechanisms for implementing role checks (substrate-native ACL, V6 author identifiers, substrate-native signing events) — that is the domain of [chapter 3](03-substrate-versioning.md) (substrate capabilities) and `guide/03-tool-guide-*.md` (substrate-specific tooling).

---

## 5.2 Base roles (SENAR §4)

### 5.2.1 Closed list

RENAR references the five base roles of SENAR §4 as the Source of Truth. The list is closed on the SENAR side; RENAR neither adds nor removes roles.

| Role | Brief semantics (for the RENAR context) |
|---|---|
| **Supervisor** | The person who initiates and oversees the work of the AI agent; in RENAR, the typical customer for requirements-elicitation, draft-generation, and ADAPT-review tasks. |
| **AI agent** | A software participant that performs the generation and maintenance of requirement artifacts (Forward interpretation of ADAPT, BR/SR/TR/SPEC/TC, updating graph links on changes). In RENAR — the **regular primary author** of artifacts ([§0.2.1](00-introduction.md#0.2.1)); includes the primary generator and the adversarial critic. The AI agent is **not** an owner ([§5.3](#53-renar-specializations-responsibility-for-artifacts)) and does **not** place signatures ([§5.5](#55-actz-dual-signature)). |
| **Architect / Tech Lead** | The person normatively accountable for technical decisions and the approval of requirement artifacts (QG-0 [§10.3.1](10-lifecycle-qg.md#10.3.1)); the signing party of ADAPT (§7.5) and the implementer-side party of the ACTZ dual signature (§5.5). The Source of Truth is the SENAR §4 role "Architect / Tech Lead"; this EN edition uses the short name **Architect** (the RU corpus renders it «Архитектор»). |
| **Reviewer** | A person or AI agent performing the adversarial review of artifacts; in RENAR — a mandatory role for QG-0 ([§10.3.1](10-lifecycle-qg.md#10.3.1)). |
| **Stakeholder** | An external interested party — the client, an authorized client representative, a product owner, an end-user representative; the source of the TZ and the client-side signing party: ACTZ ([§7.13](07-adapt.md#7.13)) and QG-4 ([§10.4.2](10-lifecycle-qg.md#10.4.2)). |

The full definition of each role's semantics — duties, constraints, interactions — is set out in SENAR §4 and applies to RENAR unchanged.

> **Naming of the Architect role.** Everywhere below in the standard, guide, and appendices, the SENAR role "Architect / Tech Lead" is denoted by the short name **Architect** (for example, "the Architect confirmed" in [§8.3](08-specifications.md#8.3), the dual signature in [§5.5](#55-actz-dual-signature)). This is a short form, not a separate role: a conformance manifest MUST use the normative SENAR name ([§5.2.2](#522-no-override)).

> **Naming of the Stakeholder role.** Everywhere below in the standard, guide, and appendices, the SENAR role "Stakeholder" is used directly as the role name (for example, "an authorized stakeholder" in [§5.3.6](#536-owner-of-the-accepted-gate-qg-4), the client-side signing party in [§5.5](#55-actz-dual-signature)). It is the normative SENAR name; a conformance manifest MUST use it verbatim ([§5.2.2](#522-no-override)).

### 5.2.2 No override

An implementation MUST NOT:

- Rename the SENAR base roles in the normative part of the conformance manifest ([§13.4](13-conformance.md#13.4)).
- Merge two base roles into one in a way that removes the possibility of independent signing (for example, merging the Architect and the Stakeholder makes the ACTZ dual signature §5.5 impossible and is **not permitted** as an imitation of two parties; a project without a second party moves into the first-party confirmation kind, [§1.4.4](01-scope.md#1.4.4), where the ACTZ has no subject — [§5.5.5](#555-combining-roles)).
- Add new base roles outside SENAR §4 without the formal SENAR Standard change procedure.

Project-local **aliases** (for example, a local name "Lead Engineer" as a synonym for the SENAR Architect / Tech Lead) are permitted in communication, but the conformance manifest ([§13.4](13-conformance.md#13.4)) MUST use the normative role names of SENAR §4.

---

## 5.3 RENAR specializations: responsibility for artifacts

Each artifact type has a normatively fixed owner — the role responsible for initiating the artifact, its substantive integrity, and its one-click promote through QG-0 ([§10.3.1](10-lifecycle-qg.md#10.3.1)).

| § | Artifact | Owner | Responsibility type | QG participants |
|---|---|---|---|---|
| 5.3.1 | **ADAPT** | Architect (implementer) | Single — an internal interpretation | QG-0: Architect; QG-3 (opt.): the Architect's signature ([§7.5](07-adapt.md#7.5)) |
| 5.3.1bis | **ACTZ** | Architect (implementer) + Client representative | Joint — both signatures REQUIRED | Signing ([§5.5](#55-actz-dual-signature)) |
| 5.3.2 | **BR / SR** | Architect | Single (Accountable); AI agent = Responsible primary generator | QG-0: Architect or authorized role-holder ([§5.4](#54-authorized-role-holder)); QG-2: automated runner; QG-4 (opt.): Stakeholder |
| 5.3.3 | **SPEC-\*** | Architect + domain technical lead | Joint by SPEC type: Architect — overall; domain TL — expertise | QG-0: Architect or authorized role-holder |
| 5.3.4 | **TR** | Architect (approval) + Engineer (execution) | Split | QG-0: Architect; QG-2: Engineer + runner |
| 5.3.5 | **TC** | Engineer + QA | Joint — Engineer authorship, QA pos/neg pairing ([§9.7](09-test-cases.md#9.7)) | QG-1: Engineer/QA + runner; QG-2: runner with V5 pin |
| 5.3.5bis | **AT** | Architect (implementer) | Split: authorship is isolated ([§9.19.2](09-test-cases.md#9.19)) — an isolated AI agent on a different model generates it; the environment binding (`environment-ref`) and the run are handled on the implementer side | The AT release acceptance gate ([§10.4.3](10-lifecycle-qg.md#10.4.3)): an automated runner |
| 5.3.6 | **QG-4 accepted** | Stakeholder (Client + Product Owner) | Single — Architect not sufficient | Only if QG-4 is in the manifest ([§10.4.2](10-lifecycle-qg.md#10.4.2)) |

### 5.3.1 Owner of ADAPT

**ACTZ** is the only RENAR artifact with **mandatory** joint responsibility: a decision does not become an obligation without the signatures of both parties ([§7.13](07-adapt.md#7.13)) — a normative safeguard against the unilateral fixing of obligations.

**ADAPT** is an artifact of the Architect's sole responsibility ([§7.5](07-adapt.md#7.5)): it is an internal interpretation, and the client does not confirm what they do not read. Protection against a unilateral interpretation of the TZ is provided not by a client signature on the ADAPT, but by the fact that **every finding MUST be closed by a signed ACTZ** ([§7.4.5](07-adapt.md#7.4.5)): an interpretation cannot rest on a decision the client never made.

### 5.3.2 Owner of BR / SR

The Architect is normatively accountable for the ADAPT → BR → SR decomposition and for the consistency of the requirements tree. The AI agent is the regular primary generator of draft BR/SR ([§0.2.1](00-introduction.md#0.2.1), [§5.2.1](#521-closed-list)), but **not** the owner: the owner is always a human or an authorized role-holder ([§5.4](#54-authorized-role-holder)). In RACI: the AI agent is **Responsible** (executes), the Architect is **Accountable** (answers for the result). A manifest's attempt to declare the AI agent as Accountable is non-conformant ([§13.3](13-conformance.md#13.3)).

### 5.3.3 Owner of SPEC-\*

The domain technical lead is the normative term for expertise on a specific SPEC type ([§8.3](08-specifications.md#8.3)): ARCH (system architect), API (API designer), DATA (data architect), INT (integration lead), PROC (process owner), UI (UX lead), AI (ML lead), SEC (security lead), OPS (operations lead), TEST (test-environment lead), DOC (documentation lead). In small projects one person MAY combine domain TL roles; merging the Architect + all domain TLs into one person is permitted when declared in the manifest ([§13.4](13-conformance.md#13.4)).

### 5.3.4 Owner of TR

TR has split responsibility: task approval rests with the Architect/authorized role-holder, execution with the Engineer. The Engineer cannot approve their own TR (a violation of [§10.2.2](10-lifecycle-qg.md#10.2.2)).

### 5.3.5 Owner of TC

The AI agent is permitted as the primary generator of TC, but MUST pass a QA check before the implementation is admitted to runs (QG-1, [§10.3.2](10-lifecycle-qg.md#10.3.2)). In projects without an explicit QA role, the QA participant becomes an engineer other than the author of the TC.

### 5.3.6 Owner of the accepted gate (QG-4)

QG-4 is the only gate for which the Architect is **not** a sufficient participant. Confirming a post-release business outcome requires an authorized stakeholder (Client + Product Owner). When QG-4 is absent from the manifest, the gate does not apply, and the Client/Product Owner role is not governed at this lifecycle stage.

### 5.3.7 Closed-list policy for owner assignments

The list §5.3.1–§5.3.6 is closed at v1. Project-local extensions (a new owner for a new type; reassigning owner authority between SENAR §4 roles) are **not permitted** without the formal standard change procedure ([§13.9](13-conformance.md#13.9)).

**Negative scenario:** an attempt to declare that an ACTZ is signed unilaterally (without the Client representative) is a violation of §5.3.1 and [§13.3](13-conformance.md#13.3); the manifest is non-conformant. Conversely: requiring a client signature on an ADAPT is redundant and is not part of base conformance — it is a `declared-stricter` extension ([§1.7.2](01-scope.md#1.7.2)).

---

## 5.4 Authorized role-holder

### 5.4.1 Normative definition

An **authorized role-holder** is a person with explicitly delegated Architect authority for a specific scope (one artifact, one artifact type, or the whole project). The delegation is recorded substrate-natively (V6 author identifier + ACL [§3.3.6](03-substrate-versioning.md#3.3.6)); the concrete mechanism is substrate-specific. "Authorized role-holder" is a normative term, identical in application across [§10.2.2](10-lifecycle-qg.md#10.2.2), [§10.3.1](10-lifecycle-qg.md#10.3.1), [§13.5](13-conformance.md#13.5).

### 5.4.2 Permitted scenarios

A participant for the **QG-0 Approval Gate** ([§10.2.2](10-lifecycle-qg.md#10.2.2)) — BR/SR/SPEC/TR/ADAPT (on the Architect side)/TC; **self-assessment** ([§13.5](13-conformance.md#13.5)) — an assessor with the role `authorized-role-holder` in the manifest.

### 5.4.3 Prohibited scenarios

An authorized role-holder does **not** replace the `client-signature` in ACTZ (the dual signature requires a client-side stakeholder, not an implementer-side authorized role-holder); does **not** replace the Stakeholder in QG-4 ([§10.4.2](10-lifecycle-qg.md#10.4.2)); does **not** replace the external assessor in a third-party independent assessment ([§13.6](13-conformance.md#13.6)).

### 5.4.4 Substrate-side authorization

The delegation MUST be recorded natively via V6 ([§3.3.6](03-substrate-versioning.md#3.3.6)): a substrate with ACL — through a role-based access list; a substrate with capability tokens — through issuing a delegating token; a substrate without explicit authorization — through a declaration of delegation in a native project artifact (a separate file with the Architect's signature). Substrate-specific details — `guide/03-tool-guide-*.md`.

---

## 5.5 ACTZ dual signature

### 5.5.1 Normative rule

The dual signature sits on the **ACTZ** — the TZ clarification protocol ([§7.13](07-adapt.md#7.13)) — not on the ADAPT. An ACTZ transitions to `signed` **only** when both signatures are present:

1. **`client-signature`** — from the Client representative (a stakeholder authorized to sign on behalf of the client).
2. **`vendor-signature`** — from the implementer.

**An ADAPT is signed by the Architect alone** ([§7.5](07-adapt.md#7.5)): it is an internal interpretation artifact, and the client never sees it.

The boundary is drawn by audience: everything put to the client and approved is an obligation (ACTZ, dual signature); everything not put to the client is an interpretation (ADAPT, the Architect's signature). A client signature on an ADAPT would be a fiction — the client would be signing an engineering document they neither read nor can assess.

The structure of the signature fields is fixed in [§7.13.3](07-adapt.md#7.13); each signature contains `signed-by`, `role`, `signed-at`, `signature-ref` (a substrate-native pointer to the signing event).

### 5.5.2 Semantics of each signature

| Signature | Confirms |
|---|---|
| **`client-signature`** (on ACTZ) | The decisions put forward for approval are accepted; the wording of the obligations ("this is what will be built") matches the client's intent; the decisions are final. |
| **`vendor-signature`** (on ACTZ) | The implementer accepts the decisions as obligations and undertakes to implement them. |
| **`architect-signature`** (on ADAPT) | The Forward interpretation is technically feasible; all backward findings are in the `resolved` state and carry a `decided-in` pointer to a clause of a signed ACTZ ([§7.4.5](07-adapt.md#7.4.5)); the decomposition into BR / SR / SPEC is safe to launch. |

The signatures are **not** interchangeable. The Architect does not confirm client semantics; the Client does not confirm technical feasibility and does not take on responsibility for the interpretation.

### 5.5.3 Negative scenarios

- **One signature missing on the ACTZ**: the protocol does not transition to `signed`; the decisions it carries are not obligations; findings that reference it through `decided-in` cannot be `resolved`, and the ADAPT does not transition to `approved` ([§7.4.5](07-adapt.md#7.4.5)).
- **One person in both ACTZ signatures**: an implementation where `client-signature.signed-by == vendor-signature.signed-by` violates §5.5.1 (two parties require two independent persons). The internal-product scenario without an independent client representative is outside the primary scope ([§1.5.4](01-scope.md#1.5.4)).
- **A client signature on an ADAPT**: redundant and normatively **not required**. An implementation that demands it is a `declared-stricter` extension ([§1.7.2](01-scope.md#1.7.2)), not base-level conformance.
- **Authorized role-holder instead of the Architect**: permitted ([§5.4.2](#542-permitted-scenarios)); the signature is recorded with `role: authorized-role-holder`.
- **Authorized role-holder instead of the Client**: **not permitted** ([§5.4.3](#543-prohibited-scenarios)); the `client-signature` MUST be from a client-side stakeholder.
- **A signed decision not reflected in any interpretation**: the ACTZ is signed, but no ADAPT carries its decisions — the obligation exists outside the requirements; **fatal** ([§7.4.5](07-adapt.md#7.4.5)).

### 5.5.4 Relationship to other gates

The ACTZ dual signature is the only normative case of a dual signature in RENAR. The other gates ([§10.3](10-lifecycle-qg.md#10.3)) are performed by a single participant (Architect, runner, stakeholder). This is a deliberate decision: the ACTZ is the point of agreement with the client, the other gates are internal implementation checks.

---

### 5.5.5 Combining roles

One person MAY combine roles — up to all the roles of chapter 5 in an organization without a second party ([§1.4.4](01-scope.md#1.4.4)). The standard neither forbids nor compensates this: when roles are combined, adversarial checking by people is absent, and this is an accepted trade-off that the conformance claim MUST state (`confirmation: first-party`, [§13.4.2](13-conformance.md#13.4.2)). As separate people appear, they take their roles, and adversarial checking returns by itself.

Two restrictions are not lifted by combining:

1. **The ACTZ dual signature is not imitated** by one person (§5.5.1, §5.5.3). Without a second party the ACTZ has no subject — it is not signed unilaterally; decisions on the concept are recorded by a revised concept signed by the responsible person.
2. **Execution and acceptance of one task are not combined**: the author of an implementation does not accept their own task and does not write the TC that checks it (P8, [§9.18](09-test-cases.md#9.18); AT authorship isolation, [§9.19.2](09-test-cases.md#9.19)). This is a restriction on the task, not on the person: one person accepts others' tasks and executes their own.

---

## 5.6 RACI matrix

The matrix fixes Responsible / Accountable / Consulted / Informed across RENAR artifact types and canonical gates. Abbreviations: **R** = Responsible (executes), **A** = Accountable (approves / answers for the result), **C** = Consulted (MUST be informed before the decision), **I** = Informed (notified after the decision).

### 5.6.1 Artifact matrix

| Activity | AI agent | Architect | Domain technical lead | Engineer | QA | Reviewer | Client | PO |
|---|---|---|---|---|---|---|---|---|
| TZ import | R | A | — | — | — | C (adversarial) | C | I |
| ADAPT creation (forward + backward) | R | A | C | — | — | C (adversarial) | C | I |
| ADAPT approval (the Architect's signature) | — | **A (`architect-signature`)** | — | — | — | C (adversarial) | I | I |
| ACTZ signing (dual signature) | — | **A (`vendor-signature`)** | — | — | — | — | **A (`client-signature`)** | I |
| ADAPT → BR decomposition | R | A | C | — | — | C | I | C |
| BR → SR decomposition | R | A | C | — | — | C | I | C |
| SPEC-\* creation | R | A | **R/A (per type)** | — | — | C | I | I |
| TR creation | R | A | C | C | — | — | I | I |
| TR implementation (execution) | — | C | C | R | — | — | I | I |
| TC creation | R | C | C | R/A | A | — | I | I |
| AT generation from the effective TZ | **R (isolated agent, different model)** | A | — | — | — | — | I | I |
| Binding an AT to its environment (`environment-ref`) | — | **A** | — | R | — | — | I | I |
| Adversarial review of an artifact | R (AI critic) | A | — | — | — | R | — | I |

### 5.6.2 Quality Gates matrix

Conformant to the normatively fixed participant table [§10.2.2](10-lifecycle-qg.md#10.2.2).

| Gate | Artifacts | Responsible | Accountable | Consulted | Informed |
|---|---|---|---|---|---|
| **QG-0 Approval Gate** | The description set version (BR, SR, SPEC, TC norms — together), TR, ADAPT (on the Architect side) | Architect or authorized role-holder | Architect | AI critic (Reviewer) | Team |
| **QG-1 Implementation Gate** | The TC implementation (not admitted → admitted, [§10.9](10-lifecycle-qg.md#10.9)) | Engineer / QA | Engineer | Automated runner | Team |
| **QG-2 Verification Gate** | The set version on a product version (the verification record, [§10.5.4](10-lifecycle-qg.md#10.5.4)); TR | Automated runner (V5 + V6) | Architect | — | Team |
| **QG-3 Architecture Gate** (optional, ADAPT) | ADAPT (`answered → approved`) | The Architect's signature; all findings closed by signed ACTZ | Architect | AI critic | Team |
| **QG-4 Acceptance Gate** (optional, BR) | Acceptance of a BR of the set version (`accepted-outcomes[]` of the version record, [§10.4.2](10-lifecycle-qg.md#10.4.2)) | Stakeholder (Client + PO) | PO | Architect | Team |
| **AT release acceptance gate** (mandatory, not a QG — [§10.4.3](10-lifecycle-qg.md#10.4.3)) | AT (all in `passing`); the product | Automated runner | Architect | — | Team, Client |

### 5.6.3 Closed list of roles in RACI

The list of role columns of the matrix §5.6.1 / §5.6.2 is closed at RENAR v1:

**AI agent**, **Architect**, **Domain technical lead** (per SPEC type), **Engineer**, **QA**, **Reviewer** (adversarial), **Client** (authorized Stakeholder), **Product Owner**.

Project-local roles (for example, "Release Manager", "Compliance Officer") are permitted as **Informed** or **Consulted** in a local practical-RACI implementation, but do **not** appear as **Responsible** or **Accountable** in the normative conformance matrix — otherwise the manifest is non-conformant ([§13.3](13-conformance.md#13.3)).

---

## 5.7 Closed-list policy (closed list)

### 5.7.1 What is fixed at v1

The SENAR §4 base roles ([§5.2.1](#521-closed-list)) are closed on the SENAR side; the owner assignment [§5.3.1–§5.3.6](#53-renar-specializations-responsibility-for-artifacts) is closed; the authorized role-holder ([§5.4](#54-authorized-role-holder)) is closed; the ACTZ dual-signature rule ([§5.5.1](#551-normative-rule)) is closed; the RACI role columns ([§5.6.3](#563-closed-list-of-roles-in-raci)) are closed.

### 5.7.2 Declared-stricter is permitted

An implementation MAY **tighten** requirements, declaring this explicitly in the manifest ([§13.4](13-conformance.md#13.4)) with the `declared-stricter` marker ([§10.10.2](10-lifecycle-qg.md#10.10.2)): require a dual signature for BR/SR (not only for ACTZ); require a client signature on an ADAPT; require an external adversarial reviewer at QG-0 for SPEC-SEC and SPEC-AI; prohibit combining the Architect and a domain technical lead.

### 5.7.3 Declared-weaker is prohibited

An implementation MUST NOT declare an ACTZ signed with a single signature; the TC author engineer approving without a QA participant; the implementer in the QG-4 participant role instead of the Stakeholder. A manifest that is declared-weaker relative to §5 is non-conformant ([§13.8](13-conformance.md#13.8) loss of conformance).

### 5.7.4 Path for extensions

Adding a new role (§5.2, §5.3, §5.6.3) or owner combination (§5.3.7) — only through the formal standard change procedure ([§13.9](13-conformance.md#13.9)): research draft → public review → minor-version bump.

---

## 5.8 Cross-references

| Source | Application |
|---|---|
| SENAR §4 | Closed list of the 5 base roles; semantics and duties (normative source) |
| [§7.5 ADAPT approval schema](07-adapt.md#7.5) | Structure of the `architect-signature` field; `client-signature` and `vendor-signature` sit on the ACTZ ([§7.13.3](07-adapt.md#7.13)) |
| [§10.2.2 QG actor table](10-lifecycle-qg.md#10.2.2) | Normative gate → participant correspondence; aligned with §5.6.2 |
| [§10.3.1 QG-0 Approval Gate](10-lifecycle-qg.md#10.3.1) | Architect or authorized role-holder as the §5.4 participant |
| [§10.4.1 QG-3 Architecture Gate](10-lifecycle-qg.md#10.4.1) | The Architect's signature on an ADAPT ([§7.5](07-adapt.md#7.5)); the dual signature (§5.5) sits on the ACTZ |
| [§10.4.2 QG-4 Acceptance Gate](10-lifecycle-qg.md#10.4.2) | Stakeholder (Client + PO) — §5.3.6 |
| [§3.3.6 V6 Author + timestamp](03-substrate-versioning.md#3.3.6) | Substrate-native authorship recording for role-holder delegation §5.4.4 |
| [§13.4 Conformance manifest](13-conformance.md#13.4) | Use of the normative role names §5.2.2 |
| [§13.5 Self-assessment](13-conformance.md#13.5) | Assessor role enum (architect / authorized-role-holder / external-assessor) — §5.4 anchor |
| `core/renar-core.md` | Conceptual overview of the standard for the human reader (non-normative); roles and signatures are fully governed here, §5 |
| `guide/03-tool-guide-*.md` | Substrate-specific delegation mechanisms (§5.4.4) and signature implementations |

---

**[← Previous: 04. Terms and definitions](04-terms.md)** · **[Table of contents](README.md)** · **[Next: 06. Requirements hierarchy →](06-requirements-hierarchy.md)**
