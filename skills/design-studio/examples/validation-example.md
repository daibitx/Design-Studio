# Validation Example — Payment Retry System

> Annotated example of a Validation review. Shows how Design Studio evaluates implementation against design without rewriting code.

---

## Context

**DS:** Reviewing `RetryWorker.ts` against Design Spec §7 (Architecture) and §15 (Key Decisions).

The design calls for:
- Worker pulls from MongoDB (Decision #2)
- Stripe idempotency keys for safety (Decision #3)
- At-most-once execution (FR-3)

---

## Finding #1: Polling Implementation

| Field | Value |
|---|---|
| **Category** | Alignment ✅ |
| **Severity** | — |
| **Location** | `RetryWorker.ts:45` |

**What's Happening:**
The worker polls MongoDB with `findOneAndUpdate` using a `PENDING → IN_FLIGHT` atomic transition, exactly as the design intended.

**What the Design Says:**
Decision #2 specified worker pulls from DB. The atomic state transition implements the at-most-once guarantee (FR-3).

**Why It Matters:**
This is correct. The atomic `findOneAndUpdate` prevents two workers from picking up the same retry — a clean, database-level concurrency control that doesn't require a distributed lock. Good implementation of the design.

**Suggestion:**
None — aligned.

---

## Finding #2: Missing Idempotency Key on Retry

| Field | Value |
|---|---|
| **Category** | Design Drift 🟠 |
| **Severity** | Critical |
| **Location** | `RetryWorker.ts:112` |
| **Design Reference** | Design Spec §15, Decision #3 |

**What's Happening:**
```typescript
// Actual implementation
const result = await stripe.paymentIntents.confirm(paymentIntentId);
```
The retry calls Stripe without passing an idempotency key, even though `RetryAttempt.idempotency_key` exists in the data model.

**What the Design Says:**
Decision #3 explicitly chose Stripe idempotency keys as the safety mechanism: "Stripe already provides the guarantee; no need to build our own."

**Why It Matters:**
If the Stripe call succeeds but the response is lost (network partition after Stripe processes but before we receive the 200), the retry will be re-queued and `confirm()` will be called again. Without an idempotency key, Stripe may process the charge twice. This is exactly the double-charge scenario FR-3 was designed to prevent.

The `idempotency_key` field exists in the schema — it was designed for this purpose — but it's not being used. This creates a real financial risk under network instability.

**Suggestion:**
Pass `RetryAttempt.idempotency_key` as the `idempotency_key` parameter in the Stripe API call. The idempotency key is already generated and persisted — it just needs to be wired through to the API call. Stripe guarantees idempotency for keys within 24 hours, which aligns with Constraint #3.

---

## Finding #3: Retryable Error Classification Too Broad

| Field | Value |
|---|---|
| **Category** | Design Flaw 🔴 |
| **Severity** | Important |
| **Location** | `FailureClassifier.ts:30-45` |
| **Design Reference** | Design Spec §3, FR-1 |

**What's Happening:**
The classifier maps all `5xx` and `network_error` to TRANSIENT, and everything else to PERMANENT. The implementation matches the design exactly.

However, during implementation, the developer discovered that Stripe returns `402 card_declined` alongside `429 rate_limit` — and our classifier treats both as PERMANENT.

**What the Design Says:**
FR-1 says: "All Stripe error codes are mapped; unknown codes default to TRANSIENT with alert."

**Why It Matters:**
A `429 rate_limit` is a transient error — waiting and retrying is the correct response. Treating it as PERMANENT means we lose transactions that would succeed on retry. The design's error classification table was incomplete — it didn't account for rate limiting.

This is a case where implementation revealed a design gap. The design should be updated, not the implementation criticized.

**Suggestion:**
Update Design Spec FR-1: add `429 rate_limit` to the TRANSIENT classification. The implementation can then follow the updated spec. Also consider: should we periodically audit Stripe's error code documentation to catch new codes?

---

## Summary

| Metric | Count |
|---|---|
| Findings Total | 3 |
| Alignment | 1 |
| Design Drift (Critical) | 1 |
| Design Flaw (Important) | 1 |

**Overall Assessment:** One critical gap (missing idempotency key) and one design improvement identified. The core architecture is sound and well-implemented. Fix Finding #2 before production deployment. Update design for Finding #3.

---

## What Makes This Good Validation

1. **Celebrates alignment** — Finding #1 acknowledges good work, not just problems
2. **Specific and actionable** — Finding #2 names the exact location, the missing parameter, and why it matters
3. **Blameless** — Finding #3 recognizes the design was incomplete, not the implementation was wrong
4. **No code rewriting** — Suggestions say what to do, not how to write it
5. **Prioritized** — Critical vs. Important is clear; the developer knows what to act on first
