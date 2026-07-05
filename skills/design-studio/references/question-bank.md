# Question Bank

> Stage-specific probing questions. Not a checklist — pick the ones that matter for the current context.

---

## Discovery Questions

### Problem Space

- What problem are we solving? (One sentence — if you can't, the problem isn't clear yet)
- Why does this need to exist? What happens if we do nothing?
- What evidence do we have that this is a real problem?
- Has anyone tried to solve this before? What happened?
- What's the simplest version of this that would still be valuable?
- What would make this project unnecessary? (If nothing, the scope may be too vague)

### Users & Stakeholders

- Who will use this? (Be specific — roles, not "users")
- What's their current workflow without this solution?
- What's their technical sophistication?
- Who benefits but won't use it directly?
- Who might be negatively affected?
- Are there conflicting stakeholder needs?

### Constraints

- What technology choices are already fixed?
- What budget or timeline constraints exist?
- What compliance or regulatory requirements apply?
- What existing systems must we integrate with?
- What team skills and capacity are available?
- What organizational policies constrain design choices?

### Edge Cases & Failures

- What's the worst thing that could happen if this fails?
- What happens under peak load? At 2 AM on a weekend?
- What if the data is wrong? Missing? Malformed?
- What if a dependent service is down? Slow? Returns garbage?
- What's the recovery process when things go wrong?
- What's the rollback plan?

### Non-Goals

- What are we explicitly NOT building?
- What similar requests have we already said no to?
- What would be "nice to have" but isn't essential?
- Where's the boundary between this and adjacent systems?
- What future requests do we anticipate but are deferring?

---

## Design Questions

### Architecture

- What are the natural boundaries in this system?
- What changes independently? What changes together?
- Where is asynchrony necessary vs. added complexity?
- What's the data flow? Where does state live?
- How do components discover and communicate with each other?
- What's the deployment model?

### Data

- What data does this system own vs. reference?
- What are the consistency requirements? (Strong, eventual, none?)
- What's the read/write pattern? (Mostly reads? Write-heavy? Balanced?)
- How large can the data grow? Over what timeframe?
- What queries must be fast? What can be slow?
- What data must never be lost? What can be reconstructed?

### Interfaces & APIs

- What's the simplest interface that satisfies the requirements?
- What does the caller need to know? What can we hide?
- How will this interface evolve? What changes are expected?
- What errors can occur? How are they communicated?
- Is the interface idempotent? Should it be?
- How is the interface versioned?

### Security

- What's the threat model? Who might attack this and why?
- What data is sensitive? Where is it exposed?
- What's the authentication and authorization model?
- Where do we validate input? At the boundary? Internally?
- What secrets does this system need? How are they managed?

### Operations

- How is this deployed? Rolled back? Scaled?
- What metrics tell us it's healthy? What alerts should fire?
- What logs do we need for debugging? What's too much?
- How do we run this locally for development?
- What's the onboarding path for a new team member?

---

## Planning Questions

### Decomposition

- What's the smallest slice that delivers value?
- What must be built first because everything depends on it?
- What can be built independently, in parallel?
- What's the riskiest part? How can we de-risk it early?
- What don't we know yet? How do we learn it?

### Task Quality

- Can someone outside this conversation understand this task?
- Is the acceptance criteria testable? ("Looks good" is not testable)
- Is the task small enough to complete in a reasonable timeframe?
- Does the task description explain why, not just what?
- Is the dependency clear? ("Depends on auth" — which part?)

### Sequencing

- What's the minimum viable first milestone?
- At what point can we get user feedback?
- Where are the natural integration points?
- What's the latest we can make a decision and still ship?
- If we had to ship in half the time, what would we cut?

---

## Validation Questions

### Alignment

- Does the module structure reflect the design?
- Are interfaces implemented as specified?
- Do key design decisions hold in the code?
- Are constraints being respected?
- Are assumptions from Discovery still valid?

### Complexity

- Is there complexity here that the design didn't intend?
- Are there abstractions that don't pull their weight?
- Could this be simpler while still meeting requirements?
- Is the code doing things the design didn't ask for?
- Are there "temporary" workarounds that became permanent?

### Design Quality

- Did implementation reveal flaws in the original design?
- Are there patterns in the code that suggest a better design?
- What would we change in the design knowing what we know now?
- Is the design still the right design given what we've learned?
