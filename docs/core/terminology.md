# Canonical PM Terminology

> Status: M1 / PM-P01 / PLAN01 — Compatibility revision ready for review
>
> Purpose: Define the canonical vocabulary used by the AI-assisted Project Management Skills. All PM skills, adapters, examples, and project artifacts should use these terms consistently unless a later approved contract version explicitly changes them.

## 1. Terminology principles

1. A term has one canonical meaning inside the PM skill suite.
2. Similar concepts remain distinct when they drive different project-management decisions.
3. PM semantics are independent of storage tools such as GitHub Projects, Plane, Jira, or Markdown.
4. Discovery does not imply acceptance into scope.
5. Urgency does not imply scope membership.
6. Package identity does not encode execution order.
7. Derived metadata must not become a second authoritative ownership / placement record.
8. A domain resource name and a workflow Status must not share ambiguous semantics.
9. Workflow state changes are governed actions; UI capability alone does not grant Transition Authority.
10. Resource creation is incomplete until required structural relationships are established and verified.

---

## 2. Structural resources

### Project

The top-level body of work being managed.

A Project provides the durable context in which Milestones, Packages, Planning Items, Discoveries, Requests, Spikes, and Project Backlog items exist.

A Project may also directly contain an accepted Package whose canonical **Execution Placement** is Project-level approved execution outside every Milestone.

A Project is not itself a workflow Status or a delivery boundary.

### Milestone

A bounded delivery objective inside a Project, governed by **Goal**, **Scope**, and **Exit Criteria**.

A Milestone contains accepted Packages whose Execution Placement points to that Milestone. It may change only through an explicit project-management decision when the change alters its approved baseline.

A Milestone is not merely a date bucket or a label for work discovered during its execution.

### Package

A coherent, independently trackable unit of accepted work.

A Package has a stable identity, a goal or deliverable, and enough boundaries for independent tracking. It may own multiple Planning Items.

Every accepted Package has exactly one canonical **Execution Placement**:

- a specific Milestone; or
- Project-level approved execution outside every Milestone.

Examples:

- `PM-P01 — Core Model & Contracts`
- `M7-ARCH01 — Campaign identity ownership correction`

### Planning Item

A concrete, independently trackable child decomposition of a Package.

A Planning Item may have its own Status, Acceptance Criteria, blocker state, discussion, and implementation details when independent tracking is useful.

Every Planning Item must have exactly one **Parent Package**. It inherits the Parent Package's Execution Placement and Milestone scope membership, if any. A Planning Item without a Parent Package is an orphaned / incomplete creation state and must not be treated as successfully created.

### Checklist Item

A lightweight, non-independent execution step contained inside a Package or Planning Item.

Use a Planning Item instead when the step requires its own lifecycle, discussion, blocker state, or acceptance record.

---

## 3. Intake and emerging-work resources

### Discovery

A structured record of something newly observed, learned, or suspected during planning, execution, review, or incident handling that may affect project work.

A Discovery captures evidence and context before deciding what should be done about it. It is **not accepted work** and does not by itself change a Milestone, Package, priority, or schedule.

> **DISCOVER ≠ ACCEPT**

### Request

A newly proposed need, idea, feature, change, or one-off piece of work that has not yet been accepted into an execution scope.

A Request may later be accepted, retained in Project Backlog, rejected, or converted into another work resource after triage.

### Project Backlog

The retained pool of potentially valuable work that is not currently committed to an active Milestone or another explicitly approved execution scope.

Project Backlog is a **scope / commitment placement concept**, not a workflow Status.

> **PROJECT BACKLOG ≠ STATUS: BACKLOG**

### Spike

A bounded investigation created when there is not enough evidence to make a responsible implementation or scope decision.

A Spike has a specific question, investigation boundary, expected outputs, and completion condition. It does not implicitly authorize production implementation.

> **UNKNOWN ≠ IMPLEMENT NOW**

---

## 4. Decision terms

### Scope Change

A decision that modifies the accepted scope of a Milestone because newly discovered work is necessary for that Milestone to achieve its existing Goal or Exit Criteria, or because an authorized decision revises the Milestone objective.

