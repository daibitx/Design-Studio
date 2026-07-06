# Design Specification: <design-name>

> Design Stage artifact. Saved to `docs/designs/<design-name>/design-spec.md`. Fill in sections appropriate to project scope. Delete sections that don't apply.

---

## 1. Project Goal

<!-- One paragraph. Anyone should understand what we're building and why. -->

---

## 2. Scope

### In Scope

1.

### Out of Scope (Non-Goals)

1.

---

## 3. Functional Requirements

| # | Requirement | Priority (P0-P3) | Acceptance Criteria |
|---|---|---|---|
| FR-1 | | P0 | |
| FR-2 | | | |

---

## 4. Non-Functional Requirements

| # | Category | Requirement | Measurement |
|---|---|---|---|
| NFR-1 | Performance | | |
| NFR-2 | Security | | |
| NFR-3 | Reliability | | |
| NFR-4 | Scalability | | |

---

## 5. Constraints

<!-- What we cannot change or must work within -->

| # | Constraint | Source | Impact |
|---|---|---|---|
| 1 | | | |

---

## 6. Assumptions

<!-- Verified and unverified assumptions the design depends on -->

---

## 7. Architecture Overview

<!-- System-level: services, communication patterns, data flows -->
<!-- Use text diagrams where helpful -->

```
[Component A] --gRPC--> [Component B] --SQL--> [Database]
```

---

## 8. Module Design

<!-- For each module: provide the interface, DTOs, and key pseudocode — not just bullet points. Every module section should contain at least one code block. -->

### Module: [Name]

- **Responsibility:**
- **Public Interface:** <!-- Paste the full interface definition (all method signatures) -->
- **DTOs & Types:** <!-- Records, enums, models this module exposes to consumers -->
- **Dependencies:** <!-- Constructor-injected interfaces (list the interface types) -->
- **DI Registration:** <!-- services.AddSingleton<IFoo, Foo>() — show the registration pattern -->
- **Key Internals:** <!-- Pseudocode for non-trivial orchestration logic; no method bodies -->

#### Interface

```csharp
public interface I[Name]
{
    // Full method signatures with parameter types and return types
}
```

#### DTOs & Types

```csharp
public record [DtoName](string Field1, int Field2);
public enum [EnumName] { Value1, Value2, Value3 }
```

#### Key Pseudocode

```csharp
// Orchestration / algorithm skeleton — call order, branching, no implementation details
```

---

## 9. Data Model

<!-- Define entities as code (classes/records), not just tables. Show field types, relationships, and constraints in the target language. -->

### Entity: [Name]

```csharp
// Entity class / record with typed fields, navigation properties, and constraint annotations
public class [Name]
{
    // Fields with types and constraints
}
```

**Relationships:**

```
[Entity A] --1:N--> [Entity B]
```

**Indexes & Constraints:**

<!-- Composite indexes, unique constraints, check constraints -->

---

## 10. API Design

<!-- Every endpoint needs: a DTO, a method signature, and error types. Not just a table. -->

### Endpoint: `METHOD /path`

- **Purpose:**
- **Auth:** <!-- [Authorize], [AllowAnonymous], specific policy -->
- **Request:**
  ```csharp
  public record [Xxx]Request(...);
  ```
- **Response (Success):**
  ```csharp
  public record [Xxx]Response(...);
  ```
- **Response (Errors):**
  ```csharp
  public enum [Xxx]ErrorCode { ... }
  // or: public record ErrorResponse(string Code, string Message, string? Details);
  ```
- **Rate Limit:**

---

## 11. State Machines

<!-- Express states as enums, transitions as code. ASCII diagrams are the summary — the enum is the spec. -->

```csharp
public enum [Entity]State { ... }
public enum [Entity]Event { ... }
```

| Current State | Event | Next State | Side Effects |
|---|---|---|---|
| | | | |

```
[State A] --event--> [State B] --event--> [State C]
```

---

## 12. Sequence Flows

<!-- For each flow: pseudocode showing the call chain, data transformation, and branching — no method bodies. -->

### Flow: [Name]

```csharp
// Pseudocode — caller to callee, data in and out, decision points
async Task<[Result]> [FlowName]([Input] input)
{
    var a = await _serviceA.GetXAsync(input.Id);     // step 1
    if (a is null) return [ErrorResult];
    var b = await _serviceB.ProcessAsync(a);          // step 2
    return new [Result](b);
}
```

```
Client → API Gateway → Service A → Database
     ←              ←          ← Result
```

---

## 13. Error Handling Strategy

- **Error Types:**
  ```csharp
  public enum ErrorCode { Validation, Authentication, NotFound, RateLimited, Internal }
  public record ErrorResponse(ErrorCode Code, string Message, string? TraceId);
  ```
- **Expected errors:** How are they communicated?
- **Unexpected errors:** How are they logged, surfaced, recovered?
- **Retry policy:**
- **Circuit breaking:**
- **Graceful degradation:**

---

## 14. Security Considerations

| Concern | Approach |
|---|---|
| Authentication | |
| Authorization | |
| Data Protection | |
| Input Validation | |
| Secrets Management | |

---

## 15. Key Design Decisions

| # | Decision | Alternatives Considered | Rationale | Trade-offs |
|---|---|---|---|---|
| 1 | | | | |

---

## 16. Technical Risks

| # | Risk | Impact | Mitigation |
|---|---|---|---|
| 1 | | | |

---

## 17. Future Extension Points

<!-- Places where the design anticipates future change -->

---

## 18. Open Questions

<!-- Questions that can be resolved during implementation without blocking the design -->
