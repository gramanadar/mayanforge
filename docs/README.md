# MayanForge — Documentation and Source of Truth

**Purpose:** This directory contains the version-controlled source material used to plan, review, and implement MayanForge. It is the reference point for architecture reconciliation and M0 contract work, **not** evidence that the platform is implemented or that every proposed design is approved.

## Start here

1. Read the [Incremental Implementation Blueprint v1.2](governance/MayanForge_Incremental_Implementation_Blueprint_v1_2.html) to understand the M0–M10 sequence and milestone gates.
2. Consult the [Master Capability Registry v1.2](../registry/MayanForge_Registry_v1_2_download.json) for source-indexed capabilities and proposed milestone mappings. The registry is a **section-level starting inventory**, not a certified complete atomic backlog.
3. Read the architecture documents relevant to the story: [Layer 1](architecture/layer1/), [Layer 2](architecture/layer2/), and [Layer 3](architecture/layer3/).
4. Use the [reviewed M0 story specifications v3](governance/MayanForge_M0_19_Story_Enrichment_Reviewed_v3.md) alongside the **live Jira issue**. The document contains proposed implementation guidance; the live issue tracks execution and review.
5. Record decisions and supporting evidence in the repository before claiming an acceptance criterion is satisfied.

## Document map

| Location | Contents | Intended use |
| --- | --- | --- |
| `architecture/layer1/` | `MayanForge_Layer1_UI_Architecture_Diagram.html`, `mayanforge_UI_v7.html` | UI structure and prototype reference; not proof of backend integration |
| `architecture/layer2/` | `MayanForge_Layer2_Service_Architecture_v2_3.html`, `MayanForge_Layer2_Service_Architecture_Diagram.html` | Proposed Layer 2 service, business authority, approvals, and API design |
| `architecture/layer3/` | `MayanForge_Layer3_Architecture_v3_3.html`, `MayanForge_Layer3_Architecture_Diagram.html` | Proposed durable orchestration, agent harness, tools, and verification design |
| `governance/` | `MayanForge_Incremental_Implementation_Blueprint_v1_2.html`, `MayanForge_Jira_Release_Epic_Story_Task_Visual_Preview_v1_0.html`, `MayanForge_M0_19_Story_Enrichment_Reviewed_v3.md` | Milestone plan, historical Jira planning preview, reviewed M0 specifications |
| `decisions/` | ADR index and decision records, to be produced during M0 | Record proposal, rationale, approver, status, and supersession |
| `../registry/` | `MayanForge_Master_Capability_Registry_v1_2.html`, `MayanForge_Registry_v1_2_download.json` | Capability inventory and traceability inputs |
| `../contracts/` | Layer 2/Layer 3 schemas and fixtures, to be produced during M0 | Versioned contract definitions and validation inputs |
| `../scripts/`, `../tests/`, `../reports/` | Validation tooling, tests, and evidence, to be produced | Reproducible acceptance evidence |

> Paths above describe the **proposed Git repository layout**. Links will work after the named files have been copied into their indicated directories. Empty directories may need a `.gitkeep` file until populated.

## Source authority and conflict resolution

- **Approved ADRs and approved versioned contracts** govern their specific decisions and interfaces. A proposal does not become authoritative merely by appearing in a document or Jira description.
- **Architecture documents** describe the current design baseline for each layer. Record contradictions or gaps; do not silently reconcile them.
- **Capability Registry v1.2** provides traceability inputs. Its records and milestone assignments require M0 reconciliation and validation.
- **Blueprint v1.2** describes the intended milestone sequence and exit gates.
- **Live Jira stories MF-40–MF-58** track M0 work and acceptance. The reviewed v3 enrichment document is a specification reference, not a replacement for Jira or an architecture approval.
- **The Jira visual preview** is a planning snapshot, not the current authoritative issue status.

When two sources disagree, log the discrepancy with both source references, identify the accountable reviewer, and obtain an explicit decision or ADR before changing a normative contract. Do not infer approval from document recency alone.

## Three-layer boundary

| Layer | Primary responsibility | Key constraint |
| --- | --- | --- |
| **Layer 1 — UI** | Next.js experience and user-facing projections | Does not independently establish authoritative execution or approval state |
| **Layer 2 — Service** | Java 21 / Spring Boot business authority, tenant/project access, versioned artifacts, approvals, admission, audit | Does not own durable workflow execution |
| **Layer 3 — Execution** | Java / Temporal orchestration, agent harness, sandbox tools, execution evidence and verification | Does not independently grant business approvals or override Layer 2 authority |

These are **design boundaries**, not a claim that the services are deployed.

## M0 implementation rules

M0 delivers reconciled sources, explicit ownership, canonical versioned commands/events and API schemas, representative valid and invalid fixtures, ADRs, threat-boundary documentation, traceability checks, and M1 entry criteria. Running UI-to-workflow integration and durable runtime behavior belong to later milestones.

For every M0 Jira story:

1. Work on the **existing issue**; do not create duplicate stories.
2. Verify prerequisites and cite the exact source document/version/section.
3. Make changes on a feature branch and submit a pull request.
4. Provide the promised artifacts and a criterion-by-criterion evidence map.
5. Run the available validation commands and record **actual** results; proposed tests are not executed evidence.
6. Mark unresolved design choices as **Proposed**, **Blocked**, or **Deferred** until an authorized reviewer decides.
7. Require independent review before merging or moving Jira to **Done**.

Never report fabricated test passes, approvals, runtime behavior, or independent-verification verdicts. Missing evidence is not a PASS.

## Decision records

Use one canonical ADR index in `decisions/` for cross-layer choices. Suggested statuses: **Proposed**, **Approved**, **Rejected**, **Deferred**, **Blocked**. Each decision should record its ID, scope, alternatives, rationale, affected contracts, approver, date, and superseded decisions. These conventions remain proposals until adopted by the team.

## Versioning and maintenance

- Preserve original source filenames and versions when importing source documents.
- Update this index when a document is added, superseded, or relocated.
- Do not overwrite an older approved baseline without a reviewable change history.
- Link contract changes to affected capabilities, Jira issues, ADRs, fixtures, and validation evidence.

**Initial execution recommendation:** begin with **MF-44** (Layer 2 source baseline) and **MF-52** (Layer 3 source baseline), then proceed through reconciliation, boundary definition, contract specification, governance checks, and **MF-51** M0 exit review according to the actual story dependencies.
