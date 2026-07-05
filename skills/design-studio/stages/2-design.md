# Stage 2 — Design

## Purpose

Transform the understanding built in Discovery into a **structured, actionable Design Specification** that serves as the implementation blueprint. The spec should be detailed enough that an engineer unfamiliar with the conversation could implement from it.

## Exit Criteria

Design ends when **all** of these are true:

- [ ] The Design Specification covers all sections appropriate to scope
- [ ] Key design decisions are documented with rationale
- [ ] Trade-offs are explicitly acknowledged
- [ ] Interfaces and contracts are defined (where applicable)
- [ ] Data models are specified (where applicable)
- [ ] Error handling strategy is defined
- [ ] The developer confirms the design matches their intent
- [ ] No "we'll figure it out during implementation" gaps remain

## Protocol

### Step 1: Determine Scope

Before writing anything, agree on what level of design is needed:

| Scope | Include | Don't Include |
|---|---|---|
| **System** | Architecture, service boundaries, communication patterns, data flows | Individual module internals |
| **Service** | API surface, data model, internal modules, dependencies | Other services' internals |
| **Feature** | User flows, state machines, error states, component tree | System-level architecture |
| **Module** | Public interface, internal structure, algorithms, data structures | System context |
| **API** | Endpoints, request/response schemas, error codes, rate limiting | Implementation details |
| **Data Model** | Entities, relationships, constraints, indexes, migration strategy | Application logic |
| **Component** | Props/inputs, states, events, rendering logic, accessibility | Application architecture |

### Step 2: Write the Design Specification

Use `templates/design-spec.md` as the base structure. Fill in sections appropriate to scope.

#### Mandatory Sections (all scopes)

1. **Project Goal** — One paragraph that anyone can understand
2. **Scope** — What's in and what's explicitly out
3. **Functional Requirements** — What the system must do
4. **Key Design Decisions** — Decisions made and why

#### Scope-Dependent Sections

| Section | System | Service | Feature | Module | API | Data |
|---|---|---|---|---|---|---|
| Architecture Overview | ✓ | ✓ | — | — | — | — |
| Module Design | ✓ | ✓ | ✓ | ✓ | — | — |
| Data Model | ✓ | ✓ | ✓ | — | ✓ | ✓ |
| API Design | ✓ | ✓ | — | ✓ | ✓ | — |
| State Machines | — | ✓ | ✓ | — | — | — |
| Sequence Flows | ✓ | ✓ | ✓ | — | — | — |
| Error Handling | ✓ | ✓ | ✓ | ✓ | ✓ | — |
| Security Considerations | ✓ | ✓ | ✓ | — | ✓ | ✓ |

### Step 3: Generate Design-Oriented Code

When code clarifies design intent, use only these forms:

```typescript
// ✓ ALLOWED: Interfaces
interface PaymentProcessor {
  process(payment: Payment): Promise<PaymentResult>;
  refund(transactionId: string): Promise<RefundResult>;
}

// ✓ ALLOWED: DTOs / Schemas
type CreateOrderRequest = {
  customerId: string;
  items: OrderItem[];
  shippingAddress: Address;
};

// ✓ ALLOWED: Type definitions
type OrderStatus = 'pending' | 'confirmed' | 'shipped' | 'delivered' | 'cancelled';

// ✓ ALLOWED: Abstract classes (structure only)
abstract class EventBus {
  abstract publish<T extends Event>(event: T): Promise<void>;
  abstract subscribe<T extends Event>(type: string, handler: EventHandler<T>): Subscription;
}

// ✗ NOT ALLOWED: Concrete implementations
// ✗ NOT ALLOWED: Business logic
// ✗ NOT ALLOWED: Complete classes with method bodies
```

### Step 4: Validate the Design

Before transitioning to Planning, self-check the design:

1. **Completeness** — Can someone implement from this without guessing?
2. **Consistency** — Do all parts of the design agree with each other?
3. **Feasibility** — Are there any known blockers?
4. **Testability** — Can each requirement be verified?
5. **Clarity** — Is every section written in plain, unambiguous language?

### Step 5: Present for Approval

Present the Design Specification as a complete document. Ask:

```
This is the design I'm proposing based on our Discovery.
Before we move to Planning:

- Does this match what you had in mind?
- Is anything missing?
- Are there decisions you'd make differently?
- Is the scope right?

Once you're satisfied, I'll break this into an implementation plan.
```

## Design Decision Documentation

Every significant design decision must be documented with this structure:

```
Decision: [What we decided]
Alternatives considered: [What else we could have done]
Rationale: [Why this choice]
Trade-offs: [What we gain, what we sacrifice]
Future impact: [How this constrains or enables future decisions]
```

## Anti-Patterns to Avoid

| Anti-Pattern | Why It's Harmful |
|---|---|
| **Over-specifying** | Designing module internals for a system-level request |
| **Under-specifying** | "We'll use a database" — which one? What schema? |
| **Gold-plating** | Designing for hypothetical future needs at the cost of present simplicity |
| **Premature optimization** | Optimizing before the design is even validated |
| **Buzzword-driven design** | Choosing Event Sourcing because it's cool, not because it fits |
| **Spec-as-code** | Writing so much pseudocode it becomes implementation |

## Transition to Planning

```
[Stage 2 Complete → Stage 3: Planning]

Design Specification is ready. It covers [sections included].
Key decisions: [summary of 2-3 most important decisions].

I'll now create an implementation roadmap.
```

## Key Mindset

> A good design doesn't answer every question — it answers the right questions clearly and leaves implementation details to the implementer. If your spec reads like source code, you've gone too far. If it reads like a fortune cookie, you haven't gone far enough.
