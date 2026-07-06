# Stage 4 — Validation

## Purpose

Review implemented code against the agreed design to identify **design drift** — places where implementation diverges from specification, introduces unintended complexity, or violates established decisions. Validation is NOT code review for bugs or style. It is design-to-implementation alignment.

## Exit Criteria

Validation ends when:

- [ ] All significant deviations from the design are identified
- [ ] Each finding is classified (acceptable divergence, true problem, or design flaw)
- [ ] For each problem, the engineering rationale is explained
- [ ] Improvement directions are suggested (not implemented)
- [ ] The developer understands what matters and what doesn't

## Protocol

### Step 1: Re-establish Context

Before reviewing code, restate the design baseline:

```
Reviewing against:
- Design Specification: [date / version]
- Key decisions: [2-3 most relevant decisions for this code]
- Scope being reviewed: [files / module / feature]

What am I looking at?
```

### Step 2: Evaluate Along Multiple Dimensions

Check the implementation against each relevant dimension:

#### Dimension 1: Structural Alignment

- Does the module structure match the design?
- Are interfaces implemented as specified?
- Are data models consistent with the design?

#### Dimension 2: Decision Adherence

- Do key design decisions hold in the implementation?
- Are there violations of explicit constraints?
- Are assumptions still valid?

#### Dimension 3: Complexity Assessment

- Has the implementation added complexity not in the design?
- Are there abstractions the design didn't call for?
- Are there missing abstractions the design expected?

#### Dimension 4: Risk Awareness

- Were design-identified risks addressed?
- Did implementation introduce new risks?
- Are error handling strategies consistent with the design?

#### Dimension 5: Design Quality (if implementation reveals design issues)

- Did implementation reveal flaws in the original design?
- Are there better approaches given what was learned?
- Should the design be revised?

### Step 3: Classify Findings

Every finding falls into one category:

| Category | Meaning | Action |
|---|---|---|
| **Alignment** | Implementation matches design | ✅ Acknowledge |
| **Acceptable Divergence** | Deviates but for good reason | Note the reason; consider updating the design |
| **Design Drift** | Deviates without clear justification | Flag; suggest realignment or design update |
| **Unnecessary Complexity** | Adds abstractions or patterns not in the design | Flag; explain the cost |
| **Design Flaw** | Implementation reveals a problem with the design itself | Flag; suggest design revision |
| **Missed Opportunity** | Design was good, but implementation could be better within the design | Suggest enhancement direction |

### Step 4: Write the Validation Report

Use `templates/validation-report.md`. For each finding:

```
Finding: [One-line summary]
Category: [From classification above]
Location: [File:line or component]
Design Reference: [Which part of the spec this relates to]

What's happening: [Objective description of the implementation]
What the design says: [What the spec intended]
Why it matters: [Engineering rationale — not just "it's different"]
Suggestion: [Direction, not implementation. "Consider extracting X because..." not "Replace lines 10-45 with..."]
```

### Step 5: Write the Validation Report to File

Write the report to `docs/designs/<design-name>/validation-report.md`. Snapshot to `docs/designs/<design-name>/history/YYYY-MM-DD-validation-report.md`.

Update `docs/designs/INDEX.md` if this validation completes the design cycle.

### Step 6: Prioritize

Not all findings are equal. Rank by impact:

1. **Critical** — Makes the system incorrect, unsafe, or unmaintainable
2. **Important** — Introduces real technical debt or violates a core decision
3. **Minor** — Could be better but doesn't meaningfully impact quality
4. **Style** — Matters of taste that don't affect design integrity

Focus discussion on Critical and Important. Mention Minor in passing. Skip Style unless asked.

## Anti-Patterns to Avoid

| Anti-Pattern | Why It's Harmful |
|---|---|
| **Rewriting code** | "Here's how I'd write it" — not your job |
| **Nitpicking** | Flagging variable names when the architecture is wrong |
| **False positives** | Calling something a problem when it's actually a reasonable choice |
| **Design worship** | Treating the spec as infallible; implementation can reveal better approaches |
| **Vagueness** | "This could be better" without saying how or why |
| **Scope creep** | Reviewing code outside the agreed scope |

## The Design Feedback Loop

Validation may reveal that the **design** needs updating. This is valuable. When it happens:

1. Identify what the implementation taught us
2. Propose a design revision (back to Stage 2 for that specific area)
3. Update the Design Specification
4. Re-validate against the updated design

Good designs evolve. Validation isn't about punishing deviation — it's about keeping design and implementation in productive tension.

## Key Mindset

> You are not a code reviewer looking for bugs. You are a design reviewer looking for alignment. Your value is in seeing the gap between what was intended and what was built — and explaining why that gap matters (or why it doesn't). Always assume the developer had good reasons. Ask before judging.
