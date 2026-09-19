---
title: "Schemas (formal)"
description: "Canonical YAML frontmatter schemas for all RENAR artifact types + cross-field validation rules."
order: 2
lang: en
version: "1.0"
---

# Schemas (formal)

> **Purpose:** machine-readable YAML frontmatter schemas for all RENAR artifact types. Used by substrate-native validators to check conformance. **Normative structure definitions** live in [`standard/06`](../../standard/en/06-requirements-hierarchy.md), [`standard/07`](../../standard/en/07-adapt.md), [`standard/08`](../../standard/en/08-specifications.md), [`standard/09`](../../standard/en/09-test-cases.md). Example validation: `node scripts/validate-schema-examples.js`. This document is a reference (informative lookup).

---

## 1. Common frontmatter (all requirement and SPEC artifacts)

Fields common to BR/SR/TR/SPEC-* (canonical v1.0). Legacy types `UIC` / `AIC` / `INT-SR` / `TS` are deprecated in v1.0 ([standard/04 §4.14.1](../../standard/en/04-terms.md#4.14.1)).

```yaml
# Identity
id: "<TYPE>-NN[.N]"
title: "<short, descriptive>"
type: BR | SR | TR | SPEC-ARCH | SPEC-API | SPEC-DATA | SPEC-INT | SPEC-PROC | SPEC-UI | SPEC-AI | SPEC-SEC | SPEC-OPS | SPEC-TEST | SPEC-DOC
slug: "<kebab-case>"

# Scope
level: system | subsystem | module
scope: { system: "<system-id>", subsystem: "<subsystem-id>" }   # subsystem=null if level=system

# Lifecycle — standard/04 §4.6, standard/10 §10.7: BR / SR / SPEC have NO status and NO version
# of their own. The state is derived from membership in a description set version
# (draft / approved / removed; the version record — §8.2 below). TR keeps its status
# (§4.6.2) — the field is declared in section 4. ADAPT (§4.6.4) and TC (§4.6.5) — own schemas below.
priority: must | should | could       # MoSCoW; SAFe — via WSJF (BR-specific)

# Provenance (conditional, standard/07 §7.4.1): ADAPT is reactive.
# Findings present → source.adapt + adapt-section mandatory. No findings → adversarial-review-ref mandatory.
# source.tz-section — always mandatory.
source:
  adapt: "ADAPT-NNN"                  # conditional
  adapt-section: "Forward §N.N"       # mandatory if adapt present
  tz-section: "§N.N"                  # always mandatory
  adversarial-review-ref: "AR-NNN"    # mandatory when adapt omitted → AR (standard/07 §7.4.6)
  document-ref: "<link>"              # pinned revision of source document
  implementation-originated: {}       # conditional; see §1.1 (standard/06 §6.13)

# Hierarchy
parent: { id: "<parent-id>", ref: "<link>" }   # id required for SR (→BR), TR (→SR); optional for BR
children: []                          # auto-derived

# Link to SPEC (graph)
constrained-by: []                    # SR → SPEC-* (typed edges)
implements-spec: []                   # TR → SPEC-*
depends-on: []                        # between SPEC

# Verification
verified-by: []                       # auto-derived; TC IDs
verifies-business-goal: ""            # optional

# AI provenance (RENAR-4+ mandatory for approved)
ai-provenance:
  generated-by: "<vendor>-<model>-<version>@<date>"
  prompt-template: "<template-path>@<version>"
  context-tokens: integer
  output-tokens: integer
  generation-time-ms: integer
  generated-at: "<ISO-8601>"
  human-edits: boolean                # true required for approved

# AI cost budget (optional)
ai-budget: { context-tokens-target: integer, context-tokens-actual: integer, output-tokens-target: integer, output-tokens-actual: integer, generation-time-target-ms: integer }

# Replacement + schema versioning
replaces: "<old-id>"
replaced-by: "<new-id>"
deprecated-date: "<ISO date>"
schema-version: "1.0"
```

### 1.1 `source: implementation-originated` — the implementation-originated requirement

A narrow legal origin class for BR / SR / SPEC ([standard/06 §6.13](../../standard/en/06-requirements-hierarchy.md#6.13)): an **internal technical detail** added during implementation (a defensive check, internal validation, logging, an edge-case branch). Client-observable behaviour MUST NOT be covered by this class — it passes only through the contractual contour (ADAPT / [ACTZ](#7.2) with signatures).

```yaml
source:
  tz-section: "§N.N"                  # always mandatory (here too)
  implementation-originated:
    change-unit-ref: "<link to the implementation change unit>"   # mandatory — provenance
    rationale: "<why the functionality was deemed necessary>"     # mandatory
    human-approval:                                               # mandatory; V6
      approved-by: "<human supervisor name>"
      role: "<role>"
      approved-at: "<ISO-datetime>"
    observable-by-client: false       # mandatory; true → non-conformance (standard/06 §6.13.4)
    covering-tc: "TC-NN"              # mandatory; the TC exists and passes before the change is merged
    mutation-check:                                               # mandatory — compensates for the absent red history
      mutants-killed: integer         # >= 1; otherwise the TC is not evidence (standard/09 §9.18.2)
      report-ref: "<link to the mutation-check report>"
```

| Rule | Level |
|---|---|
| `human-approval` is mandatory; an agent MUST NOT legalise its own addition | normative |
| `covering-tc` exists and passes **before** the change is merged | hook-enforced |
| `covering-tc` MUST NOT have a red history; a killed mutant is required instead (`mutation-check.mutants-killed >= 1`) | hook-enforced |
| `observable-by-client: true` together with `implementation-originated` is a non-conformance (an obligation to the client MUST NOT arise from code) | normative |
| The share of artifacts of this class is a counter in the drift metrics ([standard/12 §12.3](../../standard/en/12-metrics.md#12.3)) | normative |

---

## 2. BR — Business Requirement

```yaml
# Extends common §1, additionally:

level: system | subsystem             # BR at module level is prohibited (standard/06 §6.4)
scope: { system: "<system-id>", subsystem: "<subsystem-id>" }

# Cross-level link: subsystem BR → system BR (standard/06 §6.8.2)
implements:                            # array; substrate-agnostic reference
  - { id: BR-NN, scope: { system: "<system-id>" }, rationale: "<short>" }   # rationale optional
implemented-by: []                     # auto-derived (reverse edge; not written by the author)

business-context:
  stakeholder: "<role>"
  business-goal: "<short statement>"
  kpi-impact:
    - { kpi: "<name>", direction: increase|decrease, target: "<measurable>" }

# business-outcome — required for QG-4
business-outcome:
  measurement-type: kpi | survey | observation | usage
  kpi-name: "<KPI>"
  measurement-method: "<how>"
  baseline-value: number
  baseline-measured-at: "<ISO date>"
  target-value: number
  target-met-by: "<ISO date>"
  # the measured value lives in the set version record: accepted-outcomes[].measured-value / achievement (standard/10 §10.5.4)

prioritization: { framework: WSJF|RICE|MoSCoW, wsjf-score: number, prioritized-at: "<ISO date>", prioritized-by: "<role>" }

data-classification:
  contains-pii: boolean
  contains-financial: boolean
  contains-health: boolean
  contains-children-data: boolean
  retention-days: integer
  data-residency: ["RU" | "EU" | "US" | ...]

compliance:
  - { standard: "ISO 27001:2022", control: "<id>", rationale: "..." }
  - { standard: "GDPR", article: "Art.NN" }
  - { standard: "ФЗ-152", article: "ст.NN" }

ai-act: { risk-class: prohibited|high|limited|minimal, rationale: "<reason>", high-risk-domain: boolean }
```

### Fields `implements[]` / `implemented-by[]` — normative rules

| Rule | Level |
|---|---|
| `implements[]` required when `level: subsystem` AND the parent system has ≥ 1 BR in an approved version of its set | mandatory (§13.3.8) |
| The target BR MUST belong to the approved set version of its system at the approval of the version containing this BR (standard/10 §10.11.1) | hook-enforced |
| Cycle detection: the `implements` chain MUST NOT form cycles | hook-enforced |
| `implements[]` is **not** a parent edge; the ban on multiple parents (standard/06 §6.8.3) applies to SR/TR, not to BR | normative |
| Removal of the target BR from a new set version → cascade-warning for all `implemented-by` (not a cascading removal) | hook-enforced |
| Cross-substrate syntax: `id + scope.system` is substrate-independent | normative |
| Cardinality: array (0..N) | normative |

The enforcement gate is `scripts/check-implements-edge.js`. The `implemented-by` field is auto-derived; manual entry is prohibited.

---

### Reusable-component fields (standard/06 §6.14)

```yaml
# Root BR of a component (level: system), path C:
assumptions:                          # a list of assumptions about the consumer, closed for the set version
  - { id: A-01, statement: "<assumption>", confirmed-how: "<how the consumer confirms it>" }

# Root BR of a consumer (level: system):
uses:
  - component: "<system-id>"
    set-version: "N.M"                # the component's set version (V5), mandatory
    assumptions-confirmed:
      - { id: A-01, confirmed-by: "<actor>", confirmed-at: "<ISO-8601>" }

# A statement of a component's SR / SPEC:
applies-to: self | consumer           # default self; consumer — an obligation of the consumer (a static TC at the consumer)
```

---

## 3. SR — System Requirement

```yaml
# Extends common §1, additionally:

parent:
  id: "BR-NN"                         # required

# ADAPT source — via the canonical source.adapt / source.adapt-section (§1).
# There is no separate derived-from-adapt field (standard/06 §6.6.2).

constrained-by:                       # typed edges to SPEC-*
  - "SPEC-UI-NN"
  - "SPEC-API-NN"
  - "SPEC-DATA-NN"
  - "SPEC-SEC-NN"

quality-characteristic:               # ISO/IEC 25010:2023 (9 characteristics; interaction-capability ← usability, flexibility ← portability in 25010:2011; safety — new in 25010:2023)
  - functional-suitability | performance-efficiency | compatibility | interaction-capability | reliability | security | maintainability | flexibility | safety

# Inherited from parent BR (where applicable): data-classification, compliance, ai-act
```

---

## 4. TR — Task Requirement

```yaml
# Extends common §1, additionally:

parent:
  id: "SR-NN"                         # required

implements-spec: []                   # SPEC-* implemented by this task
status: draft | approved | done | obsolete   # standard/10 §10.6
source:
  set-version: "N.M"                # the set version the task is planned against (standard/10 §10.5.4)
estimated-effort: "<short statement>" # optional, free-form
```

---

## 5. SPEC-* common schema

All 12 SPEC types share a common structure (§1) plus the following SPEC-specific fields:

```yaml
type: SPEC-ARCH | SPEC-API | SPEC-DATA | SPEC-INT | SPEC-PROC | SPEC-UI | SPEC-AI | SPEC-SEC | SPEC-OPS | SPEC-TEST | SPEC-DOC

referenced-by: []                     # auto-derived
depends-on: []                        # SPEC this one depends on
compliance-refs: []                   # ISO / GDPR / ФЗ-152 / AI Act / NIST AI RMF
```

Mandatory body sections: `## Purpose`, `## Scope`, `## <Type-specific sections — see §6>`, `## Link to requirements`, `## Link to other SPEC`, `## Verification`, `## Open questions`.

---

## 6. SPEC type-specific extensions

Type-specific fields for the 12 SPEC types. Industry references are in [`standard/14`](../../standard/en/14-normative-refs.md). Legacy replacements: `UIC` → SPEC-UI, `AIC` → SPEC-AI, `INT-SR` → SPEC-INT.

### 6.1 SPEC-ARCH, SPEC-API, SPEC-DATA, SPEC-INT, SPEC-PROC

```yaml
# SPEC-ARCH
arch-style: monolith | microservices | modular-monolith | serverless | hybrid
deployment-model: cloud | on-prem | hybrid | edge
tech-stack: { languages: [], frameworks: [], data-stores: [], message-brokers: [] }
quality-attributes: [{ name: latency, target: "p95 < 200ms" }, { name: availability, target: "99.9%" }]
# the screen list is the mandatory-body section "Screen list": code (V1), name, source (SR / a SPEC-ARCH section); standard/08 §8.5.6.1

# SPEC-API
api-style: rest | graphql | grpc | websocket | async-events
api-version: "v1.2.0"
versioning-strategy: url-path | header | query-param | content-negotiation
authentication: bearer-jwt | api-key | oauth2 | mtls | none
rate-limits: [{ endpoint: "*", limit: "1000/min/key" }]
contract-file: { format: openapi-3.1 | asyncapi-2.6 | proto3, location: "contracts/<name>.yaml" }

# SPEC-DATA
data-style: relational | document | graph | columnar | hybrid
storage-engine: postgresql | mysql | couchdb | mongodb | clickhouse | ...
schema-version: "1.4.0"
pii-classification: [{ entity: User, fields: [email, phone], level: PII-high }]
retention-policies: [{ entity: Order, period: "7 years", basis: "tax law" }]
migration-strategy: forward-only | reversible | dual-write

# SPEC-INT
integration-pattern: request-response | event-driven | message-queue | webhook | file-transfer
direction: outbound | inbound | bidirectional
counterparty: { system: "<external-name>", contract-owner: "<team-or-vendor>", contract-ref: "<external-spec-url>" }
sla: { availability: "99.5%", latency-p95: "500ms", fallback: "queue + retry; manual reconciliation after 24h" }
idempotency: guaranteed | best-effort | none

# SPEC-PROC
process-style: bpmn | state-machine | saga | choreography | orchestration
state-count: integer
participants: [{ role: customer, system: client-portal }, { role: agent, system: back-office }]
sla: { end-to-end: "2 business hours" }
compensation: defined | not-applicable | manual
```

### 6.2 SPEC-UI, SPEC-AI, SPEC-SEC, SPEC-OPS

```yaml
# SPEC-UI
ui-platform: web | mobile-ios | mobile-android | desktop | tv | embedded
target-users: [{ role: end-customer, persona: "ADAPT-NNN §X.Y" }]
design-system: "<reference-or-internal>"
accessibility-level: WCAG-A | WCAG-AA | WCAG-AAA
i18n: required | not-required
mockup-links: [{ tool: figma, url: "<link>", version: "v3" }]
baseline-images: ["ai-concepts/baselines/SPEC-UI-NN-screen-01.png"]
screens: ["S-01", "S-02"]            # codes of the screens from the SPEC-ARCH list of the same version that the document covers (standard/08 §8.5.6.1)

# SPEC-AI (judge-model.vendor ≠ production-model.vendor — normative)
ai-pattern: rag | fine-tuning | prompt-engineering | tool-use | multi-agent | embedding-only
production-model: { vendor: anthropic|openai|google|local, model: "<name>", version: "<exact>" }
judge-model: { vendor: "<different-vendor>", model: "<different-model>" }
context-strategy: { embedding-model: "<model>", chunk-size: integer, chunk-overlap: integer, vector-store: pinecone|weaviate|pgvector|qdrant }
eval-strategy: { metric: accuracy|f1|rouge|custom-rubric, threshold: number, baseline-dataset: "<path>" }
cost-budget: { tokens-per-request-target: integer, tokens-per-request-ceiling: integer, monthly-budget-usd: number }

# SPEC-SEC
security-domains: [authentication, authorization, data-protection, audit, secrets-management]
auth-model: { authn: jwt-bearer|oauth2-pkce|mtls|passkey, authz: rbac|abac|relbac }
data-classification: [{ class: PII-high, fields: [...] }, { class: PCI, fields: [...] }]
threat-model-method: STRIDE | PASTA | OCTAVE
compliance: [ISO-27001, GDPR, ФЗ-152, PCI-DSS-4]

# SPEC-OPS
deployment-style: kubernetes | vm | serverless | docker-compose | bare-metal
environments:
  - { name: dev, purpose: development, scale: minimal }
  - { name: staging, purpose: integration-testing, scale: half-prod }
  - { name: prod, purpose: production, scale: full }
slo: { availability: "99.9%", error-budget-month: "43m", latency-p95: "300ms" }
observability: { logs: elastic|loki|cloudwatch, metrics: prometheus|datadog|cloudwatch, traces: jaeger|tempo|x-ray }
disaster-recovery: { rto: "<duration>", rpo: "<duration>" }
```

### 6.3 SPEC-TEST — benches and data

A test bench is not a deployment environment but a construction of proof ([`standard/08 §8.3.2`](../../standard/en/08-specifications.md#8.3.2), §8.5.10). Datasets are described **on both sides** of every integration; the expected result often lives outside our system, so reconciliation rules are a mandatory part of the schema.

```yaml
# SPEC-TEST
benches:                              # bench topology: nodes, versions, what is live / what is emulated
  - name: "<bench-id>"
    nodes: [{ name: "<node>", version: "<version>", mode: live | emulated }]
    notes: "<bench limitations>"

counterparties:                       # one entry per integration (exactly one per SPEC-INT)
  - integration: SPEC-INT-NN
    representation: real | sandbox | emulator
    endpoint: "<substrate-native pointer>"
    fidelity-rationale: "<why the representation answers like the real system>"

datasets:                             # on both sides of every integration
  - name: "<dataset-id>"
    side: ours | counterparty
    integration: SPEC-INT-NN          # null for a dataset outside an integration
    volume: "<volume>"
    origin: synthetic | anonymized-production | client-provided
    anonymized: boolean
    contains-real-client-data: boolean   # true ⇒ client-signature is REQUIRED
    retention: "<retention period>"

reconciliation-rules:                 # reconciling results against data held by foreign systems
  - name: "<rule-id>"
    integration: SPEC-INT-NN
    expected-result-source: "<where the expected result lives when it is not in our system>"
    match-keys: []
    tolerance: "<reconciliation tolerance>"

run-config:
  mocked: [SPEC-INT-NN]               # what is mocked
  live: [SPEC-INT-NN]                 # what runs live
  run-order: []                       # order of the run
  seed-mechanism: "<how the bench state is prepared>"

client-signature:                     # REQUIRED when real / personal client data is used
  signed-by: "<name>"
  role: "<role>"
  organization: "<client-org>"
  signed-at: "<ISO-datetime>"
  signature-ref: "<link>"
```

| Rule | Level |
|---|---|
| At least one `datasets[]` entry with `contains-real-client-data: true` (or `anonymized: false`) ⇒ `client-signature` MUST be filled in; otherwise — **fatal** | hook-enforced |
| `counterparties[]` MUST hold exactly one entry per integration represented on the bench | hook-enforced |
| `reconciliation-rules[].expected-result-source` MUST be non-empty when the expected result lives outside our system | normative |
| A version increment of a SPEC-TEST invalidates `verified` on the TCs that reference it through `environment-ref` ([standard/10 §10.5.4](../../standard/en/10-lifecycle-qg.md#10.5)) | hook-enforced |

### 6.4 SPEC-DOC — delivered documentation

SPEC-DOC describes the composition of the delivered result: the documents the client receives as part of the delivery ([`standard/08 §8.5.11`](../../standard/en/08-specifications.md#8.5.11)). The boundary with SPEC-OPS is membership in the delivery: an administrator manual is always SPEC-DOC, a runbook is always SPEC-OPS.

```yaml
# SPEC-DOC
deliverables:                         # one entry per document
  - name: "<document-id>"
    kind: user-manual | admin-manual | training-materials | other
    audience: "<reader role>"
    format: pdf | html | markdown | docx | other
    language: "<delivery language>"

required-sections:                    # mandatory sections — a requirement, not the vendor's discretion
  - deliverable: "<document-id>"
    sections: ["<mandatory section heading>"]

traces-to: []                         # BR / SR / SPEC whose behaviour the document describes

version-binding:
  rule: matches-system-version | matches-release-tag | manual
  system-version-ref: "<substrate-native link to the system version>"

acceptance-criteria:                  # what the document is accepted against; checked by the doc-lint
  - criterion: "<checkable assertion>"
    checked-by: TC-NN                 # a TC with automation.kind: static (standard/09 §9.8)
```

| Rule | Level |
|---|---|
| The doc-lint is mandatory: a SPEC-DOC MUST have at least one TC with `automation.kind: static`; without it the SPEC-DOC is unverifiable and **does not pass QG-2** ([standard/09 §9.8](../../standard/en/09-test-cases.md#9.8)) | hook-enforced |
| `required-sections[]` MUST be non-empty for every `deliverables[]` entry | hook-enforced |
| `traces-to[]` MUST be non-empty: the document describes behaviour stated in the requirements; the doc-lint checks coverage of roles and scenarios | normative |
| Substantive conformance of the text to implemented behaviour is OPTIONAL, via a judge agent (P7 / `eval`); no separate mechanism is introduced | normative |

---

### 6.5 SPEC-UC — use case

```yaml
# Extends §5 (common SPEC):
spec-type: SPEC-UC
role: human | agent                   # human — path through the interface (ux TC coverage); agent — through the API / CLI (system / contract)
persona: "<ADAPT§persona-ref>"        # for role: human
covers: [SR-NN, SPEC-UI-NN, SPEC-API-NN]   # ≥ 2 artifacts of the same set version
entry: SPEC-UI-NN | SPEC-API-NN       # the screen or operation the path starts from
steps:
  - n: 1
    action: "<what the executor does>"
    ref: SR-NN#n | SPEC-<TYPE>-NN#n   # MANDATORY: the address of the statement the step touches (standard/08 §8.5.12.1)
    expects: "<observable result>"
```

| Rule | Check |
|---|---|
| `ref` is non-empty for every step | hook-enforced; a step without `ref` violates structural completeness |
| `covers[]` holds ≥ 2 artifacts of the same set version | hook-enforced |
| With `role: human` every step is covered by ≥ 1 TC with `tc-type: ux` whose `verifies[]` contains `steps[].ref` | coverage-presence (standard/13 §13.3.5) + type per standard/09 §9.8 |
| With `role: agent` every step is covered by a `system` / `contract` TC | same |

---

## 7. ADAPT schema

ADAPT is a separate artifact ([`standard/07`](../../standard/en/07-adapt.md)). It is reactive: it exists only when there are findings from the adversarial review of the TZ (§7.4.1). On a "no findings" verdict, no ADAPT is created; the review outcome is issued as an AR in either case (§7.1 below), and artifacts reference it via `<artifact>.source.adversarial-review-ref`.

```yaml
# Identity
id: ADAPT-NNN
title: "Adaptation of TZ <name>"
type: ADAPT
trigger-stage: import-tz | decompose-br | decompose-sr | spec | tc   # trigger stage (standard/07 §7.4.1.4)

# Source
source-tz: { id: TZ-YYYY-NNN, signed-date: "<ISO-date>", signed-by-client: "<name-role>", document-version-ref: "<substrate-native version identifier>" }   # V5 pin
parent-adapt: { id: ADAPT-NNN, delta-tz: TZ-YYYY-NNN }   # for delta-ADAPT

# Supersession (standard/07 §7.6.4) — only for superseding-ADAPT
supersedes: ADAPT-MMM                                    # reference to the superseded ADAPT
superseded-by: ADAPT-NNN                                 # auto-derived; on the superseded one
supersession-rationale: "<contradicting BR/SR/SPEC + source>"   # mandatory if supersedes present

# Lifecycle (subset of §1: ADAPT does not use verified/deprecated/obsolete; superseded — terminal on supersession, §7.6.4)
# The client-ready state is REMOVED: putting questions to the client is a matter for the ACTZ (§7.13), not an ADAPT state (standard/10 §10.8.1).
status: draft | review | asked | answered | approved | frozen | superseded
created: "<ISO-date>"
last-updated: "<ISO-date>"

# Approval (required for approved)
approval:
  # No client-signature: an ADAPT is an internal interpretation (standard/07 §7.5).
  # The client's obligations are carried by an ACTZ (§7.13) with the dual signature.
  architect-signature: { signed-by: "<name>", role: architect, signed-at: "<ISO-datetime>" }

# Auto-derived
generates-requirements: []
generates-specs: []
open-questions-count: integer         # MUST be 0 for approved
resolved-questions-count: integer

# AI provenance
ai-provenance: { generated-by: "<vendor>-<model>-<version>@<date>", prompt-template: "<template-path>@<version>", context-tokens: integer, output-tokens: integer, human-edits: boolean }
```

Backward entries inside the body:

```yaml
id: B-NNN
category: contradiction | gap | hidden-assumption | feasibility | regulatory | terminology | scope
status: open | asked-to-client | answered | resolved | revised | frozen
tz-section: "§N.N"
description: "..."
asked-to-client: "<ISO-date>"
client-answer:
  signed-by: "<name>"
  signed-at: "<ISO-datetime>"
  channel: email | docusign | zoom-transcript | written-letter
  text: "..."
resolution: "..."                     # how the answer was integrated into Forward
decided-in: "ACTZ-NNN §M"             # mandatory for status=resolved; a clause of a signed ACTZ (§7.2)
```

A finding moves to `resolved` only once the decision on it has been **put to the client and signed**: `decided-in` points at clause `§M` of an ACTZ in status `signed` ([standard/07 §7.13.2](../../standard/en/07-adapt.md#7.13)). Referencing an ACTZ in status `draft` is forbidden ([standard/07 §7.13.4](../../standard/en/07-adapt.md#7.13)).

### 7.1 AR schema — the adversarial-review record

AR ([`standard/07 §7.4.6`](../../standard/en/07-adapt.md#7.4.6)) is an evidence record capturing the fact and the outcome of the mandatory adversarial review. It is issued in **both** outcomes; on `no-findings` it is itself the evidence referenced by `<artifact>.source.adversarial-review-ref`. It is not a requirements artifact and belongs to no closed list.

```yaml
# Identity
id: AR-NNN                            # sequential within the parent TZ; immutable
type: AR
tz-ref: TZ-YYYY-NNN                   # the (delta-)TZ under review
trigger-stage: import-tz | decompose-br | decompose-sr | spec | tc   # aligned with ADAPT.trigger-stage

# Reviewer (isolation: model != primary agent's model, standard/07 §7.10.2)
reviewer: { vendor: "<provider>", model: "<model-id>" }
primary:  { vendor: "<provider>", model: "<model-id>" }   # the primary agent's model at issuance — the second operand of the §7.10.2 condition

# Verdict
verdict: findings-present | no-findings
produces-adapt: [ADAPT-NNN]           # mandatory non-empty when findings-present; empty when no-findings

# Lifecycle
status: draft | issued | superseded
superseded-by: AR-NNN                 # mandatory when status=superseded

# Signature (V6; mandatory for issued)
signature: { author: "<reviewer-id>", timestamp: "<ISO-8601>" }
```

### 7.2 ACTZ schema — the TZ clarification protocol

ACTZ ([`standard/07 §7.13`](../../standard/en/07-adapt.md#7.13)) is an artifact of the **contractual contour**: a batch of questions, proposals and decisions put to the client and signed by both parties. The boundary with ADAPT is drawn by audience: what is shown to the client and approved is an obligation; what is not shown is interpretation. Cardinality: TZ : ACTZ = 1 : 0..N; ADAPT : ACTZ = 1 : 1..N.

```yaml
# Identity
id: ACTZ-NNN                          # sequential within the parent TZ; immutable
title: "TZ Clarification Protocol No. N"
type: ACTZ
tz-ref: TZ-YYYY-NNN                   # mandatory; the TZ the protocol belongs to

# Findings closed (conditional)
resolves:                             # B-NNN entries in an ADAPT that the protocol closes
  - { id: B-NNN, adapt: ADAPT-NNN }   # absent on a client-initiated ACTZ (standard/07 §7.13.2)

# Decisions (mandatory, non-empty)
decisions:
  - number: "§M"                      # stable clause number; the target of decided-in
    statement: "<decision in the language of obligations: 'the button is named X'>"
    tz-section: "§N.N"                # the TZ section being clarified

# TZ annexes (conditional; standard/07 §7.13.5)
annexes:
  - { name: "<annex>", version: "<new version>", document-ref: "<link>" }

# Lifecycle
status: draft | sent | signed | superseded
superseded-by: ACTZ-NNN               # mandatory if status=superseded

# Signatures (mandatory for signed; V6)
client-signature: { signed-by: "<name>", role: "<role>", organization: "<client-org>", signed-at: "<ISO-datetime>", signature-ref: "<link>" }
vendor-signature: { signed-by: "<name>", role: "<role>", signed-at: "<ISO-datetime>" }
```

| Rule | Level |
|---|---|
| `decisions[].number` is stable and immutable: `B-NNN.decided-in` and `AT.verifies[]` point at it | normative |
| A signed ACTZ is immutable; a correction is made only by a new ACTZ carrying `superseded-by` on the previous one | hook-enforced |
| Referencing an ACTZ in status `draft` from `decided-in` is forbidden | hook-enforced |
| `resolves[]` is absent on a client-initiated protocol; once signed, the decision MUST be reflected in the ADAPT | normative |
| A TZ annex is not addressable on its own: its new version is approved through `annexes[]` under the same two-sided signature | normative |
| Effective TZ = the initial TZ (with annexes) + all signed ACTZ; the later signed document prevails ([standard/07 §7.14](../../standard/en/07-adapt.md#7.14)) | normative |

---

## 8. TC — Test Case

```yaml
# Identity
id: "TC-NN[.N]"
title: "<descriptive>"
type: TC
slug: "<kebab-case>"

# Classification
tc-type: business | ux | system | contract | eval | security   # business — the former acceptance (standard/09 §9.5)
negative: boolean                     # true for the paired negative TC

# Scope
level: system | subsystem | module
scope: { system: "<system-id>", subsystem: "<subsystem-id>", module: "<module-id>" }

# A TC norm has no status of its own (standard/10 §10.7); implementation states — automation.* / last-run.* (§10.9)

# Verification mapping (≥1)
verifies:
  - { id: "<requirement-id>", statement: 5, ref: "<link>" }   # statement — the statement address <id>#n (standard/15 §15.1.1), mandatory with >1 statement; the description version is stated once — automation.set-version (standard/10 §10.5.4)

# Pair link (mandatory if negative=false and a pair exists)
paired-with: ["<TC-id>"]

# Task binding (optional; standard/09 §9.19.7)
verifies-tr: "TR-NN"                  # the task to whose scope the test is narrowed
verifies-claims: []                   # the subset of the parent SR's claims covered within that TR

# Bench and data (mandatory; standard/09 §9.3, standard/08 §8.5.10)
environment-ref: "SPEC-TEST-NN"       # conditional: mandatory if automation.kind: dynamic.
                                      # Does not apply to static checks (the doc-lint of §9.8, the
                                      # structural TC of §9.8.1): the analyzer works over artifacts

# Automation
automation:
  status: automated | manual-pending
  set-version: "N.M"                  # the set version the implementation was written against (QG-1)
  kind: dynamic | static                    # mandatory; static — the runner is a static analyser
  location: "<path-to-implementation>"      # mandatory if automated
  runner: pytest | jest | go-test | playwright | vlm-judge | ragas | pact | other
  manual-pending-until: "<ISO date>"        # mandatory if manual-pending
  manual-pending-reason: "<why>"

# Red history (standard/09 §9.18.2) — the condition for counting a TC as evidence
red-history:
  fixing-run:                               # the fixing run, performed before implementation
    date: "<ISO timestamp>"
    result: fail                            # MUST be red; pass → a signal to investigate, not a success
    run-ref: "<link>"
  green-transition:                         # the recorded red → green transition
    date: "<ISO timestamp>"
    run-ref: "<link>"
  inherited-from: "TC-NN"                   # conditional: inheritance for tasks that do not change behaviour
  not-applicable-reason: implementation-originated   # conditional: this class only; then a killed mutant is mandatory
  mutation-check: { mutants-killed: integer, report-ref: "<link>" }   # mandatory when not-applicable-reason is set

# Execution (mandatory if tc-type=ux | eval; judge.vendor ≠ production model vendor — see §6.2 SPEC-AI, §9)
judge: { vendor: "<provider>", model: "<model-id>", prompt-template: "<template-path>@<version>" }
baseline: { artifact: "<pointer>", perceptual-diff-threshold: float, metric-thresholds: {} }

# Last run (auto-managed; bot-only)
last-run:
  date: "<ISO timestamp>"
  result: pass | fail | skipped | n/a
  runner-id: "<runner@version>"
  run-ref: "<link>"
  judge-report: "<for ux/eval>"

# Replacement / obsolescence
obsolete-pending: boolean             # true on detected delta-TZ invalidation
replaces: "<old-id>"
replaced-by: "<new-id>"
obsoleted-date: "<ISO date>"

# Inherited
ai-provenance: { ... }                # see §1
```

`tc-type: business` is the canonical name; the former `acceptance` is not used in v1.0. Acceptance against the effective TZ is performed not through TC but through AT (§8.1).

### 8.1 AT schema — the acceptance test of the contractual contour

AT ([`standard/09 §9.19`](../../standard/en/09-test-cases.md#9.19)) is derived **exclusively** from the effective TZ ([standard/07 §7.14](../../standard/en/07-adapt.md#7.14)): the initial TZ with its annexes plus every signed ACTZ (§7.2). AT checks conformance to the **contract**, not to the interpretation, and is created by an isolated agent that MUST NOT be given access to the internal contour (ADAPT, BR / SR / SPEC, TC, code).

```yaml
# Identity
id: AT-NN                             # immutable
title: "<short, descriptive>"
type: AT
negative: boolean                     # mandatory; pos/neg pairing — as for TC (standard/09 §9.7)

# Verification target (mandatory; closed list of references)
verifies:
  - "TZ §N"                           # a section of the effective TZ
  - "ACTZ-NNN §M"                     # a clause of a signed protocol
# A reference to an internal artifact (BR / SR / SPEC / TC) is fatal (standard/09 §9.19.2)

tz-version: "<effective TZ revision>" # mandatory; the revision the AT was derived from

# Provenance of the isolated agent (mandatory)
generator:
  vendor: "<provider>"                # MUST differ from the primary agent's model (mirroring P7)
  model: "<model-id>"
  internal-contour-access: false      # mandatory; confirmation of no access to the internal contour
  generated-at: "<ISO-8601>"

# Lifecycle
status: draft | ready | passing | failing | obsolete

# The acceptance-trial environment and dataset (mandatory; standard/09 §9.19.3, §8.5.10).
# NOT filled in by the generator: the isolated agent does not see internal artifacts —
# the Architect or the runner sets this AFTER generation, leaving isolation intact.
environment-ref: SPEC-TEST-NN

# Automation (mandatory; execution only by an automated runner)
automation:
  status: automated | manual-pending
  kind: dynamic | static
  location: "<path-to-implementation>"
  runner: "<runner>"

# Last run (runner-managed; bot-only)
last-run:
  date: "<ISO timestamp>"
  result: pass | fail | skipped | n/a
  runner-id: "<runner@version>"
  run-ref: "<link>"
  tz-version: "<effective TZ revision at the time of the run>"
```

The body of an AT MUST contain a section with the **verbatim quotation** of the effective-TZ clause under test (`tz_text`) next to the verification steps ([standard/09 §9.19.3](../../standard/en/09-test-cases.md#9.19)).

| Rule | Level |
|---|---|
| `verifies[]` contains only `TZ §N` and `ACTZ-NNN §M`; a reference to an internal artifact is **fatal** | hook-enforced |
| `generator.internal-contour-access: true` is **fatal**: isolation is the substance of the mechanism, not hygiene | hook-enforced |
| `generator.model` differs from the primary agent's model | hook-enforced |
| ATs are regenerated before every trial; `tz-version` MUST match the current revision of the effective TZ, otherwise the trials are blocked | hook-enforced |
| The product is not submitted for hand-over until every AT is in status `passing` ([standard/10 §10.4.3](../../standard/en/10-lifecycle-qg.md#10.4)) | normative |

---

### 8.2 Description set version record (DescriptionSet)

A substrate artifact issued by QG-0 of the description set ([standard/10 §10.5.4](../../standard/en/10-lifecycle-qg.md#10.5.4)). It is the only object that carries the version of the description; BR / SR / SPEC / TC norms have no `status` and no `version` of their own.

```yaml
set-version: "N.M"                  # identifier; immutable after release (V1)
major: boolean                      # true — the presented version (full audit is mandatory)
approved-by: "<actor>"              # Architect (V6)
approved-at: "<ISO-8601>"
supersedes: "N.M-1"                 # exactly one predecessor; absent on the first version
resolves-to: "<pointer>"            # substrate-native way to resolve the version into content
                                    # (snapshot / tag / list of (artifact-id, version-id) pairs per V5)
changes:
  added:   []                       # artifacts that entered the set in this version
  changed: []                       # changed relative to N.M-1
  removed: [{ id: "<artifact-id>", replaced-by: "<artifact-id>" }]
audit:                              # mandatory when major: true
  full: boolean
  findings-ref: "<pointer>"
architecture-signoff:               # conditional: QG-3 declared and applied to the version
  signed-by: "<actor>"
  signed-at: "<ISO-8601>"
verification:                       # bot-managed; written only by the runner (QG-2)
  - product-version: "<pointer>"
    date: "<ISO-8601>"
    result: pass | fail
    runner-id: "<runner-name@version>"
    evidence-refs: []
accepted-outcomes:                  # conditional: QG-4 declared
  - br: "BR-NN"
    accepted-by: "<actor>"
    accepted-at: "<ISO-8601>"
```

Record states: `draft` (the single draft of the set) → `approved` → `superseded` ([standard/10 §10.5.1](../../standard/en/10-lifecycle-qg.md#10.5.1)). The sections `changes`, `resolves-to`, `audit` and `approved-*` MUST NOT change after release; `verification` and `accepted-outcomes` are append-only evidence.

---

### 8.3 MW — manual walkthrough record

The evidence class ([standard/09 §9.20](../../standard/en/09-test-cases.md#9.20)): not a node of the requirements graph, extends no closed list.

```yaml
id: MW-NN
type: MW
scenario: SPEC-UC-NN | SPEC-UI-NN
set-version: "N.M"
product-version: "<pointer>"
walked-by: "<actor>"                 # V6; ≠ the implementation author (P8)
walked-at: "<ISO-8601>"
findings:
  - step: 3
    observed: "<what was seen>"
    expected-ref: SR-NN | SPEC-<TYPE>-NN   # absent if the behaviour is not described
    route: implementation-originated | contractual
evidence-refs: []
```

| Rule | Check |
|---|---|
| An MW is part of no artifact's `verified-by` and of no verification entry's `evidence-refs` | hook-enforced (standard/14 §14.7) |
| Every finding carries `route` | hook-enforced |
| `walked-by` ≠ the author of the implementation of the behaviour checked | V6 |

---

## 9. Validation rules (cross-field)

Rules not expressible in pure JSON Schema; they require a custom validator. The "Formal check" column gives an executable predicate or a reference to a ready-made KG query ([reference/05 §5/§6](05-knowledge-graph-schema.md)).

| Rule | Description | Formal check |
|---|---|---|
| **ID immutable** | When a file changes, the `id` field does not change. | `diff(prev.id, curr.id) == ∅` |
| **`parent` exists** | For SR — the parent BR belongs to the same set version. | `BR[SR.parent.id] ∈ set.members`; orphans — [05 §6.1](05-knowledge-graph-schema.md#61-orphan-approved-requirements) |
| **`source.adapt` approved** | For BR/SR/SPEC — the ADAPT in `source.adapt` is in status `approved`/`frozen`. | `status(ADAPT[art.source.adapt]) ∈ {approved, frozen}` |
| **`verified-by` consistency** | TCs in `verified-by` have `verifies[].id` = this artifact. | `∀ tc ∈ art.verified-by: art.id ∈ tc.verifies[].id` |
| **`set-version` lock** | `TC.last-run.set-version` = the set version being verified; `TC.automation.set-version` = the version in which the norm has not changed since the implementation was written ([standard/10 §10.9.4](../../standard/en/10-lifecycle-qg.md#10.9.4)). | `tc.last-run.set-version == set.version ∧ tc.automation.set-version ≥ last-change(tc)`; stale — [05 §4.4](05-knowledge-graph-schema.md#44-stale-tc-criteria-version-drift) |
| **Version composition** | Every BR / SR / SPEC / TC norm of version `N.M` resolves through `resolves-to`; `changes` agrees with the content diff `N.M−1 → N.M`; `supersedes` forms a chain without branches. | `members(N.M) = resolve(set.resolves-to)`; `changes == diff(N.M−1, N.M)`; `∀ s: count(s.supersedes) ≤ 1 ∧ count(successors(s)) ≤ 1` |
| **`uses`: version and confirmation** | The `uses` edge names an approved set version of the component; every `A-NN` of that version is confirmed; the `uses` graph is acyclic; the `consumer` statements of connected components are compatible (otherwise — stop until a person decides, standard/06 §6.14.4 rule 7). | `∀ u ∈ BR.uses: approved(u.set-version) ∧ confirmed(u) = assumptions(u.component, u.set-version) ∧ acyclic(uses)` |
| **Version coverage (`coverage-presence`)** | For every normative statement of BR / SR / SPEC of the version — a pair of TC norms in the same version ([standard/13 §13.3.5](../../standard/en/13-conformance.md#13.3.5)). | `∀ a ∈ assertions(N.M): ∃ tc⁺, tc⁻ ∈ N.M: a ∈ tc.verifies` |
| **Screen-list coverage** | Every screen of the SPEC-ARCH list of the version is covered by at least one SPEC-UI of the same version; `screens[]` of every SPEC-UI stays within the list ([standard/08 §8.5.6.1](../../standard/en/08-specifications.md#8.5.6.1)); a precondition of version approval (§10.7.2). | `∀ s ∈ screens(ARCH, N.M): ∃ ui ∈ N.M: s ∈ ui.screens ∧ ∀ ui: ui.screens ⊆ screens(ARCH, N.M)` |
| **`source.adapt` for BR/SR/SPEC (conditional)** | Canonical ADAPT source when findings are present; on a "no findings" verdict — `source.adversarial-review-ref` ([standard/07 §7.4.1](../../standard/en/07-adapt.md#7.4.1)). TR — via parent SR ([standard/06 §6.6.2](../../standard/en/06-requirements-hierarchy.md#6.6.2)). | `art.type ∈ {BR, SR, SPEC-*} ⇒ art.source.adapt ≠ null ∨ art.source.adversarial-review-ref ≠ null` |
| **SPEC-AI requires ai-act** | For an AI artifact, `ai-act.risk-class` is mandatory. | `art.type == SPEC-AI ⇒ art.ai-act.risk-class ≠ null` |
| **Data residency consistency** | RU in `SR.data-classification.data-residency` ⇒ the same in the parent BR. | `'RU' ∈ SR.…data-residency ⇒ 'RU' ∈ BR[SR.parent].…data-residency` |
| **Compliance hierarchy** | `SR.compliance ⊆ parent BR.compliance` (or explicit justification). | `SR.compliance ⊆ BR[parent].compliance ∨ exists(extension-justification)` |
| **TC `automated` requires location** | `automation.status: automated` ⇒ `automation.location` non-empty. | `tc.automation.status == 'automated' ⇒ tc.automation.location ≠ ''` |
| **Negative TC mandatory** | For every normative assertion — a TC with `negative: true`. | `∀ assertion ∈ art: ∃ tc(negative: true)` ([standard/09 §9.7](../../standard/en/09-test-cases.md#9.7)) |
| **ADAPT open-questions == 0 for approved** | Approval is blocked while there are `open` / `asked-to-client` / `answered` / `revised` backward entries: every finding MUST be `resolved` ([standard/07 §7.4.5](../../standard/en/07-adapt.md#7.4.5), [standard/10 §10.8.2](../../standard/en/10-lifecycle-qg.md#10.8)). | `adapt.status == approved ⇒ count(backward[status ∈ {open, asked-to-client, answered, revised}]) == 0` ([05 §4.7](05-knowledge-graph-schema.md#47-adapt-with-open-backward-findings)) |
| **Supersession correct** | `supersedes` ⇒ non-empty `supersession-rationale` and a symmetric `superseded-by` on the target; no dangling `source.adapt` on a `superseded` ADAPT ([standard/07 §7.6.4](../../standard/en/07-adapt.md#7.6), [standard/10 §10.8.5](../../standard/en/10-lifecycle-qg.md#10.8)). | `adapt.supersedes ≠ null ⇒ adapt.supersession-rationale ≠ '' ∧ ADAPT[adapt.supersedes].superseded-by == adapt.id`; `∄ art: art.source.adapt = X ∧ status(ADAPT[X]) == superseded` |
| **Judge isolation (SPEC-AI)** | `judge.vendor` ≠ `production-model.vendor`. | `tc.judge.vendor ≠ SPEC-AI[tc.verifies].production-model.vendor` |
| **SPEC depends-on acyclic** | The `depends-on` graph between SPEC is a DAG. | cypher cycle-detection ([05 §4.6](05-knowledge-graph-schema.md#46-spec-dependency-cycle-detection)): rows ≠ ∅ ⇒ violation |
| **A `resolved` finding is decided by a signed ACTZ** | A backward finding in status `resolved` MUST carry `decided-in: ACTZ-NNN §M`, and the target ACTZ MUST be in status `signed` ([standard/07 §7.13.2](../../standard/en/07-adapt.md#7.13)). | `b.status == resolved ⇒ b.decided-in ≠ null ∧ status(ACTZ[b.decided-in]) == signed ∧ b.decided-in.§M ∈ ACTZ.decisions[].number` |
| **A `signed` ACTZ is signed by both parties** | Both signature fields are filled in; a signed protocol is immutable. | `actz.status == signed ⇒ actz.client-signature ≠ null ∧ actz.vendor-signature ≠ null` |
| **AT references the contract only** | `AT.verifies[]` contains only `TZ §N` / `ACTZ-NNN §M`; a reference to an internal artifact is fatal ([standard/09 §9.19.2](../../standard/en/09-test-cases.md#9.19)). | `∀ v ∈ at.verifies: v ~ /^(TZ §|ACTZ-\d{3} §)/`; `at.generator.internal-contour-access == false` |
| **AT is fresh against the effective TZ** | `AT.tz-version` = the current revision of the effective TZ; a divergence blocks the trials ([standard/09 §9.19.4](../../standard/en/09-test-cases.md#9.19)). | `at.tz-version == effective-tz.version` |
| **Red history is the condition for counting a TC** | A TC counts as evidence at QG-2 only with a recorded red → green transition; the exception is the `implementation-originated` class with a killed mutant ([standard/09 §9.18.2](../../standard/en/09-test-cases.md#9.18)). | `tc.red-history.green-transition ≠ null ∨ tc.red-history.inherited-from ≠ null ∨ (tc.red-history.not-applicable-reason == implementation-originated ∧ tc.red-history.mutation-check.mutants-killed ≥ 1)` |
| **`implementation-originated` is not client-observable** | The class legalises an internal detail; client-observable behaviour MUST NOT pass through it ([standard/06 §6.13.2](../../standard/en/06-requirements-hierarchy.md#6.13)). | `art.source.implementation-originated ≠ null ⇒ observable-by-client == false ∧ human-approval ≠ null ∧ mutation-check.mutants-killed ≥ 1` |
| **A dynamic TC is bound to a bench** | A TC with `automation.kind: dynamic` MUST carry an `environment-ref` to an existing SPEC-TEST: "the test passed" only means something against a specific bench and specific data ([standard/09 §9.3](../../standard/en/09-test-cases.md#9.3)). A missing field on a dynamic TC is **fatal**. Static checks (the doc-lint, the structural TC) require no bench. | `tc.automation.kind == 'dynamic' ⇒ tc.environment-ref ≠ null ∧ type(SPEC[tc.environment-ref]) == 'SPEC-TEST'` |
| **Real client data is signed off by the client** | A SPEC-TEST in which at least one dataset carries real or personal client data MUST carry a `client-signature`; volume and anonymization are the client's decision, not the vendor's ([standard/08 §8.5.10](../../standard/en/08-specifications.md#8.5.10)). A missing signature is **fatal**. | `∃ d ∈ spec.datasets: d.contains-real-client-data == true ∨ d.anonymized == false ⇒ spec.client-signature ≠ null` |
| **A SPEC-DOC is covered by the doc-lint** | A SPEC-DOC MUST have at least one doc-lint TC (`automation.kind: static`); without it the SPEC-DOC is unverifiable and **does not pass QG-2** ([standard/09 §9.8](../../standard/en/09-test-cases.md#9.8)). | `spec.type == 'SPEC-DOC' ⇒ ∃ tc ∈ spec.verified-by: tc.automation.kind == 'static'` |

---

## 10. Substrate isomorphism

Mapping for git (YAML frontmatter) ↔ document-oriented store (JSON document):

| Field (canonical) | git (YAML frontmatter) | Document store (JSON doc) |
|---|---|---|
| `id` | `id` | `_id = <project>:<doc-type>:<slug>`, field `slug` |
| `type: BR` | `type: BR` | `level: "business"` |
| `type: SPEC-API` | `type: SPEC-API` | `doc_type: "spec_api"` |
| `parent.id` | `parent.id` | `parent` |
| `children` | (auto-derived) | `children` (auto-derived) |
| `status` | `status` | `status` |
| `priority` | `priority` | `priority` |
| `source.adapt` | `source.adapt` | `created_from_adapt` |
| `constrained-by[]` | `constrained-by` | `constrained_by` (subdoc array) |
| `verified-by[]` | (auto-derived) | `linked_tests` |
| `ai-provenance.*` | nested object | nested subdocument |
| `compliance` | `compliance` array | `compliance` subdoc |
| `data-classification` | nested object | nested subdoc |
| `business-outcome` | nested object | nested subdoc |
| `replaces` / `replaced-by` | string ID | `replaces` / `replaced_by` |

Substrate-native field names MAY differ, but the **semantics and invariants are preserved** through capabilities V1–V6 ([01-glossary.md §2.7](01-glossary.md#2.7)).

---

## 11. Schema versioning

Every artifact has a `schema-version` field (semver). When the file version and the current schema do not match, the validator proposes a migration.

| Change | Bump |
|---|---|
| New optional field | minor (1.0 → 1.1) |
| New mandatory field | major (1.0 → 2.0) + migration script |
| Field removal | major + migration script |
| Enum change | minor if addition, major if removal |
| Field rename | major + migration script |

**Current schemas version:** 1.0.

---

## 12. JSON Schema fragment example (BR)

Key patterns (the full BR schema — `reference/schemas/br.json`, planned):

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://renar.tech/schemas/br.json",
  "type": "object",
  "required": ["id", "title", "type", "status", "priority", "source", "ai-provenance"],
  "properties": {
    "id":     { "type": "string", "pattern": "^BR-[0-9]{2}(\\.[0-9]+)?$" },
    "title":  { "type": "string", "minLength": 5, "maxLength": 100 },
    "type":   { "const": "BR" },
    "status": { "enum": ["draft", "approved", "verified", "accepted", "deprecated"] },
    "priority": { "enum": ["must", "should", "could"] },
    "source": { "type": "object", "required": ["tz-section"], "properties": { "adapt": { "pattern": "^ADAPT-[0-9]{3}(-delta-[0-9]+)?$" } } },
    "ai-provenance": { "required": ["generated-by", "generated-at"], "properties": { "generated-by": { "pattern": "^[a-z]+-[a-z0-9-]+@[0-9]{4}-[0-9]{2}-[0-9]{2}$" } } }
  }
}
```

Equivalent JSON schemas for SR/TR/SPEC-*/TC/ADAPT are in `reference/schemas/` (planned).

---

*Schemas reference RENAR 1.1 — see also [01-glossary.md](01-glossary.md), `standard/06`-`09` for normative definitions.*
