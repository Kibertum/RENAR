---
title: "Substrate: document-oriented store (overview)"
description: "Informative overview of a V1–V6 implementation on a document-oriented store substrate — without vendor-specific detail."
order: 4
lang: en
version: "1.0"
---

# 04. Substrate: document-oriented store (overview)

> **Informative appendix.** The normative V1–V6 capabilities are in [standard/03](../../standard/en/03-substrate-versioning.md). A practical example on a distributed VCS (git) is in [guide/03](03-tool-guide-git.md).

A **document-oriented store** is a substrate where RENAR artifacts are stored as versioned documents: a stable identifier, a chain of revisions (revision token), atomic update, and an API gateway for lifecycle transitions and signatures.

RENAR is formally **not tied** to the document-store class of systems — only the capabilities (**V1–V6**) are normatively prescribed ([§3.3](../../standard/en/03-substrate-versioning.md#3.3)).

---

## 1. When to choose a document-oriented store

A good fit if:

- you need a centralized enterprise requirements store across several projects;
- non-technical stakeholders work through a web UI rather than through a VCS;
- federation of cross-project links and search is a **key** product requirement;

A poor fit if:

- the team is already standardized on a git workflow with PR/MR review;
- there are no resources to maintain a document-store server + search index + API gateway;
- you need **fully-fledged** test cases (`TC`) as objects in the same environment without separately configuring custom document types (see [§9](../../standard/en/09-test-cases.md) — the presence of `TC` is required for declared RENAR conformance);

---

## 2. Mapping the V1–V6 capabilities (generalized)

| Capability | Typical document-store mechanism |
|---|---|
| **V1 — immutable history** | A chain of immutable document revisions + a stable `_id` |
| **V2 — atomic unit of change** | A single whole document-version increment per step |
| **V3 — comparison and review (diff)** | An approval flow through the API and the UI; interception of direct status edits |
| **V4 — branching and merging** | Draft documents or conflict branches (depending on product capabilities) |
| **V5 — version pinning** | A field referencing the revision in linked artifacts |
| **V6 — author and timestamp** | Document fields `author` + `updated_at` per revision |

A comparison with a distributed VCS is in [§3.4](../../standard/en/03-substrate-versioning.md#3.4) (an illustrative table; not a normative contract).

---

## 3. Migration VCS ↔ document store

Migration is possible if both substrate implementations preserve:

- the canonical artifact identifiers (`BR`/`SR`/`SPEC`/`ADAPT`);
- the traceability links;
- the lifecycle states from the closed list in [§10](../../standard/en/10-lifecycle-qg.md).

The migration procedure is a project-specific runbook; the normative minimum is a check of V1–V6 before cutover ([§3.8](../../standard/en/03-substrate-versioning.md#3.8)).

---

## 4. See also

- [03-tool-guide-git.md](03-tool-guide-git.md) — the substrate-specific guide for a distributed VCS (git)
- [03-substrate-versioning.md](../../standard/en/03-substrate-versioning.md) — the normative V1–V6
- [02-schemas.md](../../reference/en/02-schemas.md) — canonical fields; the substrate-native projection is informative
