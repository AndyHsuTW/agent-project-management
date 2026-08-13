# Canonical PM Resource Model

> Status: M1 / PM-P01 / PLAN02 — Ready for re-review
>
> Purpose: Define the canonical project-management resources, their ownership and structural relationships, and the integrity rules that all PM skills and storage adapters must preserve.

This document is the authoritative source for **resource structure and relationship integrity**. It intentionally does not define workflow status transitions, review authority, approval policy, or discovery decision routing; those belong to their dedicated contracts.

## 1. Design principles

1. Resource structure is independent of any storage product.
2. Structural ownership, execution placement, workflow status, scope commitment, provenance, and dependency are separate dimensions.
3. Every structural relationship has one authoritative source of truth.
4. Child resources do not duplicate ownership metadata that can be derived from their parent unless an adapter explicitly maintains a denormalized projection.
5. Creating an entity is not enough: required relationships and required project metadata must also be established and verified.
6. A resource whose required relationships are missing is in an incomplete / invalid creation state and must not be reported as successfully created.
7. Project Backlog membership is an explicit project-level placement relation; it is not inferred from workflow status or missing Milestone metadata.
8. Every accepted Package must have exactly one explicit **Execution Placement**: either one Milestone or Project-level approved execution outside all Milestones.

---

## 2. Canonical resource hierarchy

```mermaid
flowchart TD
    PROJECT[Project]

    PROJECT --> M[Milestone]
    M --> MP[Package]
    MP --> MPI[Planning Item]
    MPI --> MCI[Checklist Item]

    PROJECT --> XP[Package\nProject-level approved execution]
    XP --> XPI[Planning Item]
    XPI --> XCI[Checklist Item]

    PROJECT --> PB[Project Backlog]
    PROJECT --> D[Discovery]
    PROJECT --> R[Request]
    PROJECT --> S[Spike]

    D -. provenance .-> MP
    D -. provenance .-> MPI
    D -. decision may create or place work .-> PB
    D -. decision may create accepted work .-> MP
    D -. decision may create approved project-level work .-> XP
```

The solid hierarchy represents structural ownership / execution placement. The two Package nodes are the **same canonical Package resource type** under two valid execution placements:

1. Milestone-owned execution, or
2. Project-level approved execution outside all Milestones.

Dashed arrows represent contextual or decision-driven relationships, not ownership.

### 2.1 Project

The Project is the durable top-level management boundary.

A Project owns or provides context for:

- Milestones,
- Project-level approved execution Packages,
- Project Backlog membership,
- Packages,
- Planning Items through their Package,
- Discoveries,
- Requests,
- Spikes,
- other project records introduced by approved future contracts.

A Project is not a workflow Status and does not itself imply a delivery deadline.

### 2.2 Milestone

A Milestone is a bounded delivery objective governed by Goal, Scope, and Exit Criteria.

Canonical structural rule:

> A Milestone directly owns accepted Packages, not Planning Items.

Planning Items inherit Milestone membership through their Parent Package.

A Milestone should therefore not maintain a second, independent list of the same Planning Items as direct scope children.

### 2.3 Package

A Package is the stable, independently trackable unit of accepted work.

A Package must have exactly one canonical **Execution Placement**:

- **Milestone placement** — the Package is directly committed to exactly one Milestone; or
- **Project-level approved execution placement** — the Package is approved for execution directly under the Project and intentionally belongs to no Milestone.

A Package may:

- belong to a Milestone,
- be approved for Project-level execution outside all Milestones,
- own zero or more Planning Items,
- participate in dependency relationships with peer work,
- carry its own workflow Status, Priority, Sequence, Acceptance Criteria, and implementation context.

A Package must not simultaneously use both canonical execution placements. Moving a Package between placements is a governed project-management mutation, not a workflow Status change.

Package identity is stable and does not encode execution order.

### 2.4 Planning Item

A Planning Item is an independently trackable child decomposition of a Package.

Canonical invariant:

> **EVERY PLANNING ITEM HAS EXACTLY ONE PARENT PACKAGE.**

