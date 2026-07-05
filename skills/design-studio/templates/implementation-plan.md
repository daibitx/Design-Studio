# Implementation Plan

> Template — roadmap derived from the Design Specification.

---

## Plan Metadata

| Field | Value |
|---|---|
| **Design Spec Reference** | |
| **Date** | |
| **Total Milestones** | |
| **Total Tasks** | |
| **Parallelizable Work** | |

---

## Dependency Graph

```
M1: [Name]
├── M1-T1: [Task] (no deps)
├── M1-T2: [Task] (depends on M1-T1)
└── M1-T3: [Task] (depends on M1-T1)

M2: [Name]
├── M2-T1: [Task] (depends on M1-T2, M1-T3)
└── M2-T2: [Task] (depends on M1-T3)
```

---

## Milestone 1: [Name]

**Goal:** <!-- What does this milestone deliver? -->

**Design References:** <!-- Links to Design Spec sections -->

### Tasks

#### M1-T1: [Task Name]

| Field | Value |
|---|---|
| **Purpose** | Why this task exists (links to requirement) |
| **Dependencies** | None / Mx-Ty |
| **Effort** | S / M / L / XL |
| **Parallel with** | M1-Tx (if applicable) |

**Acceptance Criteria:**

1.
2.

**Implementation Considerations:**

-

---

#### M1-T2: [Task Name]

| Field | Value |
|---|---|
| **Purpose** | |
| **Dependencies** | |
| **Effort** | |
| **Parallel with** | |

**Acceptance Criteria:**

1.

**Implementation Considerations:**

-

---

## Risk Mitigation Plan

| Risk (from Design Spec) | Mitigation Task | Milestone |
|---|---|---|
| | | |

---

## Suggested Implementation Order

1. Start with [Mx-Ty] — no dependencies, establishes foundation
2. Parallelize [tasks] after [dependency] completes
3. Riskiest work: [tasks] — do early, prototype if needed
4. Integration: [tasks] — after core is stable

---

## Task Checklist

- [ ] M1-T1: [Task Name]
- [ ] M1-T2: [Task Name]
- [ ] M2-T1: [Task Name]
- [ ] M2-T2: [Task Name]
