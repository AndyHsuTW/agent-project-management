# Canonical PM Resource Model

> Status: M1 / PM-P01 / PLAN02 — Ready for re-review
>
> Purpose: Define the canonical project-management resources, their ownership and structural relationships, and the integrity rules that all PM skills and storage adapters must preserve.

This document is the authoritative source for **resource structure and relationship integrity**. It intentionally does not define workflow status transitions, review authority, approval policy, or discovery decision routing; those belong to their dedicated contracts.

## 1. Design principles

1. Resource structure is independent of any storage product.
2. Structural placement / ownership, workflow status, scope commitment, provenance, and dependency are separate dimensions.
3. **Execution Placement is the canonical structural placement relation for an accepted Package; it is not a second ownership record.**
4. Every structural relationship has one authoritative source of truth.
5. Child resources do not duplicate ownership metadata that can be derived from their parent unless an adapter explicitly maintains a denormalized projection.
6. Creating an entity is not enough: required relationships and required project metadata must also be established and verified.
7. A resource whose required relationships are missing is in an incomplete / invalid creation state and must not be reported as successfully created.
8. Project Backlog membership is an explicit project-level placement relation; it is not inferred from workflow status or missing Milestone metadata.
9. Every accepted Package must have exactly one explicit **Execution Placement**: either one Milestone or Project-level approved execution outside all Milestones.

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

The solid edges are canonical structural relationships.

For an accepted Package, the incoming structural edge is its **Execution Placement**. The two Package nodes above are the **same canonical Package resource type** shown under its two valid placements:

1. Milestone execution — `Milestone → Package`, or
2. Project-level approved execution — `Project → Package` outside all Milestones.

These are not duplicated ownership plus placement records; the Execution Placement edge itself is the Package's authoritative structural placement.

For lower-level work, `Package → Planning Item → Checklist Item` represents Parent / Child decomposition.

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

> A Milestone is a valid Execution Placement for accepted Packages, not for Planning Items.

When a Package's Execution Placement points to a Milestone, that Package is directly committed to that Milestone.

Planning Items inherit Milestone membership through their Parent Package.

A Milestone should therefore not maintain a second, independent list of the same Planning Items as direct scope children.

### 2.3 Package

A Package is the stable, independently trackable unit of accepted work.

A Package must have exactly one canonical **Execution Placement**, which is also the Package's authoritative structural placement relation:

- **Milestone placement** — `Milestone → Package`; the Package is directly committed to exactly one Milestone; or
- **Project-level approved execution placement** — `Project → Package`; the Package is approved for execution directly under the Project and intentionally belongs to no Milestone.

A Package may:

- belong to a Milestone through its Execution Placement,
- be approved for Project-level execution outside all Milestones through its Execution Placement,
- own zero or more Planning Items,
- participate in dependency relationships with peer work,
- carry its own workflow Status, Priority, Sequence, Acceptance Criteria, and implementation context.

A Package must not simultaneously use both canonical execution placements. An adapter must not create a second Package ownership field in parallel with Execution Placement.

Moving a Package between placements is a governed project-management mutation, not a workflow Status change.

Package identity is stable and does not encode execution order.

### 2.4 Planning Item

A Planning Item is an independently trackable child decomposition of a Package.

Canonical invariant:

> **EVERY PLANNING ITEM HAS EXACTLY ONE PARENT PACKAGE.**

A Planning Item:

- must have exactly one Parent Package,
- inherits the Parent Package's Execution Placement and Milestone scope membership, if any,
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

The canonical model distinguishes the following relationship types while preserving a single source of truth for each persisted relation.

### 5.1 Structural relationships

Structural relationships answer:

> Where does this resource belong in the canonical PM model?

They include two important subtypes:

```text
Structural relationship
├─ Package Execution Placement
│  ├─ Milestone → Package
│  └─ Project → Package [Project-level approved execution]
└─ Parent / Child decomposition
   ├─ Package → Planning Item
   └─ Planning Item → Checklist Item
```

`Project → Milestone` is also a structural containment relationship at the delivery-boundary level.

A single persisted relationship may have a specialized semantic name. In particular, `Milestone → Package` and `Project → Package [approved execution]` are called **Execution Placement** because they identify the approved execution scope of a Package.

The specialized name does **not** require a second ownership record.

### 5.2 Execution Placement

Execution Placement is the **Package-specific structural placement relation**.

It answers:

> Under which approved execution scope does this accepted Package currently belong?

Every accepted Package has exactly one canonical Execution Placement:

```text
Milestone:<id>
```

or:

```text
Project-level approved execution
```

For Package resources:

> **EXECUTION PLACEMENT = AUTHORITATIVE STRUCTURAL PLACEMENT**

There must not be a separate competing `Package Owner`, `Structural Owner`, or equivalent canonical relationship that duplicates the same Milestone / Project placement.

The relation is mutually exclusive for a Package at a point in time.

Project-level approved execution is used for accepted work that must execute outside every Milestone, such as an approved Interruption. It does **not** silently enlarge the active Milestone.

A Package placed in Project-level approved execution requires an explicit approved decision / authorization context from the applicable decision and approval contracts. Merely having no Milestone does not establish this placement.

A storage adapter must expose or derive from one authoritative representation enough information for a PM skill to distinguish:

- Milestone Package,
- Project-level approved execution Package,
- Project Backlog work,
- other Project-level records.

If a storage product exposes both a native relationship and a projection field, one must be designated authoritative and the other treated as derived metadata; both must not be independent writable sources of truth.

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

Dependency is orthogonal to structural placement / Parent / Child relationships.

### 5.6 Workflow relationship