The defining question is:

> If this work is not done, can the Milestone still satisfy its approved Goal and Exit Criteria?

If the answer is no and evidence is sufficient, the work is a candidate Scope Change.

### Interruption

A decision to pause or deprioritize current work so another urgent item can be handled now **without declaring that urgent item part of the current Milestone's scope**.

An approved Interruption may therefore result in an accepted Package whose Execution Placement is Project-level approved execution.

> **URGENT ≠ IN SCOPE**

### Reject

A decision that no tracked actionable work should be created or retained for a Discovery or Request in its current form.

Reject is appropriate for duplicate, invalid, already resolved, already covered, or intentionally unpursued work. Valuable non-urgent work belongs in Project Backlog rather than being rejected merely for lack of urgency.

---

## 5. Planning and delivery terms

### Goal

The outcome a Project, Milestone, Package, or Spike is intended to achieve. Goal describes why the work exists and what outcome is desired, not detailed implementation steps.

### Scope

The boundary of work currently accepted as part of a Project, Milestone, or Package. Scope changes through an explicit decision, not silently because new work was discovered.

### Exit Criteria

The conditions that must be true before a Milestone can be considered successfully complete.

> Exit Criteria answer: **Can this Milestone close?**

### Acceptance Criteria

The verifiable conditions that must be satisfied for a Package, Planning Item, or other deliverable to be accepted as complete.

> Acceptance Criteria answer: **Is this specific work item done correctly?**

> **Exit Criteria govern Milestone closure; Acceptance Criteria govern work-item acceptance.**

### Review Result

The explicit outcome of a review against Acceptance Criteria or another defined review target. Review Result is a reviewer recommendation; it is **not** a workflow Status.

Canonical outcomes include:

- `ACCEPT`
- `REVISE`

A `REVISE` result normally returns the work item from `Review` to `In Progress`; it does not require a separate `Revision Required` Status.

The detailed review response format, finding rules, re-review rules, and transition matrix belong to `docs/core/workflow-contract.md` / PLAN07.

---

## 6. Scheduling and execution dimensions

### Status

The current workflow state of a tracked work item.

Canonical M1 statuses:

- **Backlog** — tracked but not currently ready for execution.
- **Ready** — accepted and actionable now.
- **In Progress** — actively being worked on.
- **Blocked** — cannot proceed because of an unresolved constraint.
- **Review** — implementation or drafting is complete and awaiting validation or acceptance.
- **Done** — acceptance requirements have been satisfied and the item is complete.

Status does not define scope, commitment, priority, Execution Placement, or permission to change state. A Package committed to a Milestone may legitimately have `Status = Backlog` while waiting for execution.

### Action Owner

The role expected to take the next meaningful action while a work item is in a given Status.

> Action Owner answers: **Who is expected to act now?**

It is not necessarily the same as GitHub assignee, issue creator, structural owner, or person viewing the item. The authoritative Status-to-Action-Owner mapping belongs to PLAN07.

### Transition Authority

The role or policy authority permitted to perform a specific workflow Status transition.

> Transition Authority answers: **Who is allowed to move this item from the current Status to the proposed next Status?**

Technical ability to edit a Project field does not imply Transition Authority. The full transition matrix belongs to PLAN07.

### Priority

Relative importance used to decide which eligible work should be considered before other eligible work. Canonical values may use `P0 Critical`, `P1 High`, `P2 Normal`, and `P3 Low`.

Priority does not encode planned execution order.

### Urgency

Time sensitivity or consequence of delay. Urgency is an input to triage and prioritization; high urgency does not imply Milestone scope membership.

### Sequence

The planned relative execution position among peer Packages or other explicitly ordered resources. Sequence is mutable planning metadata; Package identity remains stable when Sequence changes.

> **PACKAGE ID ≠ EXECUTION ORDER**

---

## 7. Relationship terms

### Parent / Child

A structural decomposition relationship in which a higher-level resource contains lower-level work.

For accepted work, the canonical structure has two valid Package placement paths:

```text
Project
├─ Milestone
│  └─ Package
│     └─ Planning Item
│        └─ Checklist Item
│
└─ Package [Project-level approved execution]
   └─ Planning Item
      └─ Checklist Item
```

