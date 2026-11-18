# MayanForge — M0 Existing Jira Story Enrichment

**Status:** REVIEWED SPECIFICATION v3 — proposed implementation guidance; architectural decisions remain subject to explicit approval.
**Scope:** Existing stories MF-40–MF-58 only; no new stories.
**Sources reviewed:** Master Capability Registry v1.2; Incremental Implementation Blueprint v1.2; Layer 2 Service Architecture v2.3; Layer 3 Architecture v3.3; Jira story inventory.
**Interpretation:** Technical implementation steps and test scenarios below are proposed acceptance specifications derived from the architecture. They are not claims that software exists or that all design choices are approved.

## v3 review controls and decision rules

**Review status:** Agent-ready specification proposals, not approved architecture. Applying these descriptions to Jira is a planning update, not approval of design decisions or completion of work. **No new Jira issues are requested.**

**Artifact paths below are proposed repository conventions**, not existing files. The owner/reviewer roles below are proposed assignments, not named individuals. Architecture decisions (including HTTP error codes, schema strictness, compatibility policy, CI tool choice and artifact locations) require explicit review before becoming normative.

**Shared evidence standard:** For each story, capture a repository commit or immutable document revision, validation command or manual review procedure, observed result, mapping from each acceptance criterion to evidence, named reviewer/decision record, and outstanding risks. A proposed test is not evidence of an executed test.

**Shared ADR status vocabulary (proposed):** Proposed, Approved, Rejected, Deferred, Blocked. MF-48 and MF-57 must use one canonical ADR index; each cross-layer decision has one accountable approver. Names and approvals are **TBD**, not presumed.

**Dependency interpretation:** “Depends on” means the referenced M0 story's baseline artifact must be reviewable before dependent sign-off; work may proceed concurrently. References to later enrichment of an existing guide are nonblocking. These are **proposed** dependencies, not Jira issue links. The v3 review removed circular sign-off dependencies (MF-40/MF-41, MF-44/MF-49, MF-52/MF-53, MF-47/MF-56, MF-55/MF-56, MF-48/MF-57).

**M0 exit:** M0 can pass with approved contract/design artifacts, reproducible schema/CI checks, documented open decisions and M1 entry gates. It must not claim runtime behavior, deployment, workflow reliability or production security has been validated.

## M0 completion rule
M0 delivers reconciled source inventory, explicit ownership, versioned cross-layer contracts, fixtures, threat/ADR decisions and M1 readiness. Runtime integration tests and running workflows belong to later milestones; M0 acceptance is based on reviewable contracts, reproducible validation scripts and documented decisions.

---

## MF-40 — [MF-GOV-0213] Registry coverage CI validation

**Technical context:** Governance · registry JSON · CI  
**Original Jira summary:** Capability coverage CI check  
**Original epic:** M0-E01 — Source reconciliation & coverage  
**Source:** This implementation recommendation · Proposed governance additions  
**Classification:** New recommendation · Not asserted as present in uploaded architecture

### Objective

Make missing, duplicated, or orphaned capability records visible before a planning change is accepted.

### Proposed implementation

1. Use Master Capability Registry v1.2 JSON as the sole input for canonical capability IDs; document its schema and validation rules.
2. Create a deterministic validation script checking ID uniqueness, required source references, target milestone presence, and recognized layer/classification values.
3. Report coverage by Cataloged / Contracted / Implemented / Verified / Deferred / Blocked / Superseded without interpreting a source heading as implemented.
4. Publish machine-readable errors and a human-readable CI summary; make invalid registry structure fail the check.
5. Keep the check read-only: it must not rewrite capability IDs or silently reassign milestones.

### Acceptance criteria — Given / When / Then

1. Given an unchanged valid registry, when the check runs twice, then it produces the same result and counts.
2. Given duplicate MF IDs or a missing source reference, when validation runs, then CI fails and identifies the affected rows.
3. Given a heading-level entry without an atomic contract, when counted, then it remains cataloged rather than being marked contracted or implemented.
4. Given a deferred entry, when validation runs, then it remains visible in the coverage report.
5. Given an unsupported milestone or status, when validation runs, then it fails with an actionable diagnostic.

### Concrete deliverables and proposed repository locations

- `registry/schema.md; scripts/validate_registry; reports/registry-coverage.json; fixtures/registry/{valid,duplicate-id,missing-source,unknown-milestone}.json`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-49, MF-53 for source inventory; MF-40 validates registry structure independently of MF-41 contract links.
- **Scope/ownership rule:** A structural pass must not claim full architectural coverage; 217 records are first-pass section-level inventory, and milestone mappings remain provisional until approved.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Run validator twice on unchanged data and diff outputs; run each invalid fixture and capture exit status, row ID and error code.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

CI logs, sample valid/invalid fixtures, deterministic coverage report, reviewer sign-off.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-GOV-0213`; milestone: `M0`; existing Jira issue: `MF-40`.
- Source: `This implementation recommendation`; section: `Proposed governance additions`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-41 — [MF-GOV-0214] Capability-to-contract traceability map

**Technical context:** Governance · registry · JSON Schema/OpenAPI references  
**Original Jira summary:** Feature-to-contract traceability automation  
**Original epic:** M0-E01 — Source reconciliation & coverage  
**Source:** This implementation recommendation · Proposed governance additions  
**Classification:** New recommendation · Not asserted as present in uploaded architecture

### Objective

Make each registry capability traceable to the proposed cross-layer contract, implementation milestone, and validation evidence.

### Proposed implementation

1. Define a versioned mapping record: MF ID, source file/section, owner layer, principal milestone, contract reference(s), decision status, evidence reference(s).
2. Map all M0 entries first, then specify the process for later entries without pretending section headings are atomic features.
3. Use stable contract IDs and versioned references rather than copy/pasted contract definitions.
4. Add a checker for dangling capability IDs, broken contract references, missing owner layers, and conflicting mappings.
5. Document how one capability may require prerequisite work in earlier milestones even when its principal target is later.

### Acceptance criteria — Given / When / Then

1. Given a mapped capability, when its MF ID is queried, then its source and contract/version references are recoverable.
2. Given a contract reference that does not exist, when validation runs, then the mapping is flagged.
3. Given a cross-layer capability, when reviewed, then both interface ownership and authoritative state ownership are explicit.
4. Given a capability spanning milestones, when mapped, then its principal milestone and earlier prerequisites are separately represented.
5. Given an unresolved contract, when reported, then its status is proposed or blocked rather than approved.

### Concrete deliverables and proposed repository locations

- `traceability/m0-capability-contract-map.json; traceability/schema.json; scripts/check_traceability; reports/traceability-gaps.md`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-40 for validated IDs; MF-47, MF-50, MF-56 for canonical contract identifiers.
- **Scope/ownership rule:** Allow one-to-many contract links and explicit unmapped/not-yet-contracted status; never infer complete contracts from heading-level records.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Fixture with missing contract ID, duplicate mapping, unresolved mapping and future-milestone prerequisite; capture checker outputs.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Traceability export, referential-integrity checks, sample cross-layer mappings, review record.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-GOV-0214`; milestone: `M0`; existing Jira issue: `MF-41`.
- Source: `This implementation recommendation`; section: `Proposed governance additions`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-42 — [MF-GOV-0215] Versioned capability change log

