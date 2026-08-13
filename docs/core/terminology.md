# Canonical PM Terminology

> Status: M1 / PM-P01 / PLAN01 — Ready for re-review
>
> Purpose: Define the canonical vocabulary used by the AI-assisted Project Management Skills. All PM skills, adapters, examples, and project artifacts should use these terms consistently unless a later approved contract version explicitly changes them.

## 1. Terminology principles

1. A term has one canonical meaning inside the PM skill suite.
2. Similar concepts must remain distinct when they drive different project-management decisions.
3. Project-management semantics are independent of any storage tool such as GitHub Projects, Plane, Jira, or Markdown.
4. Discovery does not imply acceptance into scope.
5. Urgency does not imply scope membership.
6. Package identity does not encode execution order.
7. Child resources should not duplicate ownership metadata that can be derived from their parent unless an adapter requires a denormalized projection.
8. A domain resource name and a workflow status must not share ambiguous semantics.
9. Workflow state changes are governed actions; UI capability alone does not grant transition authority.
10. Resource creation is incomplete until required structural relationships are established and verified.

---

## 2. Structural resources

### Project

The top-level body of work being managed.

A Project provides the durable context in which Milestones, Packages, Planning Items, Discoveries, Requests, Spikes, and Project Backlog items exist.

A Project is not itself an execution status or a delivery boundary.

### Milestone

A bounded delivery objective inside a Project.

A Milestone defines a meaningful outcome to be achieved and is governed by a **Goal**, **Scope**, and **Exit Criteria**.

A Milestone contains accepted Packages. A Milestone may change only through an explicit project-management decision when the change alters its baseline scope.

A Milestone is not merely a date bucket or a label for work discovered during its execution.

### Package

A coherent, independently trackable unit of accepted work that contributes to a Milestone or another explicitly approved work stream.

A Package should have a stable identity, a goal or deliverable, and enough boundaries that its progress can be tracked independently.

Examples:

- `PM-P01 — Core Model & Contracts`
- `M7-ARCH01 — Campaign identity ownership correction`

A Package may own multiple Planning Items.

### Planning Item

A child work item that describes a concrete piece of planning or implementation detail required to complete a Package.

A Planning Item may have its own status, acceptance criteria, blocker state, and implementation details when independent tracking is useful.

A Planning Item is not automatically a separate Milestone scope item; its scope membership is inherited through its parent Package unless the project model explicitly states otherwise.

Every Planning Item must have exactly one **Parent Package**. A Planning Item without a Parent Package is an orphaned / invalid state and must not be treated as successfully created.

### Checklist Item

A lightweight, non-independent execution step contained inside a Package or Planning Item.

A Checklist Item should be used when the step does not need its own lifecycle, discussion, blocker state, or acceptance record.

Use a Planning Item instead when independent tracking is useful.

---

## 3. Intake and emerging-work resources

### Discovery

A structured record of something newly observed, learned, or suspected during planning, execution, review, or incident handling that may affect project work.

A Discovery captures evidence and context before deciding what should be done about it.

A Discovery is **not accepted work** and does **not** by itself change a Milestone, Package, priority, or schedule.

Canonical rule:

> DISCOVER ≠ ACCEPT

A Discovery normally enters triage.

### Request

A newly proposed need, idea, feature, change, or one-off piece of work that has not yet been accepted into a Milestone or Package.

A Request may originate from a user, stakeholder, agent, review, or another system.

A Request can later be accepted, retained in the Project Backlog, rejected, or converted into another work resource after triage.

### Project Backlog

The retained pool of potentially valuable work that is not currently committed to an active Milestone or other explicitly approved execution scope.

Project Backlog is a **scope / commitment concept**, not a workflow Status.

A Project Backlog item may later be prioritized and accepted into a Milestone or another work stream.

Canonical distinction:

> PROJECT BACKLOG ≠ STATUS: BACKLOG

### Spike

A bounded investigation created when there is not enough evidence to make a responsible implementation or scope decision.

A Spike must have:

- a specific question,
- an investigation boundary,
- expected outputs,
- a completion condition.