The incoming relationship to an accepted Package is its **Execution Placement**. `Package → Planning Item → Checklist Item` is Parent / Child decomposition.

A Discovery is not automatically a child of the Milestone in which it was discovered.

### Execution Placement

The **Package-specific canonical structural placement relation** that answers:

> Under which approved execution scope does this accepted Package belong?

Every accepted Package has exactly one Execution Placement:

```text
Milestone:<id> → Package
```

or:

```text
Project → Package [Project-level approved execution]
```

For a Package:

> **EXECUTION PLACEMENT = AUTHORITATIVE STRUCTURAL PLACEMENT**

Execution Placement is **not a second ownership record**. An adapter must not maintain an independently authoritative `Package Owner`, `Structural Owner`, or equivalent relation that duplicates the same placement.

Planning Items inherit their Parent Package's Execution Placement; they do not own a competing canonical placement record.

Detailed placement integrity, creation verification, Project Backlog mutual exclusion, and storage-adapter rules belong to `docs/core/resource-model.md` / PLAN02.

### Parent Package

The single Package that structurally owns a Planning Item.

For `Work Type = Planning`, Parent Package is mandatory and unique.

> **EVERY PLANNING ITEM HAS EXACTLY ONE PARENT PACKAGE**

A body reference such as `Parent Package: #1` does not replace the actual structural Parent relationship.

### Discovered In

Context identifying where a Discovery was encountered, such as Project, Milestone, Package, Planning Item, execution, review, or incident activity.

`Discovered In` records provenance; it does not define scope ownership or Execution Placement.

> **FOUND DURING MILESTONE ≠ BELONGS TO MILESTONE**

### Dependency

A relationship in which one work item affects another item's ability or order to proceed, such as blocks, blocked by, requires, or prerequisite for.

Dependency is different from Parent / Child and Execution Placement.

### Ownership

The structural relationship that determines where a resource belongs in the canonical PM model.

Ownership has one authoritative source of truth. For an accepted Package, that source is its Execution Placement. Derived metadata must not create contradictory ownership records.

---

## 8. Approval and decision-state terms

### Recommendation

An Agent's proposed project-management decision based on available evidence. A Recommendation is not an approved decision unless policy explicitly permits autonomous execution for that decision type.

### Approval

Explicit authorization required by policy before a governed project mutation is performed.

### Human Approval Gate

The policy boundary that prevents an Agent from performing a governed mutation until the applicable human authorization requirement has been satisfied.

This document defines the terms only. The authoritative approval matrix and autonomous-action policy belong to `docs/core/approval-policy.md` / PLAN05.

---

## 9. Canonical distinctions

| Concept A | Concept B | Canonical distinction |
|---|---|---|
| Milestone | Package | Milestone is a delivery boundary; Package is a trackable accepted work unit. |
| Package | Execution Placement | Package is the work resource; Execution Placement identifies its approved structural placement. |
| Execution Placement | Ownership | For an accepted Package, Execution Placement is its authoritative structural ownership / placement relation, not an additional record. |
| Package | Planning Item | Package is the stable work unit; Planning Item is its independently trackable decomposition. |
| Planning Item | Parent Package | Every Planning Item belongs to exactly one Parent Package. |
| Planning Item | Checklist Item | Planning may have an independent lifecycle; Checklist is lightweight detail. |
| Discovery | Accepted Work | Discovery captures information; triage decides whether work is accepted. |
| Request | Project Backlog | Request is proposed work; Project Backlog retains valuable uncommitted work. |
| Project Backlog | Status: Backlog | Project Backlog describes commitment / placement; Status: Backlog describes workflow readiness. |
| Project Backlog | Execution Placement | Project Backlog is outside approved execution; Execution Placement applies to accepted Packages. |
| Scope Change | Interruption | Scope Change modifies current Milestone scope; Interruption changes immediate execution priority while current Milestone scope stays unchanged. |
| Scope Change | Spike | Scope Change is a decision with sufficient evidence; Spike gathers evidence. |
| Project Backlog | Reject | Project Backlog retains potentially valuable work; Reject intentionally stops actionable tracking. |
| Status | Action Owner | Status describes workflow state; Action Owner identifies who should act next. |
| Action Owner | Transition Authority | Action Owner identifies who should act; Transition Authority identifies who may perform a transition. |
| Status | Priority | Status is lifecycle state; Priority is relative importance. |
| Priority | Urgency | Priority is comparative ordering; Urgency is time sensitivity. |
| Priority | Sequence | Priority influences preference; Sequence records planned order. |
| Review Result | Status | Review Result is review outcome; Status is workflow state. |
| Exit Criteria | Acceptance Criteria | Exit Criteria close a Milestone; Acceptance Criteria validate specific work. |
| Parent / Child | Dependency | Parent / Child is decomposition; Dependency is an execution constraint. |
| Discovered In | Ownership | Discovered In is provenance; Ownership determines structural belonging. |
| Package ID | Sequence | ID is stable identity; Sequence is mutable order. |