**Technical context:** Governance · registry history · Git  
**Original Jira summary:** Versioned feature change log  
**Original epic:** M0-E03 — Governance & acceptance  
**Source:** This implementation recommendation · Proposed governance additions  
**Classification:** New recommendation · Not asserted as present in uploaded architecture

### Objective

Preserve an auditable explanation of changes to capability meaning, mapping, or status.

### Proposed implementation

1. Define a structured change entry with MF ID, before/after value, reason, affected source, contract impact, reviewer and decision date.
2. Use Git history and review artifacts as the authoritative change evidence; do not build a database service in M0.
3. Define change categories: added, clarified, split, merged, deferred, superseded, milestone remapped.
4. Keep old IDs discoverable; require explicit lineage links when a heading is decomposed into atomic requirements.
5. Publish a sample change log and a short authoring/review procedure.

### Acceptance criteria — Given / When / Then

1. Given a capability mapping change, when reviewed, then its prior value and rationale remain recoverable.
2. Given a split capability, when published, then the original ID points to its successor requirements.
3. Given a change without reviewer or source justification, when validated, then it is flagged for approval.
4. Given a deferred capability, when updated, then it remains in the registry and is not silently deleted.
5. Given a no-op registry revision, when compared, then no fabricated change entry is produced.

### Concrete deliverables and proposed repository locations

- `governance/capability-change-log.md; governance/change-record.schema.json; fixtures/change-record-example.json`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-40, MF-41; MF-43 for impact review fields.
- **Scope/ownership rule:** Record author, timestamp, before/after, affected MF IDs, rationale, review status and approval reference; preserve immutable history.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Review sample create/amend/supersede changes and demonstrate retained prior revisions and link integrity.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Change-log schema, sample before/after diff, lineage fixture, review sign-off.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-GOV-0215`; milestone: `M0`; existing Jira issue: `MF-42`.
- Source: `This implementation recommendation`; section: `Proposed governance additions`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-43 — [MF-GOV-0217] Expansion impact review template

**Technical context:** Governance · architecture change control  
**Original Jira summary:** Expansion impact review template  
**Original epic:** M0-E03 — Governance & acceptance  
**Source:** This implementation recommendation · Proposed governance additions  
**Classification:** New recommendation · Not asserted as present in uploaded architecture

### Objective

Make future scope expansion safe without implementing expansion capabilities during M0.

### Proposed implementation

1. Create a reusable impact template for new role, skill, stage activity, integration, or workflow kind.
2. Require source intent, MF IDs, owning layer, API/event changes, migration impact, permissions, state/invalidation effects, verification coverage and rollout.
3. Include policy/tool-gateway, tenant isolation, human approvals, idempotency and evidence binding as explicit review prompts.
4. Require ADR or contract-version update where a proposed expansion changes an invariant.
5. Exercise the template against one hypothetical integration, clearly marked as a planning example.

### Acceptance criteria — Given / When / Then

1. Given an expansion proposal, when reviewed, then every impact category has a response or explicit not-applicable rationale.
2. Given a new agent role, when proposed, then it is evaluated as a Harness profile rather than assumed to require a new service.
3. Given a changed approval subject, when reviewed, then digest invalidation and downstream gate effects are addressed.
4. Given a change with missing security assessment, when submitted, then approval remains blocked.
5. Given a sample review, when inspected, then no future feature is represented as already implemented.

### Concrete deliverables and proposed repository locations

- `governance/expansion-impact-template.md; governance/impact-review-example.md`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-42; MF-45, MF-54 for invariants and trust boundaries.
- **Scope/ownership rule:** Impact assessment covers contracts, owners, stage gates, tenants, idempotency, evidence, cost and milestones; non-applicable fields require reasons.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Run template against one representative proposed workflow addition and verify unresolved high-risk impacts block approval.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Template, completed example, checklist validation, architecture reviewer approval.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-GOV-0217`; milestone: `M0`; existing Jira issue: `MF-43`.
- Source: `This implementation recommendation`; section: `Proposed governance additions`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-44 — [MF-L2-0037] Layer 2 architecture baseline and reading guide

**Technical context:** Layer 2 · Java 21/Spring Boot · architecture baseline  
**Original Jira summary:** 01 · Executive summary and reading guide  
**Original epic:** M0-E03 — Governance & acceptance  
**Source:** MayanForge_Layer2_Service_Architecture_v2_3.html · 01 · Executive summary and reading guide  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Provide an unambiguous implementation entry point to the proposed business service architecture.

### Proposed implementation

1. Publish a concise architecture index pointing to Layer 2 v2.3 sections for modules, contracts, data, security, failure handling and acceptance gates.
2. State that Layer 2 is a proposed Java 21/Spring Boot modular monolith, not a running deployment.
3. Summarize Layer 2 ownership of business records, versioned drafts, approvals, admission, projections and audit.
4. Explain the authoritative distinction from Layer 1 display and Layer 3 workflow/evidence execution.
5. Identify the canonical versions of UI v7, Layer 2 v2.3, Layer 3 v3.3 and blueprint v1.2.