A Spike does not implicitly authorize production implementation.

Canonical rule:

> UNKNOWN ≠ IMPLEMENT NOW

---

## 4. Decision terms

### Scope Change

A decision that modifies the accepted scope of a Milestone because newly discovered work is necessary for that Milestone to achieve its existing Goal or Exit Criteria, or because the user explicitly approves a revised Milestone objective.

The defining question is:

> If this work is not done, can the Milestone still satisfy its approved Goal and Exit Criteria?

If the answer is no and evidence is sufficient, the work is a candidate Scope Change.

A Scope Change changes the Milestone baseline and therefore is subject to the configured approval policy.

### Interruption

A decision to pause or deprioritize current work so that another urgent item can be handled now, without declaring that urgent item part of the current Milestone's scope.

Typical effects:

- current work may be paused,
- schedule may be affected,
- Milestone scope remains unchanged.

Canonical rule:

> URGENT ≠ IN SCOPE

### Reject

A decision that no tracked work should be created or retained for the Discovery or Request in its current form.

Typical reasons include:

- duplicate,
- already resolved,
- already covered by existing accepted work,
- incorrect observation,
- no meaningful project impact,
- intentionally not pursued.

Reject must not be used merely because work is not urgent; valuable non-urgent work belongs in the Project Backlog.

---

## 5. Planning and delivery terms

### Goal

The outcome a Project, Milestone, Package, or Spike is intended to achieve.

A Goal describes **why the work exists and what outcome is desired**, not the detailed implementation steps.

### Scope

The boundary of work that is currently accepted as part of a Project, Milestone, or Package.

Scope answers what is included and, where useful, what is explicitly excluded.

Scope is changed through an explicit decision, not silently because new work was discovered.

### Exit Criteria

The conditions that must be true before a Milestone can be considered successfully complete.

Exit Criteria apply to the Milestone as a delivery boundary.

They answer:

> Can this Milestone close?

### Acceptance Criteria

The verifiable conditions that must be satisfied for a Package, Planning Item, or other deliverable to be accepted as complete.

They answer:

> Is this specific work item done correctly?

Canonical distinction:

> Exit Criteria govern Milestone closure; Acceptance Criteria govern work-item acceptance.

### Review Result

The explicit outcome of a review against Acceptance Criteria or another defined review target.

A Review Result describes the reviewer recommendation; it is **not** a workflow Status.

Canonical review outcomes are expected to include at least:

- `ACCEPT`
- `REVISE`

A `REVISE` result normally returns the work item from `Review` to `In Progress`; it does not require a separate `Revision Required` workflow Status.

The full review response structure, finding format, severity rules, re-review rules, and transition matrix are defined by the Workflow / Review Contract rather than by this terminology document.

---

## 6. Scheduling and execution dimensions

### Status

The current workflow state of a tracked work item.

Canonical M1 statuses:

- **Backlog** — a tracked work item is not currently ready for execution. This Status does not indicate whether the item is inside or outside a Milestone scope.
- **Ready** — accepted and actionable now.
- **In Progress** — actively being worked on.
- **Blocked** — cannot proceed because of an unresolved dependency, decision, incident, or other constraint.
- **Review** — implementation or drafting is complete and awaiting validation or acceptance.
- **Done** — acceptance criteria have been satisfied and the item is complete.

Status describes workflow state. It does not define scope, commitment, priority, or permission to change that state.

In particular, a Package already committed to a Milestone may legitimately have `Status = Backlog` while waiting for execution.

### Action Owner

The role expected to take the next meaningful action while a work item is in a given Status.

Action Owner answers:

> Who is expected to act now?

Examples include Implementer, Reviewer, Approver, and PM / Governor.

Action Owner is a workflow responsibility concept. It is not necessarily the same as the GitHub assignee, issue creator, resource owner, or person currently viewing the item.

The authoritative mapping from Status to Action Owner is defined by the Workflow / Review Contract.

### Transition Authority

The role or policy authority permitted to perform a specific workflow Status transition.

Transition Authority answers:

> Who is allowed to move this work item from the current Status to the proposed next Status?

