# Design Specification Example — Payment Retry System

> Abbreviated example. Real spec would be more detailed. Shows structure and level of detail expected.

---

## 1. Project Goal

Build a reliable payment retry mechanism that automatically recovers from transient payment gateway failures, reducing lost transactions by an estimated 95% without introducing double-charge risk.

## 2. Scope

### In Scope
- Classification of payment failures (transient vs. permanent)
- Configurable retry policy with exponential backoff
- Idempotent retry execution
- Retry state persistence across service restarts
- Monitoring and alerting on retry patterns

### Out of Scope
- Merchant-initiated manual retries
- Payment method fallback (try card A, then card B)
- Customer-facing retry status UI (separate feature)

## 3. Functional Requirements

| # | Requirement | Priority | Acceptance Criteria |
|---|---|---|---|
| FR-1 | Classify payment failures as TRANSIENT or PERMANENT | P0 | All Stripe error codes are mapped; unknown codes default to TRANSIENT with alert |
| FR-2 | Automatically retry TRANSIENT failures up to N times | P0 | Configurable per merchant; default: 3 retries over 1 hour |
| FR-3 | Guarantee at-most-once execution per payment attempt | P0 | No duplicate charges under concurrent retry + original completion |
| FR-4 | Survive service restarts without losing retry state | P1 | Pending retries resume within 30s of restart |
| FR-5 | Expose retry metrics (attempts, outcomes, latency) | P2 | Dashboard shows retry success rate, histogram by attempt number |

## 7. Architecture Overview

```
                         ┌──────────────┐
Payment Request ────────▶│ Payment API  │
                         └──────┬───────┘
                                │ TRANSIENT failure
                                ▼
                         ┌──────────────┐
                         │ Retry Queue  │──▶ Persisted (MongoDB)
                         └──────┬───────┘
                                │ scheduled
                                ▼
                         ┌──────────────┐
                         │ Retry Worker │──▶ Stripe API
                         └──────┬───────┘
                                │ result
                                ▼
                    ┌─────────────────────┐
                    │ Outcome (success /   │
                    │ permanent_fail /     │
                    │ exhausted)           │
                    └─────────────────────┘
```

## 9. Data Model

### Entity: RetryAttempt

| Field | Type | Constraints | Description |
|---|---|---|---|
| id | UUID | PK | Unique attempt identifier |
| payment_id | UUID | FK, indexed | Original payment identifier |
| attempt_number | int | 1..max_retries | Which attempt this is |
| status | enum | PENDING, IN_FLIGHT, SUCCESS, FAILED, EXHAUSTED | Current state |
| failure_classification | enum | TRANSIENT, PERMANENT | Cached from first failure |
| next_retry_at | timestamp | indexed, nullable | When to attempt next retry |
| idempotency_key | string | unique | For Stripe idempotency |
| created_at | timestamp | | |
| updated_at | timestamp | | |

## 10. API Design

### Endpoint: Internal — no new public API

The retry mechanism is transparent to callers. The existing `POST /payments` endpoint gains internal behavior:

- **TRANSIENT failure:** Payment is persisted with status `PENDING_RETRY`. Caller receives `202 Accepted` with `retry_token`.
- **PERMANENT failure:** Unchanged — caller receives appropriate 4xx.
- **Retry success:** Payment transitions to `COMPLETED`. Webhook fires as normal.
- **Retry exhausted:** Payment transitions to `FAILED`. Webhook fires with `retry_exhausted` reason.

## 15. Key Design Decisions

| # | Decision | Alternatives | Rationale | Trade-offs |
|---|---|---|---|---|
| 1 | MongoDB for retry queue (not Redis) | Redis + fallback, DB table | Must survive restarts; already operational with MongoDB | Higher latency vs Redis; acceptable given retry timescale (minutes, not ms) |
| 2 | Worker pulls from DB (not push-based events) | Pub/sub, Kafka | Simpler operational model; retry volume doesn't justify streaming infra | Must tune poll interval; slight delay in retry pickup |
| 3 | Stripe idempotency keys for safety | Custom distributed lock | Stripe already provides the guarantee; no need to build our own | Tight coupling to Stripe; acceptable since migration unlikely |
