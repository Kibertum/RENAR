---
title: "Substrate: VCS (git)"
description: "Implementing V1-V6 on git: .req repo layout, submodule pinning, PR/MR workflow, pre-commit hooks, delta-TZ flow."
order: 3
lang: en
version: "1.0"
---

# 03. Substrate: VCS — git

> A concrete implementation of RENAR on git. The `.req` repository structure, submodule pinning between `.req` and `.src`, the PR/MR review workflow, pre-commit hooks for capabilities V1-V6, and the delta-TZ workflow. This guide is informative; the normative content (capability requirements, schemas, lifecycle) lives in `standard/`. For an alternative on a document-oriented store, see [guide/04-document-store-substrate](04-document-store-substrate.md).
>
> **Prerequisites:** [standard/03-substrate-versioning](../../standard/en/03-substrate-versioning.md) (the normative capabilities V1-V6), [reference/02-schemas](../../reference/en/02-schemas.md) (frontmatter schemas).

---

## 1. When to choose git as the substrate

Git is the default substrate for:
- Open standards and projects (no dependency on internal infrastructure).
- External clients with no dedicated document-store infrastructure.
- Teams with an established PR workflow.
- Projects where requirements and code share one ecosystem (the same VCS provider).

Git is **not** optimal when:
- You need frequent concurrent edits to a single artifact by several authors without merge conflicts.
- You need built-in full-text search without external tools.
- You need a UI for non-technical stakeholders (PMs, legal) without the git CLI.

In such cases, consider a document-oriented store — [guide/04](04-document-store-substrate.md).

---

## 2. Two-repository layout

The canonical structure is two linked repositories.

```text
<project>/
├── <project>.req/                ← REQUIREMENTS repository (a separate VCS repo)
│   ├── tz/                       ← TZ-YYYY-NNN.md (immutable after registration)
│   ├── actz/                     ← ACTZ-NNN.md (TZ clarification protocols, dual signature)
│   ├── adapt/                    ← ADAPT-NN.md (bridge artifacts)
│   ├── ar/                       ← AR-NNN.md (adversarial-review records, §7.4.6)
│   ├── br/                       ← BR-NN.md
│   ├── sr/                       ← SR-NN.md
│   ├── specs/                    ← SPEC-* by subfolder (arch/, api/, data/, int/, …)
│   │   ├── arch/  api/  data/  ui/  ai/  int/  proc/  sec/  ops/  test/  doc/
│   ├── tr/                       ← TR-NN.md (task requirements)
│   ├── tests/                    ← TC-NN.md (test cases; `TC` are standalone artifacts)
│   ├── at/                       ← AT-NN.md (acceptance tests from the effective TZ)
│   ├── dpia/                     ← DPIA-NN.md (optional, for regulated projects)
│   ├── library/                  ← templates, patterns
│   ├── docs/                     ← AI-generated documentation
│   ├── COVERAGE.md               ← auto-generated (`[coverage]` commits)
│   ├── REQUIREMENTS.md           ← auto-generated index
│   └── TEST-PLAN.md              ← auto-generated
└── <project>.src/                ← IMPLEMENTATION repository
    ├── src/                      ← code
    ├── tests/                    ← TC implementations (addressed by `automation.location`)
    ├── requirements/             ← submodule → <project>.req @ <commit>
    ├── .gitmodules
    └── README.md
```

In `<project>.req/.gitattributes`, for bot-generated artifacts:

```text
COVERAGE.md      linguist-generated=true
REQUIREMENTS.md  linguist-generated=true
TEST-PLAN.md     linguist-generated=true
docs/**          linguist-generated=true
```

This excludes them from code statistics and substantially shrinks the PR diff.

---

## 3. Capability mapping V1-V6 onto git