### Acceptance criteria — Given / When / Then

1. Given a new coding agent, when reading the guide, then it can locate the authoritative section for each Layer 2 responsibility.
2. Given a proposed endpoint, when referenced, then it is labeled proposed rather than deployed.
3. Given a business approval requirement, when traced, then Layer 2 ownership is identified.
4. Given a workflow-execution concern, when traced, then Layer 3 is identified as authority.
5. Given conflicting versions, when the guide is reviewed, then the baseline and unresolved discrepancy are explicit.

### Concrete deliverables and proposed repository locations

- `architecture/l2/README.md; architecture/source-baseline.yaml`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** None for initial architecture index; MF-45, MF-46 and MF-49 subsequently enrich the guide without blocking its baseline.
- **Scope/ownership rule:** Index source sections without rewriting their normative meaning; mark proposed versus accepted versus implemented status explicitly.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Verify links, versions and authority labels with an L2 reviewer and a new-reader navigation walkthrough.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Versioned index, owner review, link checks, architecture status statement.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L2-0037`; milestone: `M0`; existing Jira issue: `MF-44`.
- Source: `MayanForge_Layer2_Service_Architecture_v2_3.html`; section: `01 · Executive summary and reading guide`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-45 — [MF-L2-0038] Layer 2 scope, invariants and non-goals

**Technical context:** Layer 2 · security · business invariants  
**Original Jira summary:** 02 · Scope, invariants and non-goals  
**Original epic:** M0-E01 — Source reconciliation & coverage  
**Source:** MayanForge_Layer2_Service_Architecture_v2_3.html · 02 · Scope, invariants and non-goals  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Translate the Layer 2 scope into explicit constraints that future code and tests must preserve.

### Proposed implementation

1. Create a normative table of Layer 2-owned versus Layer 3-owned and Layer 1-owned state.
2. Specify invariant statements for tenant/project authorization, immutable version-bound approvals, guarded stage transitions and idempotent admission.
3. Document that HTTP 202 is acceptance, not completion; agent self-reported PASS is not independent verification.
4. Define a non-goals list excluding agent loops, Temporal workflow history, sandbox execution and autonomous deployment authority.
5. Assign each invariant to its proposed enforcement point and future validation test.

### Acceptance criteria — Given / When / Then

1. Given a proposed Layer 2 module, when reviewed, then its responsibilities do not include agent tool execution.
2. Given an approval tied to revision A, when revision B is introduced, then the contract forbids silently transferring that approval.
3. Given a 202 response, when documented, then it cannot be interpreted as successful workflow completion.
4. Given a user without project access, when evaluating a business operation, then deny-by-default authorization is required.
5. Given a quality verdict, when traced, then the independent evaluator is the authority.

### Concrete deliverables and proposed repository locations

- `architecture/l2/invariants.md; architecture/l2/ownership-matrix.csv; architecture/l2/non-goals.md`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-49, MF-53; MF-54 for cross-layer security.
- **Scope/ownership rule:** Each invariant has stable ID, owning layer, enforcement boundary, negative example, and later milestone test mapping.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Review tenant, approval revision, admission retry, HTTP 202 and verification-authority counterexamples against invariant matrix.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Invariant matrix, threat/ownership review, negative-test specifications, signed baseline.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L2-0038`; milestone: `M0`; existing Jira issue: `MF-45`.
- Source: `MayanForge_Layer2_Service_Architecture_v2_3.html`; section: `02 · Scope, invariants and non-goals`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-46 — [MF-L2-0040] Layer 2 non-ownership and forbidden coupling

**Technical context:** Layer 2 ↔ Layer 3 · module boundaries  
**Original Jira summary:** Layer 2 does not own  
**Original epic:** M0-E01 — Source reconciliation & coverage  
**Source:** MayanForge_Layer2_Service_Architecture_v2_3.html · 02 · Scope, invariants and non-goals  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Prevent the business service from becoming a second orchestrator or bypassing execution policy.

### Proposed implementation

1. Enumerate excluded concerns: workflow scheduling, DAG state, Harness execution, tool effects, LLM calls, sandbox control and verdict computation.
2. Define allowed integration ports for execution commands, status events, approval signals and evidence references.
3. Write architecture tests or static rules proposed for M1 to forbid direct dependencies from business modules to agent/worker implementations.
4. Specify that UI-visible status is an eventually consistent projection, not a competing workflow state machine.
5. Record exceptions as explicit ADRs; do not silently permit shared-table writes across ownership boundaries.

### Acceptance criteria — Given / When / Then

1. Given a business-module design, when it schedules Temporal tasks directly from a UI controller, then the design review rejects the coupling.
2. Given a workflow status update, when received, then it is projected from Layer 3 rather than invented by Layer 2.
3. Given a request to invoke a tool, when routed, then the Layer 3 Tool Gateway remains the enforcement boundary.
4. Given shared PostgreSQL infrastructure, when data ownership is reviewed, then cross-layer write privileges remain restricted.
5. Given a proposed exception, when considered, then an ADR is required before acceptance.

### Concrete deliverables and proposed repository locations