A Planning Item:

- must have exactly one Parent Package,
- inherits the execution placement and Milestone scope membership, if any, from that Parent Package,
- may have its own Status, Acceptance Criteria, blocker state, discussion, and deliverable,
- must not maintain a conflicting independent Milestone or execution-placement ownership record.

A Planning Item without a Parent Package is an **orphan** and is not considered successfully created.

### 2.5 Checklist Item

A Checklist Item is lightweight execution detail embedded in a Package or Planning Item.

It should not be promoted to an independent resource unless it requires its own lifecycle, discussion, blocker state, acceptance record, or ownership relationship.

---

## 3. Project Backlog model

### 3.1 Definition

The Project Backlog is the Project-level retained-work collection for potentially valuable work that is **not currently committed to an active Milestone or another explicitly approved execution scope**.

Project Backlog is a scope / commitment placement concept.

It is not a workflow Status.

Canonical rule:

> **PROJECT BACKLOG ≠ STATUS: BACKLOG**

### 3.2 Authoritative membership

Project Backlog membership must be represented by an explicit, authoritative placement relationship that a PM skill or adapter can query deterministically.

The canonical domain model requires an effective relation equivalent to:

```text
Project Backlog membership = TRUE | FALSE
```

The storage adapter may implement this as a field, collection, label, relationship, or another supported representation, but it must provide one unambiguous source of truth.

### 3.3 Forbidden inference rules

A PM skill or adapter must not infer Project Backlog membership solely from either of these conditions:

```text
Status = Backlog
```

or:

```text
Milestone = empty
```

Reasons:

- A Package already committed to a Milestone may legitimately have `Status = Backlog` while waiting for execution.
- A Project-level approved execution Package intentionally has no Milestone while still being accepted work.
- A Planning Item may intentionally have no direct Milestone metadata because its execution placement and Milestone ownership are inherited from its Parent Package.
- Other project-level resources may legally have no Milestone while still not being Project Backlog work.

### 3.4 Typical Project Backlog members

Depending on triage outcome, Project Backlog may retain resources such as:

- Requests,
- deferred Bugs,
- deferred architecture work,
- work resulting from a Discovery,
- future Spike candidates,
- other uncommitted but valuable work.

The decision to place an item in Project Backlog belongs to the decision model, not this structural contract.

### 3.5 Project Backlog versus accepted execution

A resource retained in Project Backlog is outside approved execution scope.

Once work is accepted as a Package for execution, it must leave Project Backlog placement and receive exactly one canonical Execution Placement:

```text
Milestone:<id>
```

or:

```text
Project-level approved execution
```

An item must not simultaneously be authoritative Project Backlog work and an accepted execution Package.

---

## 4. Intake and emerging-work resources

### 4.1 Discovery

A Discovery is a project-level evidence and context record created before a project-management decision is made.

A Discovery may record `Discovered In` links to a Milestone, Package, Planning Item, review, execution activity, or incident.

`Discovered In` is provenance only.

It does not create structural ownership under the resource where the Discovery was found.

Canonical rule:

> **FOUND DURING MILESTONE ≠ BELONGS TO MILESTONE**

A Discovery may later be linked to resulting work or Project Backlog placement after triage, but the Discovery itself does not silently modify scope.

### 4.2 Request

A Request is proposed work that has not yet been accepted into an execution scope.

A Request may exist at Project level while awaiting triage, may be retained in Project Backlog, may be rejected, or may result in accepted work.

The resource model defines where it can exist; the decision model defines how that decision is made.

### 4.3 Spike

A Spike is a bounded investigation resource.

A Spike may be Project-level or associated with an approved Package depending on why the investigation exists. Its structural placement must be explicit and must not be inferred from where the uncertainty was first discovered.

The Spike's decision semantics and completion routing belong to the decision model.

---

## 5. Relationship taxonomy

The canonical model distinguishes the following relationship types.

### 5.1 Structural ownership

Structural ownership answers:

> Where does this resource belong?

Examples:

```text
Project → Milestone
Milestone → Package
Project → Package (Project-level approved execution)
Package → Planning Item
Planning Item → Checklist Item
```

Structural ownership must have one authoritative source of truth.

### 5.2 Execution Placement

Execution Placement answers:

> Under which approved execution scope does this accepted Package currently belong?

Every accepted Package has exactly one canonical Execution Placement:

```text
Milestone:<id>
```

or:

```text
Project-level approved execution
```

The relation is mutually exclusive for a Package at a point in time.

Project-level approved execution is used for accepted work that must execute outside every Milestone, such as an approved Interruption. It does **not** silently enlarge the active Milestone.

A Package placed in Project-level approved execution requires an explicit approved decision / authorization context from the applicable decision and approval contracts. Merely having no Milestone does not establish this placement.

A storage adapter must provide one authoritative representation that lets a PM skill distinguish:

- Milestone Package,
- Project-level approved execution Package,
- Project Backlog work,
- other Project-level records.

### 5.3 Project Backlog placement

Project Backlog placement answers:

> Is this retained work currently outside approved execution scope and placed in the Project Backlog?

This is a Project-level commitment / placement relationship, not a workflow state.

Project Backlog placement and accepted Package Execution Placement are mutually exclusive.

### 5.4 Provenance (`Discovered In`)

Provenance answers:

> Where was this information discovered?

A provenance relationship does not imply ownership, scope membership, priority, or execution order.

### 5.5 Dependency

Dependency answers:

> What work constrains another work item's ability or order to proceed?

Examples include:

- blocks,
- blocked by,
- requires,
- prerequisite for.

Dependency is orthogonal to Parent / Child ownership.

### 5.6 Workflow relationship

Workflow state and state transitions describe execution lifecycle, not structural ownership or Execution Placement.

The authoritative workflow state machine belongs to `docs/core/workflow-contract.md` once PLAN07 is complete.

---

## 6. Ownership and scope inheritance

### 6.1 Milestone ownership chain

For normal Milestone work, the canonical ownership chain is:

```text
Project
└─ Milestone
   └─ Package
      └─ Planning Item
```

The Package is the direct Milestone scope member.

The Planning Item inherits that scope membership from the Package.

### 6.2 No duplicate child Milestone ownership

A Planning Item should not maintain an independent canonical Milestone ownership record when its Parent Package already determines that relationship.

If a storage tool exposes a duplicated Milestone field for convenience, that value is only a denormalized projection and must never override the Parent Package relationship.

If the projected Milestone conflicts with the Parent Package's Milestone, the Parent Package relationship is authoritative and the adapter must report or repair the inconsistency.

### 6.3 Project-level approved execution chain

Approved work may execute outside every Milestone without changing any Milestone scope.

The canonical ownership chain is:

```text
Project
└─ Package [Execution Placement = Project-level approved execution]
   └─ Planning Item
```

This path is used when a project-management decision authorizes immediate or active work but explicitly keeps that work outside Milestone scope.

For example, an approved Interruption may create or activate a Package under Project-level approved execution while the interrupted Milestone remains unchanged.

Canonical rules:

```text
approved execution work ≠ current Milestone member
```

and:

```text
Milestone = empty ≠ Project-level approved execution
```

The second rule matters because absence of Milestone metadata alone cannot distinguish approved Project-level execution from Project Backlog work or other Project-level records.

### 6.4 Execution Placement inheritance

Planning Items inherit their Parent Package's Execution Placement.

Therefore:

- a Planning Item under a Milestone Package belongs to that Milestone scope indirectly;
- a Planning Item under a Project-level approved execution Package remains outside every Milestone;
- the Planning Item must not maintain a competing canonical execution-placement record.

### 6.5 Moving between placements

Changing a Package from one execution placement to another changes project commitment / scope placement and must be performed through the applicable decision / approval process.

Examples include:

```text
Project Backlog → Milestone
Project Backlog → Project-level approved execution
Project-level approved execution → Milestone
Milestone → Project Backlog
```

These are not ordinary workflow Status transitions.

---

## 7. Planning Item parent invariant