Workflow state and state transitions describe execution lifecycle, not structural placement.

The authoritative workflow state machine belongs to `docs/core/workflow-contract.md` once PLAN07 is complete.

---

## 6. Placement and scope inheritance

### 6.1 Milestone Execution Placement chain

For normal Milestone work, the canonical structural chain is:

```text
Project
└─ Milestone
   └─ Package
      └─ Planning Item
```

For the Package, `Milestone → Package` is its single authoritative Execution Placement.

The Planning Item inherits that Package's Milestone scope membership.

### 6.2 No duplicate child Milestone ownership

A Planning Item should not maintain an independent canonical Milestone ownership record when its Parent Package already determines that relationship.

If a storage tool exposes a duplicated Milestone field for convenience, that value is only a denormalized projection and must never override the Parent Package relationship and inherited Execution Placement.

If the projected Milestone conflicts with the Parent Package's Execution Placement, the Parent Package-derived placement is authoritative and the adapter must report or repair the inconsistency.

### 6.3 Project-level approved Execution Placement chain

Approved work may execute outside every Milestone without changing any Milestone scope.

The canonical structural chain is:

```text
Project
└─ Package [Execution Placement = Project-level approved execution]
   └─ Planning Item
```

For the Package, `Project → Package [approved execution]` is its single authoritative Execution Placement.

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

Planning Items inherit their Parent Package's Execution Placement; they do not own a second Execution Placement relation.

Therefore:

- a Planning Item under a Milestone Package belongs to that Milestone scope indirectly;
- a Planning Item under a Project-level approved execution Package remains outside every Milestone;
- the Planning Item must not maintain a competing canonical execution-placement record.

### 6.5 Moving between placements

Changing a Package from one Execution Placement to another changes project commitment / scope placement and must be performed through the applicable decision / approval process.

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
Execution Placement is the Package's authoritative structural placement
Execution Placement matches the approved decision
No competing Package ownership / placement relationship exists
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
7. **EXECUTION PLACEMENT IS THE AUTHORITATIVE STRUCTURAL PLACEMENT FOR AN ACCEPTED PACKAGE; IT IS NOT A SECOND OWNERSHIP RECORD.**
8. Valid Package Execution Placements are one Milestone or Project-level approved execution outside all Milestones.
9. A Package must not simultaneously use Milestone placement and Project-level approved execution placement.
10. An adapter must not maintain an independently authoritative Package ownership field that duplicates Execution Placement.
11. **PROJECT BACKLOG ≠ STATUS: BACKLOG.**
12. Project Backlog membership must have one explicit, authoritative representation.
13. Project Backlog membership and accepted Package Execution Placement are mutually exclusive.
14. Project Backlog membership must not be inferred solely from `Status = Backlog`.
15. Project Backlog membership must not be inferred solely from `Milestone = empty`.
16. Project-level approved execution must not be inferred solely from `Milestone = empty`.
17. `Discovered In` is provenance, not ownership.
18. Parent / Child and Dependency relationships are different and must not be substituted for each other.
19. Resource creation is incomplete until required relationships and metadata are persisted and verified.
20. An adapter that cannot complete required resource integrity must report `PARTIAL` or `FAILED`, not success.
21. Technical storage convenience must not override the canonical structural relationship model.
22. Moving work between Project Backlog, Milestone execution, and Project-level approved execution is a governed placement mutation, not an ordinary workflow Status transition.

---

## 10. Initial GitHub mapping

The canonical model is product-independent. The initial GitHub implementation is expected to map resources as follows:

| Canonical resource / relation | Initial GitHub representation | Authority note |
|---|---|---|
| Project | GitHub Project | Management / projection layer |
| Milestone | GitHub repository Milestone | Delivery-boundary resource |
| Package — Execution Placement = Milestone | GitHub Issue, `Work Type = Package`, native Milestone set | Native Milestone is the authoritative Package Execution Placement; no separate Package ownership field is needed |
| Package — Execution Placement = Project-level approved execution | GitHub Issue, `Work Type = Package`, no native Milestone plus adapter-defined explicit Execution Placement | The explicit placement representation is the authoritative Package structural placement; empty Milestone alone is insufficient |
| Planning Item | GitHub Issue, `Work Type = Planning` | Must have native Parent/Sub-issue relationship to Package |
| Parent Package | GitHub Parent issue / Sub-issue relationship | Authoritative structural decomposition relationship |
| Checklist Item | Markdown task list inside Package / Planning Item | Lightweight, non-independent step |
| Discovery | GitHub Issue, `Work Type = Discovery` | Project-level until triage decides resulting work placement |
| Request | GitHub Issue, `Work Type = Request` | May later become Project Backlog or accepted work |
| Spike | GitHub Issue, `Work Type = Spike` | Placement depends on approved context |
| Project Backlog membership | Adapter-defined explicit Project representation | Must not be inferred from Status or empty Milestone |
| Dependency | GitHub-supported dependency / linkage representation when available, otherwise adapter-managed explicit relation | Must remain distinct from structural placement and Parent/Sub-issue |

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

For a Milestone Package:

```text
Native Milestone = authoritative Execution Placement
Custom "Execution Placement" field = optional derived projection only
```

For Project-level approved execution, GitHub has no native Milestone-equivalent relation. The adapter must therefore define one explicit Project-level representation and treat **that representation as the Package's canonical Execution Placement / structural placement**; it must not create a separate ownership record or infer the placement from an empty Milestone field.

---

## 11. Boundary with other core contracts

This document owns:

- canonical resource hierarchy,
- structural relationship taxonomy,
- Package Execution Placement as the canonical Package structural placement relation,
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
