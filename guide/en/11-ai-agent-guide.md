---
title: "Working with the AI agent"
order: 11
lang: en
---
# 11. Working with the AI agent

> **This is not "manual mode."** A common misconception: since RENAR requires human approvals, everything must be done by hand. The opposite is true — the bulk of the work (drafts of `BR`/`SR`/`SPEC`/`TC`, the initial interpretation of the TZ, the search for contradictions) is done by the agent. The human does not type artifacts; the human **approves** them at a few fixed points. This chapter shows how to load the standard into the agent, what commands to give, and what you get as output.
>
> The normative delegation model — [standard/05 Roles](../../standard/en/05-roles.md) and the ADAPT approval points — [standard/07 §7](../../standard/en/07-adapt.md). The self-sufficient edition for the agent is `RENAR-AGENT-EN.md` (in the repository root and at [renar.tech](https://renar.tech)).

---

## 1. Who does what: the delegation map

The agent is an executor: it produces and maintains artifacts, but it does not own them and does not sign. The human is the owner and the one who approves. The boundary runs along fixed points:

| Step | The agent does | The human approves |
|---|---|---|
| TZ import | Registers the TZ as an immutable document, captures the client's signature | — (the client signs when delivering the TZ) |
| Adversarial review | A critic agent on a **different model** issues the verdict "findings present" / "no findings" | The Architect records the verdict as evidence |
| ADAPT | Prepares a draft: forward interpretation + backward findings | **The Architect's signature** — and only theirs: the ADAPT is not put to the client |
| ACTZ | Prepares a draft TZ clarification protocol from the findings | **Dual signature:** client + contractor. Before it goes out, the Architect aggregates and rephrases the questions |
| Decomposition | Creates `BR` → `SR` → `SPEC` → `TR` with provenance | The Architect approves the transition to `approved` (QG-0) |
| Tests | Writes `TC` pairs (positive + negative) — **before** the implementation and **not** by the agent that writes it | The automated run records the result (QG-2) |
| Acceptance tests | An **isolated** agent on a different model derives `AT` from the effective TZ alone | The release gate: every `AT` green |
| Delivery | Presents the business outcome | The stakeholder accepts it (QG-4) |

If human approval is not obtained, the artifact stays in `draft`, and the substrate blocks the transitions. The agent never declares itself the owner and never signs.

**The boundary between the two loops.** Everything shown to the client and signed is an obligation (ACTZ). Everything not shown is an interpretation (ADAPT). The agent MUST tell them apart: a question that requires a client decision goes into a draft ACTZ; it is not "filled in" inside the forward interpretation ([standard/07 §7.13](../../standard/en/07-adapt.md#7.13)).

### 1.1 The roles of a vendor's production model ↔ RENAR roles

A vendor organization usually keeps a role list of its own. RENAR does not redefine it ([standard/05 §5.2.2](../../standard/en/05-roles.md#5.2.2)): it is enough to map every role to the closed list of §5 and to a signature zone. An example mapping for a production model with seven roles:

| Vendor role | Zone of responsibility | RENAR role ([§5.2.1](../../standard/en/05-roles.md#5.2.1)) | Signs |
|---|---|---|---|
| Project manager | the order portfolio and capacity | Supervisor | — |
| Chief architect | the architecture portfolio, stacks, verification rules | Architect (organization-level norms; tightenings — `declared-stricter`, [§5.7.2](../../standard/en/05-roles.md#5.7.2)) | stack rules, standard solutions |
| Client manager | the interface with the client | the vendor's authorized person ([§5.4](../../standard/en/05-roles.md#5.4)) | `vendor-signature` of the ACTZ; arranges the stakeholder's signature |
| Order system architect | the architecture of the order, the description set | Architect — owner of ADAPT, SPEC, the set version ([§5.3](../../standard/en/05-roles.md#5.3)) | ADAPT, the set version, protocols, benches (SPEC-TEST) |
| Development supervising engineer | the agent fleet, the course of development | Supervisor; task acceptance — under the prohibition to combine execution and acceptance of one task ([§5.5.5](../../standard/en/05-roles.md#5.5.5)) | task acceptance |
| AI engineer | agent profiles, prompts | Supervisor (AI-agent profiles; `ai-provenance`, [standard/04 §4.10.1](../../standard/en/04-terms.md#4.10.1)) | — |
| Platform administrator | infrastructure | not a RENAR role — provides the substrate capabilities V1–V6 ([standard/03](../../standard/en/03-substrate-versioning.md)) | — |

The client is the Stakeholder (`client-signature`); the agent is the AI agent: it produces artifacts, owns none and signs nothing; the operator is the Supervisor. One person MAY combine roles, and that is named, not compensated; only the execution and the acceptance of one task and the two sides of the ACTZ signature are not combined.

---

## 2. How to load the standard into the agent

The agent does not need the entire normative corpus — for everyday work a single file is enough.

1. Download `RENAR-AGENT-EN.md` — the self-sufficient operational edition (≈400 lines): the artifact map, the preconditions of artifacts, ADAPT, the closed lists, the hard rules, the recording forms (frontmatter + body sections).
2. Place the file beside the agent — in the project context, the system prompt, or as a session attachment (depends on your tool).
3. Give the setup command: "Study this file — it is the RENAR standard. From now on do requirements engineering strictly by it."
4. Optionally add project context: the registry of systems and subsystems, a link to your artifact substrate, the naming convention.

Attach the full corpus (`standard/`, `reference/`) only when a formal dispute over the exact wording of a norm arises — for most tasks the operational edition is enough.

---

## 3. How to give commands

Commands to the agent are ordinary natural-language statements tied to the artifact the agent produces. Below are typical examples; substitute your own identifiers.

**TZ import and review:**

```text
Here is the client TZ (file TZ-2026-001). Register it as an immutable document.
Then run an adversarial review: issue the verdict "findings present" or "no findings",
with reasoning across the 7 categories (contradiction, gap, hidden assumption,
feasibility, regulatory, terminology, scope).
```

**Creating an ADAPT (if there are findings):**

```text
The review found findings. Prepare an ADAPT draft: a forward interpretation by TZ
section plus backward findings. Do not fill in for the client — turn whatever is
unclear into a finding. The ADAPT is an internal document; it does not go to the client.
```

**The TZ clarification protocol (ACTZ):**

```text
Collect the findings that require a client decision into a draft ACTZ — "TZ Clarification
Protocol No. 1". Phrase every clause in the language of obligations ("the domain is
compared case-insensitively"), not of interpretation, and add the contractor's proposal.
Status: draft — putting it to the client and signing it is my job.
```

**Decomposition:**

```text
ACTZ-001 is signed by both parties and the ADAPT is approved. Set decided-in on the
protocol clauses in every closed finding. Derive a BR (business goal) from the ADAPT,
then SR (system behavior), then SPEC of the needed types. Set source and parent on
every artifact. Show the relationship graph before committing.
```

**Tests:**

```text
For every normative statement in SR-03, write a TC pair: positive and negative.
The Pass criterion must be binary and observable. There is no implementation yet, and
there must not be: first we freeze the tests, then the code — and a different agent
will write the code.
```

**Acceptance tests (a separate session, a different model):**

```text
Here is the effective TZ: TZ-2026-001 plus the signed ACTZ-001 and ACTZ-002. That is all
you have and all you will get — no requirements, no specifications, no tests, no code.
Derive the ATs: acceptance tests per contract clause, with a verbatim quote of the clause
next to the verification steps, in positive/negative pairs.
```

A good command names the **input** (which artifact), the **action** (what to produce), and the **expectation** (what to check). The agent handles the routine itself; step in where your decision is needed.

---

## 4. What you get as output

The agent returns not chat text but ready substrate artifacts — with machine-readable frontmatter and a body in fixed sections:

- **`BR`** — the business need: who, what, why; success criteria; context with a reference to an ADAPT.
- **`SR`** — system behavior: one requirement, behavior, constraints, link to `SPEC`.
- **`SPEC-<TYPE>`** — a specification of one of the twelve types, linked to the `SR` via `constrained-by[]`.
- **`TC`** — positive/negative pairs with `verifies[]` pointing at the verified artifact.
- **`ACTZ`** — a draft TZ clarification protocol: decision clauses in the language of obligations, `status: draft` until it is put to the client.
- **`AT`** — acceptance tests referencing the TZ and the ACTZs only, with a verbatim quote of the contract clause and the provenance of the isolated generator.

Every artifact has its `source` set (origin from the TZ or an ADAPT) and, for AI-generated ones, an `ai-provenance` block (model, prompt template, the fact of a human edit). Ready copy-paste skeletons for all forms are in [reference/12 Document templates](../../reference/en/12-document-templates.md).

The mark of good output: from any artifact a chain traces upward — to the `SR`, to the `BR`, to a TZ section. If the chain breaks, the artifact is not ready.

---

## 5. Points of human approval

Delegation to the agent is broad, but it has hard boundaries. A human is required in five places (normatively — [standard/10 Lifecycle and quality gates](../../standard/en/10-lifecycle-qg.md)):

1. **TZ signature** — the client signs the contractual input; the agent only registers it.
2. **The ACTZ dual signature** — the client and the contractor sign the TZ clarification protocol. This is the only place where an obligation to the client comes into being ([§7.13](../../standard/en/07-adapt.md#7.13)).
3. **The Architect's signature on the ADAPT (QG-3)** — the Architect confirms that every finding is worked through (each carrying a `decided-in` on a clause of a signed ACTZ) and that the interpretation is feasible. The client does not sign the ADAPT: they never saw it ([§7.5](../../standard/en/07-adapt.md#7.5)).
4. **QG-0 approval** — the Architect moves `BR`/`SR`/`SPEC` from `draft` to `approved`. Only after this is further decomposition allowed.
5. **Acceptance** — the release gate on the ATs (all green) and, optionally, QG-4 on the business outcome.

Between these points the agent acts on its own. An attempt to make the agent sign, or to declare it `Accountable`, breaks the role model.

---

## 6. Who writes the test, and why it MUST be red

This is the one part of the work where the agent is explicitly **forbidden** to do everything at once — and the prohibition is substantive, not ceremonial.

**Authorship isolation** ([standard/09 §9.18](../../standard/en/09-test-cases.md#9.18)). A test written by the same agent that writes the code — and especially one written **after** the code — honestly checks what was written. But what it ought to check is the requirement, not the code. So the isolation rests on three axes:

| Axis | What it means in practice |
|---|---|
| Time | The `TC` is frozen in `ready` before the task starts. The substrate will not let a TR open while the tests are unfrozen |
| Author | The agent writing the `TC` is not the one writing the implementation: a different session or a different model |
| Change | An edit to the Pass/Fail criteria of a frozen test is approved by an engineer; silently "nudging the test to fit the code" is not allowed |

**Red history** ([§9.18.2](../../standard/en/09-test-cases.md#9.18)). A test never observed red is not evidence. The pinning run happens **before** implementation, and the test MUST fail: the functionality under test does not exist yet. Green on the pinning run is not a success but a signal to investigate: either the test checks nothing, or the functionality is already there.

There is one exception — tests of class `implementation-originated` ([standard/06 §6.13](../../standard/en/06-requirements-hierarchy.md#6.13)): an internal technical detail added by the agent during implementation (a defensive check, logging) that is not observable by the client. Such a test is written after the code and is born green — by construction it has no red history. The compensation is mandatory: its teeth are proven by a **killed mutant**. And the class boundary is hard: client-observable behavior is not legalized through it — an obligation to the client cannot arise out of code.

**Isolation of the AT generator** ([§9.19.2](../../standard/en/09-test-cases.md#9.19)). The acceptance tests are derived by a separate agent on a different model, whose input is **only** the effective TZ. Access to the ADAPT, the `BR`/`SR`/`SPEC`, the `TC`s, and the code is forbidden — not out of hygiene, but because an AT is a simulation of the client's gaze. An agent that has seen the ADAPT will reproduce the same interpretation, and the ATs will start confirming it instead of the contract: the acceptance level collapses into the verification level.

---

## 7. A full session end to end

To dispel the sense of "magic", here is an end-to-end scenario — what the agent does and where the human steps in. The order of steps is an illustration: the agent edition sets the preconditions of artifacts (its section 4), while the order of work is your organization's production model ([standard/01 §1.2.1](../../standard/en/01-scope.md#1.2.1)):

1. You hand the agent the TZ and `RENAR-AGENT-EN.md`. The agent registers the TZ.
2. A critic agent (a different model) runs an adversarial review → verdict "findings present".
3. The agent prepares an ADAPT draft with the findings and a draft ACTZ. The **Architect** brings the protocol into a form the client can understand, puts it forward, and signs it together with the client → the ACTZ is signed; the findings receive their `decided-in`; the **Architect** approves the ADAPT with their signature.
4. The agent derives `BR` → `SR` → `SPEC`, shows the relationship graph. The **Architect** approves QG-0.
5. A test agent writes the `TC` pairs; the pinning run is red; the tests are frozen.
6. A **different** agent writes the implementation until the tests turn green; the runner records the result (QG-2).
7. Before the trials an isolated agent derives the `AT`s from the effective TZ and runs them. All green → the product can be submitted.
8. The agent assembles the business outcome. The **stakeholder** accepts it (QG-4).

The human joins in to approve, not to type artifacts — but join in they must, and only they put a signature under an obligation.

---

## 8. Common mistakes

| Mistake | Why it is bad | What to do instead |
|---|---|---|
| Asking the agent to "just write code" from the TZ | The source of truth is lost — code without requirements | Requirements first, then implementation derived from them |
| Skipping the adversarial review | "No ADAPT needed" becomes a silent assumption | The review is always mandatory; "no findings" is a recorded verdict of a different model |
| Letting the agent sign an ADAPT or an ACTZ | The agent is not the owner or a signatory | The signature is always a human: the Architect signs the ADAPT; the client and the contractor sign the ACTZ |
| Treating a finding as closed without a signed ACTZ | The interpretation rests on a decision the client never made | `resolved` only with a `decided-in` on a clause of a signed protocol |
| Sending the agent's raw output to the client as a protocol | The client signs what they do not understand | The Architect aggregates and rephrases the questions — in the language of obligations |
| Reconstructing `SR` from finished code | Source-of-truth inversion | Change the requirement → then the code; the exception is a justified bug-fix |
| Using one model to both generate and check | No independence of review | The critic is on a different model than the generator |
| One agent writes both the code and its test | The test checks the code, not the requirement | Different agents; the test is frozen before implementation and was red at least once |
| Letting the AT generator peek at the SRs or the ADAPT "for context" | The ATs start confirming the interpretation instead of the contract | Input: the effective TZ only; a different model; no internal loop whatsoever |
| Running ATs derived six months ago at the trials | The system is presented against a contract that no longer exists | ATs are regenerated before every round of trials |

---

[← Back to the guide overview](README.md)
