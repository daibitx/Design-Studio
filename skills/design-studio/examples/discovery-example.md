# Discovery Example — Payment Retry System

> Annotated example of systematic multi-round Discovery. Shows how Design Studio works through the Boundary Checklist one dimension at a time, probing each deeply before advancing.

---

## Round 1: Establish Starting Point

**D:** We need to build a payment retry system. When a payment fails, we should retry it a few times before giving up.

**DS:** Let me understand the starting point. Three questions:

- In one sentence: what problem are we solving?
- What prompted this now? Was there an incident?
- What have you already considered or ruled out?

> *DS opens with exactly 3 orienting questions. Not 10. Not "what are your requirements."*

**D:** We're losing about 200 transactions a day to transient payment gateway failures. Last week we had a 45-minute Stripe outage and lost almost a thousand. We've already ruled out switching payment providers — we're locked into Stripe for the foreseeable future.

**DS:** Noted. So this is revenue recovery, not provider migration. A 45-minute outage losing ~1000 transactions gives us a rough scale.

*Updates Design Context: Problem Statement → "Recover revenue lost to transient Stripe failures (~200/day baseline, spike to ~1000 during outages)"*

---

## Round 2: Dimension 1 — Problem & Value

**DS:** Let me dig into the problem itself before we think about solutions.

- What's the actual revenue impact? Rough order of magnitude per lost transaction?
- What would a "good enough" solution look like? 90% recovery? 99%?
- If we did nothing for 6 months, would this get worse, stay the same, or become irrelevant?

> *DS begins Dimension 1. Three questions. Stays on this dimension.*

**D:** Average transaction is about $45, so 200/day is ~$9K, a Stripe outage day is ~$45K. A good solution should recover 95%+. If we do nothing, it'll get worse — we're growing 15% month over month.

**DS:** So annualized, this is roughly a $3M+ problem and growing. That tells me the design should be robust, not a quick script. A 95% recovery target means we can accept up to 5% permanent loss — helpful for scoping retry budgets.

*Updates Design Context: Value → "~$3M+/year and growing at 15% MoM. Target: 95%+ recovery."*

---

## Round 3: Dimension 2 — Users & Stakeholders

**DS:** Let's talk about who this touches.

- Who's the direct user of this system? (Not "the customer" — which team or service calls it?)
- Who gets paged if retries stop working at 3 AM?
- Is there a customer support workflow for permanently failed payments?

> *DS stays on Users & Stakeholders. Doesn't jump to architecture.*

**D:** The checkout service calls it. The payments team owns it. If retries stop, the payments on-call gets paged. Right now, support manually reaches out to customers whose payments fail — that's maybe 50 cases a day, and it's a terrible experience.

**DS:** So this also reduces manual support burden — 50 cases/day is real cost. And the on-call rotation for the payments team needs to understand this system. That affects how we design observability.

*Updates Design Context: Stakeholders → "Checkout service (caller), Payments team (owner + on-call), Support team (beneficiary)."*

---

## Round 4: Dimension 3 — Constraints

**DS:** Let's map what we can't change.

- You mentioned Stripe lock-in. Are there specific Stripe API versions or features we must use?
- What's the existing tech stack? What languages, databases, and infrastructure are already in place?
- Any compliance requirements? PCI-DSS obviously — anything else?

**D:** Stripe API version 2023-08. Our stack is TypeScript/Node.js, MongoDB, deployed on AWS ECS. PCI-DSS Level 2 — we don't store raw card data, but payment IDs are sensitive. No other regulatory constraints. The checkout service already has a 30-second timeout — we can't exceed that for the initial payment call.

**DS:** The 30-second checkout timeout is a key constraint. The retry mechanism has to be asynchronous — we can't make the user wait for retries. And MongoDB is already operational, which will matter when we think about where retry state lives.

*Updates Design Context: Constraints → "Stripe v2023-08, TS/Node/MongoDB/AWS ECS, PCI-DSS L2, checkout timeout ≤ 30s → retry must be async."*

---

## Round 5: Dimension 4 — Failure & Edge Cases (part 1 — the hard questions)

**DS:** Now the uncomfortable part. Let's talk about what can go wrong.

- What's the worst credible scenario? I'm thinking: double-charge. If our retry logic has a bug and charges the same customer twice, what's the blast radius?
- What happens if MongoDB (where retry state lives, presumably) is unavailable during a payment attempt?

> *DS starts the Failure dimension. Only 2 questions because these are heavy ones that deserve thought.*

**D:** Double-charge is the nightmare scenario. Even one instance is a customer trust issue plus regulatory liability. We need at-most-once guarantee, period. If MongoDB is down, the checkout still needs to work — payments shouldn't be blocked. Maybe we accept the loss and alert? That's better than blocking revenue.

**DS:** Good instinct. "Fail open" for the happy path (accept payment, queue retry later if persistence fails) vs. "fail closed" for retry execution (never risk double-charge). These are different failure modes with different answers.

> *DS doesn't solve it — just reflects it back with sharper framing.*

---

## Round 6: Dimension 4 — Failure & Edge Cases (part 2 — digging deeper)

**DS:** A few more edge cases:

