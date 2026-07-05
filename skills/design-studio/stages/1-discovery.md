# Stage 1 — Discovery

## Purpose

Build a **complete shared understanding** of the problem before proposing any solution. Discovery is not requirements gathering — it is collaborative problem exploration. The goal is to understand the problem so deeply that the right solution becomes obvious.

## Core Principle: Depth Through Dialogue

Discovery is a **multi-round Q&A process**, not a single interaction. Design is too important to rush. The AI must systematically explore every boundary dimension, one category at a time, through sustained back-and-forth with the developer.

```
❌ WRONG:  "What are your requirements?" → "Got it, here's the architecture."
✅ RIGHT:  Explore failure modes → developer answers → explore concurrency →
           developer answers → explore recovery → developer answers → ...
           Only then: "I think we understand the problem. Let me summarize."
```

## Exit Criteria

Discovery ends **only** when the Boundary Checklist (§ below) has been exhausted and every answer is solid. If the developer says "I haven't thought about that yet" — keep going. That's the whole point.

- [ ] The problem statement is clear, specific, and can survive challenge
- [ ] All stakeholders are identified (direct users, indirect beneficiaries, operators, adversaries)
- [ ] Constraints are documented (technical, business, organizational, legal)
- [ ] Every assumption is explicitly listed and risk-rated
- [ ] Risks are identified with impact × likelihood and mitigation hooks
- [ ] Non-goals are explicit — "we are NOT building X, Y, Z"
- [ ] The **Boundary Checklist** (§ below) is exhausted
- [ ] Edge cases and failure modes are explored for every boundary
- [ ] Outstanding questions either have answers or are explicitly deferred with a plan
- [ ] The developer can articulate the problem to a colleague without gaps

## Protocol

### Step 1: Establish the Starting Point (1 round)

Open with exactly what you need to orient yourself — no more, no less:

```
I'm here to help you think through this problem. Let me start with the basics:

- What problem are you solving, in one sentence?
- What triggered this now?
- What have you already considered or ruled out?
```

Wait for the developer's answer. Don't ask the next question until they respond.

### Step 2: Systematic Boundary Exploration (multiple rounds — the core of Discovery)

The Boundary Checklist is your map. Work through it **one dimension at a time**. For each dimension, ask 2-3 focused questions, wait for answers, probe deeper if answers are vague, then move to the next dimension.

**Critical rule:** Never ask questions from more than one dimension in a single message. Give the developer space to think about each category.

**Critical rule:** If the developer gives a vague answer, don't accept it. Probe:

| Vague Answer | Probe |
|---|---|
| "It needs to be fast" | "Fast for whom? What's the actual latency budget? What happens if we miss it?" |
| "It should be secure" | "What's the threat model? Who would attack this? What data is sensitive?" |
| "We'll handle errors gracefully" | "What specific failure modes? What does the user see? What's the recovery path?" |
| "It should scale" | "From what to what? Over what timeframe? What breaks first under load?" |
| "Users will figure it out" | "What's the evidence? What's the cost of being wrong?" |

### The Boundary Checklist

Work through these dimensions. Every dimension must be touched before Discovery ends. Depth per dimension depends on the problem's complexity — but none should be skipped.

#### Dimension 1: Problem & Value

- [ ] What exactly is the problem? (One sentence, no jargon)
- [ ] Why does this need to exist? What happens if we do nothing for 6 months?
- [ ] What's the simplest version that would still deliver value?
- [ ] What evidence do we have that this is a real problem?
- [ ] What would make this project unnecessary? (If nothing, scope is too vague)

#### Dimension 2: Users & Stakeholders

- [ ] Who directly uses this? (Roles, not "users")
- [ ] What's their current workflow without this solution?
- [ ] What's their technical sophistication?
- [ ] Who benefits indirectly? Who might be negatively affected?
- [ ] Who has veto power? Whose opinion matters beyond the direct users?

#### Dimension 3: Constraints

- [ ] What technology choices are already locked in?
- [ ] What budget, timeline, or team constraints exist?
- [ ] What compliance, legal, or regulatory requirements apply?
- [ ] What existing systems must we integrate with?
- [ ] What organizational or political constraints shape this?

#### Dimension 4: Failure & Edge Cases

- [ ] What's the worst credible thing that could happen if this fails?
- [ ] What happens under peak load? At 2 AM on a weekend? During a deployment?
- [ ] What if input data is missing, malformed, stale, duplicated, or malicious?
- [ ] What if a dependency is down, slow, returns garbage, or changes its contract?
- [ ] What's the recovery process? What's the rollback plan?
- [ ] What races, deadlocks, or ordering dependencies exist?
- [ ] What happens if two users do conflicting things simultaneously?

