# Anti-Patterns

> Common design mistakes, why they're harmful, and how to steer back. Use these to recognize bad patterns early and guide toward better alternatives.

---

## Discovery Anti-Patterns

### Solution-First Thinking

**What it looks like:** "We need a Kafka cluster" — said before anyone has described the problem.

**Why it's harmful:** Locks you into a solution before understanding requirements. The tool shapes the problem instead of the problem shaping the tool choice.

**How to steer back:** "Before we discuss technology, can you describe the problem this solves? What would happen if we didn't build anything?"

---

### Assumption-as-Fact

**What it looks like:** "Users will want this feature" — stated as certainty without evidence.

**Why it's harmful:** Designs built on unverified assumptions collapse on contact with reality.

**How to steer back:** "How do we know users want this? What have we observed? What's the risk if this assumption is wrong?"

---

### Scope Creep in Discovery

**What it looks like:** Every answer leads to "and also we should…" — the problem keeps expanding.

**Why it's harmful:** The problem becomes unbounded. No design can satisfy an ever-growing scope.

**How to steer back:** "Let's capture that as a future consideration. For now, can we agree on the minimum version that would be valuable?"

---

### Premature Precision

**What it looks like:** Debating milliseconds of latency before agreeing on what the system does.

**Why it's harmful:** Wastes time on details that may be irrelevant once the high-level design is clear.

**How to steer back:** "Let's establish the shape of the solution first, then optimize. The right architecture makes performance tuning easier later."

---

## Design Anti-Patterns

### Resume-Driven Development

**What it looks like:** Choosing technology because it's on the developer's learning wishlist, not because it fits the problem.

**Why it's harmful:** The team pays the complexity cost forever; the developer gets the resume line item.

**How to steer back:** "What's the simplest technology that meets our requirements? What would we choose if we had to maintain this for five years?"

---

### Spec-by-Implementation

**What it looks like:** The "design" is a prototype that sort of works, declared as the specification.

**Why it's harmful:** Prototypes optimize for speed of creation, not correctness or maintainability. The spec inherits prototype constraints.

**How to steer back:** "The prototype showed us what's possible. Now let's design what's right — treating the prototype as one data point, not the blueprint."

---

### Over-Abstraction

**What it looks like:** Every concrete need is met with a generic framework. "We'll build a plugin system" for two plugins.

**Why it's harmful:** Abstractions have a cost: indirection, learning curve, debugging difficulty. Pay that cost only when the abstraction earns its keep.

**How to steer back:** "Can we solve the concrete problem first? If a pattern emerges across 2-3 concrete cases, we can abstract then. It's easier to abstract from examples than from speculation."

---

### Buzzword-Driven Architecture

**What it looks like:** "We'll use Event Sourcing, CQRS, DDD, and gRPC" — for a CRUD app with 100 users.

**Why it's harmful:** Every architectural pattern has a complexity budget. Spend it where the problem demands it, not where the blog post recommends it.

**How to steer back:** "Which of these patterns directly address a requirement or constraint? Which are adding complexity without a clear payoff?"

---

### Analysis Paralysis

**What it looks like:** Every decision spawns three more decisions. The design never converges.

**Why it's harmful:** At some point, you know enough. Perfect is the enemy of shipped.

**How to steer back:** "We have enough information to make a reasonable decision. What's the worst case if we're wrong? Can we design so that reversing this decision is low-cost?"

---

## Planning Anti-Patterns

### Waterfall Pretense

**What it looks like:** A Gantt chart with every task perfectly sequenced, no feedback loops, no unknowns.

**Why it's harmful:** Software is discovered during implementation. Plans that don't acknowledge this set unrealistic expectations.

**How to steer back:** "Let's plan the first milestone in detail, subsequent milestones in outline. We'll update the plan as we learn."

---

### No Acceptance Criteria

**What it looks like:** Tasks that say "Implement the payment module" with no definition of done.

**Why it's harmful:** "Done" means different things to different people. Without shared criteria, tasks never truly finish.

**How to steer back:** "How will you know this is done? What would you test? What would make you confident saying 'this works'?"

---

### Dependency Ostrich

**What it looks like:** Every task marked as independent when they obviously depend on each other.

**Why it's harmful:** Teams start work they can't finish, context-switch constantly, and nothing integrates until the end.

**How to steer back:** "If Task B needs Task A's output to function, let's make that explicit. It's better to know the real dependency than to pretend it doesn't exist."

---

## Validation Anti-Patterns

### Rewrite Reflex

**What it looks like:** "This isn't how I would have done it" → rewriting the implementation.

**Why it's harmful:** Design Studio doesn't own the code. Rewriting disrespects the developer's choices and the context they had during implementation.

**How to steer back:** "The implementation differs from the design here. Can you walk me through why you chose this approach? There might be context I'm missing."

---

### Spec Worship

**What it looks like:** Treating the Design Specification as infallible scripture. Any deviation is wrong.

**Why it's harmful:** Implementation reveals things design didn't. Good engineers adapt. The spec should evolve with new understanding.

**How to steer back:** "This differs from the spec, but your approach might be better. Let's evaluate: does this solve the problem more effectively? Should we update the spec?"

---

### Finding-as-Criticism

**What it looks like:** Presenting every deviation as a failure. "You violated the design here, here, and here."

**Why it's harmful:** Creates defensiveness, not collaboration. The goal is alignment, not blame.

**How to steer back:** Frame findings neutrally: "Here's the design intent, here's the implementation, here's the gap. Some gaps are fine — let's figure out which ones matter."
