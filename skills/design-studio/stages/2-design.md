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

### Step 3: Embed Code Design Throughout the Spec

**This is not a separate step.** Code-level design must be woven into every relevant section of the specification. A spec that describes behavior only in prose and tables is incomplete — it forces the implementer to translate requirements into interfaces and types from scratch.

#### The Rule

**Every section that describes behavior MUST include corresponding code contracts.** The spec should answer not just "what does it do?" but "what does it look like in code?"

#### Section → Code Form Mapping

| Spec Section | Must Include | Optional (when clarifying) |
|---|---|---|
| Module Design | Public interfaces (full method signatures), DTOs/records with typed fields | DI registration snippet, constructor dependency list |
| API Design | Request/response DTOs, endpoint method signatures, error code enums | Auth attribute declarations |
| Data Model | Entity classes/records with field types + constraints, relationship navigation properties | Migration notes, index declarations |
| State Machines | State enums, transition table as code, `IStateMachine<TState, TEvent>` interface | Guard condition signatures |
| Sequence Flows | Key orchestrating method pseudocode (no bodies, just call order + data flow) | Concurrency primitive declarations |
| Error Handling | Error DTO/record hierarchy, error code enums, `Result<T>` or exception type tree | Retry policy pseudocode |
| Security | Permission code constants, auth attribute usage pattern | Token validation flow pseudocode |

#### Language-Aware Examples

Match the code to the developer's stack. Here are patterns for common languages:

**C# / .NET:**

```csharp
// ✓ Interfaces with full signatures
public interface ICaptchaService
{
    Task<CaptchaDto> GenerateAsync(int width = 300, int height = 150);
    Task<bool> ValidateAsync(string captchaId, int captchaX, int tolerancePx = 3);
}

// ✓ DTOs / Records with types
public record CaptchaDto(string CaptchaId, string BackgroundImage, string SliderImage, int SliderY);
public record LoginResult(bool Success, string? ErrorMessage, string? AccessToken);

// ✓ Enums for state machines
public enum LoginState { Idle, Editing, Validating, Authenticating, Authenticated }
public enum LoginEvent { FormFilled, LoginClicked, ValidationFailed, AuthSuccess, AuthFailed, NetworkError }

// ✓ Pseudocode for orchestration (no bodies)
async Task<LoginResult> HandleLoginAsync()
{
    var captcha = await _captchaService.GenerateAsync();  // GET /api/captcha
    // ... user fills form + slides captcha ...
    var token = await _authService.GetTokenAsync(username, password, captcha.Id, captcha.X);
    if (token is null) return LoginResult.Failed;
    await _accountStorage.SaveAsync(account);
    return LoginResult.Success(token);
}

// ✓ DI registration pattern
services.AddSingleton<ICaptchaService, CaptchaService>();
services.AddSingleton<IAccountStorage>(sp => new AccountStorage("accounts.db"));
```

**TypeScript:**

```typescript
// ✓ Interfaces
interface CaptchaService {
  generate(width?: number, height?: number): Promise<CaptchaDto>;
  validate(captchaId: string, captchaX: number, tolerancePx?: number): Promise<boolean>;
}

// ✓ DTOs
type CaptchaDto = { captchaId: string; backgroundImage: string; sliderImage: string; sliderY: number; };
type LoginResult = { success: true; accessToken: string } | { success: false; errorMessage: string };
```

**Go:**

```go
// ✓ Interfaces
type CaptchaService interface {
    Generate(ctx context.Context, width int, height int) (*CaptchaDto, error)
    Validate(ctx context.Context, captchaID string, captchaX int) (bool, error)
}

// ✓ Structs as DTOs
type CaptchaDto struct {
    CaptchaID       string `json:"captchaId"`
    BackgroundImage string `json:"backgroundImage"`
    SliderImage     string `json:"sliderImage"`
    SliderY         int    `json:"sliderY"`
}
```

**Python:**

```python
# ✓ Protocols (structural interfaces)
class CaptchaService(Protocol):
    async def generate(self, width: int = 300, height: int = 150) -> CaptchaDto: ...
    async def validate(self, captcha_id: str, captcha_x: int, tolerance_px: int = 3) -> bool: ...

# ✓ Dataclasses as DTOs
@dataclass
class CaptchaDto:
    captcha_id: str
    background_image: str
    slider_image: str
    slider_y: int
```

#### What NOT to Write

```csharp
// ✗ Method bodies with implementation logic
public async Task<bool> ValidateAsync(string captchaId, int captchaX, int tolerancePx)
{
    var session = await _store.GetAsync(captchaId);  // ← implementation
    if (session is null) return false;
    return Math.Abs(session.ExpectedX - captchaX) <= tolerancePx;
}

// ✗ Business logic or algorithms in full detail
// ✗ Database access code (LINQ queries, raw SQL)
// ✗ Complete constructors with body
```

**The test:** Could someone paste the interface into their IDE and have it compile? Yes — good. Could they paste a class and have it *work*? No — that's implementation.

### Step 4: Validate the Design

Before transitioning to Planning, self-check the design:

1. **Completeness** — Can someone implement from this without guessing?
2. **Consistency** — Do all parts of the design agree with each other?
3. **Feasibility** — Are there any known blockers?
4. **Testability** — Can each requirement be verified?
5. **Clarity** — Is every section written in plain, unambiguous language?
6. **Code Density** — Run this checklist against the spec you just produced:

| Check | If Missing, Add... |
|---|---|
| Does every module have a public interface? | `IModuleService` with full method signatures |
| Does every API endpoint have a request/response DTO? | Record/class with typed fields |
| Does every state machine have a corresponding enum? | `enum StateName { ... }` |
| Does every sequence flow have pseudocode? | Key method call chain, no bodies |
| Does error handling define error types/codes? | Error enum or `ErrorDetails` record |
| Do security considerations reference concrete attributes/permissions? | `[Authorize]`, permission code constants |
| Are there prose-only sections with no code at all? | Scan each section — if a section describes behavior with zero code, it's under-designed |

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

### Step 6: Write the Design Specification

When the developer approves the design, write it to `docs/designs/<design-name>/design-spec.md`. Then snapshot a copy to `docs/designs/<design-name>/history/YYYY-MM-DD-design-spec.md`.

**Update `docs/designs/INDEX.md`:** change this design's Stage to "Design" (or keep "Design" if already there). Update the Updated date.

**Update `docs/designs/<design-name>/design-context.md`:** set Stage=Design.

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
| **All-prose-no-code** | Every section is tables and bullet points. No interfaces, no DTOs, no pseudocode. The implementer has to design the code from scratch — that's the AI's job. |
| **Gold-plating** | Designing for hypothetical future needs at the cost of present simplicity |
| **Premature optimization** | Optimizing before the design is even validated |
| **Buzzword-driven design** | Choosing Event Sourcing because it's cool, not because it fits |
| **Spec-as-code** | Writing so much pseudocode it becomes implementation |
| **Copy-paste interfaces** | Duplicating the same method signature in multiple modules without explaining the contract difference |

## Transition to Planning

```
[Stage 2 Complete → Stage 3: Planning]

Design Specification is ready. It covers [sections included].
Key decisions: [summary of 2-3 most important decisions].

I'll now create an implementation roadmap.
```

## Key Mindset

> A good design spec is half prose, half code contracts. The prose explains why and what. The interfaces, DTOs, enums, and pseudocode define how things connect. If your spec reads like a requirements document, it's incomplete. If it reads like source code, you've gone too far. Aim for the engineer to say: "I can see exactly what to build — the types are here, the interfaces are here, I just need to fill in the bodies."
