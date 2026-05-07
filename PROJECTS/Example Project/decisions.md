# Decisions Log: Spring 2027 Loyalty Program Relaunch

> *A running log of significant decisions and the reasoning behind them. Lighter than a full ADR, heavier than a Slack message. Newest at top.*
> *Use this format on any project where you'll want to remember "why did we decide that?" six months later.*

---

## 2027-02-08 — Tier names and progression model finalized

**Decision:** Four tiers named Hiker, Climber, Summiteer, Pathfinder. Progression by trailing-12-month spend, with floor protection (you don't drop a tier until 90 days after the spend window closes).

**Why:**
- Discovery research (Jan) showed customers wanted *aspirational identity*, not generic "Silver/Gold/Platinum." The outdoor-themed names tested 2x better in concept testing.
- Trailing-12-month is industry standard and easier for customers to understand than calendar-year.
- Floor protection prevents a customer from being upset about losing their tier the day after a big spend window expires. Cost is small (modeled at <2% of program cost) and customer-trust impact is high.

**Alternatives considered:**
- 3 tiers (rejected: not enough differentiation between top customers)
- 5 tiers (rejected: top-tier becomes too rare to be aspirational)
- Calendar-year reset (rejected: confusing and feels punitive)

**Approver:** Priya Shah, VP Marketing

---

## 2027-01-22 — Smile.io selected over Yotpo extension and in-house build

**Decision:** Replace Yotpo with Smile.io as the loyalty platform.

**Why:**
- Yotpo's roadmap doesn't include the tier features we need; we'd be building on top of a platform that's drifting away from our use case.
- In-house was rejected because it pulls engineering from the product roadmap; the savings don't pay back in <3 years.
- Smile.io has 3 reference customers in our space who report >40% engagement lift after switching from points-only programs.

**Cost:** $48k/year platform + ~$15k one-time implementation (vendor team).

**Risk accepted:** 240k member migration with potential ~3% manual reconciliation. Budgeted Aiden + 1-week contractor.

**Approver:** Priya Shah, VP Marketing; Tess Howard, CFO (budget sign-off)

---

## 2027-01-15 — Out of scope: gamification (badges, streaks)

**Decision:** Don't include gamification mechanics in v1.

**Why:**
- Customer research found gamification ranked 6th of 7 desired features (below access, exclusive content, partner experiences, faster shipping, easier returns, and points themselves).
- Gamification adds 4-6 weeks to the build and is hard to undo if it doesn't land.
- Can be added in Phase 2 if engagement targets aren't being met.

**Reconsider when:** Q3 2027 readout if engagement is below 30%.

---

## 2027-01-08 — Out of scope: B2B / wholesale loyalty

**Decision:** B2B customers will not be eligible for the consumer Trail Club program.

**Why:**
- B2B accounts buy at wholesale pricing; reward economics don't work the same way.
- Wholesale team has indicated they want a separate program design tailored to dealers, not a tag-on to the consumer program.
- Treating them differently avoids the awkwardness of a dealer principal earning Pathfinder status from their store's purchases.

**Next step:** Wholesale team to scope a separate B2B loyalty initiative for FY28 planning.