A user or Agent being technically able to edit a Project field does not imply Transition Authority.

Examples:

- `In Progress → Review` is normally initiated by the Implementer.
- `Review → In Progress` after `REVISE` is normally initiated by the Reviewer.
- `Review → Done` after `ACCEPT` is performed only by the authorized Reviewer / Approver path defined by policy.

The complete transition matrix is defined by the Workflow / Review Contract.

### Priority

The relative importance used to decide which eligible work should be considered before other eligible work.

Canonical Project field values may use:

- `P0 — Critical`
- `P1 — High`
- `P2 — Normal`
- `P3 — Low`

Priority does not encode planned execution order.

### Urgency

The time sensitivity or consequence of delaying a Discovery, Request, incident, or work item.

Urgency is an input to triage and prioritization.

A highly urgent item can still be outside the current Milestone scope and therefore become an Interruption rather than a Scope Change.

### Sequence

The planned relative execution position among peer Packages or other explicitly ordered resources.

Sequence is mutable planning metadata.

Package identity must remain stable when Sequence changes.

Example:

```text
M7-P05     Sequence 50
M7-ARCH01  Sequence 55
M7-P06     Sequence 60
```

The insertion of `M7-ARCH01` does not rename `M7-P06`.

Canonical rule:

> PACKAGE ID ≠ EXECUTION ORDER

---

## 7. Relationship terms

### Parent / Child

An ownership or decomposition relationship in which a higher-level resource contains lower-level work.

Canonical structural relationship:

```text
Project
└─ Milestone
   └─ Package
      └─ Planning Item
         └─ Checklist Item
```

A Discovery is not automatically a child of the Milestone in which it was discovered. Its location is recorded as context until triage decides the resulting work relationship.

### Parent Package

The single Package that structurally owns a Planning Item.

For `Work Type = Planning`, Parent Package is mandatory and unique.

Canonical invariant:

> EVERY PLANNING ITEM HAS EXACTLY ONE PARENT PACKAGE

A Planning Item without a Parent Package is an orphaned / incomplete creation state. A Planning Item with multiple competing parent ownership records is invalid.

The Parent Package is the authoritative source from which Milestone membership is normally derived; the Planning Item should not maintain an independent conflicting Milestone ownership record.

### Discovered In

Context identifying where a Discovery was encountered, for example:

- Project,
- Milestone,
- Package,
- Planning Item,
- execution or review activity.

`Discovered In` records provenance. It does not define scope ownership.

Canonical rule:

> FOUND DURING M7 ≠ BELONGS TO M7

### Dependency

A relationship in which one work item affects another item's ability or order to proceed.

Typical dependency semantics include:

- blocks,
- blocked by,
- requires,
- prerequisite for.

Dependency is different from Parent / Child: a child belongs to a structural decomposition, while a dependency describes execution constraint or ordering.

### Ownership

The structural relationship that determines where a resource belongs in the canonical PM model.

Ownership should have one authoritative source of truth. Derived metadata should not create contradictory ownership records.

---

## 8. Approval and decision-state terms

### Recommendation

An Agent's proposed project-management decision based on available evidence.

A Recommendation is not an approved decision unless policy explicitly permits autonomous execution for that decision type.

### Approval

Explicit authorization required by policy before a governed project mutation is performed.

Typical approval states may include:

- `pending`
- `approved`
- `rejected`
- `deferred`

### Human Approval Gate

The policy boundary that prevents an Agent from performing a governed project mutation until the applicable approval requirement has been satisfied.

This terminology document defines what an Approval and Human Approval Gate mean. It does **not** define which actions require approval.

The authoritative approval matrix and autonomous-action policy are defined by `docs/core/approval-policy.md` when PM-P01-PLAN05 is completed.

---

## 9. Canonical distinctions

The following distinctions are normative:

