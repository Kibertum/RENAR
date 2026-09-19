---
title: "Examples library"
description: "Index of RENAR E2E examples: quickstart, login, GDPR export, API webhook, SPEC-AI eval, delta-TZ."
order: 9
lang: en
version: "1.0"
---

# 09. Examples library

> Eight scenarios (E1–E8): six in-doc examples (E3–E8) plus two walkthroughs (E1–E2). Each in-doc example follows the chain **TZ → ADAPT → BR/SR → SPEC → TC → QG** in full or in part.

| # | Scenario | Document | Audience | Focus |
|---|---|---|---|---|
| E1 | Email/password sign-up (minimal) | [00-quickstart](00-quickstart.md) | Beginner | 30 min, minimal artifacts |
| E2 | Login + profile + permissions (full) | [01-walkthrough](01-walkthrough.md) | Tech Lead, QA | Full lifecycle, AI generation of TC |
| E3 | Personal-data export (GDPR / FZ-152) | [§2](#2-e3--personal-data-export-gdpr-art-15--fz-152) | Legal, PM, Architect | Compliance, SPEC-DATA/SEC |
| E4 | Webhook idempotency (REST API) | [§3](#3-e4--webhook-idempotency-spec-api) | Backend, Architect | `tc-type: contract`, SPEC-API |
| E5 | RAG assistant (SPEC-AI eval) | [§4](#4-e5--rag-assistant-spec-ai) | AI engineer | `tc-type: eval`, judge isolation |
| E6 | Delta-TZ: scope dispute | [§5](#5-e6--delta-tz-scope-dispute) | PM, Client, Architect | ADAPT backward, immutable TZ |
| E7 | A subsystem as a standalone product | [§6](#6-e7--a-subsystem-as-a-standalone-product-implements-edge) | Architect | The `implements[]` edge, §6.8.2 |
| E8 | The interface part of E3: an example to the end | [§7](#7-e8--the-interface-part-of-e3-an-example-carried-through-to-the-end) | UI/UX, Architect, QA | Filled SR / SPEC-UI / `ux`-TC bodies |

---

## 2. E3 — Personal-data export (GDPR Art. 15 / FZ-152)

**Context:** the SaaS "AcmeCRM" stores customer PII. The regulator and the contract require a machine-readable export to be delivered on a data-subject request within ≤30 days.

### 2.1 TZ fragment (immutable)

```markdown
# TZ-2026-002 — Data subject export

§3.1 A data subject can request a full export of their PII via the UI "Privacy → Export my data".
§4.2 Format: JSON + CSV bundle, zip, SHA-256 checksum.
§4.3 SLA: the download link is ready within ≤ 72 hours of verified identity.
§4.4 The export includes: profile, activity log for 24 months, marketing consents.
§4.5 The request is logged; a repeat export — no more than once every 30 days without an administrator override.
```

### 2.2 ADAPT (abridged)

```yaml
id: ADAPT-002
type: ADAPT
status: approved
source-tz: { id: TZ-2026-002, signed-date: "2026-05-01" }
```

**Forward §3:** the export is asynchronous (job queue); identity — via the existing MFA flow.

**Backward (resolved):**

| # | Finding | Resolution |
|---|---|---|
| B-01 | "24 months of activity" — calendar or rolling? | Rolling 730 days |
| B-02 | Admin override — who approves? | Role `privacy-officer` + an audit log |

### 2.3 BR + SR

```yaml
# br/BR-02-data-subject-export.md
id: BR-02
type: BR
title: "The data subject receives a machine-readable PII export"
source: { adapt: ADAPT-002, adapt-section: "Forward §3" }
compliance:
  - { standard: GDPR, control: "Art. 15" }
  - { standard: "FZ-152", control: "art. 14" }
```

```yaml
# sr/SR-08-export-request.md
id: SR-08
type: SR
parent: { id: BR-02 }
title: "An authenticated user initiates an export job"
constrained-by: ["SPEC-API-04", "SPEC-DATA-02", "SPEC-SEC-03"]
```

```yaml
# sr/SR-09-export-delivery.md
id: SR-09
type: SR
parent: { id: BR-02 }
title: "The user downloads the ready bundle via a time-limited signed URL"
constrained-by: ["SPEC-API-04", "SPEC-SEC-03"]
```

### 2.4 SPEC (excerpts)

**SPEC-DATA-02** — the export-bundle schema (tables: `users`, `activity_events`, `marketing_consents`).

**SPEC-SEC-03** — signed URL TTL 24h; rate limit 1 export / 30 days; role `privacy-officer` for the override.

**SPEC-API-04** — `POST /v1/privacy/export`, `GET /v1/privacy/export/{job_id}`.

### 2.5 TC (pos/neg for SR-08)

```yaml
---
id: TC-080
title: "An export job is created for a verified user"
type: TC
tc-type: system
automation: { status: automated, set-version: "1.0" }
verifies:
  - id: SR-08
negative: false
---
## When
POST /v1/privacy/export (session: verified-user)
## Then
- 202 + job_id; status pending; audit event recorded
```

```yaml
---
id: TC-081
title: "Export rejected without an MFA-verified session"
type: TC
tc-type: security
automation: { status: automated, set-version: "1.0" }
verifies:
  - id: SR-08
negative: true
---
## When
POST /v1/privacy/export (session: password-only, no MFA)
## Then
- 403; job not created; security audit event
```

Analogous pairs for SR-09 (signed URL valid / expired).

### 2.6 Quality gates

| Quality gate | Evidence |
|---|---|
| **QG-ADAPT-approve** (by meaning, near the "architecture gate": `QG-3`) | ADAPT-002 in `approved`, backward findings closed |
| **QG-2 Verification Gate** | `TC-080`…`081` pass; the `set-version` in `last-run` matches (`1.0`) |

### 2.7 What next

- The full scenario under `git` — [03-tool-guide-git](03-tool-guide-git.md)
- Compliance mapping — [06-compliance](06-compliance.md)
- Conformance manifest — [reference/08](../../reference/en/08-conformance-self-assessment.md)
- The interface part of this same example, carried through to filled bodies — [§7 E8](#7-e8--the-interface-part-of-e3-an-example-carried-through-to-the-end)

---

## 3. E4 — Webhook idempotency (SPEC-API)

**Context:** a payment provider sends `POST /webhooks/payment` with an `Idempotency-Key`. Duplicates MUST NOT create a double charge.

**TZ (fragment):** "A repeat webhook with the same key within 24h returns the same `payment_id`, HTTP 200."

**SR + SPEC:**

```yaml
id: SR-20
type: SR
constrained-by: ["SPEC-API-07"]
```

**SPEC-API-07** — the contract: headers, body schema, response codes 200/400/409.

**TC (contract pair):**

```yaml
id: TC-200
type: TC
tc-type: contract
verifies: [{ id: SR-20 }]
negative: false
```

```yaml
id: TC-201
type: TC
tc-type: contract
verifies: [{ id: SR-20 }]
negative: true
```

Neg: a second key with a different body → 409 Conflict.

**Quality gate `QG-2`:** both `TC`s pass; the `set-version` in `last-run` matches (`1.0`).

---

## 4. E5 — RAG assistant (SPEC-AI)

**Context:** an in-app chat answers from the knowledge base; eval against a golden Q&A set.

**SPEC-AI-02** — model card, fallback, cost cap.

**TC eval (judge ≠ production):**

```yaml
id: TC-300
type: TC
tc-type: eval
verifies: [{ id: SPEC-AI-02 }]
judge:
  vendor: "<vendor>"
  model: "eval-judge-v2"          # ≠ the production model of SPEC-AI-02 (§9.6.2)
baseline:
  artifact: "<golden Q&A dataset>"
  metric-thresholds: {}
negative: false
```

Neg eval: prompt injection in the user message → refusal + audit event (`tc-type: eval`, adversarial negative).

See [standard/09 §9.6.2](../../standard/en/09-test-cases.md), [guide/07 §3.5](07-failure-modes.md#3.5).

---

## 5. E6 — Delta-TZ: scope dispute

**Context:** after the signed TZ the client asks to "add PDF export" — outside the scope of ADAPT-002.

**The correct path to the Source of Truth (SoT):**

1. Do **not** edit the immutable TZ and do **not** silently extend the SR.
2. Draw up a **delta-TZ**; the adversarial reviewer reviews it and issues a verdict ([§7.6.1](../../standard/en/07-adapt.md#7.6)). On a "findings present" verdict a delta-ADAPT `ADAPT-002-delta-1` is created (forward + backward); on "no findings" no delta-ADAPT is created and derived artifacts reference the delta-TZ via `source.tz-section`.
3. Backward finding: "PDF export was not part of Forward §3 of ADAPT-002."
4. The client's decision on the finding is drawn up as an **ACTZ** — a TZ clarification protocol with a dual signature ([standard/07 §7.13](../../standard/en/07-adapt.md#7.13)); the finding receives `decided-in: ACTZ-003 §1`.
5. The delta-ADAPT is approved by the Architect's signature ([§7.5](../../standard/en/07-adapt.md#7.5)) → new SR/SPEC/TC. The effective TZ is recomputed, and the ATs are regenerated from the new revision.

**Anti-pattern:** a direct commit to `sr/SR-08` without a `delta-ref` — drift class 4.11.6 ([standard/04 §4.11](../../standard/en/04-terms.md)).

See [standard/07 §7.6](../../standard/en/07-adapt.md), [guide/02-transition-guide](02-transition-guide.md).

---

## 6. E7 — A subsystem as a standalone product (`implements`-edge)

**Context:** the platform `acme` (a system) consists of the subsystem `acme.notify` — a separate product with its own business owner (Notify Lead), its own team, and its own release cycle. Scenario §6.8.2 of the RENAR Standard.

### 6.1 Artifact hierarchy

```text
acme (system)
├── BR-01 (order intake with AI assistance)               level: system
├── BR-05 (monitoring and alerts for the operations team)  level: system
└── acme.notify (subsystem, standalone product)
    └── BR-01 (multichannel notification delivery)         level: subsystem
         implements:
           - id: BR-01      (elaborates "order intake": notifications on status changes)
             scope: { system: acme }
           - id: BR-05      (elaborates "monitoring": notifications on SLA breaches)
             scope: { system: acme }
         ↓
         SR-01..SR-12 (notify-internal requirements)
         SPEC-INT-01 (integration with acme via the message bus)
         SPEC-API-01 (REST API for notify clients)
```

`acme.notify.BR-01` is the root node of its own requirements tree (`parent` is absent, as for any BR). The link to the system is expressed by a **typed** `implements[]` edge, not a parent-edge.

### 6.2 frontmatter `acme.notify/br/BR-01-multichannel-delivery.md` (fragment)

```yaml
---
id: BR-01
title: "Multichannel notification delivery"
type: BR
level: subsystem
scope:
  system: acme
  subsystem: acme.notify

owner: "Notify Lead (notify-side); Architect (acme-side)"

source:
  adapt: ADAPT-NOTIFY-001
  adapt-section: "Forward §2"
  tz-section: "TZ-NOTIFY §3"

# === implements-edge §6.8.2 ===
implements:
  - id: BR-01
    scope:
      system: acme
    rationale: "ADAPT-NOTIFY-001 §2.1 — notifications on order status change"
  - id: BR-05
    scope:
      system: acme
    rationale: "ADAPT-NOTIFY-001 §2.4 — SLA alerts for operations"

business-context:
  stakeholder: "Notify Lead"
  business-goal: "Timely delivery of notifications to the end-customer and the operations team"
---

# BR-01: Multichannel notification delivery

The notify subsystem delivers notifications …
```

### 6.3 Machine-readable trace chain

```text
TC-NOTIFY-15
  → verifies SR-NOTIFY-08 (rate-limit for the email channel)
        ├─ parent:   acme.notify.BR-01 v1.2
        │              └─ implements: acme.BR-01 v3.0, acme.BR-05 v1.4
        │                              (typed cross-level edge)
        ├─ source.adapt: ADAPT-NOTIFY-001 §Forward §2.2
        └─ constrained-by: SPEC-API-01, SPEC-OPS-01   (scope: acme.notify)
```

On an audit, the full chain is reconstructed from TC-NOTIFY-15: SR → subsystem BR → system BR. Before v1.0 (without the `implements`-edge) the chain broke off at `acme.notify.BR-01` and continued only through the prose of the "Context" section.

### 6.4 Gates on the substrate side

When `acme.notify.BR-01` is approved, the substrate-native hook checks (see [standard/10 §10.11.1](../../standard/en/10-lifecycle-qg.md#10.11.1)):

1. `acme.BR-01` and `acme.BR-05` exist in the substrate by `id + scope.system`.
2. Both target BRs belong to the approved set version of their system.
3. The chain `acme.notify.BR-01 → acme.BR-01 → …` does not form a cycle.
4. `acme.notify.BR-01.level = subsystem` (rule: `implements[]` does not apply at `level: system`).

If any check fails, the approval is blocked. Behavior when `acme.BR-01 = deprecated`: a warning, not fatal (the Architect decides: update `implements` to the current BR or mark `acme.notify.BR-01` as requiring review).

### 6.5 Evolution "module → subsystem" via `implements`

When a module acquires a business owner ([standard/06 §6.9.1](../../standard/en/06-requirements-hierarchy.md#6.9.1)):

1. The business owner is captured via an ADAPT backward finding (category `scope`).
2. After the delta-ADAPT is approved, the module is promoted to a subsystem; a subsystem BR is created.
3. **New step (v1.0+):** the subsystem BR declares `implements[]` on the applicable system BRs. Without this step, the §6.8.2 scenario carries an absence of traceability incompatible with v1.1 conformance.

Anti-pattern: creating `acme.notify.BR-01` with `level: subsystem` but without `implements[]` — formally permitted on v1.0 (recommended), but non-conformant on v1.1+. If the parent system has an approved BR, the absence of `implements[]` MUST be **explicitly justified** in the "Context" section of the BR with a reference to ADAPT§.

See [standard/06 §6.5.2](../../standard/en/06-requirements-hierarchy.md#6.5.2), [§6.8.2](../../standard/en/06-requirements-hierarchy.md#6.8.2), [§6.10.3](../../standard/en/06-requirements-hierarchy.md#6.10.3), [standard/13 §13.3.8](../../standard/en/13-conformance.md#13.3.8).

## 7. E8 — The interface part of E3: an example carried through to the end

> **Informative section.** The example is not normative text and creates no obligations. Where it diverges from the chapters of the standard, the chapters prevail. The normative requirements the example follows are named by reference in every subsection.

**Why it is here.** The other examples in the library stop at frontmatter and excerpts: they show the **shell** of an artifact — identifiers, statuses, graph edges. But the agent deriving the implementation reads not the shell but the **statement**. E8 continues E3 ([§2](#2-e3--personal-data-export-gdpr-art-15--fz-152)) with an interface part and carries through to the end three bodies of which the corpus held not one: an SR body ([standard/06 §6.6.3](../../standard/en/06-requirements-hierarchy.md#6.6.3)), a SPEC-UI body ([standard/08 §8.5.6](../../standard/en/08-specifications.md#8.5.6)), and a pair of `ux`-TC ([standard/09 §9.6.1](../../standard/en/09-test-cases.md#9.6.1)).

**What it proves — and what it does not.** The boundary is better drawn up front, before reading. The example tests the **form** of the mandatory body: that the sections of §8.4.1 and §8.5.6 fill in meaningfully, that the requirement to "cover every action available through the interface" is operationalizable, that a `ux`-TC is written per §9.6.1 without inventing fields. The example does **not** test **volume**: it holds three screens, and on three screens the conclusion "the all-screens requirement is affordable on a real system" cannot be earned.

---

### 7.1 The boundary of "all screens" runs along the Scope of the specification

One particular of §8.5.6 surfaces only when one tries to write the body, and it is worth naming before the example.

The wording of the SPEC-UI mandatory body requires describing "all screens of the system". Read literally, it would mean that one SPEC-UI must describe the interface of the entire system — from sign-in to administration. That is not how it works and not how it was intended: the boundary of what is described is drawn by the **Scope section** of the same mandatory body ([standard/08 §8.4.1](../../standard/en/08-specifications.md#8.4.1), item 2), while "all screens" means **completeness inside the declared Scope, with no selection by significance**.

Both halves matter:

- **Scope may be narrowed** — it is part of the mandatory body and is declared explicitly, so what falls outside it is not lost silently but visible as a boundary.
- **Inside the Scope nothing may be selected out** — this is precisely what the "Coverage completeness" clause of §8.4.1 prohibits: not "describe the important export screens" but "describe all the export screens".

Below, the Scope is declared as the personal-data export interface; it holds three screens, and all three are described.

**Completeness across the system — the screen list.** Scope and §8.4.1 give completeness inside a document; that the sum of all SPEC-UIs covers the whole interface is ensured by the screen list in the mandatory body of `SPEC-ARCH-01` of the E3 system and the coverage rule ([standard/08 §8.5.6.1](../../standard/en/08-specifications.md#8.5.6.1)). `SPEC-ARCH-01` was not shown in §2.4; here is the part of its list that concerns the "Privacy" section:

| Code | Screen | Source | Covering SPEC-UI |
|---|---|---|---|
| S-01 | Export request | `SR-10` | `SPEC-UI-02` |
| S-02 | Export in preparation | `SR-10` | `SPEC-UI-02` |
| S-03 | Request refused | `SR-10` | `SPEC-UI-02` |
| S-04 | Second-factor identity confirmation | `SR-08` | `SPEC-UI-01` |
| S-05 | Granting an override (the `privacy-officer` role) | `SPEC-ARCH-01`, the "privacy-officer workspace" container | — |

The last column is not part of the list but the outcome of the check at the approval of the set version: SPEC-ARCH keeps the list, every SPEC-UI declares its coverage in `screens[]`. S-05 is in the list and no SPEC-UI covers it — a set version with such a list **is not approved** ([standard/10 §10.7.2](../../standard/en/10-lifecycle-qg.md#10.7.2)) until the screen is described or removed from the list by a decision visible in the version record. The source of the list is the description only: `SR-08` and `SR-10` name the screens, `SPEC-ARCH-01` names the workspace container; a route found in the implementation and absent from the list is a source-of-truth drift and a backward finding, not grounds to extend the list after the fact.

---

### 7.2 SR-10 — a filled body (§6.6.3)

Continues `SR-08` from [§2.3](#23-br--sr): that one describes the creation of the export job on the service side, `SR-10` — on the interface side.

```yaml
# sr/SR-10-export-request-ui.md
id: SR-10
type: SR
parent: { id: BR-02 }
title: "A data subject initiates an export through the privacy interface"
source: { adapt: ADAPT-002, adapt-section: "Forward §3", tz-section: "§3.1" }
constrained-by: ["SPEC-UI-02", "SPEC-SEC-03"]
```

#### Requirement

WHEN the data subject confirms the request on the "Privacy → Export my data" screen, the system MUST create an export job and take the user to the status screen of that job.

> The form is event-driven ([standard/06 §6.6.3.1](../../standard/en/06-requirements-hierarchy.md#6.6.3.1)): the condition marker WHEN stands as the first word. The statement is one: creating the job and moving to the status screen are a single observable outcome of one action, not two independent requirements.

#### Behavior

The request screen is available to the data subject in the "Privacy" section and shows the composition of the export before it is ordered: the profile, the activity log for 730 calendar days, and marketing consents (TZ §4.4 in [§2.1](#21-tz-fragment-immutable), clarification `B-01` from [ADAPT-002](#22-adapt-abridged)).

WHILE the subject's identity is not confirmed by a second factor, the system MUST keep the "Order export" action unavailable and show the reason for unavailability next to it. Confirmation is performed by the existing second-factor mechanism; no separate mechanism is introduced for export.

IF less than 30 days have passed since the previous export and no override has been granted, THEN the system MUST refuse the order. The date from which an order becomes possible again is named to the subject on the refusal screen (TZ §4.5 in [§2.1](#21-tz-fragment-immutable)).

After the job is created, the system shows the preparation status and stays on the status screen until a download link appears or until the 72-hour preparation deadline expires (TZ §4.3 in [§2.1](#21-tz-fragment-immutable)). Delivery of the finished archive is regulated separately — `SR-09`.

Every order, every refusal, and every override is written to the audit log (TZ §4.5 in [§2.1](#21-tz-fragment-immutable), clarification `B-02`).

#### Constraints

Preparation deadline — no more than 72 hours from the moment of identity confirmation. Frequency — no more than one order per 30 days without an override. An override is granted by a participant in the `privacy-officer` role. Full constraints live in `SPEC-SEC-03`.

#### Link to SPEC

`SPEC-UI-02` governs the interface part: the set of screens, action coverage, the waiting and refusal states, accessibility, and language versions. `SPEC-SEC-03` governs the frequency rule, the role granting an override, and the lifetime of the download link.

---

### 7.3 The five statement forms on one body of material

[Standard/06 §6.6.3.1](../../standard/en/06-requirements-hierarchy.md#6.6.3.1) introduces five forms. On the E3 material they look as follows — the table shows the form, not the full text of the requirements:

| Case | Statement |
|---|---|
| Ubiquitous | The system MUST write every export order to the audit log. |
| State-driven | WHILE the subject's identity is not confirmed by a second factor, the system MUST keep the "Order export" action unavailable. |
| Event-driven | WHEN the data subject confirms the request, the system MUST create an export job. |
| Unwanted behavior | IF less than 30 days have passed since the previous export, THEN the system MUST refuse the order. |
| Optional feature | WHERE a granted override is present, the system MUST accept the order earlier than 30 days. |

Three particulars of the form are visible in these same rows: WHERE marks presence, not place; THEN after IF is mandatory; one statement — one sentence, so that where one was tempted to write "refuse and name the date", the refusal and the message are split into two sentences, because a glued statement is verified indivisibly and a single TC would prove half of it.

---

### 7.4 SPEC-UI-02 — a filled mandatory body (§8.4.1 + §8.5.6)

```yaml
# specs/ui/SPEC-UI-02-privacy-export.md
id: SPEC-UI-02
type: SPEC
spec-type: SPEC-UI
title: "Personal-data export interface"
source: { adapt: ADAPT-002, adapt-section: "Forward §3" }
depends-on: ["SPEC-SEC-03", "SPEC-API-04"]
screens: ["S-01", "S-02", "S-03"]     # codes from the SPEC-ARCH-01 screen list (§7.1)
ui-platform: web
target-users: [{ role: data-subject, persona: "ADAPT-002 §2.1" }]
design-system: "AcmeCRM Design Kit 4"
accessibility-level: WCAG-AA
i18n: required
mockup-links: [{ tool: figma, url: "<mockup link>", version: "v3" }]
baseline-images:
  - "ai-concepts/baselines/SPEC-UI-02-screen-01.png"
  - "ai-concepts/baselines/SPEC-UI-02-screen-02.png"
  - "ai-concepts/baselines/SPEC-UI-02-screen-03.png"
```

#### Purpose

The specification governs the interface through which a data subject orders an export of their personal data, watches it being prepared, and receives a refusal with an explainable reason. It gives the agent implementing the interface a single source of truth about the set of screens and about what is available on them; whatever is not described here has no source of truth and is not subject to guesswork ([standard/02 §2.3.3](../../standard/en/02-methodology-positioning.md#2.3.3)).

#### Scope

**In:** the three screens of the "Privacy → Export my data" section — export request, preparation status, refusal; every action available to the data subject on those screens; the cross-cutting elements of those screens.

**Out:** the second-factor confirmation screen (S-04 of the list: the rule — `SPEC-SEC-03`, the screen — `SPEC-UI-01`; reused unchanged); the interface of the participant in the `privacy-officer` role who grants the override (a separate specification, not covered in E8); the email notification that the archive is ready (not a screen).

#### Overall interface structure

"Privacy" is a tab in account settings. Export is one of its items; the export screens form a linear sequence with no branching: request → status, or request → refusal. A return to settings is available from any of the three screens. There are no pop-up dialogs in the section: refusal and status are full screens, because both are addressable by link and both must survive a page reload.

#### Screens

There are three screens; all three are described.

| Code | Screen | When it is shown |
|---|---|---|
| S-01 | Export request | The subject opened "Export my data"; no active job exists |
| S-02 | Export in preparation | A job is created and unfinished, or finished and the link is still alive |
| S-03 | Request refused | The order was refused by the frequency rule or by unconfirmed identity |

**S-01 "Export request".** Contains: the section heading; the list of what will go into the archive — profile, activity log for 730 days, mailing consents; a line about the preparation deadline of up to 72 hours; a line about the frequency of no more than once per 30 days; an identity-confirmation marker with its current state; the "Order export" action; the "Back to settings" action.

**S-02 "Export in preparation".** Contains: the preparation state in words, without a completion percentage — the share is unknown on the interface side; the job creation time; the preparation deadline; the "Refresh status" action; the "Cancel export" action; the "Back to settings" action. Once the link appears, the same screen gains the "Download archive" action and the link lifetime.

**S-03 "Request refused".** Contains: the reason for refusal in plain language; the date from which an order becomes possible again — for a refusal by frequency; the "Request an override" action; the "Back to settings" action. For a refusal by unconfirmed identity, the "Confirm identity" action is shown instead of the date.

#### User journeys and action coverage

Coverage is checked by name: every action of every screen is named below at least once ([standard/08 §8.5.6](../../standard/en/08-specifications.md#8.5.6) — "covering every user action available through the interface").

| Journey | Screens | Actions covered |
|---|---|---|
| J-1. Ordinary order | S-01 → S-02 | Expanding the archive composition list, "Order export", "Refresh status", "Download archive" |
| J-2. Order before identity confirmation | S-01 → S-03 → S-01 | "Confirm identity" |
| J-3. Repeat order before the deadline | S-01 → S-03 | "Request an override" |
| J-4. Abandoning a started export | S-02 → S-01 | "Cancel export" |
| J-5. Leaving the section | S-01, S-02, S-03 | "Back to settings" from each of the three screens |

Reconciliation by count: there are eight actions across the three screens — expanding the archive composition list, "Order export", "Back to settings", "Refresh status", "Cancel export", "Download archive", "Request an override", "Confirm identity". All eight are named in journeys J-1…J-5. There are no uncovered actions.

#### Cross-cutting elements

**Access rights.** All three screens are available only to the account owner. A participant in the `privacy-officer` role does not see these screens and cannot order an export on the subject's behalf; their interface is out of Scope.

**Notifications.** The interface does not itself notify of readiness: the notification goes by email. S-02 says so explicitly, so the subject does not keep the tab open.

**Error states.** A refusal by the rules is S-03, a full screen. A connection failure and unavailability of the export service are shown on the screen where they occurred, as a message with a "Retry" action; they do not move the user to S-03, because the order was not refused but not delivered.

**Empty states.** S-01 has no empty state — the composition list is never empty. On S-02 an empty state is impossible by construction: the screen is shown only when a job exists.

#### Tone and style

The subject is addressed formally. The reason for refusal is named directly and without apology: "Export is available once every 30 days. Your next order — from 12 June". Service words — "job", "archive", "activity log" — are shown to the user as such and are not replaced by internal identifiers.

#### Accessibility

The level is WCAG-AA. What matters for these three screens: a state change on S-02 is announced to the screen reader as a live region, otherwise the subject never learns the archive is ready; the reason the "Order export" action is unavailable is associated with the action programmatically, not merely placed next to it; a refusal on S-03 is not conveyed by color alone; the keyboard traversal order matches the reading order on all three screens.

#### Language versions

Russian and English are mandatory. The date of a possible repeat order is rendered in the locale format, not in one shape for both versions. The length of the refusal-reason line is not bounded by the button width: the English wording is longer than the Russian one, and the screen must wrap it rather than truncate it.

#### Link to requirements

`SR-10` — ordering an export through the interface (`constrained-by`). `SR-09` — delivery of the finished archive, whose interface part is limited to the "Download archive" action on S-02. `BR-02` — the business requirement from which both are derived.

#### Link to other SPEC

`SPEC-SEC-03` — the frequency rule, the role granting an override, the link lifetime; the interface displays these rules but does not define them. `SPEC-API-04` — the source of the job states displayed on S-02.

#### Verification

`TC-090` and `TC-091` (§7.5). Accessibility is verified by separate TC of type `accessibility`, language versions by TC of type `i18n`; they are not expanded in E8.

---

### 7.5 A pair of `ux`-TC (§9.6.1)

The two-layer structure of §9.6.1: the intent layer is executed by the driving agent, the perceptual-check layer by the judging model. The mandatory frontmatter extension is `judge.vendor`, `judge.model`, `baseline.artifact`, `baseline.perceptual-diff-threshold`. Body sections follow [standard/09 §9.4](../../standard/en/09-test-cases.md#9.4), including the mandatory "Out of scope".

```yaml
---
id: TC-090
title: "The subject orders an export and sees the preparation screen"
type: TC
tc-type: ux
automation: { status: automated, set-version: "1.0" }
verifies:
  - id: SR-10
    statement: 1                  # SR-10#1 — "Requirement", event-driven form
negative: false
judge: { vendor: google, model: "gemini-2.5-pro" }
baseline:
  artifact: "ai-concepts/baselines/SPEC-UI-02-screen-02.png"
  perceptual-diff-threshold: 0.03
---
```

**Context.** `SR-10`, the "Requirement" section: "WHEN the data subject confirms the request … the system MUST create an export job and take the user to the status screen of that job". Screen S-02 per `SPEC-UI-02`.

**Preconditions.** A subject account with a confirmed second factor; no active export jobs; the previous export was more than 30 days ago. The state is prepared by the seed mechanism.

**Steps.** Intent, not selectors: the data subject wants to obtain an archive of their data after entering the privacy section.

**Pass criterion.** The rendered state shows the preparation screen: the state is named in words, the job creation time and the preparation deadline are shown, the refresh-status action is available. The perceptual difference from the baseline does not exceed 0.03.

**Fail criterion.** Observable signs of violation: still on the request screen; completion shown as a percentage; the deadline missing or shown as "unknown"; a download link presented before the archive is ready; two jobs created on a single confirmation.

**Postconditions.** Exactly one export job in the preparation state; an audit log entry created. The job is removed by the cleanup mechanism after the run.

**Out of scope.** Delivery of the finished archive and the link lifetime are not verified — covered by the TC pair for `SR-09`. Refusal by the frequency rule is not verified — covered by `TC-091`. Screen accessibility is not verified — covered by TC of type `accessibility`.

```yaml
---
id: TC-091
title: "A repeat order before 30 days is refused with the date named"
type: TC
tc-type: ux
automation: { status: automated, set-version: "1.0" }
verifies:
  - id: SR-10
    statement: 5                  # SR-10#5 — "Behaviour", refusal by the frequency rule
negative: true
judge: { vendor: google, model: "gemini-2.5-pro" }
baseline:
  artifact: "ai-concepts/baselines/SPEC-UI-02-screen-03.png"
  perceptual-diff-threshold: 0.03
---
```

**Context.** `SR-10`, the "Behavior" section: "IF less than 30 days have passed since the previous export and no override has been granted, THEN the system MUST refuse the order". Screen S-03 per `SPEC-UI-02`.

**Preconditions.** A subject account with a confirmed second factor; the previous export finished 5 days ago; no override was granted.

**Steps.** The data subject wants to order an export again shortly after receiving an archive.

**Pass criterion.** The rendered state shows the refusal screen: the reason is named in plain language, the date of the next possible order is given, the override-request action is available. The perceptual difference from the baseline does not exceed 0.03.

**Fail criterion.** Observable signs of violation: an export job created against the rule; the refusal shown without the next-order date or with a date in the past; the refusal conveyed by color alone, without text; the refusal shown as a pop-up instead of a screen; the override-request action missing; the reason rendered as a raw service error message.

**Postconditions.** No new export jobs; an audit log entry about the refusal is created.

**Out of scope.** Granting the override by a participant in the `privacy-officer` role is not verified — their interface is out of `SPEC-UI-02` Scope. Refusal by unconfirmed identity is not verified — a separate TC of the same form pair.

**Baselines.** Both TC are compared against the baseline images from the specification's `baseline-images`. Replacing a baseline goes through the approval mechanism with the `[baseline-update]` tag ([standard/09 §9.13](../../standard/en/09-test-cases.md#9.13)); automatic update is prohibited, otherwise a changed interface declares itself correct.

---

### 7.6 SPEC-UC-01 — use case (§8.5.12)

The scenario stitches `SR-10`, `SPEC-UI-02` and `SPEC-API-01`; the executor is a person, so every step is covered by a `ux` TC and not by a `system` TC bypassing the interface ([standard/09 §9.8](../../standard/en/09-test-cases.md#9.8)).

```yaml
---
id: SPEC-UC-01
title: "Ordering a personal-data export by the data subject"
type: SPEC-UC
spec-type: SPEC-UC
level: system
role: human
persona: "ADAPT-003 §Persona: data subject"
covers: [SR-10, SPEC-UI-02, SPEC-API-01]
entry: SPEC-UI-02
steps:
  - { n: 1, action: "Opens the export request screen", ref: SPEC-UI-02, expects: "Screen S-01 with the request form" }
  - { n: 2, action: "Confirms the request", ref: SR-10#1, expects: "An export job is created; transition to S-02" }
  - { n: 3, action: "Waits for preparation", ref: SPEC-UI-02, expects: "S-02 shows the job state" }
  - { n: 4, action: "Gets a refusal when re-ordering within 30 days", ref: SR-10#5, expects: "S-03 with the reason for refusal" }
source: { adapt: "ADAPT-003", adapt-section: "Forward §4", tz-section: "§5.2" }
---
## Goal and executor
The data subject orders an export of their data and sees the job state.

## Preconditions
Identity confirmed; no previous export, or ≥ 30 days have passed.

## Main path
Steps 1–3 from the frontmatter; each references a statement of `SR-10` or a screen of `SPEC-UI-02`.

## Alternative and negative branches
Step 4 — refusal by period (`SR-10`, "Behaviour"); refusal by unconfirmed identity — outside the Scope (see `SPEC-UI-02`).

## Postconditions
The export job exists in the "in preparation" state, or the refusal is recorded in the log.

## Covering TCs
`TC-090` (steps 1–3, positive), `TC-091` (step 4, negative); both `tc-type: ux`.
```

A step without `ref` violates structural completeness ([§8.5.12.1](../../standard/en/08-specifications.md#85121-step-reference-to-a-statement)): a set version with such a scenario is not approved.

### 7.7 What this example does not prove

Stated explicitly, so the example is not read as more than it is.

- **The affordability of "all screens" on a real system.** There are three screens. On three screens the form of the mandatory body was tested, not its volume. A product with fifty screens will carry a different cost, and this example does not measure it.
- **The fitness of the statement form against accumulated description.** The five forms of §6.6.3.1 were applied to five statements written in the form from the outset. Converting an existing description into these forms is a different task, and the modality of the rule stays advisory precisely for that reason ([standard/06 §6.6.3.1](../../standard/en/06-requirements-hierarchy.md#6.6.3.1)).
- **A machine check of screen-list coverage.** The table in §7.1 was compiled by hand; a static TC that reconciles `screens[]` of all SPEC-UIs against the `SPEC-ARCH-01` list is not written in the example.
- **The workability of the perceptual check.** `TC-090` and `TC-091` are written per §9.6.1 but were never run: there are no baseline images in the corpus, and no judging model was connected to the example. What was verified is that the fields of §9.6.1 fill in without inventing new ones — no more.

---

*RENAR Guide 1.0 — renar.tech*