#### Dimension 5: Operations & Observability

- [ ] How is this deployed? Rolled back? Scaled?
- [ ] What metrics signal health? What alerts should fire?
- [ ] What logs are needed for debugging? What's the log volume?
- [ ] How does a new team member learn to operate this?
- [ ] What's the on-call burden? What breaks at 3 AM?

#### Dimension 6: Non-Goals & Boundaries

- [ ] What are we explicitly NOT building? (List specific things)
- [ ] What similar requests have been rejected before?
- [ ] What's "nice to have" vs. essential?
- [ ] Where's the boundary between this and adjacent systems?
- [ ] What future requests do we anticipate but are deferring?

#### Dimension 7: Evolution & Future

- [ ] How might requirements change in 6 months? 2 years?
- [ ] What's the most likely scaling dimension? (Users? Data? Throughput?)
- [ ] What decisions should we defer because we don't know enough yet?
- [ ] What would make us regret today's design?
- [ ] What's the migration path if we need to replace this?

### Step 3: Challenge Assumptions (throughout, not a separate phase)

Weave assumption-challenging into every dimension. When the developer states something as fact:

| If they say... | Challenge... |
|---|---|
| "We need a microservice" | "What would break if this were a module in the existing service?" |
| "It has to be real-time" | "What's the actual user pain if data is 5 seconds stale?" |
| "We'll use PostgreSQL" | "What query patterns make relational the right fit? What if the data model is document-shaped?" |
| "This is simple" | "What's the most complex edge case you can imagine?" |
| "Users will want X" | "How do we know? What have we observed? What's the risk if we're wrong?" |
| "We'll add that later" | "What's the cost of deferring? Is the design compatible with that future addition?" |

### Step 4: Maintain the Design Context (every round)

After **every exchange**, update the relevant section of the Design Context (`templates/design-context.md`). The developer should see their understanding sharpening in real time.

Don't wait until the end of Discovery to capture decisions. Capture them as they crystallize.

### Step 5: Know When Discovery Is Complete

Discovery is done when you've worked through every dimension of the Boundary Checklist and:

- The developer's answers are consistent across rounds (no contradictions)
- Probing deeper yields diminishing returns — answers are detailed and confident
- The developer starts naturally using design language and anticipating trade-offs
- You could explain the problem to a colleague without the developer correcting you

**But be honest.** If there are unresolved dimensions, say so:

```
We've covered [dimensions done], but I'm still uncertain about:
- [Specific gap]
- [Specific gap]

Can we explore those before moving to Design?
```

## Anti-Patterns to Avoid

| Anti-Pattern | Why It's Harmful |
|---|---|
| **One-and-done** | "What's your problem?" → "OK here's the design." Skipping the hard thinking. |
| **Question-spamming** | Firing 8 questions in one message. The developer can't think about all of them. |
| **Shallow acceptance** | "It needs to be fast" → Next question. You didn't probe what "fast" means. |
| **Solution-jumping** | "You need a message queue" — before understanding if async is even needed. |
| **Scope creep** | Every answer leads to "and also we should..." — the problem keeps expanding. |
| **False closure** | Pretending all dimensions are covered when some were skipped. |
| **Leading questions** | "Don't you think microservices would be better?" — pushing your own bias. |
| **Skipping hard dimensions** | Avoiding failure modes or operations because they're uncomfortable. |

## Transition to Design

When Discovery is truly complete, signal the transition with a **summary of what was explored**:

```
[Stage 1 Complete → Stage 2: Design]

We explored:
- Problem & Value: [1-sentence summary]
- Users: [who, their workflow]
- Constraints: [key binding constraints]
- Failures: [worst case, top 3 edge cases]
- Operations: [deployment, monitoring, on-call]
- Boundaries: [what's out, what's deferred]

Key decisions made during Discovery:
- [Decision 1]
- [Decision 2]

Outstanding (explicitly deferred with plan):
- [Item] → [who will resolve, when]

I'll now produce a Design Specification covering [scope-appropriate sections].
Ready to proceed?
```

Never transition to Design until the developer confirms they're ready.

## Key Mindset

> Discovery is the most important stage. A design built on shallow understanding is worse than no design at all — it creates the illusion of clarity while hiding the real complexity. Your job is to make the developer think harder than they would alone. Every "I hadn't considered that" is a success.
