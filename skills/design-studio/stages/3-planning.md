# Stage 3 — Planning

## Purpose

Transform the Design Specification into a **structured implementation roadmap** — a sequence of milestones, tasks, and subtasks with clear dependencies and acceptance criteria. The plan should enable independent implementation without requiring the developer to re-derive the design.

## Exit Criteria

Planning ends when:

- [ ] The project is broken into milestones with clear goals
- [ ] Each milestone is decomposed into tasks and subtasks
- [ ] Dependencies are explicitly mapped
- [ ] Every task has acceptance criteria
- [ ] The developer can articulate the implementation sequence
- [ ] Riskiest tasks are identified and scheduled early

## Protocol

### Step 1: Identify Milestones

Group work into logical, value-delivering chunks:

```
A milestone should:
- Deliver something tangible (a running module, a testable API, an integrated feature)
- Be independently verifiable
- Take days, not weeks (for a feature) or weeks, not months (for a system)
- Have a clear "done" state
```

### Step 2: Decompose into Tasks

For each milestone, list the tasks required:

```
Task structure:
├── Task ID: M1-T1
├── Summary: One line describing the work
├── Purpose: Why this task exists (links to requirements)
├── Dependencies: Tasks that must complete first
├── Estimated effort: Relative sizing (S/M/L/XL), not hours
├── Acceptance criteria: Verifiable conditions for "done"
└── Design references: Links to relevant parts of the Design Spec
```

### Step 3: Map Dependencies

Draw the dependency graph:

```
M1: Foundation
├── T1: Data model (no dependencies)
├── T2: Repository layer (depends on T1)
└── T3: Basic API skeleton (depends on T1)

M2: Core Feature
├── T4: Business logic (depends on T2, T3)
├── T5: Input validation (depends on T3)
└── T6: Integration tests (depends on T4, T5)

M3: Polish
└── T7: Error handling hardening (depends on M2 complete)
```

### Step 4: Write Acceptance Criteria

Every task needs criteria. Bad vs. good:

| Bad | Good |
|---|---|
| "Implement auth" | "POST /auth/login accepts email+password, returns JWT on success, 401 on failure; JWT validates against /auth/verify" |
| "Add error handling" | "All API endpoints return structured errors: { code, message, details? }; unhandled exceptions become 500 with logged trace ID" |
| "Create database" | "Migrations create users, orders, products tables matching the schemas in Design Spec §3.2; rollback tested" |

### Step 5: Identify Risks and Mitigations

For each milestone, ask:

- What's most likely to go wrong?
- What's the hardest part?
- What do we know least about?
- What should we spike/prototype first?

Schedule the riskiest work early.

### Step 6: Present the Plan

Output using `templates/implementation-plan.md`. The plan should be:

- **Readable** — A new team member should understand the sequence
- **Trackable** — Each task can be checked off
- **Flexible** — Dependencies are explicit so reordering is possible
- **Grounded** — Every task traces back to a design requirement

### Step 7: Write the Implementation Plan

When the developer confirms the plan, write it to `docs/designs/<design-name>/implementation-plan.md`. Then snapshot a copy to `docs/designs/<design-name>/history/YYYY-MM-DD-implementation-plan.md`.

Update `docs/designs/INDEX.md` to reflect the new stage and status.

## Parallelism Guidance

Mark tasks that can run in parallel:

```
[M1-T2] and [M1-T3] can run in parallel after [M1-T1]
[M2-T4] and [M2-T5] can run in parallel after [M1-T2, M1-T3]
```

## Anti-Patterns to Avoid

| Anti-Pattern | Why It's Harmful |
|---|---|
| **Waterfall illusion** | Pretending every task can be perfectly sequenced upfront |
| **False precision** | "Task takes 4.7 hours" — you don't know that |
| **Dependency spaghetti** | Everything depends on everything else |
| **No acceptance criteria** | "Implement the thing" — how do you know it's done? |
| **Too granular** | 50 tasks for a 2-day feature |
| **Too coarse** | "Build the system" as one task |

## Transition to Implementation

```
[Stage 3 Complete → Ready for Implementation]

The roadmap is ready:
- Milestones: [count]
- Tasks: [count]
- Parallelizable work: [identified]

You can now implement following this plan. When you have code to review,
come back and I'll switch to Validation mode (Stage 4).
```

## Key Mindset

> A plan is a tool for thinking, not a contract. It should provide clarity and sequence without pretending to predict the future. The best plans make dependencies visible, risks explicit, and success verifiable.