- `architecture/l2/forbidden-couplings.md; architecture/l2/boundary-examples.md`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-45; MF-54.
- **Scope/ownership rule:** Document prohibited direct sandbox/tool/Temporal-history mutations from Layer 2 and allowed command/event adapters; examples are conceptual, not code.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Review at least five proposed component interactions and flag boundary violations with owner and correction.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Dependency/ownership diagram, forbidden-dependency checks specification, review decisions.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L2-0040`; milestone: `M0`; existing Jira issue: `MF-46`.
- Source: `MayanForge_Layer2_Service_Architecture_v2_3.html`; section: `02 · Scope, invariants and non-goals`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-47 — [MF-L2-0086] Layer 2–Layer 3 contract alignment

**Technical context:** Layer 2/3 · Spring Boot · Temporal · event contracts  
**Original Jira summary:** 25 · Layer 2 ↔ Layer 3 contract alignment  
**Original epic:** M0-E02 — Canonical contracts & ADRs  
**Source:** MayanForge_Layer2_Service_Architecture_v2_3.html · 25 · Layer 2 ↔ Layer 3 contract alignment  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Agree on one execution-admission and event protocol between business authority and workflow authority.

### Proposed implementation

1. Reconcile Layer 2 section 25 with Layer 3 normative execution contracts; publish a single versioned contract index.
2. Specify ExecutionCommand envelope, workflowKind enumeration, actor/tenant/project identity, subject digests, idempotency, budget/policy refs and correlation fields.
3. Specify StageOutputProduced, execution/task status, approval signals, defects, candidate and verification evidence references.
4. Normalize JSON wire casing to camelCase and shared status/verdict enums; document snake_case in diagrams as notation only.
5. Create success, duplicate, stale-version, unauthorized and replay fixtures, including 202 meaning admitted not completed.

### Acceptance criteria — Given / When / Then

1. Given equivalent Layer 2 and Layer 3 fixtures, when validated, then both agree on field names, versions and enum values.
2. Given a missing subject digest or tenant identity, when the command is validated, then it is rejected.
3. Given an equivalent idempotent retry, when modeled, then the same logical execution reference is required.
4. Given an event replay, when projected, then it does not create duplicate business effects.
5. Given an approval for a stale digest, when signaled, then the contract prevents progression.

### Concrete deliverables and proposed repository locations

- `contracts/cross-layer/protocol-alignment.md; contracts/cross-layer/compatibility-matrix.md; fixtures/cross-layer/protocol-cases.json`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-45, MF-53 and MF-54. MF-47 defines protocol semantics first; MF-56 subsequently encodes those semantics as schemas. MF-50 owns the HTTP API surface.
- **Scope/ownership rule:** MF-47 owns reconciliation and protocol behavior, NOT duplicate schema definitions. Schema field decisions are references to MF-56. Enumerate required/optional fields, compatibility policy, replay, idempotency, correlation, stale approval and error routing.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Validate each protocol scenario against MF-56 schemas; document expected accept/reject outcomes and Layer 2/3 owner review.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Versioned schemas, cross-service fixture suite, negative cases, contract owner sign-off.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L2-0086`; milestone: `M0`; existing Jira issue: `MF-47`.
- Source: `MayanForge_Layer2_Service_Architecture_v2_3.html`; section: `25 · Layer 2 ↔ Layer 3 contract alignment`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-48 — [MF-L2-0087] Layer 2 ADRs, open questions and traceability

**Technical context:** Layer 2 · ADR repository · cross-layer review  
**Original Jira summary:** 26 · Architecture decisions, open questions and traceability  
**Original epic:** M0-E01 — Source reconciliation & coverage  
**Source:** MayanForge_Layer2_Service_Architecture_v2_3.html · 26 · Architecture decisions, open questions and traceability  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Turn proposed Layer 2 decisions and unresolved design questions into an owned, reviewable decision log.

### Proposed implementation

1. Extract proposed decisions from Layer 2 section 26 and identity/session sections 27–31 without silently treating them as approved.
2. Record each ADR with context, alternatives, consequences, affected MF IDs, owner and decision status.
3. Track open choices including IdP, tenancy isolation, artifact storage, service-to-service authentication and broker/outbox topology.
4. Link each decision to contracts, API ownership and future acceptance tests.
5. Set explicit M1 blockers versus timeboxed later decisions; document who approves the baseline.

### Acceptance criteria — Given / When / Then

1. Given an unresolved security choice, when reviewed, then it has an owner and cannot be silently defaulted.
2. Given an approved ADR, when traced, then its affected contracts and MF IDs are listed.
3. Given conflicting Layer 2 and Layer 3 statements, when logged, then neither is silently overwritten.
4. Given a timeboxed decision, when its date passes, then it is flagged as blocked or escalated.
5. Given a new decision, when added, then it uses a stable ADR identifier and preserves history.

### Concrete deliverables and proposed repository locations

- `decisions/ADR-index.md; decisions/l2-open-questions.md; decisions/adr-template.md`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-49, MF-45 and MF-54. MF-48 establishes the shared ADR index; MF-57 contributes Layer 3 decisions to that index.
- **Scope/ownership rule:** One ADR namespace and one status per cross-layer decision. Statuses proposed/approved/rejected/deferred/blocked; named accountable decision authority must be assigned, not assumed.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Test ADR index for duplicate IDs, missing owner, unresolved M1 blocker, and conflicting cross-layer status.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

ADR log, open-questions register, decision-to-contract links, review minutes.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L2-0087`; milestone: `M0`; existing Jira issue: `MF-48`.
- Source: `MayanForge_Layer2_Service_Architecture_v2_3.html`; section: `26 · Architecture decisions, open questions and traceability`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-49 — [MF-L2-0088] Layer 2 reconciliation notes

**Technical context:** Layer 2 · source reconciliation  
**Original Jira summary:** Reconciliation notes  
**Original epic:** M0-E01 — Source reconciliation & coverage  
**Source:** MayanForge_Layer2_Service_Architecture_v2_3.html · 26 · Architecture decisions, open questions and traceability  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Document specific differences between prior UI/service assumptions and the current Layer 2 v2.3 baseline.

### Proposed implementation

1. Build a delta table covering UI v7, Layer 2 v2.3 and Layer 3 v3.3 workflow and approval semantics.
2. Check intake scope approval, architecture proposals then system design, task review before merge, verification sign-off and human-triggered repair.
3. Reconcile stage draft generation commands, StageOutputProduced projection, reopen/invalidation and change-plan approval.
4. Record every mismatch as resolved, proposed resolution or blocked with evidence/source section.
5. Avoid asserting that an alignment note is a tested implementation.

### Acceptance criteria — Given / When / Then

1. Given a UI action, when reconciled, then it has a named Layer 2 endpoint and associated workflow kind or is flagged missing.
2. Given a changed approval type, when compared, then both layers agree on its immutable subject.
3. Given a repair flow, when reviewed, then a failed test cannot automatically authorize repair.
4. Given an unresolved conflict, when published, then the conflict remains visible.
5. Given a resolved conflict, when closed, then the chosen source version and reviewer are recorded.

### Concrete deliverables and proposed repository locations

- `reconciliation/l2-v2.3-delta-matrix.md; reconciliation/l2-conflicts.md`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-44 and MF-52 for versioned source indexes; reconcile Layer 2 independently, then compare MF-53 Layer 3 findings.
- **Scope/ownership rule:** For each discrepancy record both exact source sections, observed conflict, proposed disposition, owner, decision link and whether M1 is blocked.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Reconcile each listed human gate, stage-output event, repair and invalidation scenario with two-source citations and reviewer decision.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Reconciliation matrix, action-to-endpoint map, discrepancy closure evidence.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L2-0088`; milestone: `M0`; existing Jira issue: `MF-49`.
- Source: `MayanForge_Layer2_Service_Architecture_v2_3.html`; section: `26 · Architecture decisions, open questions and traceability`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-50 — [MF-L2-0093] Layer 2 API and error contract fixtures