### 7.1 Valid state

A valid Planning Item must satisfy all of the following:

```text
Work Type = Planning
Parent Package exists
Parent Package relationship count = 1
Parent Package is a Package resource
Required project metadata is present
Relationship verification succeeds
```

### 7.2 Invalid states

The following are invalid / incomplete states:

```text
Planning Item with no Parent Package
Planning Item with multiple competing Parent Packages
Planning Item whose parent is not a Package
Planning Item whose body names a parent but no structural relationship exists
Planning Item whose required Project metadata could not be established
```

Text such as:

```text
Parent Package: #1
```

inside an Issue body is documentation only. It does not replace the canonical structural Parent relationship.

### 7.3 Orphan handling

If an orphan Planning Item is detected, the Agent must:

1. stop claiming successful creation,
2. identify the intended Parent Package if evidence is sufficient,
3. establish and verify the Parent relationship when authorized and technically possible,
4. otherwise report partial failure and request the required human or capable-adapter action,
5. avoid starting normal execution until the structural integrity problem is resolved.

---

## 8. Resource creation transaction

Resource creation is a logical transaction, even when the storage system requires several API calls.

### 8.1 Canonical creation phases

```mermaid
flowchart LR
    A[Identify resource type]
    B[Create primary entity]
    C[Establish required structural relationships]
    D[Set required project metadata]
    E[Verify persisted relationships and metadata]
    F{Integrity satisfied?}
    G[Creation COMPLETE]
    H[Creation PARTIAL / FAILED]
    I[Repair or human handoff]

    A --> B --> C --> D --> E --> F
    F -->|Yes| G
    F -->|No| H --> I
```

### 8.2 Creation result

Creation result is not a workflow Status. It is an operation result.

Canonical operation outcomes are:

- `COMPLETE` — entity and all required relationships / metadata were created and verified.
- `PARTIAL` — primary entity exists, but one or more required relationships or metadata writes could not be completed.
- `FAILED` — the primary resource could not be created or the operation cannot be safely represented.

### 8.3 Mandatory verification

An Agent must verify persisted state after multi-step creation when the adapter supports verification.

For a Planning Item this includes verifying at least:

```text
Issue exists
Work Type = Planning
Parent Package relationship exists
Parent Package is the intended Package
Required Project membership / metadata exists
```

For an accepted Package this includes verifying at least:

```text
Package exists
Work Type = Package
Exactly one canonical Execution Placement exists
Execution Placement matches the approved decision
Project Backlog placement is not simultaneously authoritative
Required Project membership / metadata exists
```

The Agent must not convert a partial result into a success message merely because the Issue itself exists.

### 8.4 Adapter capability failure

If the adapter cannot create a required relationship, the correct result is:

```text
Creation Result = PARTIAL
```

with an explicit handoff describing:

- what was created,
- what remains missing,
- why the adapter could not complete it,
- what authorized human or capable adapter action is required.

---

## 9. Canonical resource integrity invariants

All PM skills and adapters must preserve these rules:

1. **EVERY PLANNING ITEM HAS EXACTLY ONE PARENT PACKAGE.**
2. A Planning Item without its structural Parent relationship is orphaned and incomplete.
3. A body reference to a parent does not substitute for the structural Parent relationship.
4. Planning Items inherit Execution Placement and Milestone scope, if any, from their Parent Package.
5. Child resources must not create a competing canonical Milestone or Execution Placement source of truth.
6. **EVERY ACCEPTED PACKAGE HAS EXACTLY ONE EXECUTION PLACEMENT.**
7. Valid Package Execution Placements are one Milestone or Project-level approved execution outside all Milestones.
8. A Package must not simultaneously use Milestone placement and Project-level approved execution placement.
9. **PROJECT BACKLOG ≠ STATUS: BACKLOG.**
10. Project Backlog membership must have one explicit, authoritative representation.
11. Project Backlog membership and accepted Package Execution Placement are mutually exclusive.
12. Project Backlog membership must not be inferred solely from `Status = Backlog`.
13. Project Backlog membership must not be inferred solely from `Milestone = empty`.
14. Project-level approved execution must not be inferred solely from `Milestone = empty`.
15. `Discovered In` is provenance, not ownership.
16. Parent / Child and Dependency relationships are different and must not be substituted for each other.
17. Resource creation is incomplete until required relationships and metadata are persisted and verified.
18. An adapter that cannot complete required resource integrity must report `PARTIAL` or `FAILED`, not success.
19. Technical storage convenience must not override the canonical structural relationship model.
20. Moving work between Project Backlog, Milestone execution, and Project-level approved execution is a governed placement mutation, not an ordinary workflow Status transition.

