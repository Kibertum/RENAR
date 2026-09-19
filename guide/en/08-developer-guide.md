---
title: "Developer Guide"
order: 8
lang: en
---
# 08. Developer Guide

> **Example for the `git` substrate only.** This chapter illustrates **one** possible scenario, using GitHub and subsystems split across repositories (`.req` and `.src` directories). **RENAR is not tied to any specific substrate implementation**; your hooks and pipelines may be arranged differently. The generally applicable chapters are [§6 Requirements hierarchy](../../standard/en/06-requirements-hierarchy.md), [§8 Specifications](../../standard/en/08-specifications.md), [§10 Lifecycle and quality gates](../../standard/en/10-lifecycle-qg.md). For an alternative that does not use a VCS as the file base, see [04-document-store-substrate.md](04-document-store-substrate.md).
>
> **Fictional company AcmeCorp:** the `acme-platform` platform and four subsystems — `acme-portal`, `acme-ai`, `acme-orchestrator`, `acme-site`. Replace the names in the examples with your own; references to the normative chapters are unaffected.

---

## 1. What you need to know before you start

**Two repository types.** Each subsystem has two separate repositories:

| Repository | Suffix | What's inside | Who writes it |
|---|---|---|---|
| Requirements | `.req` | BR, SR — what the system must do | Architect, Tech Lead |
| Source code | `.src` | Code, tests, CI/CD | Developer |

The split is deliberate: requirements are versioned independently of the code, have a different review cycle, and have different access rights.

**Hierarchy:** System (`acme-platform`) → Subsystem (`acme-portal`, `acme-ai`, `acme-orchestrator`, `acme-site`) → Module (if present). Each level has its own `.req` repository; upper-level requirements are parents for the requirements below them.

**Three requirement levels:**

```text
BR  — Business Requirement  (why the business needs it)
 └── SR  — System Requirement  (what the system does)
      └── TR  — Task Requirement  (the specifics for implementation)
                  ↑ these are the fields of your task in the tracker, not a file
```

**TR is not a file** — it is Goal + Acceptance Criteria in a tracker task.

---

## 2. Initial setup of the local environment

**Step 1.** Confirm with your Tech Lead which subsystem you are working on: `acme-portal` (the customer portal), `acme-ai` (the AI pipeline), `acme-orchestrator` (the orchestrator), `acme-site` (the public site).

**Steps 2-3.** Create the structure and clone the repositories:

```bash
mkdir -p ~/projects/acmecorp/acme-platform && cd ~/projects/acmecorp/acme-platform

# Required for everyone — the system-level parent requirements (read-only):
git clone git@github.com:acmecorp/acme-platform/acme-platform.req

# Required — your subsystem's repositories:
SUBSYSTEM=acme-portal   # replace with your own
mkdir -p $SUBSYSTEM
git clone git@github.com:acmecorp/acme-platform/$SUBSYSTEM/$SUBSYSTEM.req $SUBSYSTEM/$SUBSYSTEM.req
git clone git@github.com:acmecorp/acme-platform/$SUBSYSTEM/$SUBSYSTEM.src $SUBSYSTEM/$SUBSYSTEM.src

# As needed — the requirements of adjacent subsystems (read-only):
mkdir -p acme-ai && git clone git@github.com:acmecorp/acme-platform/acme-ai/acme-ai.req acme-ai/acme-ai.req
```

**Step 4.** Verify: `find ~/projects/acmecorp -maxdepth 4 -name ".git" -type d | sed 's|/.git||' | sort`. The expected result for an Acme Portal developer:

```text
~/projects/acmecorp/acme-platform/acme-platform.req
~/projects/acmecorp/acme-platform/acme-portal/acme-portal.req
~/projects/acmecorp/acme-platform/acme-portal/acme-portal.src
```

---

## 3. Folder structure on the local machine

The local structure **mirrors** the repository hierarchy in the hosting platform — the relative `../..` paths between repositories become predictable.

> For the directory layout (the substrate layout) see [guide/03 §2 and §4](03-tool-guide-git.md): the canonical repository scheme + pinning `.src` → `.req` via a submodule (capability V5). Here is the typical local placement for an IDE: several clones lying side by side, without a mandatory submodule in the working folder.

```text
~/projects/acmecorp/acme-platform/
  acme-platform.req/          # system requirements (read-only)
  acme-portal/
    acme-portal.req/          # your subsystem's requirements
    acme-portal.src/          # your subsystem's code — this is where you work
  acme-ai/{acme-ai.req,acme-ai.src}/             # cloned as needed
  acme-orchestrator/{acme-orchestrator.req,acme-orchestrator.src}/
  acme-site/{acme-site.req,acme-site.src}/
```

> **Rule:** do not change the folder names — they match the project names in the git hosting platform. This lets scripts and CI work with the same paths everywhere.

---

## 4. Setting up the workspace in the IDE

**VS Code Workspace.** Create `<subsystem>.code-workspace` in the subsystem folder:

```bash
cat > ~/projects/acmecorp/acme-platform/acme-portal/acme-portal.code-workspace << 'EOF'
{
  "folders": [
    { "path": "acme-portal.src", "name": "Code — Acme Portal" },
    { "path": "acme-portal.req", "name": "Requirements — Acme Portal" },
    { "path": "../acme-platform.req", "name": "Requirements — System (read only)" }
  ],
  "settings": { "files.exclude": { "**/.git": true } }
}
EOF
```

Open it: `code ~/projects/acmecorp/acme-platform/acme-portal/acme-portal.code-workspace`. The side panel shows all three repositories at once.

**JetBrains (IntelliJ, WebStorm, PyCharm).** Open each repository as a separate module via **File → Attach Project** or **Project Structure → Modules**.

---

## 5. Working with requirements

**5.1 How to read.** Before starting a task, read the SR (`acme-portal.req/sr/SR-NN-*.md`). In the SR frontmatter, find the `parent` field — a link to the parent BR:

```yaml
parent: { id: BR-01, repo: "acmecorp/acme-platform/acme-platform.req", file: "br/BR-01-order-ai-dev.md" }
```

Open the parent BR — this is the "why the functionality exists".

**5.2 Tracing.** The chain: `Task TASK-42 → SR-01 (acme-portal.req/sr/...) → BR-01 (acme-platform.req/br/...)`. If anything is unclear, move up the chain.

**5.3 Changing a requirement.** The developer opens an Issue in the `.req` repository describing the mismatch between the SR and the actual behavior. The Tech Lead / Architect makes the change:

```bash
cd acme-portal.req
git checkout -b change/TZ-2026-002-totp
# Edit the SR file; update version + updated in the frontmatter; update source.tz-section to the delta-TZ section
git add sr/SR-01-auth.md
git commit -m "[delta:TZ-2026-002] SR-01 v1.2: add TOTP"
# Open an MR/PR in the git hosting platform
```

> **Forbidden:** committing requirement changes directly to `main` without an MR/PR and review.

**5.4 What you must not do.** Change `acme-platform.req` without the Architect's agreement; change the requirements of adjacent subsystems (`acme-ai.req`, `acme-orchestrator.req`); delete requirement files (only move them to `status: deprecated`); create version files (`SR-01-v1.md`, `SR-01-v2.md`) — the history lives in `git log`.

---

## 6. Working with tasks

**6.1 Before you take a task** — all QG-0 Approval Gate items are satisfied ([reference/01 §2.4](../../reference/en/01-glossary.md); pre-v1.0 legacy "Context Gate"):

- The Goal is formulated; Acceptance Criteria exist — concrete, testable, independent.
- There is at least one negative scenario in the AC.
- The task references an SR (a field in the tracker); a work type is assigned.
- If it touches security — `threat-surface` is declared.

If something is missing — **do not take the task into work**; go back to the Supervisor/Tech Lead.

**6.2 Acceptance Criteria.** Each AC is a separate test. Good AC: `POST /auth/login returns 200 and a JWT token with valid credentials`; `POST /auth/login returns 401 with an incorrect password` (a negative scenario); `POST /auth/login returns 422 if the email field is missing`; `the JWT token expires after 24 hours`.

Bad AC (do not take such a task): "Login should work correctly"; "Error handling should be robust"; "The system should be secure".

**6.3 If an AC is unclear or contradicts the SR:** read the SR + the parent BR; add a comment to the task with a concrete question; do not interpret silently — an AI interprets silently, which is exactly why clear requirements are needed.

---

## 7. The Git process

**7.1 Working with code (`.src`).** The standard feature-branch workflow:

```bash
cd acme-portal/acme-portal.src
git checkout main && git pull
git checkout -b feat/TASK-42-totp-auth
# ... work ...
git add src/auth/totp.py
git commit -m "feat(auth): implement TOTP authentication (TASK-42)"
# Open an MR/PR in the git hosting platform
```

Commit format: `<type>(<scope>): <description> (<TASK-ID>)`.

**7.2 Working with requirements (`.req`).** The branch for changing a requirement (see §5.3 above).

**7.3 Linking the MR/PR to the task.** In the MR/PR description, specify: `Closes #42` + `Related SR: SR-01 (acme-portal.req)`. If the change touches several `.req` repositories — `Related: acmecorp/acme-platform/acme-platform.req#15`.

**7.4 Branch naming:**

| What you're doing | Branch |
|---|---|
| New functionality | `feat/TASK-NN-slug` |
| Bug fix | `fix/TASK-NN-slug` |
| Changing a requirement | `change/TZ-YYYY-NNN-slug` |
| New requirement | `feat/BR-NN-slug` or `feat/SR-NN-slug` |

---

## 8. Common scenarios