**Technical context:** Layer 2 · REST/OpenAPI · RFC 9457 · concurrency  
**Original Jira summary:** 29 · API contracts, errors and end-to-end examples  
**Original epic:** M0-E02 — Canonical contracts & ADRs  
**Source:** MayanForge_Layer2_Service_Architecture_v2_3.html · 29 · API contracts, errors and end-to-end examples  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Make proposed business APIs and error behavior testable before M1 implementation.

### Proposed implementation

1. Extract candidate REST routes and DTOs from Layer 2 sections 12 and 29; mark endpoints proposed until approved.
2. Define OpenAPI schemas for commands, idempotency headers, If-Match/ETag revision preconditions, errors and SSE projections.
3. Standardize RFC 9457-style problem responses, permission-denied behavior, conflict/stale revision and rate-limit responses.
4. Create fixture examples for one complete UI command through admission, asynchronous status and read projection.
5. Include negative fixtures for cross-tenant access, duplicate/conflicting idempotency key, stale revision and malformed payload.

### Acceptance criteria — Given / When / Then

1. Given a valid request fixture, when schema-validated, then required fields and response status match the contract.
2. Given a stale If-Match precondition, when modeled, then a conflict/precondition failure is returned without mutation.
3. Given an unauthorized tenant request, when validated, then the contract does not leak another tenant’s resource.
4. Given a duplicate key with different payload, when modeled, then the contract rejects the conflict.
5. Given an accepted asynchronous command, when documented, then 202 does not claim completion.

### Concrete deliverables and proposed repository locations

- `contracts/http/api-inventory.md; contracts/http/openapi.yaml; contracts/http/errors.md; fixtures/http/{positive,negative}/*.json`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-45, MF-47, MF-56; MF-48 for unresolved API decisions.
- **Scope/ownership rule:** API inventory must distinguish approved from proposed routes; specify authentication, tenant scoping, request/response and error schemas, asynchronous 202 semantics, ETag/concurrency only where approved or proposed ADR.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Run OpenAPI linter/schema validation; verify positive and 401/403/404/409/422 or documented equivalents per operation; record unapproved status-code choices.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

OpenAPI draft, example requests/responses, schema checks, API/security review.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L2-0093`; milestone: `M0`; existing Jira issue: `MF-50`.
- Source: `MayanForge_Layer2_Service_Architecture_v2_3.html`; section: `29 · API contracts, errors and end-to-end examples`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-51 — [MF-L2-0100] Layer 2 API ownership and M1 entry gates

**Technical context:** Layer 2 · module ownership · delivery sequencing  
**Original Jira summary:** 31 · API ownership matrix, delivery plan and acceptance gates  
**Original epic:** M0-E02 — Canonical contracts & ADRs  
**Source:** MayanForge_Layer2_Service_Architecture_v2_3.html · 31 · API ownership matrix, delivery plan and acceptance gates  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Assign a single owner to every initial UI-to-business endpoint and define measurable M1 readiness.

### Proposed implementation

1. Create page/action → API → module → command/event → Layer 3 workflow mapping.
2. Identify the first end-to-end M1 path: UI request → Spring Boot admission → Temporal workflow → event inbox/projection → UI status.
3. Define ownership for identity, project, approval, admission, outbox/inbox, artifact and projection modules.
4. Record implementation dependencies, tests and decision blockers; separate M0 contract work from M1 coding tasks.
5. Publish objective M0 exit checks for schema fixtures and authorization/stale-version negative cases.

### Acceptance criteria — Given / When / Then

1. Given an initial UI action, when mapped, then one Layer 2 owner and backend path are named.
2. Given an action without an endpoint, when checked, then the gap blocks the baseline.
3. Given an M1 task, when sequenced, then prerequisite contracts and infrastructure decisions are identified.
4. Given an exit gate, when evaluated, then evidence is a reproducible fixture or approved document, not an invented runtime test.
5. Given a Layer 3 workflow kind, when traced, then its initiating API is discoverable.

### Concrete deliverables and proposed repository locations

- `delivery/m1-entry-gates.md; delivery/api-ownership-matrix.csv; delivery/m1-dependency-map.md`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-47, MF-48, MF-50, MF-54, MF-56, MF-57; final M0 exit integration.
- **Scope/ownership rule:** Gate each M1 slice on approved ownership, contracts, ADR disposition, evidence link and executable acceptance test plan; distinguish M1 planned tests from M0 executed checks.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Audit one end-to-end proposed UI → L2 → Temporal → UI status slice and demonstrate pass/fail gate evaluation.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

API ownership matrix, M1 dependency map, gate checklist, cross-layer sign-off.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L2-0100`; milestone: `M0`; existing Jira issue: `MF-51`.
- Source: `MayanForge_Layer2_Service_Architecture_v2_3.html`; section: `31 · API ownership matrix, delivery plan and acceptance gates`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-52 — [MF-L3-0103] Layer 3 execution architecture baseline and reading guide

**Technical context:** Layer 3 · Java 21 · Temporal · Agent Harness  
**Original Jira summary:** Executive summary and reading guide  
**Original epic:** M0-E03 — Governance & acceptance  
**Source:** MayanForge_Layer3_Architecture_v3_3.html · Executive summary and reading guide  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Provide a single accurate entry point for agents implementing the proposed durable execution engine.

