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

<!-- For each module: responsibility, public interface, dependencies, key internal structure -->

### Module: [Name]

- **Responsibility:**
- **Public Interface:**
- **Dependencies:**
- **Key Internals:**

---

## 9. Data Model

<!-- Entities, relationships, constraints, key fields -->

### Entity: [Name]

| Field | Type | Constraints | Description |
|---|---|---|---|
| | | | |

**Relationships:**

```
[Entity A] --1:N--> [Entity B]
```

---

## 10. API Design

<!-- Endpoints, request/response schemas, error codes, auth -->

### Endpoint: `METHOD /path`

- **Purpose:**
- **Auth:**
- **Request:**
- **Response (Success):**
- **Response (Errors):**
- **Rate Limit:**

---

## 11. State Machines

<!-- For any entity or flow with complex state transitions -->

```
[State A] --event--> [State B] --event--> [State C]
```

| Current State | Event | Next State | Side Effects |
|---|---|---|---|
| | | | |

---

## 12. Sequence Flows

<!-- Key interactions between components -->

```
Client → API Gateway → Auth Service → Database
     ←              ←             ← Result
```

---

## 13. Error Handling Strategy

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