**Scenario 1. A new task:** open it in the tracker → check QG-0 → find the link to the SR → open the SR in `acme-portal.req/sr/` → read the SR + the parent BR (if context is needed) → run the TCs bound to the task (they are already frozen and **red** — that is normal: they were written before you and not by you, [standard/09 §9.18](../../standard/en/09-test-cases.md#9.18)) → create the `feat/TASK-NN-slug` branch in `.src` → implement until the TCs turn green → MR/PR → review → merge.

> **Why the test is already written, and not written by you.** A test born next to the code checks the code, not the requirement. So the TC is frozen before the task starts and is written by a different agent or engineer. If while implementing you added an internal technical detail that is not in the requirements (a defensive check, logging, an edge-case branch), it can be legalized through the class `source: implementation-originated` ([standard/06 §6.13](../../standard/en/06-requirements-hierarchy.md#6.13)): it needs provenance, explicit approval by a human supervisor, a covering test before the merge, and a **killed mutant** (such a test has no red history — it is born green). Client-observable behavior is not legalized this way: a screen, a field, a message, a deadline — those go through the contractual loop only.

**Scenario 2. A mismatch between the SR and the task's requirement:** do NOT do anything silently → add a comment to the task ("SR-01 §4 describes X, the task requires Y — a contradiction, please clarify") → wait for the Supervisor/Architect's answer → do not take the task into work until it is resolved.

**Scenario 3. Understanding where a requirement came from.** In the `.req` repository: `git log -- sr/SR-01-auth.md` (the change history) or `git show <commit-hash>` (the details of a specific change). Or, in the file's frontmatter, find the `source` field — a link to the TZ and its section.

**Scenario 4. Integration (Acme Portal → Acme AI).** Clone the requirements of the adjacent subsystem and add them to the workspace; open `acme-platform.req/specs/int/SPEC-INT-01-acme-portal-acme-ai.md` — the contract is described there. Integration contracts in canonical form are `SPEC-INT-NN` ([standard/08 §8.5.4](../../standard/en/08-specifications.md#8.5.4)), not the legacy `INT-SR`. The subsystem's SR references them via `constrained-by: [SPEC-INT-NN]`. A SPEC-INT always lives in `acme-platform.req/specs/int/` — never inside the subsystems.

**Scenario 5. Delta-TZ.** This is the work of the Architect/Tech Lead, but for the developer: wait for the MR with the updated SRs to merge → `git pull` in `.req` → read `tz/TZ-YYYY-NNN-delta-N.md` (the delta-TZ text) → check whether the changes affect your current tasks → if so, update or close them in agreement with the Supervisor.

**Scenario 6. Updating the local requirements repositories:**

```bash
find ~/projects/acmecorp -name "*.req" -type d | while read repo; do
  echo "Updating $repo..."
  git -C "$repo" pull --ff-only
done
```

---

## 9. Access rights — who can change what

| Repository | Developer | Tech Lead | Architect |
|---|---|---|---|
| `acme-platform.req` (system BR/SR) | Read-only | Read-only | Write via MR |
| `acme-portal.req` (subsystem SR) | Read-only | Write via MR | Write via MR |
| `acme-portal.src` (code) | Write via MR | Write + review | — |
| `acme-ai.req` (another subsystem) | Read-only | Read-only | — |

**If you need to change a requirement** — open an Issue in the `.req` repository; do not commit directly.

---

## 10. Quick reference

**Where to look when something is unclear:**

| Question | Where |
|---|---|
| What should the system do? | `acme-portal.req/sr/SR-NN-*.md` |
| Why does the business need it? | `acme-platform.req/br/BR-NN-*.md` |
| Where did this requirement come from? | frontmatter `source` → TZ |
| How did the requirement change? | `git log -- sr/SR-NN-*.md` |
| How does the integration with Acme AI work? | `acme-platform.req/specs/int/SPEC-INT-01-*.md` |
| What changed with the latest TZ? | `acme-platform.req/tz/TZ-YYYY-NNN-delta-N.md` |

**Checklist before creating an MR:** the code covers all the ACs from the task; there are tests for the negative scenarios; the MR/PR contains a `Closes #NN` reference; if behavior changed — an Issue was opened in `.req` to update the SR.

**Git commands** (the most frequent):

```bash
git -C <portal.req> pull                                # update the requirements
git -C <portal.req> log -- sr/SR-01-auth.md             # the history of a specific SR
grep -r "id: SR-01" ~/projects/acmecorp/ --include="*.md"   # find a requirement by ID
grep -r "SR-01" ~/projects/acmecorp/ --include="*.md"       # all tasks referencing SR-01
```

**Glossary:** BR — Business Requirement; SR — System Requirement; TR — Goal + AC in the tracker; AC — Acceptance Criteria; QG-0 — Approval Gate (canonical v1.0; pre-v1.0 legacy "Context Gate"); `.req` — the git repository with the subsystem's requirements; `.src` — the git repository with the code; SPEC-INT — an integration specification (canonical v1.0; pre-v1.0 `INT-SR`); delta-TZ — an addition to the TZ on a repeat order; deprecated — an outdated requirement (not deleted, only flagged).