### Proposed implementation

1. Publish a versioned index for orchestrator, Harness, Tool Gateway, independent verification, sandbox, skills and Watchdog sections.
2. Describe the proposed three-plane architecture and how one shared Harness hosts logical role profiles.
3. Label all workflows and examples as proposed; no actual MayanForge runs or deployments are asserted.
4. Link the corresponding Layer 2 contracts and M0 blueprint gates.
5. Explain where stage-specific evaluation differs from final independent verification.

### Acceptance criteria — Given / When / Then

1. Given a new engineer, when reading the guide, then they can locate the contract and ownership section for each execution component.
2. Given a role family, when described, then it is a Harness profile rather than a required microservice.
3. Given a quality claim, when referenced, then independent evidence is required.
4. Given a workflow example, when displayed, then it is clearly marked illustrative until implemented.
5. Given a document revision, when indexed, then the version is explicit.

### Concrete deliverables and proposed repository locations

- `architecture/l3/README.md; architecture/l3/role-workflow-index.md`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** None for initial architecture index; MF-53 and MF-56 subsequently enrich the guide without blocking its baseline.
- **Scope/ownership rule:** Distinguish logical agent roles from independently deployed services; label all proposed workflow examples and evidence states.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Validate role/workflow links, source versions, and independent verification terminology with L3 reviewer.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Architecture index, terminology review, source link check, baseline approval.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L3-0103`; milestone: `M0`; existing Jira issue: `MF-52`.
- Source: `MayanForge_Layer3_Architecture_v3_3.html`; section: `Executive summary and reading guide`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-53 — [MF-L3-0107] Layer 3 source reconciliation and scope

**Technical context:** Layer 3 · design delta · workflow inventory  
**Original Jira summary:** Source reconciliation and scope  
**Original epic:** M0-E01 — Source reconciliation & coverage  
**Source:** MayanForge_Layer3_Architecture_v3_3.html · Source reconciliation and scope  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Reconcile Layer 3 v3.3 with Layer 2 v2.3 and UI v7 without losing earlier execution invariants.

### Proposed implementation

1. Document retained invariants: durable orchestration, sandbox isolation, policy gateway, immutable evidence and independent evaluation.
2. Enumerate v3.3 workflow kinds, including stage draft generation, Build execute/repair, verification run and assistant requests.
3. Check v3.2 human task review, verification sign-off and human-triggered repair against current contracts.
4. Identify unresolved topology, tenancy and adapter choices and assign owners.
5. Create a versioned scope statement separating M0 design, M1 foundation and later Harness/verification implementation.

### Acceptance criteria — Given / When / Then

1. Given a workflow kind, when traced, then its Layer 2 initiation path is identified.
2. Given a historical invariant, when reconciled, then it remains retained or has an explicit ADR explaining a change.
3. Given a v3.3 addition, when recorded, then its event/approval impact is listed.
4. Given an unresolved infrastructure choice, when documented, then no implementation-ready claim is made.
5. Given a feature scheduled after M0, when listed, then its earlier safety prerequisites are not silently deferred.

### Concrete deliverables and proposed repository locations

- `reconciliation/l3-v3.3-workflow-inventory.csv; reconciliation/l3-delta-matrix.md`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-52 for Layer 3 source index; MF-49 for Layer 2 reconciliation baseline. MF-54 consumes this scope, not vice versa.
- **Scope/ownership rule:** Enumerate workflow kinds with initiator, owner, approval gate, output event and maturity status; record v3.2→v3.3 changes and unresolved topology decisions.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Check each named stage-draft workflow and human gate against source sections; sample mismatch must surface as unresolved rather than silently resolved.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Workflow inventory, version-delta matrix, open decision log, reviewer sign-off.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L3-0107`; milestone: `M0`; existing Jira issue: `MF-53`.
- Source: `MayanForge_Layer3_Architecture_v3_3.html`; section: `Source reconciliation and scope`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-54 — [MF-L3-0113] Cross-layer authority and trust-boundary matrix

**Technical context:** Layer 3 · threat modeling · policy boundary  
**Original Jira summary:** Authority, state ownership and trust boundaries  
**Original epic:** M0-E03 — Governance & acceptance  
**Source:** MayanForge_Layer3_Architecture_v3_3.html · Authority, state ownership and trust boundaries  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Ensure business, workflow, tool and verification authorities are independently enforced.

### Proposed implementation

1. Produce a responsibility matrix for business records, Temporal workflow state, agent attempts, tool actions, human decisions, candidate artifacts and verdicts.
2. Model trust boundaries A–E: browser/external callers, untrusted model content, sandbox execution, independent deployment/quality gates and authenticated Layer 2 decisions.
3. Specify allowed command/event flows and prohibit bypass of the Tool Gateway or self-approval by agents.
4. Define the tenant and project identity propagation and independent reauthorization requirements.
5. Write threat scenarios for forged approval, stale digest, cross-tenant event, tool-policy bypass and fabricated PASS.

### Acceptance criteria — Given / When / Then

1. Given an agent-generated approval, when reviewed, then it cannot satisfy a human gate.
2. Given untrusted repository content, when passed to an agent, then it is never treated as tool authorization.
3. Given a tool request, when mapped, then enforcement occurs at the Tool Gateway.
4. Given a changed candidate digest, when verified, then prior candidate-bound verdicts cannot be reused.
5. Given a cross-tenant event, when validated, then it is rejected or quarantined by contract.

### Concrete deliverables and proposed repository locations