- What happens if a retry succeeds but our system crashes before we record the success? The retry would re-queue. How do we prevent re-charging?
- What's Stripe's idempotency guarantee? How long does an idempotency key remain valid?
- What if someone manually refunds a payment while it's in the retry queue?

> *DS stays on Failures. This is the most important dimension — it deserves multiple rounds.*

**D:** Stripe idempotency keys are valid for 24 hours. If we use them, a retry replay is safe. Manual refund during retry... I hadn't thought of that. I guess the retry should check current payment state before attempting? Or refund should cancel pending retries?

**DS:** Both directions need thinking: retry checks state before acting, and refund cancels pending retries. That's a bidirectional dependency. Good catch — we'll need that in the design.

*Updates Design Context: Edge Cases → "Crash-after-success → idempotency key. Manual refund vs. pending retry → bidirectional state check needed."*

---

## Round 7: Dimension 5 — Operations & Observability

**DS:** Let's shift to operations.

- The payments on-call gets paged if retries stop. What does "retries stopped" look like? What metric triggers the page?
- What's the retry volume at peak? You said ~200/day baseline, but what's the burst during a Stripe outage?
- How long do we retain retry records? Is there a data retention policy?

**D:** We'd want to page if the success rate drops below some threshold — maybe <80% over 5 minutes. Peak retry volume during an outage could be the full transaction rate, about 1000/minute. We probably only need retry records for 30 days for debugging, then archive.

**DS:** 1000/minute is a meaningful throughput number. That'll inform whether we need a dedicated worker pool or can piggyback on existing infrastructure. And 80% over 5 minutes as a page threshold — noted.

*Updates Design Context: Operations → "Peak 1000 retries/min. Alert: <80% success over 5min window. Retention: 30d hot, archive after."*

---

## Round 8: Dimension 6 — Non-Goals & Boundaries

**DS:** Almost done with exploration. Let's define what we're NOT building.

- You mentioned no provider migration. What about payment method fallback — try credit card A, then credit card B?
- Should customers see retry status? "Your payment is being retried"?
- What about merchant-initiated manual retries from a dashboard?

**D:** No payment method fallback — that's a separate feature. Customer retry status UI is planned but owned by the frontend team, not this system. We just need to expose the state via API. Merchant manual retries are out of scope for now.

**DS:** Clear boundaries. The system exposes retry state to the frontend but doesn't own the UI. That's an interface contract we'll need in the design.

*Updates Design Context: Non-Goals → "No payment method fallback. No retry UI (expose state only). No merchant manual retry dashboard."*

---

## Round 9: Dimension 7 — Evolution & Future

**DS:** Last dimension. Let's think forward.

- You're growing 15% MoM. Does the design need to handle 10x current volume? 100x?
- If Stripe changes their idempotency guarantees, how coupled are we?
- If this becomes a general-purpose retry mechanism (not just payments), what would break?

**D:** 10x volume in the next 18 months is realistic. We should design for that but not over-optimize now. Stripe coupling is acceptable — migration would be a rewrite anyway. General-purpose retry? Hadn't considered it, but I could see order confirmation or email delivery using similar patterns.

**DS:** Interesting. We don't need to build a general-purpose retry framework now, but we should avoid baking payment-specific assumptions into the retry infrastructure. A clean separation between "retry mechanism" and "what to retry" would make future reuse cheaper.

*Updates Design Context: Evolution → "Plan for 10x volume (18mo). Keep retry mechanism decoupled from payment specifics. Stripe coupling acceptable."*

---

## Discovery Complete — Transition

**DS:**

```
[Stage 1 Complete → Stage 2: Design]

We explored all 7 dimensions over 9 rounds:
- Problem & Value: $3M+/year revenue recovery, 95%+ target, 15% MoM growth
- Users: Checkout service (caller), Payments team (owner/on-call), Support (beneficiary)
- Constraints: Stripe v2023-08, TS/Node/MongoDB/AWS ECS, PCI-DSS L2, 30s timeout
- Failures: Double-charge is nightmare, idempotency keys are safety net, manual refund vs. retry race
- Operations: 1000/min peak, alert at <80% success, 30d retention
- Boundaries: No method fallback, no retry UI, no merchant dashboard
- Evolution: 10x volume in 18mo, keep retry/payment decoupled

Key decisions:
1. Retry must be async (30s checkout timeout constraint)
2. At-most-once execution via Stripe idempotency keys
3. Fail-open for payment acceptance, fail-closed for retry execution

I'll now produce a Design Specification covering architecture, data model, API,
error handling, and key algorithms.

Ready?
```

**D:** Let's do it.

---

## What Makes This Good Discovery

1. **9 rounds, not 2.** The AI stayed in Discovery until every dimension was exhausted.
2. **One dimension at a time.** Never mixed failure questions with operations questions.
3. **2-3 questions per round.** The developer had space to think, not a wall of questions.
4. **Vague answers were probed.** "It should scale" → "From what to what? Over what timeframe?"
5. **The hard stuff was first.** Failure modes got 2 rounds because they're the riskiest dimension.
6. **Design Context evolved visibly.** After every round, decisions were captured.
7. **The AI never solved.** It reflected, reframed, sharpened — but never said "here's what you need."
8. **Developer discovered their own gaps.** "Manual refund during retry... I hadn't thought of that." — That's the point.