| Capability ([standard/03 §3.3](../../standard/en/03-substrate-versioning.md#3.3)) | Norm | Git mechanism |
|---|---|---|
| V1 — immutable history | Past artifact states cannot be rewritten retroactively | Immutable commit history; protected branch + force-push ban on `main`; the `id:` in frontmatter is stable |
| V2 — atomic change unit | A change applies as a whole or not at all | An atomic commit / squash-merge PR as a single unit; a delta-ADAPT = one PR |
| V3 — diff & review | A proposed change is reviewed before approval | `git diff` + PR/MR review with a mandatory approve before merge |
| V4 — branching / change-set | A draft is kept separate from the approved truth | The set-draft branch against `main` (the approved version); a PR is a change-set, merging = release of version `N.M` |
| V5 — cross-substrate version pin | The implementation references a specific set version | Submodule pin between `.req` and `.src`; the set version tag `renar-vN.M` = `set-version`; `automation.set-version` in TC |
| V6 — author & timestamp | Every change unit records its author and time | Commit metadata: author + date; for an AI agent — `ai-provenance` |

> RENAR checks on git (schema validation, status-transition control, coverage reports, link integrity, reconciliation / drift detection) are **enforcement** mechanisms layered on top of the capabilities, not V1–V6 themselves. Their split across pre-commit / CI is §3.1 and §8 of this guide; the normative drift classes are [standard/04 §4.11](../../standard/en/04-terms.md#4.11).

> **State of these scenarios.** The mechanisms described below for `git` are the **v1.0 target picture**. In practice only `scripts/validate-frontmatter.js` is ready; the rest (`validate-lifecycle`, `validate-references`, `generate-coverage`, `detect-drift`, and others) are in the **phase-8 backlog** (section §8 of this guide). Until the scripts exist, the listed capabilities are upheld manually and through code review; automatic enforcement of RENAR-3+ levels "out of the box" on `git` is not yet achievable. The same caveat applies to the document store ([guide/04](04-document-store-substrate.md)).

---

## 4. Submodule pinning

`<project>.src` pins a **specific commit** of `<project>.req` through a git submodule.

### 4.1 How it works

- In `<project>.src`, the `requirements/` directory is a submodule on `<project>.req`.
- At build / CI time the code knows: "I implement the requirements as of commit `abc1234`."
- A developer working a task opens `requirements/sr/SR-05.md` via an ordinary `cat` or the IDE — it is a file in the worktree.

### 4.2 Bump pattern

When the requirements are updated:
1. A PR into `<project>.req` with the requirement changes → review → merge.
2. A **separate** PR into `<project>.src` that **only** moves the submodule pointer:
   ```bash
   cd requirements
   git pull origin main
   cd ..
   git add requirements
   git commit -m "bump requirements: TZ-2026-042 delta + 3 new SR"
   ```
3. This PR makes it explicit: "the requirements were updated to commit X."

### 4.3 Why submodule, not subtree / monorepo

- **Provenance:** for any code commit, the exact requirement version it implemented is known.
- **Review isolation:** requirement review (in `.req`) and code review (in `.src`) do not get mixed.
- **delta-TZ atomicity:** a delta is an atomic PR into `.req` plus a subsequent submodule bump in `.src`. There is no "partially applied delta."
- **Document-store compatibility:** when migrating to a document-oriented store, submodule pinning becomes revision-token pinning — the concept is the same.

Alternatives (subtree, monorepo): consider them only if a submodule does not work for reasons specific to your VCS provider; in all other cases submodule is the recommendation.

---

## 5. PR/MR review workflow

Two review levels, split across the repositories.

### 5.1 Review in `<project>.req`

**Reviewer focus:**
- frontmatter is schema-valid.
- The citation to the TZ / ADAPT is present and valid.
- The `parent` / `verified-by` / `constrained-by` links exist.
- The lifecycle transition is legitimate.
- A source citation is present in the body for every normative statement (at RENAR-4+).
- The adversarial AI-review prompt passed (at RENAR-5).

**Approval = QG-0** ([standard/10 §10.3.1](../../standard/en/10-lifecycle-qg.md)).

### 5.2 Review in `<project>.src`

**Reviewer focus:**
- The submodule pointer matches a merged commit in `.req`.
- The implementation references the approved set version through `automation.set-version` (tag `renar-vN.M`).
- New / changed TC match the contract in `.req`.
- No changes to TC pass/fail criteria without a `[test-spec-change]` tag.

**Merging the draft branch into `main` = QG-0 of the set** (release of version `N.M`, tag `renar-vN.M`); **QG-2** ([standard/10 §10.3.3](../../standard/en/10-lifecycle-qg.md)) — the verification entry of the version after all TCs pass on it.

### 5.3 Forbidden anti-patterns

- **A PR spanning both `.req` and `.src`** — it breaks review isolation; the substrate hook forbids it.
- **Changing the submodule pointer without a merged commit in `.req`** — the pointer references an untracked commit; CI blocks it.
- **`[test-spec-change]` without a separate approval** — a TC pass/fail criterion changed together with a code fix; CI blocks the merge.

---

## 6. Pre-commit and pre-merge hooks

The minimal substrate hook set for RENAR-3+ on git.

### 6.1 Pre-commit (in `<project>.req`)

```bash
# Runs on commit into .req
# Capability V2: schema validation
yamllint --strict $(git diff --cached --name-only | grep '\.md$')
node scripts/validate-frontmatter.js $(git diff --cached --name-only --diff-filter=AM)

# Capability V3: legal lifecycle transitions
node scripts/validate-lifecycle.js $(git diff --cached --name-only --diff-filter=M)

# Capability V5: reference integrity (fast check of changed files only)
node scripts/validate-references.js --changed-only $(git diff --cached --name-only)
```

### 6.2 Pre-merge (CI job in `<project>.req`)

The full checks that are too slow for pre-commit:

```yaml
- name: Full reference validation (V5)
  run: node scripts/validate-references.js --all

- name: Coverage report regeneration (V4)
  run: node scripts/generate-coverage.js
  # commits as [coverage] bot user

- name: Drift detection (reconciliation, weekly)
  run: node scripts/detect-drift.js
  if: github.event.schedule == '0 0 * * 0'
```

### 6.3 Pre-merge (CI job in `<project>.src`)

```yaml
- name: Submodule points to merged commit
  run: |
    cd requirements
    git fetch origin
    git merge-base --is-ancestor HEAD origin/main

- name: TC implementations pinned to the current set-version
  run: node scripts/validate-tc-version-pinning.js

- name: No Pass/Fail change without [test-spec-change]
  run: node scripts/validate-test-spec-changes.js
```

---

## 7. TZ workflow on git

### 7.1 Forward workflow (project from scratch)

Initial creation of the requirements axis from a new TZ:

1. **branch in `.req`:** `git checkout -b init/TZ-2026-001` in `<project>.req`.
2. **TZ registration:** `tz/TZ-2026-001.md` (immutable after registration; record the client signature and date).
3. **Adversarial review of the TZ and — on findings — the ADAPT:** the review is always mandatory and its verdict is recorded as `ar/AR-001.md` ([§7.4.1.2](../../standard/en/07-adapt.md#7.4.1)). On a "findings present" verdict, `adapt/ADAPT-001.md` is created — Forward (an interpretation per § of the TZ) + Backward (findings), [standard/07](../../standard/en/07-adapt.md); the ADAPT is an internal document and is not put to the client. On a "no findings" verdict no ADAPT is created, and BR / SR / SPEC carry `source.tz-section` + `source.adversarial-review-ref: AR-001` ([§7.4.1.1](../../standard/en/07-adapt.md#7.4.1)).
4. **ACTZ:** findings that require a decision from the client are put forward as the protocol `actz/ACTZ-001.md` — "TZ Clarification Protocol No. 1" ([§7.13](../../standard/en/07-adapt.md#7.13)). `status: draft → sent → signed`; the client's and the contractor's signatures are recorded with author + timestamp (V6). Once signed, every finding receives `decided-in: ACTZ-001 §M`.
5. **Architect's signature → QG-ADAPT-approve:** ADAPT moves to `approved` (the Architect's signature alone, [§7.5](../../standard/en/07-adapt.md#7.5)), `open-questions-count == 0`.
6. **Decomposition:** the AI agent derives BR from the approved ADAPT, then SR (`source.adapt`) and SPEC (`source.adapt`; SR references them through `constrained-by[]`, [§8.6.1](../../standard/en/08-specifications.md#8.6)), plus paired pos/neg TC.
7. **QG-0 approval** of each artifact (`draft → approved`); a CI dry-run of the new TC.
8. **PR into `.req` → merge;** the bot regenerates REQUIREMENTS.md / COVERAGE.md / TEST-PLAN.md.
9. **Set up `.src`:** add the submodule `requirements/ → <project>.req @ <commit>`, create TR, implement, QG-2.
10. **AT before the trials:** an isolated agent (a different model, with no access to `.src` or to the internal artifacts of `.req`) derives `at/AT-NN.md` from the effective TZ — `tz/` plus the signed `actz/` ([§7.14](../../standard/en/07-adapt.md#7.14)). The run refreshes `ACCEPTANCE.md`; the release gate requires every AT to be green ([§10.4.3](../../standard/en/10-lifecycle-qg.md#10.4)).

### 7.2 Delta-TZ workflow

The full sequence for applying a new delta-TZ.

1. **branch in `.req`:** `git checkout -b change/TZ-2026-042` in `<project>.req`.
2. **delta-TZ creation, adversarial review, and — on findings — the delta-ADAPT:** `tz/TZ-2026-042-delta.md` (the delta text); the adversarial reviewer issues a verdict, recorded as `ar/AR-NNN.md`. On a "findings present" verdict, `adapt/ADAPT-NNN-delta.md` is created (forward interpretation + backward findings, [standard/07 §7.6](../../standard/en/07-adapt.md#7.6)); on a "no findings" verdict no delta-ADAPT is created and the affected BR / SR / SPEC reference `source.tz-section: TZ-2026-042-delta` directly ([§7.6.1](../../standard/en/07-adapt.md#7.6)). The client's decisions on the delta-TZ findings are recorded in a new `actz/ACTZ-NNN.md` (dual signature); the delta-ADAPT is approved by the Architect's signature before the affected requirements are modified. The effective TZ is recomputed — and the ATs are regenerated from it.
3. **Impact analysis:** the AI agent finds the affected BR / SR / SPEC / TC; marks the TC as `obsolete-pending`.
4. **Update artifacts:** the AI agent updates / creates BR / SR / SPEC and paired TC (pos+neg) on the same branch.
5. **Adversarial critic review:** at RENAR-5 — mandatory (a different AI model).
6. **CI dry-run:** the new TC must run without infrastructure errors.
7. **Finalize:** merge the draft into `main` = set version `N.M+1` (tag `renar-vN.M+1`, version record), regenerate REQUIREMENTS.md / COVERAGE.md / TEST-PLAN.md (bot).
8. **PR into `.req` → QG-0 approval → merge.**
9. **branch in `.src`:** `git checkout -b change/TZ-2026-042` in `<project>.src`.
10. **Bump submodule:** `cd requirements && git pull && cd ..`.
11. **Create TR / tasks:** for new / changed SR (via `/task` or substrate-specific tooling).
12. **PR into `.src` "bump requirements + tests + new tasks" → review → merge.**
13. **Development:** pick up a TR → implement → CI → the bot fills `last-run` in the TC.
14. **QG-2:** on green TCs on the version — the runner appends the verification entry to the version record; the requirement has no separate status.

Every step except (1) and (9) is automated natively for the substrate through scripts or CI.

---

## 8. Migration notes: scripts/ modernization

The existing [scripts/](../../scripts/) are bash + node helpers from an early era of the project. Modernization status as of v1.0:

- ✅ **Legacy terms cleaned up.** `req-branch.sh`, `req-finalize.sh`, `req-ai-instructions.md`, `req-use-template.sh` no longer use the deprecated `INT-SR`, `AIC`, `UIC`, `tech-specs`, `ai-concepts`, `ui-concepts`. The closed list of current types is [standard/04 §4.4](../../standard/en/04-terms.md#4.4) (BR/SR/TR + 11 SPEC). Residual mentions in [reference/01-glossary.md](../../reference/en/01-glossary.md) and [reference/02-schemas.md](../../reference/en/02-schemas.md) are legacy mappings for projects migrating from earlier versions.
- ✅ **Schema validator created.** `scripts/validate-frontmatter.js` — a Node ES module that checks the frontmatter of every `.md` in `standard/`, `guide/`, `reference/`, `core/`: required fields `title` / `order` / `lang ∈ {ru, en}`. Run: `node scripts/validate-frontmatter.js [--quiet]`; exit 0 if all are valid, exit 1 on the first error. The schema source is [reference/02-schemas](../../reference/en/02-schemas.md).
- ✅ **ADAPT in the forward workflow.** Initial creation (TZ → adversarial review → an ADAPT on findings → BR) is documented in §7.1 of this guide; the delta workflow with the delta-ADAPT is in §7.2, per [standard/07 §7.6](../../standard/en/07-adapt.md#7.6).
- ⏳ **Pre-commit hooks** (§6.1) are still partly scattered across `req-finalize.sh`. The target state is to factor them into separate `scripts/validate-*.js`; `validate-frontmatter.js` is the first such module.

This chapter describes the **target script set** at the v1.0 release; lines without a ✅ mark are work for **phase 8** (continuation of the backlog).

---

## 9. Common operations

Shortcuts for frequent operations.

| Operation | Command |
|---|---|
| Find an SR by ID | `grep -r "^id: SR-12" sr/` |
| Find the TC verifying SR-12 | `grep -rl "id: SR-12" tests/` |
| Find an orphan SR (no TC) | `node scripts/find-orphans.js sr` |
| Create a new SR from a template | `cp library/templates/sr.md sr/SR-NN.md && $EDITOR sr/SR-NN.md` |
| Diff frontmatter over a period | `git log --all --since=...  -- sr/` |
| Current set version | `git describe --tags --match 'renar-v*'` |
| List stale TC (last-run < current version) | `node scripts/list-stale-tc.js` |

---

## 10. CI/CD integration patterns

### 10.1 Bot-commit conventions

Auto-generated artifacts are committed by substrate hooks with conventional commit-message tags for machine parsing:

| Tag | When | What is committed |
|---|---|---|
| `[coverage]` | Post-merge in `.req` | Regeneration of COVERAGE.md / REQUIREMENTS.md / TEST-PLAN.md |
| `[baseline-update]` | After approval of a PR that changes a baseline | Regeneration of PNG baselines for SPEC-UI |
| `[bump-req]` | Submodule bump in `.src` | Only the submodule pointer + minimal metadata |
| `[reconcile]` | The reconciliation hook finds drift | Auto-fix of obvious mismatches; flag for the rest |
| `[test-spec-change]` | A TC pass/fail criterion changed | Manual; requires a separate approval from a reviewer |

The substrate hook parses the commit message and applies different validation rules depending on the tag. `[coverage]` / `[bump-req]` / `[reconcile]` commits come from the bot user; `[baseline-update]` / `[test-spec-change]` come from a human + bot signature.

### 10.2 Bot-user setup

- A separate bot account (e.g. `renar-bot@<org>`) with **write** permission in `<project>.req` and `<project>.src`.
- Bot commits are signed (GPG / SSH commit signature).
- The bot user **cannot** approve a PR (separation of duties — approval is always human).

### 10.3 branch protection

In both repositories:
- `main` (or `master`) is protected. A push requires a merged PR.
- Required status checks: schema validation (V2), reference integrity (V5), lifecycle validation (V3).
- Reviewers required: ≥ 1 for most; ≥ 2 for priority=must BR / SR / SPEC.

---

## 11. Cross-references

- [standard/03-substrate-versioning](../../standard/en/03-substrate-versioning.md) — the normative substrate requirements (capabilities V1-V6).
- [reference/02-schemas](../../reference/en/02-schemas.md) — frontmatter schemas for the validation hooks.
- [02-transition-guide](02-transition-guide.md) — where this guide fits into the path from pre-RENAR to RENAR-N.
- [04-document-store-substrate](04-document-store-substrate.md) — an informative overview of the document-oriented store (the git alternative).
- [07-failure-modes](07-failure-modes.md) — what can go wrong on git as the substrate (the hook-bypass scheme, etc.).
- [standard/10-lifecycle-qg](../../standard/en/10-lifecycle-qg.md) — the normative lifecycle transitions that the validate-lifecycle hook must enforce.

---

## 12. Open questions

- Should the minimal hook implementations be standardized as a **single** specification description that both VCS and the document store rely on?
- Submodule vs subtree: which conditions make subtree an acceptable alternative? The guide is currently unambiguously in favor of submodule.
- `[coverage]` bot user: best practice for signing / permissions in multi-org projects?
- Drift-detection cadence: weekly is a good default, but what about high-velocity teams (> 50 PR/week)?