---

## 10. Initial GitHub mapping

The canonical model is product-independent. The initial GitHub implementation is expected to map resources as follows:

| Canonical resource / relation | Initial GitHub representation | Authority note |
|---|---|---|
| Project | GitHub Project | Management / projection layer |
| Milestone | GitHub repository Milestone | Directly associated with Package Issues in the initial single-repo model |
| Package — Milestone placement | GitHub Issue, `Work Type = Package`, native Milestone set | Native Milestone is authoritative for this placement |
| Package — Project-level approved execution | GitHub Issue, `Work Type = Package`, no native Milestone plus adapter-defined explicit Execution Placement | Empty Milestone alone is insufficient; explicit placement + approved decision context are required |
| Planning Item | GitHub Issue, `Work Type = Planning` | Must have native Parent/Sub-issue relationship to Package |
| Parent Package | GitHub Parent issue / Sub-issue relationship | Authoritative structural relationship |
| Checklist Item | Markdown task list inside Package / Planning Item | Lightweight, non-independent step |
| Discovery | GitHub Issue, `Work Type = Discovery` | Project-level until triage decides resulting work placement |
| Request | GitHub Issue, `Work Type = Request` | May later become Project Backlog or accepted work |
| Spike | GitHub Issue, `Work Type = Spike` | Placement depends on approved context |
| Execution Placement | Native Milestone for Milestone Packages; adapter-defined explicit Project representation for Project-level approved execution | Must be deterministically queryable and mutually exclusive |
| Project Backlog membership | Adapter-defined explicit Project representation | Must not be inferred from Status or empty Milestone |
| Dependency | GitHub-supported dependency / linkage representation when available, otherwise adapter-managed explicit relation | Must remain distinct from Parent/Sub-issue |

### 10.1 GitHub Milestone limitation

GitHub native Milestones are repository-scoped.

The initial implementation intentionally assumes a single-repository Milestone model. Cross-repository Milestone / initiative modeling is outside PLAN02 and requires an explicit later design rather than silently overloading the native GitHub Milestone concept.

### 10.2 Project fields are projections, not alternate ownership records

Custom Project fields may expose derived values for filtering, grouping, automation, or explicit placement when no canonical native relation exists.

They must not create competing ownership truth when a canonical native relationship already exists.

For example:

```text
Planning Item Parent issue = authoritative
Planning Item custom "Parent Package" text = non-authoritative duplicate and should be avoided
```

For Project-level approved execution, GitHub has no native Milestone-equivalent relation that represents this canonical placement. The adapter must therefore define one explicit Project-level representation and treat it as authoritative for that placement; it must not infer the placement from an empty Milestone field.

---

## 11. Boundary with other core contracts

This document owns:

- canonical resource hierarchy,
- structural ownership,
- Package Execution Placement semantics,
- Project Backlog placement semantics,
- Planning Parent invariant,
- orphan / incomplete resource handling,
- resource creation integrity,
- product-independent mapping requirements.

It does **not** own:

- Discovery decision routing (`decision-model.md`, PLAN03),
- skill-to-skill handoff schema (`handoff-contract.md`, PLAN04),
- approval matrix (`approval-policy.md`, PLAN05),
- workflow Status transitions / Action Owner / Transition Authority / Review contract (`workflow-contract.md`, PLAN07).

This separation is normative: downstream skills and adapters should reference the appropriate source of truth instead of duplicating these policies.