| Concept A | Concept B | Canonical distinction |
|---|---|---|
| Milestone | Package | Milestone is a delivery boundary; Package is a trackable unit of accepted work inside that boundary. |
| Package | Planning Item | Package is the stable work unit; Planning Item is its independently trackable decomposition. |
| Planning Item | Parent Package | Every Planning Item must belong to exactly one Parent Package; Parent Package is its structural ownership link. |
| Planning Item | Checklist Item | Planning has an independent lifecycle when needed; Checklist is lightweight execution detail. |
| Discovery | Accepted Work | Discovery captures information; triage decides whether work is accepted. |
| Request | Project Backlog | Request is incoming proposed work; Project Backlog is retained work not committed to active Milestone scope. |
| Project Backlog | Status: Backlog | Project Backlog describes commitment/scope; Status: Backlog describes workflow readiness. |
| Scope Change | Interruption | Scope Change modifies current Milestone scope; Interruption changes immediate execution priority while scope stays unchanged. |
| Scope Change | Spike | Scope Change is a decision with sufficient evidence; Spike gathers evidence when the decision is not yet justified. |
| Project Backlog | Reject | Project Backlog retains potentially valuable work; Reject intentionally stops tracking it as actionable work. |
| Status | Action Owner | Status describes workflow state; Action Owner identifies the role expected to act next in that state. |
| Action Owner | Transition Authority | Action Owner identifies who should act; Transition Authority identifies who may perform a specific Status transition. |
| Status | Priority | Status describes workflow state; Priority describes relative importance. |
| Priority | Urgency | Priority is comparative ordering; Urgency is time sensitivity / consequence of delay. |
| Priority | Sequence | Priority influences what should be preferred; Sequence records planned relative execution order. |
| Review Result | Status | Review Result is the outcome of a review; Status is the current workflow state. |
| Exit Criteria | Acceptance Criteria | Exit Criteria close a Milestone; Acceptance Criteria validate a specific work item. |
| Parent / Child | Dependency | Parent / Child is structural decomposition; dependency is an execution constraint. |
| Discovered In | Ownership | Discovered In records provenance; ownership determines where accepted work belongs. |
| Package ID | Sequence | ID is stable identity; Sequence can change without renaming the Package. |

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
8. A Scope Change must not be silently introduced by an Agent.
9. An Interruption must not be represented as a Scope Change merely because it is urgent.
10. A Planning Item should not duplicate Milestone ownership when that ownership is inherited from its Parent Package.
11. Recommendation and approval are distinct states.
12. Review Result and workflow Status are distinct states.
13. Status, Priority, Urgency, Sequence, and Scope are separate dimensions.
14. A Status transition must follow the applicable Transition Authority; technical edit access does not grant workflow authority.
15. A child resource must not be reported as successfully created until required parent relationships and required project metadata have been established or an explicit partial-failure state has been reported.

---

## 11. Storage-tool mapping note

The canonical vocabulary describes the PM domain, not a particular product.

For the initial GitHub implementation, expected mappings include:

| Canonical concept | Initial GitHub representation |
|---|---|
| Project | GitHub Project |
| Milestone | GitHub Milestone |
| Package | GitHub Issue + `Work Type = Package` |
| Planning Item | GitHub Sub-issue + `Work Type = Planning` |
| Checklist Item | Markdown task item |
| Project Backlog | Tracked Issue/Request outside active Milestone scope, represented according to adapter policy |
| Status | GitHub Project `Status` field |
| Status: Backlog | GitHub Project `Status = Backlog` |
| Action Owner | Workflow contract / adapter projection; may be derived rather than stored as a Project field |
| Transition Authority | Workflow contract / policy; not inferred from GitHub UI edit permission |
| Priority | GitHub Project `Priority` field |
| Sequence | GitHub Project `Sequence` number field |
| Parent Package | GitHub Parent issue relationship for a Planning sub-issue |
| Parent / Child | GitHub Parent issue / Sub-issue relationship |

These mappings are adapter decisions. They do not redefine the canonical PM meanings above.

---

## 12. Change control for terminology

Changes to this terminology must:

1. identify the term being changed,
2. explain the ambiguity or deficiency,
3. assess compatibility impact on existing PM skills and adapters,
4. update related contracts and examples together,
5. preserve project history rather than silently reinterpret old records.

This document is the terminology source of truth for M1 until superseded by an approved version.