- `security/authority-matrix.md; security/trust-boundaries.md; security/threat-scenarios.yaml`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-45, MF-46, MF-53.
- **Scope/ownership rule:** Identify policy decision/enforcement points, actor/tenant propagation, human versus agent authority and candidate-bound evidence; deny-by-default is a proposed normative rule requiring approval.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Review negative scenarios for forged approval, cross-tenant event, prompt injection, gateway bypass and stale verification evidence.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Trust-boundary diagram, threat matrix, negative security fixtures, security sign-off.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L3-0113`; milestone: `M0`; existing Jira issue: `MF-54`.
- Source: `MayanForge_Layer3_Architecture_v3_3.html`; section: `Authority, state ownership and trust boundaries`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-55 — [MF-L3-0131] Stage-specific Harness evaluation contract baseline

**Technical context:** Layer 3 · stage evaluation profiles · versioned evidence  
**Original Jira summary:** 04 · Proposed versioned contracts  
**Original epic:** M0-E02 — Canonical contracts & ADRs  
**Source:** MayanForge_Layer3_Architecture_v3_3.html · Stage-Specific Harness Evaluation: six SDLC profiles  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Define stage-specific progress and evaluation contracts without implementing separate Harness engines.

### Proposed implementation

1. Locate and inventory the Stage-Specific Harness Evaluation section; distinguish evaluation profiles from independent final Verification.
2. Specify StageEvaluationProfile and StageEvaluationResult references, pinning rules and task-attempt association.
3. Document the six SDLC stages and which progress/evidence signals each profile requires.
4. Define PASS/FAIL/INCONCLUSIVE/BLOCKED semantics and missing-evidence behavior.
5. Provide fixture examples for profile-version mismatch, absent evidence and changed subject digest.

### Acceptance criteria — Given / When / Then

1. Given a task attempt, when evaluated, then its pinned evaluation-profile digest is recorded.
2. Given missing required evidence, when evaluated, then PASS is prohibited.
3. Given a profile change mid-attempt, when checked, then the existing attempt retains its pinned version.
4. Given a changed candidate digest, when reevaluated, then prior evidence is not silently carried forward.
5. Given a stage-level evaluation, when reported, then it is not represented as independent release verification.

### Concrete deliverables and proposed repository locations

- `contracts/evaluation/evaluation-profile.schema.json; contracts/evaluation/evidence-binding.md; fixtures/evaluation/{valid,stale,forged}/*.json`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-54 for trust boundaries and MF-47 for protocol semantics; MF-55 defines evaluation-profile constraints consumed by MF-56. Unresolved choices are registered in MF-57.
- **Scope/ownership rule:** Pin profile version and evaluation attempt to candidate/artifact digest, execution ID and evidence IDs. Distinguish stage Harness assessment from independent verifier verdict; no self-reported PASS promotion.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Schema-check valid and stale-profile/candidate fixtures; independently verify expected rejection and record reviewer evidence.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Profile/result schema drafts, six-stage coverage matrix, negative fixtures, evaluation owner review.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L3-0131`; milestone: `M0`; existing Jira issue: `MF-55`.
- Source: `MayanForge_Layer3_Architecture_v3_3.html`; section: `Stage-Specific Harness Evaluation: six SDLC profiles`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-56 — [MF-L3-0149] Normative Layer 3 execution schemas and fixtures

**Technical context:** Layer 3 · ExecutionCommand · TaskContract · events  
**Original Jira summary:** Normative cross-layer execution contracts  
**Original epic:** M0-E02 — Canonical contracts & ADRs  
**Source:** MayanForge_Layer3_Architecture_v3_3.html · Normative cross-layer execution contracts  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Freeze the first version of the Layer 3 wire contracts used by the M1 skeleton.

### Proposed implementation

1. Define versioned schemas for ExecutionCommand, StageOutputProduced, TaskContract, TaskReviewDecision, RepairRequest, CandidateManifest, ToolActionRequest and TaskResult.
2. Include schemaVersion, IDs, subject digests, policy/skill/profile locks, correlation, budgets, tool scopes and immutable evidence references as applicable.
3. Normalize camelCase JSON wire fields and shared uppercase status/verdict vocabulary with Layer 2.
4. Define idempotency and stable action IDs for external effects; avoid claiming blanket exactly-once delivery.
5. Create positive and malformed/expired/unauthorized/stale/cross-tenant fixtures and compatibility checks.

### Acceptance criteria — Given / When / Then

1. Given a valid ExecutionCommand, when validated, then mandatory identity, version, budget and subject fields are present.
2. Given an unknown workflowKind or schema version, when validated, then it is rejected or explicitly handled by version policy.
3. Given a duplicate external-effect action ID, when modeled, then replay is reconciled rather than executed twice blindly.
4. Given a TaskResult, when checked, then evidence references and attempt identity are preserved.
5. Given Layer 2 and Layer 3 contract suites, when run, then field casing and enum values agree.

### Concrete deliverables and proposed repository locations

- `contracts/execution/schema-index.md; contracts/execution/schemas/*.json; fixtures/execution/{valid,invalid}/*.json; scripts/validate_execution_contracts`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-47 for protocol semantics, MF-54 for authority and MF-55 for evaluation profile constraints; MF-56 owns canonical schema files and fixtures.
- **Scope/ownership rule:** MF-56 is single canonical owner for execution schema artifacts. Define schema IDs, semantic version rules, required/optional fields, enum registry, identity and digest binding, compatibility/migration and unknown-field handling; choices requiring approval remain open ADRs.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Run deterministic schema checks on success, missing identity, invalid enum, stale digest, duplicate/replay and incompatible-version fixtures; include commands and outputs.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

JSON Schemas, golden fixtures, compatibility test plan, both-layer owner approval.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L3-0149`; milestone: `M0`; existing Jira issue: `MF-56`.
- Source: `MayanForge_Layer3_Architecture_v3_3.html`; section: `Normative cross-layer execution contracts`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-57 — [MF-L3-0158] Layer 3 ADR and unresolved-risk register

**Technical context:** Layer 3 · ADR-001–ADR-025 · architecture governance  
**Original Jira summary:** Architecture decisions and unresolved questions  
**Original epic:** M0-E02 — Canonical contracts & ADRs  
**Source:** MayanForge_Layer3_Architecture_v3_3.html · Architecture decisions and unresolved questions  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Convert Layer 3 candidate decisions and unresolved questions into an explicit approved-or-blocked decision baseline.

### Proposed implementation

1. Import ADR-001–ADR-025 from Layer 3 v3.3, retaining the source statuses (retained, candidate, new proposal, updated).
2. Record decision owners and consequences for Temporal authority, sandbox threat model, skill locking, verification isolation, human gates and repair.
3. Resolve or timebox the choices explicitly left open: tenant isolation, Temporal deployment, tool API/adapter, credential brokerage, signing, retention and evaluation thresholds.
4. Map decisions to Layer 2 APIs, Layer 3 schemas and M1/M2 dependencies.
5. Publish a risk register with mitigations and acceptance conditions; do not mark proposed ADRs approved by drafting them.

### Acceptance criteria — Given / When / Then

1. Given a source ADR, when imported, then its original identifier and status are preserved.
2. Given an unresolved trust boundary, when assessed, then a responsible decision maker and blocker status are recorded.
3. Given a new decision that conflicts with Layer 2, when reviewed, then cross-layer sign-off is required.
4. Given an approved ADR, when traced, then impacted schemas and tests are linked.
5. Given an unapproved design proposal, when published, then it remains visibly proposed.

### Concrete deliverables and proposed repository locations

- `decisions/ADR-index.md; decisions/l3-open-questions.md; decisions/l3-risk-register.md`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-53 and MF-54 for Layer 3 scope and trust boundaries; MF-48 establishes the shared ADR index.
- **Scope/ownership rule:** No separate cross-layer ADR status ledger. Track Temporal topology, sandbox isolation, gateway enforcement, evaluator independence, budget enforcement with owner, options, risk and blocking milestone.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Reconcile sample cross-layer decision against MF-48; ensure no duplicate ID, conflicting status or unowned M1 blocker.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

ADR ledger, risk register, dependency links, signed decision log.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L3-0158`; milestone: `M0`; existing Jira issue: `MF-57`.
- Source: `MayanForge_Layer3_Architecture_v3_3.html`; section: `Architecture decisions and unresolved questions`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## MF-58 — [MF-L3-0159] Layer 3 references and evidence-status declaration

**Technical context:** Layer 3 · reference catalog · verification provenance  
**Original Jira summary:** Reference links and verification status  
**Original epic:** M0-E03 — Governance & acceptance  
**Source:** MayanForge_Layer3_Architecture_v3_3.html · Reference links and verification status  
**Classification:** Source heading · Heading-level inventory; detailed requirements require section-level implementation review

### Objective

Prevent external technology references or design diagrams from being mistaken for implementation evidence.

### Proposed implementation

1. Catalog cited references to Temporal, Spring AI, OPA, OpenTelemetry, Agent Skills and OWASP as supporting documentation only.
2. Add an explicit status legend for proposed, contracted, implemented, verified, deferred and blocked.
3. Specify the minimum provenance for future evidence: execution/attempt ID, artifact digest, evaluator identity, timestamp, test run and immutable evidence URI.
4. Distinguish agent self-report, Harness stage evaluation, independent verification and human sign-off.
5. Include a document banner stating no actual MayanForge execution/security test/deployment is demonstrated by these architecture files.

### Acceptance criteria — Given / When / Then

1. Given a vendor documentation link, when reviewed, then it is not counted as a MayanForge passing test.
2. Given a proposed workflow diagram, when published, then it is marked not deployed.
3. Given a future PASS claim, when audited, then independent evaluator and candidate-bound evidence are required.
4. Given an agent self-report without test receipts, when assessed, then verification remains unproven.
5. Given a broken reference, when checked, then it is flagged rather than silently removed.

### Concrete deliverables and proposed repository locations

- `architecture/l3/reference-manifest.yaml; evidence/m0-source-status.md; scripts/check_reference_manifest`
- Every deliverable must have an owner role, revision identifier, source references and a review status. Repository paths are proposals until approved.

### Story-specific boundaries and decision ownership

- **Proposed dependencies:** MF-52, MF-53, MF-56, MF-57.
- **Scope/ownership rule:** Reference manifest identifies exact version and source location, verification status, and last checked date; broken links and unknown claims remain visible.
- **Decision authority:** relevant Layer 2, Layer 3, governance and/or security reviewer as applicable; named accountable approver **TBD**. Unresolved decisions must reference the shared MF-48/MF-57 ADR index.

### Acceptance-to-evidence verification procedure

- Run link/manifest checker with broken-link fixture and manually verify proposed-vs-implemented declarations.
- Record a criterion-to-evidence matrix for all acceptance scenarios already listed above, including expected and actual results; mark unexecuted checks **Not run**, not **Passed**.
- **Failure condition:** Missing or contradictory source trace, absent required deliverable, unreviewed normative decision, or failed required validation prevents story acceptance.

### Validation evidence

Reference inventory, evidence provenance template, broken-link report, verification reviewer sign-off.

### Dependencies and implementation constraints

Use the **story-specific dependencies and boundaries above**. Preserve registry IDs and source versions. Do not implement later-milestone runtime services as part of M0. Record open architectural choices as proposed or blocked; do not fabricate tests, approvals or execution evidence.

### Traceability and review

- Capability ID: `MF-L3-0159`; milestone: `M0`; existing Jira issue: `MF-58`.
- Source: `MayanForge_Layer3_Architecture_v3_3.html`; section: `Reference links and verification status`.
- Review required: confirm deliverable boundaries, normative contract details, and accountable approver before implementation or marking Done.

---

## M0 proposed review and dependency sequence

1. **Baseline and authority:** MF-44, MF-45, MF-46, MF-49, MF-52, MF-53, MF-54. Outputs: source/version manifest, reconciliation matrices, ownership and threat boundaries.
2. **ADRs and contract authority:** MF-48 and MF-57 establish one decision index; MF-47 reconciles protocol behavior; MF-56 authors canonical execution schemas; MF-55 authors evaluation binding; MF-50 authors HTTP surface and fixtures.
3. **Traceability and change governance:** MF-40 validates registry structure (not completeness); MF-41 links capability IDs to contracts; MF-42 and MF-43 define change history and impact review; MF-58 checks L3 references and evidence status.
4. **Exit review:** MF-51 verifies M1 readiness with criterion-to-evidence links and records unresolved blockers.

**Approval checklist:** No duplicate contract schema owners; all 19 original MF IDs preserved; each artifact versioned; every normative decision approved or marked blocked; test commands/fixtures provided where appropriate; all Given/When/Then criteria mapped to evidence; M0/M1 boundary respected; Jira update requires separate explicit approval.
