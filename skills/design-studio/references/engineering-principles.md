# Engineering Principles

> First-principles reasoning toolkit. Use these to evaluate decisions, challenge assumptions, and articulate design rationale.

---

## 1. Separation of Concerns

> A module should have one reason to change.

**When to apply:** Evaluating module boundaries, deciding what belongs together.

**Probe:** "If X changes, how many modules are affected? Could we reduce that number?"

**Anti-signal:** A module whose name contains "and" (e.g., `UserAndPaymentManager`).

---

## 2. Single Responsibility

> Every component should do one thing well.

**When to apply:** Designing classes, functions, services.

**Probe:** "Can you describe what this does without using 'and' or 'or'?"

**Anti-signal:** A function with more than ~3 levels of abstraction mixing.

---

## 3. Least Knowledge (Law of Demeter)

> A module should only talk to its immediate friends.

**When to apply:** API design, object graph navigation.

**Probe:** "How deep does this code reach into its dependencies? Can it ask instead of reach?"

**Anti-signal:** `a.getB().getC().getD().doSomething()` — four dots of doom.

---

## 4. Explicit Over Implicit

> Make behavior obvious. Don't hide it.

**When to apply:** Configuration, framework design, error handling.

**Probe:** "Would a new team member understand this behavior by reading the code, or does it rely on hidden conventions?"

**Anti-signal:** "Magic" behavior that works differently depending on context that isn't visible at the call site.

---

## 5. Fail Fast

> Detect problems at the earliest possible moment.

**When to apply:** Input validation, startup checks, contract enforcement.

**Probe:** "If something is wrong, how quickly does the system surface it? At startup, at request time, or silently?"

**Anti-signal:** `catch (Exception e) { // ignore }` with no logging.

---

## 6. Idempotency

> Doing something twice should have the same effect as doing it once.

**When to apply:** Payment processing, API design, distributed systems.

**Probe:** "If this operation is executed twice (network retry, user double-click), what happens?"

**Anti-signal:** "Just make sure you don't call it twice" — if it can be called twice, it will be.

---

## 7. Backpressure

> Fast producers must not overwhelm slow consumers.

**When to apply:** Queue design, streaming systems, API rate limiting.

**Probe:** "What happens when the producer is 10x faster than the consumer? 100x?"

**Anti-signal:** Unbounded queues with no circuit breaker.

---

## 8. Graceful Degradation

> When something fails, the system should continue working at reduced capability.

**When to apply:** Service dependencies, feature flags, external API calls.

**Probe:** "If this dependency is down, does the entire system stop, or does it degrade gracefully?"

**Anti-signal:** A single dependency failure takes down the entire application.

---

## 9. Cheapest Thing That Works

> Prefer the simplest solution that meets requirements and constraints.

**When to apply:** Technology choices, architecture decisions.

**Probe:** "What's the simplest thing that could possibly work? Why isn't that enough?"

**Anti-signal:** "We might need it someday" as justification for added complexity.

---

## 10. Design for Deletion

> Code is a liability. Design so that removing things is easy.

**When to apply:** Module boundaries, service decomposition, feature flags.

**Probe:** "If we needed to remove this feature entirely, how many places would we touch?"

**Anti-signal:** A feature that's woven through 50 files with no clear boundary.

---

## 11. Loose Coupling, High Cohesion

> Things that change together should live together. Things that change separately should be separate.

**When to apply:** Module decomposition, service boundaries.

**Probe:** "Do these two things change for the same reasons? If not, why are they together?"

**Anti-signal:** A module that imports from every other module in the system.

---

## 12. Make Invalid States Unrepresentable

> Design types so that invalid combinations cannot be expressed.

**When to apply:** Data model design, type system usage, state machines.

**Probe:** "Can the type system prevent this bug? Is there a state that shouldn't exist but our types allow?"

**Anti-signal:** Multiple nullable fields where exactly one should be set — use a discriminated union instead.

---

## Using These Principles

When evaluating a design decision or implementation:

1. Identify which principles are relevant to the situation
2. Explain **why** they apply, not just **that** they apply
3. Acknowledge when principles are in tension (e.g., "Cheapest Thing That Works" vs. "Design for Deletion")
4. Recommend based on the specific context, not dogmatic adherence

> Principles are tools for reasoning, not rules for compliance.
