---
title: "Walkthrough"
description: "A full-size end-to-end RENAR walkthrough on the Login Flow project for AcmeCorp."
order: 1
lang: en
version: "1.0"
---

# 01. Walkthrough: Login Flow for AcmeCorp

> One full RENAR cycle from a signed TZ to an accepted release. The example is an internal tool with registration via corporate email and 2FA. The goal is to show **every phase** on a single medium-sized project.
>
> **Context:** AcmeCorp, ~1 sprint of team work, stack Next.js + FastAPI + PostgreSQL. RENAR maturity at the RENAR-3+ level (full ADAPT + TC + adversarial). The example is **substrate-independent**: operations go through the V1–V6 capabilities; the concrete directory layout is in [03-tool-guide-git](03-tool-guide-git.md) or [04-document-store-substrate](04-document-store-substrate.md).
>
> **Prerequisites:** [00-quickstart](00-quickstart.md), [core/renar-core](../../core/en/renar-core.md), [reference/01-glossary](../../reference/en/01-glossary.md).

**The phases are an illustration, not a norm.** The order of phases 0–9 is one possible order of work; the standard governs preconditions and rules, not the sequence ([standard/01 §1.2.1](../../standard/en/01-scope.md#1.2.1)). An organization with a different process conforms to the standard as long as the preconditions of every artifact hold.

**Reader's route.** Phases 0–2 — context gathering, signing the TZ, the ADAPT and the TZ clarification protocol (ACTZ). Phases 3–4 — decomposition into BR/SR/SPEC and generation of pos/neg pairs for TC. Phase 5 — the canonical gates QG-0 (requirement approval) and QG-1 (the TC `draft → ready` transition only). Phases 6–7 — TR implementation and verification (QG-2). Phases 8–9 — a delta-TZ on changes and acceptance: AT against the effective TZ plus the optional QG-4 on the business outcome.

---

## Phase 0 — Requirements elicitation

Before signing the TZ. The AI agent runs 2–3 interviews with stakeholders (Sales Director, IT Manager) and gathers context in structured form.

Phase 0 artifacts (informative, not normative for RENAR Core): `elicitation/{domain-context.md, sales-director.yaml, it-manager.yaml, findings-clustered.md, critic-review.md, multi-model-diff.md}`. Phase 0 is not fixed in Core — it belongs to the elicitation methodology, out of scope for RENAR v1.0 ([standard/01 §1.3](../../standard/en/01-scope.md#1.3)).

---

## Phase 1 — TZ import

After the elicitation iterations, the client signs `TZ-2026-042`:

```markdown
# TZ-2026-042 — Login Flow for AcmeCorp Internal Tool
Signing date: 2026-05-03 · Parties: AcmeCorp + VendorCorp

## §1. Goals
Cut AcmeCorp employees' time to enter the tool to <2 minutes from first arrival to full access.

## §2. Functional requirements
### FR-001. Registration by corporate email
An employee registers via an email in the @acmecorp.com domain. An email outside the domain — denial with an explanation.

### FR-002. Two-factor authentication (TOTP)
After registration, mandatory 2FA setup via TOTP.

### FR-003. Access recovery via the corporate administrator
On loss of the 2FA device — recovery via a ticket in IT support.

## §3. Non-functional requirements
### NFR-001. Performance: Login <2 seconds (p95).
### NFR-002. Security: bcrypt cost-factor ≥ 12; login logs — 1 year; lockout after 5 failed attempts in 15 minutes.
### NFR-003. Jurisdiction: all data in the RU (government contracts).
```

The TZ is signed → **immutable**. Any edits go through ADAPT (Phase 2) or a delta-TZ (Phase 8). The runtime MUST register the immutable TZ as a revision (V1+V2) with AI provenance (V6).

---

## Phase 2 — ADAPT and ACTZ (interpretation and the clarification protocol)

This is where the two loops part ways. The **ADAPT** is the internal interpretation: its audience is the engineer, the agent, and the reviewer, and it is signed by the Architect ([standard/07 §7.5](../../standard/en/07-adapt.md#7.5)). The **ACTZ** is the TZ clarification protocol: whatever was put to the client and signed by both parties — that is, an obligation ([§7.13](../../standard/en/07-adapt.md#7.13)). The boundary runs along the audience: what is shown to the client and approved is an obligation; what is not shown is an interpretation.

**2.1 The primary agent generates a draft ADAPT.** Input: TZ-2026-042 (immutable). Output: draft ADAPT-001 + Forward sections (across §2 FR + §3 NFR) + Backward findings (6 candidates) + V6 provenance.

**2.2 Adversarial review.** A separate critic agent (a different model) checks the backward findings; it blocks adapt-approve while critical findings remain open. Examples:

```text
[HIGH] B-001 reclassify gap → hidden-assumption
[HIGH] missed backward: case-sensitivity email in FR-001
[MEDIUM] B-004 terminology: define "employee" via User.role
[MEDIUM] B-006 feasibility: rate-limit scope (IP vs email vs session)
```

**2.3 Iterative resolution.** The Architect adjusts the Forward and Backward, the AI regenerates. After 2 adversarial cycles: 7 backward entries (B-001..B-007), all reclassified or prepared to be put to the client; the Forward covers §2 + §3 of the TZ in full.

**2.4 ACTZ-001 — TZ Clarification Protocol No. 1.** Five findings out of seven require a decision from the client. The Architect does not forward the agent's raw output: they aggregate the questions, rephrase them in the language of obligations ("the domain is compared case-insensitively"), attach proposals — and put them forward as a single protocol. The client signs; so does the contractor.

```yaml
---
id: ACTZ-001
title: "TZ Clarification Protocol No. 1 for TZ-2026-042"
type: ACTZ
tz-ref: TZ-2026-042
resolves: [B-001, B-002, B-004, B-006, B-007]
status: signed
client-signature: { signed-by: "A. A. Ivanova", role: "Product Lead", organization: "AcmeCorp", signed-at: "2026-05-04T11:30:00Z" }
vendor-signature: { signed-by: "P. P. Petrov", role: architect, organization: "VendorCorp", signed-at: "2026-05-04T11:50:00Z" }
---

## §1. Email case
`@AcmeCorp.com` and `@acmecorp.com` denote the same employee: the domain is compared case-insensitively.

## §2. The term "employee"
An employee is an account with the role `employee` in the corporate directory.

## §3. The lockout boundary
5 failed attempts within 15 minutes are counted per "IP + email" pair, not per session.
```

Every finding that requires a decision from the client receives a `decided-in: ACTZ-001 §M` — a pointer to a clause of the **signed** protocol. A finding cannot become `resolved` without such a pointer, and an ADAPT cannot be approved while findings remain unclosed ([§7.4.5](../../standard/en/07-adapt.md#7.4.5)).

**2.5 ADAPT in status `approved`.** The signature is the Architect's alone: the client never saw this document and never signed it — their decisions live in ACTZ-001.

```yaml
---
id: ADAPT-001
title: "Adaptation of TZ-2026-042 — Login Flow AcmeCorp"
type: ADAPT
trigger-stage: import-tz
source-tz: { id: TZ-2026-042, signed-date: "2026-05-03", signed-by-client: "AcmeCorp PM" }
status: approved
approval:
  # no client-signature per §7.5 — the ADAPT is not put to the client
  architect-signature: { signed-by: "P. P. Petrov", role: architect, signed-at: "2026-05-04T12:00:00Z" }
generates-requirements: [BR-01, BR-02, SR-01, SR-02, SR-03, SR-04, SR-05, SR-06, SR-07]
generates-specs: [SPEC-UI-01, SPEC-API-01, SPEC-DATA-01, SPEC-SEC-01]
open-questions-count: 0
resolved-questions-count: 7
ai-provenance: { generated-by: "anthropic-claude-opus-4-7@2026-05-04", prompt-template: "prompts/adapt-from-tz.md@v2.1", human-edits: true }
---
```

Once approved, ADAPT-001 is **immutable**.

The **effective TZ** ([§7.14](../../standard/en/07-adapt.md#7.14)) at this point: `TZ-2026-042` + `ACTZ-001`. That is the benchmark the system will be delivered against in phase 9. The ADAPT is not part of the acceptance benchmark — the client answers only for what they signed.

---

## Phase 3 — Decomposition into BR / SR / SPEC

**3.1 Decomposition.** Operation: `decompose`. Input: approved ADAPT-001. Output: draft BR (2), SR (7), SPEC (4) + adversarial-review artifacts.

**3.2 Adversarial findings:**

```text
[HIGH] BR-01 stakeholder field empty — who owns the business goal?
[HIGH] SR-05 says "bcrypt cost-factor 12" — this is a deployment detail, it belongs in SPEC-SEC-01, not in the SR.
[MEDIUM] NFR-003 (jurisdiction) is not reflected in the data-classification of SR-01.
[MEDIUM] SPEC-UI-01 has no accessibility-level — WCAG-AA minimum for a corporate tool.
→ 4 findings → fix → re-generate.
```

**3.3 Final artifact set:**

```text
acmecorp-requirements/
├── br/
│   ├── BR-01-self-service-registration.md
│   └── BR-02-secure-mfa.md
├── sr/
│   ├── SR-01-email-domain-validation.md       (FR-001)
│   ├── SR-02-totp-enrollment.md               (FR-002 setup)
│   ├── SR-03-totp-verification.md             (FR-002 verify)
│   ├── SR-04-password-recovery-via-admin.md   (FR-003)
│   ├── SR-05-rate-limiting-failed-logins.md   (NFR-002)
│   ├── SR-06-audit-logging.md                 (NFR-002 audit)
│   └── SR-07-data-residency-ru.md             (NFR-003)
├── specs/
│   ├── ui/SPEC-UI-01-login-flow.md
│   ├── api/SPEC-API-01-auth.md
│   ├── data/SPEC-DATA-01-user-model.md
│   └── sec/SPEC-SEC-01-auth-policy.md
└── tz/TZ-2026-042.md
```

**3.4 Example: SR-01 (frontmatter + body):**

```yaml
---
id: SR-01
title: "Email domain validation at registration"
type: SR
parent: { id: BR-01 }
source: { adapt: "ADAPT-001", adapt-section: "Forward §2.1", tz-section: "§2 FR-001" }
constrained-by: ["SPEC-API-01", "SPEC-DATA-01", "SPEC-SEC-01"]
data-classification: { contains-pii: true, data-residency: ["RU"], retention-days: 365 }
compliance: [{ standard: "FZ-152", article: "art. 13.1" }]
ai-provenance: { generated-by: "anthropic-claude-opus-4-7@2026-05-04", prompt-template: "prompts/decompose-adapt.md@v2.1", context-tokens: 12450, output-tokens: 320, human-edits: true }
---

## Description
Registration is allowed only if the email belongs to the `@acmecorp.com` domain. Other domains are rejected with an explanation.

## Behavior
- email NOT from `@acmecorp.com` → 422 with `{"error":"email-domain-not-allowed", "allowed-domain":"acmecorp.com"}`. [ADAPT-001 Forward §2.1; TZ-2026-042 §2 FR-001]
- email from `@acmecorp.com` → standard registration (SPEC-API-01).
- The whitelist is stored in `SPEC-SEC-01.allowed-domains`, extended without a release.
- Domain comparison is case-insensitive (ADAPT-001 Forward §2.1).

## Constraints
- `*.acmecorp.com` subdomain — a separate Architect decision (out of scope).
```

**3.5 SPEC-API-01 (fragment):**

```yaml
---
id: SPEC-API-01
title: "Authentication REST API"
type: SPEC-API
source: { adapt: "ADAPT-001", adapt-section: "Forward §2" }
api-style: rest
api-version: "v1.0.0"
versioning-strategy: url-path
authentication: bearer-jwt
rate-limits: [{ endpoint: "POST /auth/login", limit: "5/15min/ip+email" }]
contract-file: { format: openapi-3.1, location: "contracts/auth-api.yaml" }
depends-on: ["SPEC-DATA-01", "SPEC-SEC-01"]
---

## Endpoints

### POST /auth/register
- body: `{"email": "<corp-email>", "password": "<strong>"}`
- 201 → `{"user_id": "<uuid>", "verified": false, "totp_setup": false}` · 422 → invalid · 409 → email exists

### POST /auth/login
- body: `{"email", "password", "totp": "<6digits>"}`
- 200 → `{"access_token": "<jwt>", "expires_in": 3600}` · 401 → invalid · 429 → rate limit

### POST /auth/totp-setup — see SR-02.

## Error model
A single structure: `{"error": "<code>", "details": {...}}`.
```

---

## Phase 4 — TC generation (pos/neg pairs)

Operation: `tc-generate` SR-01 → pos/neg TC pairs per testable assertion ([standard/09 §9.7](../../standard/en/09-test-cases.md#9.7): for each assertion in an SR — at least one positive + negative TC pair).

**TC-001 (positive):**

```yaml
---
id: TC-001
title: "Registration with an allowed domain — happy path"
type: TC
tc-type: system
verifies: [{ id: SR-01 }]
negative: false
automation: { status: automated, location: "tests/auth/test_registration.py::test_allowed_domain_succeeds", runner: pytest }
---

## Context
SR-01, "Requirement" section: registration with an email from a whitelisted domain creates an account.

## Preconditions
- The DB is empty; email alice@acmecorp.com is not registered.

## Steps
POST /auth/register {email: "alice@acmecorp.com", password: "ValidPass123!"}

## Pass criterion
- status 201; body contains {"user_id": "<uuid>", "verified": false, "totp_setup": false}; the User is created in the DB; a verification email is sent (mock SES).

## Fail criterion
- status ≠ 201; plaintext password in the body/logs; a User with a different email (case mismatch); the verification email is not sent.

## Postconditions
- The User is removed by the seed mechanism; the mock SES queue is cleared.

## Out of scope
- TOTP setup → TC-005 (SR-02); rate limiting → TC-009 (SR-05).
```

**TC-004 (negative):**

```yaml
---
id: TC-004
title: "Registration with a disallowed domain — denial with an explanation"
type: TC
tc-type: system
verifies: [{ id: SR-01 }]
negative: true
automation: { status: automated, location: "tests/auth/test_registration.py::test_disallowed_domain_rejected", runner: pytest }
---

## Context
SR-01, "Behaviour" section: an email outside the whitelisted domain is rejected with an explainable reason.

## Preconditions
- email "bob@gmail.com" (outside the whitelist).

## Steps
POST /auth/register {email: "bob@gmail.com", password: "ValidPass123!"}

## Pass criterion
- status 422; body == {"error": "email-domain-not-allowed", "allowed-domain": "acmecorp.com"}; the User is NOT created in the DB; no email is sent; an audit entry about the rejected attempt (for SR-06).

## Fail criterion
- status ≠ 422; the User is created; an email is sent (security leak); the audit entry is missing.

## Postconditions
- The audit entry is removed by the seed mechanism.

## Out of scope
- The audit entry format → TC-021 (SR-06).
```

---

## Phase 5 — Quality gates before code (QG-0 and QG-1)

After generating TCs for all 7 SRs — 26 test-case entries in total (pos/neg pairs + extra negatives for SR-05, SR-06).

**5.1 QG-0 — approval of set version 1.0.** BR-01, seven SRs, the SPECs and 26 TC norms are approved as one version ([standard/10 §10.5.2](../../standard/en/10-lifecycle-qg.md#10.5.2)). Preconditions: `source.adapt = ADAPT-001 (approved)`; the tree is complete (every SR has a `parent`, every SPEC a source); the adversarial review succeeded for every artifact; a pair of TC norms for every statement; the assertions and links cite sections of ADAPT-001. Postcondition: version record `1.0` with the Architect's signature; BR/SR/SPEC have no separate statuses.

**5.2 QG-1 — admission of TC implementations.** Per [§10.3.2](../../standard/en/10-lifecycle-qg.md), QG-1 applies only to the TC implementation and separates check code admitted to runs from code not yet ready. Preconditions: `automation.set-version: "1.0"` points to the approved version (V5); `automation.status` + `location` are valid; static checks and the dry-run pass; the fixing red run is recorded; the mandatory body sections are filled in. Postcondition: the implementation is admitted.

After **QG-0** of version 1.0 and **QG-1** on each TC implementation, TR work can be opened (Phase 6).

---

## Phase 6 — Implementation

> **The order is not accidental.** The TCs were frozen (`ready`) in phase 5 — **before** the first line of code, and they were not written by the agent that now writes the implementation ([standard/09 §9.18](../../standard/en/09-test-cases.md#9.18)). A test born next to the code checks the code, not the requirement. Hence the red-history requirement as well: before implementation the TC is run and MUST fail — a test that has never been red proves nothing.

**6.1 Task creation (TR).** Operation `sync-tasks`: input — the verified SR/SPEC set; output — 7 TRs in the implementation tracker (parent SR, implements-spec[], QG-0 ready with Goal + AC).

**6.2 A developer picks up TR-101.** QG-0 checks: Goal from SR-01; AC list (4 items); parent.id resolves (approved); implements-spec present; negative scenario in AC → the work session is allowed.

**6.3 Implementation (fragment):**

```python
# acmecorp-login.src/src/auth/registration.py
from fastapi import HTTPException
from config import settings   # allowed-domains from SPEC-SEC-01

def validate_email_domain(email: str) -> None:
    domain = email.split("@", 1)[-1].lower()
    if domain not in settings.AUTH_ALLOWED_DOMAINS:
        raise HTTPException(status_code=422, detail={
            "error": "email-domain-not-allowed",
            "allowed-domain": settings.AUTH_ALLOWED_DOMAINS[0],
        })
```

```python
# acmecorp-login.src/tests/auth/test_registration.py
def test_allowed_domain_succeeds(client, db, mock_ses):
    r = client.post("/auth/register", json={"email": "alice@acmecorp.com", "password": "ValidPass123!"})
    assert r.status_code == 201
    assert "user_id" in r.json()
    user = db.query(User).filter_by(email="alice@acmecorp.com").one()
    assert user.verified is False
    mock_ses.send_email.assert_called_once_with(template_id="verification-email", to_email="alice@acmecorp.com")

def test_disallowed_domain_rejected(client, db):
    r = client.post("/auth/register", json={"email": "bob@gmail.com", "password": "ValidPass123!"})
    assert r.status_code == 422
    assert r.json() == {"error": "email-domain-not-allowed", "allowed-domain": "acmecorp.com"}
    assert db.query(User).count() == 0
```

**6.4 Substrate-side validation hook:**

```text
[hook] Checking links for TR-101: parent.id SR-01 (approved); implements-spec [SPEC-API-01, SPEC-SEC-01].
[hook] Negative TCs: SR-01.verified-by includes TC-002, TC-004 (negative).
✓ Change allowed.
```

---

## Phase 7 — QG-2 (verification gate)

**7.1 CI runs the TCs.** `pytest acmecorp-login.src/tests/auth/test_registration.py` → 4 TC PASSED → the Bot updates `last-run.result = pass`, `last-run.set-version = 1.0` in the TC files. Each TC's run history shows the "red → green" transition: the pinning run before implementation was red — the condition under which a TC counts as evidence at QG-2 ([§9.18.2](../../standard/en/09-test-cases.md#9.18)).

**7.2 Spot-check ([standard/09 §9.14](../../standard/en/09-test-cases.md#9.14)).** Once per sprint the Engineer manually runs 5 passing TCs selected by machine signals (priority — TCs with no red history, then mutant survivors, then the `implementation-originated` class; the remainder by random top-up), and checks the actual result against the SR. Selected: TC-001, TC-008, TC-012, TC-019, TC-024 → 5/5 match.

**7.3 Verification entry of version 1.0.** QG-2 preconditions: all TCs of the version passed with `last-run.set-version = 1.0`; both TCs of every pair; red history; the spot-check passed. Postcondition: the runner appends `verification` to the version record 1.0 ([§10.5.4](../../standard/en/10-lifecycle-qg.md#10.5.4)) — SR-01 is verified as part of the pair (version 1.0, build), it has no status of its own; the coverage index is updated.

---

## Phase 8 — Delta-TZ

**8.1 The client, a week later:**

```markdown
# TZ-2026-051 — Addendum to TZ-2026-042
Base: TZ-2026-042

## §2 (change) FR-001 (extension)
Additionally allow @subsidiary.acmecorp.com (the subsidiary company). The whitelist is extended to 2 domains.
```

**8.2 Delta-ADAPT + ACTZ-002.** Operation `adapt-from-tz (delta)`: input — TZ-2026-051 + parent ADAPT-001; output — draft ADAPT-001-delta-1 + delta Forward + backward findings (e.g. B-008 scope). Finding B-008 ("is `@sub.subsidiary.acmecorp.com` to be treated as allowed?") is put to the client as the protocol `ACTZ-002` — "TZ Clarification Protocol No. 2"; once signed, B-008 receives `decided-in: ACTZ-002 §1`, and the delta-ADAPT is approved by the Architect's signature. The effective TZ becomes: `TZ-2026-042` + `TZ-2026-051` + `ACTZ-001` + `ACTZ-002`.

**8.3 Impact analysis.** Operation `impact-analysis --delta TZ-2026-051`:

```text
Affected:
  BR-01 (scope extension)
  SR-01: changed in the draft → enters version 1.1
  TC-001..004: norms changed → implementations become stale (automation.set-version 1.0 ≠ 1.1); +2 new TC (subsidiary domain)
  TR-115: new implementation task
  SPEC-SEC-01: allowed-domains extended
```

**8.4 Apply delta.** The Architect opens the changes with the marker `[delta:TZ-2026-051]`. The AI updates SR-01 (extends the whitelist) and generates 2 new TCs. Implementation in TR-115. CI runs the TCs, the bot updates last-run. The draft is approved as version 1.1 (QG-0 of the set); the implementations are re-created against the new norm (`automation.set-version: "1.1"`). After the spot-check — the verification entry of version 1.1.

> **Note (simple delta).** If the adversarial reviewer returns a "no findings, no clarifications" verdict ([§7.4.1.2](../../standard/en/07-adapt.md#7.4.1)), **no delta-ADAPT is created** — BR/SR/SPEC get `source.tz-section` directly with a fixed `adversarial-review-ref`. A trivial change (a field rename) produces neither an interpretation nor a protocol: there is nothing to ask the client about.

---

## Phase 8a — Three inputs of a set change

A delta-TZ is not the only way a description set gets a new version. A change arrives through three inputs; they differ by the moment and by what is already known, not by cost: each gives a minor version at once ([standard/10 §10.5.1](../../standard/en/10-lifecycle-qg.md#10.5.1)), and tasks grow from the changed set, not from the text of the input.

| Input | When | What is known | Provenance in BR / SR / SPEC | What it produces |
|---|---|---|---|---|
| **Delta-TZ** (phase 8) | the client changed the promise | the new contract text | `source.tz-section` to the delta; with findings — ADAPT / ACTZ | a set version; rework with annulment of verification |
| **Concept for a change** | an engineer sees an improvement before there is a task — before the task's QG-0 | the target structure: how it should be | `source.adversarial-review-ref` + a reference to the accepted concept in the version record | a set version (minor) |
| **Backward finding / problem** | during a task or after the fact: a failed TC, a defect, a client remark | only "what is wrong" | depends on the outcome of the analysis (table below) | a diagnosis → an implementation defect or a set version |

**A concept for a change** describes the target structure — without comparisons with the current one, without variants and without open questions; it does not enter the set and is not signed by the client. A concept accepted by the Architect **is entered into the set** and gives a minor; from then on it is version history, not a source of truth. A deferred implementation is a queue entry without a set version of its own. A verbal instruction or a wish is not an input: it is written up as a concept. For decisions verified by the behaviour of a screen rather than by text, the concept is complemented or replaced by a clickable prototype on notional data — only what is entered from it into a SPEC-UI enters the set.

**Outcomes of a problem analysis.** The analysis establishes who is wrong, and only then does a defect appear. Five outcomes and their place in RENAR:

| Outcome | Who is wrong | In RENAR | The set | The contract |
|---|---|---|---|---|
| **C** — code | the implementation | a source-of-truth drift ([standard/04 §4.11](../../standard/en/04-terms.md#4.11), class 4.11.3) → a TR against the current version | untouched | untouched |
| **T** — test | the TC implementation (the norm is right) | the TC implementation is re-created against the same norm ([standard/09 §9.9](../../standard/en/09-test-cases.md#9.9)); the red history is kept | untouched | untouched |
| **D** — description | the set | a backward finding of the internal contour ([standard/06 §6.13.2](../../standard/en/06-requirements-hierarchy.md#6.13.2)): `gap`, `hidden-assumption`, `contradiction`, `terminology`, `feasibility` without client-observable consequences | a minor at once or a queue entry | untouched |
| **P** — the promise has to change | the contract | a backward finding with a contractual outcome: `scope`, `contradiction`, `regulatory` or any category with client-observable behaviour → an ACTZ draft, dual signature ([standard/07 §7.13](../../standard/en/07-adapt.md#7.13)) | a version after the ACTZ signature; annulment of verification of the affected TCs | changed |
| **N** — something not promised is needed | outside the order | `scope` (out) → a delta-TZ or a new TZ; does not enter the current set | untouched | new |

Screening out — "a requirement outside the implementation scope" — does not become a problem and leaves no record. The boundary the agent does not cross: outcomes **D, P, N are declared only by a person** — the Architect for D, the vendor's authorized person together with the Architect for P / N ([standard/05 §5.4](../../standard/en/05-roles.md#5.4)); the agent proposes the diagnosis and prepares the ACTZ draft but neither approves the version nor signs. C and T the agent analyzes and fixes itself: the set is untouched, the fix goes as an ordinary task.

---

## Phase 9 — Acceptance: AT against the effective TZ + QG-4

**9.1 Regenerating the ATs.** Before the trials, an isolated agent re-derives the acceptance tests — **AT** — from the current revision of the effective TZ ([standard/09 §9.19](../../standard/en/09-test-cases.md#9.19)). Its input is only `TZ-2026-042` + `TZ-2026-051` + `ACTZ-001` + `ACTZ-002`. It has no access to the ADAPT, the BR/SR/SPEC, the TCs, or the code, and its model differs from the primary agent's — otherwise it would reproduce the same interpretation and acceptance would degenerate into a second round of verification.

```yaml
---
id: AT-03
title: "Registration from the subsidiary domain"
type: AT
verifies: [{ tz: "TZ-2026-051 §2" }, { actz: "ACTZ-002 §1" }]
tz-version: "effective@2026-05-14"
generator: { vendor: "<vendor-B>", model: "<model-B>", internal-context-access: false }
negative: true
status: passing
environment-ref: SPEC-TEST-01
automation: { kind: dynamic, location: "at/test_subsidiary_domain.py", runner: pytest }
---

## tz_text
"Additionally allow @subsidiary.acmecorp.com (a subsidiary). The whitelist is extended to 2 domains."
```

A verbatim quote of the contract clause next to the verification steps is a mandatory part of the AT body: at the trials it is the clause of the contract itself that is presented, not a paraphrase of it.

**9.2 The release gate.** The product is not submitted for delivery until every AT is in status `passing` ([§10.4.3](../../standard/en/10-lifecycle-qg.md#10.4)). The run gives 11/12 green: `AT-07` ("access recovery via the administrator") is red while every TC is green. That is the most valuable row of the routing table ([§9.19.5](../../standard/en/09-test-cases.md#9.19)): AT ✗ / TC ✓ — an **interpretation** error, not a code error. The analysis confirms it: the ADAPT reduced recovery to a password reset, whereas the TZ requires a ticket to IT support. The fix goes into the ADAPT (errata) and the derived SR/TC, after which AT-07 turns green. The `ACCEPTANCE.md` report — AT coverage across the sections of the effective TZ — is refreshed automatically.

**9.3 QG-4 (optional) — the business outcome.** Four weeks after release the business effect is measured. The gate does not replace contractual acceptance and is not mixed with it: the goal can be met while the contract is not, and vice versa.

```text
BR-01 KPI: Time-to-first-login — target <2min P95, actual 1.4min (143%)
BR-02 KPI: 2FA adoption — target ≥95%, actual 97%
```

**9.4 Sign-off.** The client accepts the result: the BR of the set version is accepted (QG-4), the mark is in the version record. Archive: `ACCEPTANCE.md` + `QG-4-REPORT-v1.0.md` + lessons learned `lessons/2026-Q2.md`.

---

## Final artifacts

```text
acmecorp-requirements/
├── adapt/                 ADAPT-001-main.md (frozen) + ADAPT-001-delta-1.md (frozen)
├── actz/                  ACTZ-001.md + ACTZ-002.md (signed, immutable)
├── br/                    BR-01 + BR-02 (status: accepted)
├── sr/                    SR-01 (v1.1, verified) + SR-02..SR-07 (v1.0, verified)
├── specs/                 SPEC-UI-01 + SPEC-API-01 + SPEC-DATA-01 + SPEC-SEC-01 (verified; SPEC-SEC-01 v1.1)
├── tests/                 TC-001..TC-028 (28 TC, 100% passing)
├── at/                    AT-01..AT-12 (regenerated before the trials, 100% passing)
├── tz/                    TZ-2026-042.md + TZ-2026-051.md (delta, immutable)
├── elicitation/           # Phase 0 artifacts
├── lessons/2026-Q2.md     # Phase 9 lessons
├── ACCEPTANCE.md          # AT coverage across the effective TZ
└── QG-4-REPORT-v1.0.md    # business-outcome report
```

The effective TZ here is not a file but a computed sum: `TZ-2026-042` + `TZ-2026-051` + `ACTZ-001` + `ACTZ-002`. That is exactly what was fed to the AT generator.

---

## Project metrics

| Metric | Value |
|---|---|
| RDLT (TZ signed → all SR verified) | 11 days |
| Coverage Velocity | 100% over 2 sprints |
| Hallucination Rate (detected) | 0% |
| Adversarial findings found (cycle 1) | 4 high + 2 medium |
| Test-spec drift on the delta-TZ | 0% |
| Signed ACTZs | 2 (TZ Clarification Protocols No. 1 and No. 2) |
| ATs at the trials | 12 (1 red → an interpretation error, fixed) |
| Acceptance disputes | 0 (1 finding, resolved before sign-off) |
| Cost per BR | $0.46 (gen) + $0.18 (critic) = $0.64 |
| Total AI cost | ~$8.50 |
| BRs accepted | 2/2 |
| Days to accept | 35 |

---

## What this example shows

1. **Transparency** — every artifact has provenance, every transition is a gate with explicit conditions.
2. **Speed** — decomposing an approved ADAPT — tens of seconds + 2 adversarial cycles.
3. **Traceability** — from a line in the TZ to a passing TC in a handful of substrate query operations.
4. **Delta-TZ** — the affected SR/SPEC/TC/TR are computed automatically.
5. **Two loops instead of one** — the client signs the ACTZ (obligations), the Architect signs the ADAPT (interpretation). The argument "is this a clarification or already a change?" never arises: it is enough to ask whether the decision was put to the client.
6. **Acceptance against the contract** — the ATs, derived by an isolated agent from the effective TZ, caught an interpretation error that all 28 green TCs missed by construction.
7. **Closing the loop** — QG-4 ties the result to business metrics (KPI achievement).
8. **AI-nativeness** — the critic and the generator are different models (isolation); the spot-check finds discrepancies that an automated run alone may miss.

---

## What's next

- [02-transition-guide.md](02-transition-guide.md) — transitioning from a legacy approach.
- [03-tool-guide-git.md](03-tool-guide-git.md) — git as a substrate.
- [04-document-store-substrate.md](04-document-store-substrate.md) — a document-oriented substrate.
- [05-safe-comparison.md](05-safe-comparison.md) — comparison with SAFe / BABOK / ISO 29148.
- [06-compliance.md](06-compliance.md) — compliance mapping (GDPR / FZ-152 / AI Act).
- [07-failure-modes.md](07-failure-modes.md) — failure modes.

---

*RENAR Walkthrough 1.0 — renar.tech*