---

## 10. Canonical invariants

All PM skills must preserve these invariants unless an approved future contract version changes them:

1. **DISCOVER ≠ ACCEPT**
2. **URGENT ≠ IN SCOPE**
3. **FOUND DURING MILESTONE ≠ BELONGS TO MILESTONE**
4. **UNKNOWN ≠ IMPLEMENT NOW**
5. **PACKAGE ID ≠ EXECUTION ORDER**
6. **PROJECT BACKLOG ≠ STATUS: BACKLOG**
7. **EVERY PLANNING ITEM HAS EXACTLY ONE PARENT PACKAGE**
8. **EVERY ACCEPTED PACKAGE HAS EXACTLY ONE EXECUTION PLACEMENT**
9. **EXECUTION PLACEMENT IS THE AUTHORITATIVE STRUCTURAL PLACEMENT FOR AN ACCEPTED PACKAGE; IT IS NOT A SECOND OWNERSHIP RECORD**
10. A Scope Change must not be silently introduced by an Agent.
11. An Interruption must not be represented as a Scope Change merely because it is urgent.
12. A Planning Item must not duplicate canonical Milestone / Execution Placement ownership inherited from its Parent Package.
13. Recommendation and Approval are distinct.
14. Review Result and workflow Status are distinct.
15. Status, Priority, Urgency, Sequence, Scope, and Execution Placement are separate concerns.
16. A Status transition must follow Transition Authority; technical edit access does not grant workflow authority.
17. A resource must not be reported as successfully created until required structural relationships and required project metadata are established or an explicit partial-failure result is reported.

---

## 11. Storage-tool mapping note

The canonical vocabulary describes the PM domain, not a particular product.

Initial GitHub mappings include:

| Canonical concept | Initial GitHub representation |
|---|---|
| Project | GitHub Project |
| Milestone | GitHub Milestone |
| Package | GitHub Issue + `Work Type = Package` |
| Package Execution Placement = Milestone | Native GitHub Milestone relationship |
| Package Execution Placement = Project-level approved execution | Adapter-defined explicit Project representation; empty Milestone alone is insufficient |
| Planning Item | GitHub Sub-issue + `Work Type = Planning` |
| Parent Package | GitHub Parent issue relationship |
| Checklist Item | Markdown task item |
| Project Backlog | Adapter-defined explicit retained-work placement |
| Status | GitHub Project `Status` field |
| Action Owner | Workflow contract / optional adapter projection |
| Transition Authority | Workflow contract / policy, not GitHub UI permission |
| Priority | GitHub Project `Priority` field |
| Sequence | GitHub Project `Sequence` number field |
| Dependency | GitHub-supported linkage or adapter-managed explicit relation |

These mappings do not redefine the canonical meanings. Detailed mapping integrity belongs to PLAN02 and the future GitHub adapter.

---

## 12. Change control for terminology

Changes to this terminology must:

1. identify the term being changed or added,
2. explain the ambiguity or deficiency,
3. assess compatibility impact on existing PM skills and adapters,
4. update related canonical contracts together when necessary,
5. preserve project history rather than silently reinterpret old accepted records.

This document is the terminology source of truth for M1 until superseded by an approved version.