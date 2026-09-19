---
title: "Substrate versioning (V1–V6)"
order: 3
lang: en
---
# 03. Substrate versioning — V1–V6

> **Part of the RENAR Standard v1.1** · [← Table of contents](README.md)

## 3.1 The six substrate capabilities

The Source-of-Truth inversion from [chapter 2](02-methodology-positioning.md#2.3) — the requirement wins, not the code — rests on a single physical condition: the substrate MUST faithfully remember what changed and when. If a past state can be rewritten after the fact, the phrase "the implementation was accepted against the requirements of version X" loses its meaning, and with it the whole acceptance contract collapses. So the six capabilities V1–V6 are not an infrastructure detail but the foundation on which the RENAR idea stands at all; that is why we put them up front, before the chapters on artifacts.

This chapter gives a **detailed normative formulation** of each capability: preconditions, postconditions, the relation to the trace chain of the Source of Truth, and examples of mapping onto common substrates. The conceptual rationale is [§2.5](02-methodology-positioning.md#2.5) (statement 3, "Positioning in the methodology typology").

The concrete versioning mechanism — distributed or centralized VCS, a document store with conflict resolution — is **interchangeable**. RENAR governs capabilities, not tools.

## 3.2 Rationale for mandatoriness

Let us examine, one by one, exactly what breaks without each capability. The relation here is structural, not operational: it is not about team discipline, but about the fact that without the capability the corresponding property of truth simply cannot be expressed.

| No capability | What becomes impossible | Relation in RENAR |
|---|---|---|
| V1 (immutable history) | "The implementation was built against the requirements as of date X" | provenance, audit log, acceptance gate |
| V2 (atomic change unit) | Guaranteed consistency of changes | delta-ADAPT as an atomic change unit (see [§7.6](07-adapt.md#7.6)) |
| V3 (diff & review) | Approval of requirements and specifications | [QG-0](10-lifecycle-qg.md#10.3), [QG-ADAPT-approve](07-adapt.md#7.9) |
| V4 (branching / change-set) | A draft is separable from approved truth | the set draft → approved version `N.M` ([§10.5.1](10-lifecycle-qg.md#10.5.1)) |
| V5 (cross-substrate version pin) | Pinning a description version from the implementation | the `automation.set-version` field in TC — the description set version ([§9](09-test-cases.md), [§10.5.4](10-lifecycle-qg.md#10.5.4)) |
| V6 (author + timestamp) | provenance of every change | signatures in [ADAPT](07-adapt.md), `ai-provenance` in [SR/SPEC](06-requirements-hierarchy.md) |

Therefore a substrate without V1–V6 — a flat file server with no history, a document store without conflict resolution, any mechanism without immutable change tracking — **does not implement RENAR** regardless of its other merits. This is a structural constraint, not a question of team discipline.

---

## 3.3 Normative definitions of V1–V6

Paragraphs §3.3.1–§3.3.6 are formulated **independently of any concrete substrate**. The names of specific products appear only in §3.4 (example) and §3.6 (language illustrations).

### 3.3.1 V1 — immutable history

**Capability (immutable history):** the substrate MUST ensure that, for any artifact, any past state is addressable and recoverable without loss.

**Preconditions:** the artifact is registered in the substrate as a versioned object.

**Postconditions:** for any point in time T in the artifact's history there exists a stable version identifier through which the state at moment T is fully recoverable.

**Without V1 it is impossible to:**

- Recover the artifact's state as of the contract-signing date.
- Compare the current state with a baseline.
- Build an audit log for conformance ([chapter 13 §13.4](13-conformance.md)).
- Set a baseline point for delta-ADAPT.

Concrete realizations on different substrates — §3.4.

### 3.3.2 V2 — atomic change unit

**Capability (atomic change unit):** the substrate MUST ensure that any change to an artifact (or to a consistent group of artifacts) is recorded as a single transaction: all or nothing. Intermediate inconsistent states are not observable from the outside.

**Preconditions:** the substrate has a notion of a transaction (atomic change).

**Postconditions:** after an atomic change, either all edits are visible to an observer or none are. An intermediate "inconsistent" state MUST NOT exist.

**Without V2 it is impossible to:**

- Update a BR and its related SR consistently in one transaction.
- Guarantee consistency between a requirement and its `linked-tasks[]` metadata.
- Carry out ADAPT approval as a single atomic action (the Architect's signature + the `answered → approved` transition in one transaction); ACTZ signing is a separate atomic action carrying the dual signature.
- Roll back a change without a "half-applied" state.

### 3.3.3 V3 — diff & review

**Capability (diff & review):** the substrate MUST ensure that a proposed (but not yet integrated) change is representable as a diff against a baseline state — the approved description set version ([chapter 10 §10.5](10-lifecycle-qg.md#10.5)) or the approved state of a contractual artifact — and that a human or AI agent with review authority can approve or reject it **before** it is included in approved truth.

**Preconditions:** the proposed change exists separately from approved truth (see V4).

**Postconditions:** before approval the change exists but is not considered part of the Source of Truth. After approval it becomes part of the Source of Truth as an atomic change unit (V2).

**Without V3 it is impossible to:**

- Run the Quality Gates: QG-0 — approval of requirements and specifications ([§10.3.1](10-lifecycle-qg.md#10.3)); QG-ADAPT-approve — ADAPT approval ([§7.9](07-adapt.md#7.9)).
- Apply the Architect's signature to the ADAPT ([§7.5](07-adapt.md#7.5)) and the dual signature to the ACTZ ([§7.13](07-adapt.md#7.13)).
- Review code and specification independently (see [§2.3.3 (2)](02-methodology-positioning.md#2.3.3)).
- Use adversarial review as a gate.

### 3.3.4 V4 — branching / change-set

**Capability (branching / change-set):** the substrate MUST separate **work in progress** (WIP) from **approved truth** (the Source of Truth) so that several independent changes can be developed in parallel without affecting the Source of Truth.

**Preconditions:** the artifact is registered in the substrate.

**Postconditions:** for any artifact at a given moment there MAY exist (a) exactly one approved version (the Source of Truth) and (b) zero or more drafts — each of which is either integrated through V3 or rejected.

**Without V4 it is impossible to:**

- Separate the `draft` lifecycle status from `approved` ([chapter 10](10-lifecycle-qg.md)).
- Run several delta-ADAPTs in parallel.
- Hold backward findings in `asked-to-client` without blocking approved truth.
- Evolve SPEC-* experimentally without affecting requirements derived from the implementation.

### 3.3.5 V5 — cross-substrate version pin

**Capability (cross-substrate version pin):** substrate A that uses an artifact of substrate B MUST be able to pin a specific version of substrate B's artifact as a stable cross-substrate identifier. This identifier MUST unambiguously recover the state of the artifact in substrate B.

**Preconditions:** substrate A and substrate B satisfy V1 (each has a stable version identifier).

**Postconditions:** for every pinned reference in substrate A to an artifact of substrate B there exists a pair `(artifact-id, version-id)`, and through it the full state of substrate B's artifact at the moment of pinning is recovered.

**Without V5 it is impossible to:**

- The `automation.set-version` and `last-run.set-version` fields in TC — the description set version ([chapter 9 §9.3](09-test-cases.md#9.3), [§10.5.4](10-lifecycle-qg.md#10.5.4)).
- Tie the implementation substrate (code) to a specific version of the requirements substrate (SR/SPEC).
- Guarantee: "this implementation was accepted against the requirements of version X."
- Compute the TC freshness metric (a pinned version older than the current one — the TC is stale).

### 3.3.6 V6 — author + timestamp

**Capability (author + timestamp):** for every atomic change unit (V2) the substrate MUST register an identifiable author (a human or an AI agent with a unique id) and a timestamp with a precision no coarser than one second.

**Preconditions:** the substrate has an author identification system.

**Postconditions:** for any atomic change unit the query "who? when?" yields an unambiguous answer.

**Without V6 it is impossible to:**

- Apply the ACTZ dual signature (a signature = author + timestamp in the substrate's native form); an ADAPT is signed by the Architect alone.
- Record `ai-provenance` in artifact frontmatter.
- Build an audit log for conformance.
- Compute the adversarially-found metric (the distribution of backward findings across authors).

---

## 3.4 Mapping V1–V6 onto concrete substrates (example)

> **Informative.** The table shows how V1–V6 are usually realized on common substrates. It is **not** part of the normative contract. Any substrate that satisfies V1–V6 implements chapter 3 — whether or not it appears in the table.

| Capability | Git | Mercurial | SVN | Perforce | Document store (example) |
|---|---|---|---|---|---|
| V1 — immutable history | commits, hash-chain | changesets, hash-chain | revisions, sequential numbering | changelists | revision tree per doc (`_rev`) |
| V2 — atomic change unit | commit | commit | atomic revision | changelist submit | document update (single _rev advance) |
| V3 — diff & review | merge request / pull request | hg phabricator, mq | svn diff + commit gate | swarm review | API workflow + Hub UI approval |
| V4 — branching / change-set | branches | named branches, bookmarks | branches (copy semantic) | branches / streams | conflict branches / WIP docs / draft status |
| V5 — cross-substrate version pin | submodule SHA | subrepo changeset | externals (peg rev) | branches / streams reference | `_rev` reference + `created_by_order` |
| V6 — author + timestamp | commit metadata | commit metadata | revision properties | changelist metadata | doc fields (`author`, `updated_at`) |

Practical workflows for each substrate:

- [guide/03 — git](../../guide/en/03-tool-guide-git.md)
- [guide/04 — document store](../../guide/en/04-document-store-substrate.md)

---

## 3.5 A substrate that does not satisfy V1–V6

The following configurations **do not implement RENAR**, because they violate one or more capabilities:

| Configuration | Violation |
|---|---|
| Flat file server; "version" = renaming the file | V1 (no stable history; renaming = loss of provenance) |
| Document store without conflict resolution | V2 (an inconsistent state is possible) |
| Wiki without revision history | V1, V6 |
| Wiki with revision history but no approval procedure | V3 |
| VCS using `mtime` instead of an immutable identifier | V1, V5 |
| Substrate that edits historical revisions in place | V1 (history is mutable — an audit log is impossible) |

A team **adopting** RENAR on a substrate from this list **MUST** either migrate to a suitable substrate or build a **compensating layer** that provides V1–V6 on top of the underlying storage. In both cases the [conformance manifest](#3.7) MUST explicitly document how V1–V6 are realized.

---

## 3.6 Substrate-independent language

RENAR normative paragraphs use **substrate-independent** wording: "atomic change unit" (V2), "version pin" (V5), the `approved` state (after V3), "draft" (V4). Terms **specific to a substrate** (tool names and their primitives) are permitted only:

- in illustrative tables with an explicit marker (as in §3.4);
- in cross-references to a substrate-specific [guide/](../../guide/en/README.md);
- in appendices to normative chapters.

### 3.6.1 Examples: normative form and a git illustration

| Normative (substrate-independent) wording | Git illustration (not the norm) |
|---|---|
| "A requirement change is recorded as an atomic change unit with author and time (V2, V6)" | "The change is recorded as a commit" |
| "A change passes diff & review before integration (V3)" | "The change passes merge-request review before merge into main" |
| "The implementation substrate pins a specific version of the requirements substrate (V5)" | "`.src` pins the `.req` submodule SHA" |
| "The ACTZ dual signature — two independent author+timestamp events (V6)" | "The dual signature — two approvals in the merge-request UI" |
| "Editing an already-approved requirement outside an atomic change unit with review is a violation" | "A force-push to an approved branch is blocked by hooks" |

### 3.6.2 The substrate as a conformance parameter

Each team fixes its **substrate choice** in the [conformance manifest](#3.7) with an explicit mapping V1–V6 → substrate-native primitives. When the substrate changes (for example, from git to a document store) the manifest is updated and a regression check of V1–V6 is run (§3.8).

---

## 3.7 Conformance manifest

The canonical schema is [§13.4](13-conformance.md#13.4), the file `RENAR-CONFORMANCE.yaml` (YAML 1.2) at the root of the requirements substrate. Chapter 3 governs the substrate-related part: the declaration of the chosen substrate and the V1–V6 mapping (the `substrate-capabilities` field, [§13.4.2](13-conformance.md#13.4.2)).

```yaml
# Fragment of RENAR-CONFORMANCE.yaml — the substrate-specific part
# (full schema — §13.4.2)
substrate-capabilities:
  v1-immutable-history:  declared    # the substrate primitive and how it is checked — via substrate-id
  v2-atomic-change-unit: declared
  v3-diff-review:        declared
  v4-branching:          declared
  v5-version-pin:        declared
  v6-author-timestamp:   declared
  substrate-id: "<substrate-native pointer to guide/03..06>"
```

Confirmation of the mandatory clauses (§2.3 Source-of-Truth inversion, §7 ADAPT, §8 closed list of 12 SPEC types, §9 pos/neg, and others) goes in the `mandatory-clauses-confirmed` field ([§13.3](13-conformance.md#13.3)–[§13.4](13-conformance.md#13.4)).

The manifest is **REQUIRED** for any conformance claim at level RENAR-1 and above ([chapter 13](13-conformance.md)).

---

## 3.8 Substrate migration

When changing the substrate, the team **MUST** perform the steps in order:

1. **Pre-migration audit:** the target substrate satisfies V1–V6; otherwise migration is prohibited until a compensating layer exists.
2. **Manifest draft:** an updated `RENAR-CONFORMANCE.yaml` with the new mapping.
3. **Isomorphism check:** all artifacts (BR, SR, SPEC, ADAPT, TC) are transferable without loss of fields, ids, or provenance. All ids are **immutable** — renaming during migration is prohibited.
4. **Atomic switchover:** the migration is an atomic change unit at the process level; a single-moment transfer of all artifacts. **Using two substrates as the Source of Truth in parallel is prohibited** (see [§2.3.3 (1)](02-methodology-positioning.md#2.3.3)).
5. **Post-migration check:** a regression check of V1–V6 on real artifacts.
6. **Manifest registration:** the updated `RENAR-CONFORMANCE.yaml` is registered in the new substrate as an atomic change unit (V2).

After switchover the old substrate is an archive (a read-only snapshot) or is decommissioned. Working on two substrates as the Source of Truth in parallel is prohibited.

---

## 3.9 Relation to other chapters

| Chapter | Relation |
|---|---|
| [02 Positioning in the typology §2.5](02-methodology-positioning.md#2.5) | Conceptual rationale for V1–V6 |
| [06 Requirements hierarchy](06-requirements-hierarchy.md) | frontmatter relies on V1 (immutable id), V5 (version pin) |
| [07 ADAPT](07-adapt.md) | ADAPT approval by the Architect's signature — V3 + V6; the ACTZ dual signature — V3 + V6; delta-ADAPT — V1 + V2 + V4 |
| [08 Specifications](08-specifications.md) | `constrained-by[]`, `depends-on[]`, `referenced-by[]` — through V5 |
| [09 Test cases](09-test-cases.md) | `automation.set-version` / `last-run.set-version` — a direct application of V5 to the set version |
| [10 Lifecycle and QG](10-lifecycle-qg.md) | Status transitions — V2 + V3 + V4 |
| [11 Maturity model](11-maturity-model.md) | RENAR-3+: V5 required; RENAR-4+: V6 + ai-provenance |
| [13 Conformance](13-conformance.md) | `RENAR-CONFORMANCE.yaml` — a mandatory artifact |
| [guide/03](../../guide/en/03-tool-guide-git.md) | Practice on git |
| [guide/04](../../guide/en/04-document-store-substrate.md) | Practice on a document store |
