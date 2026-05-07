# Project Brief: Spring 2027 Loyalty Program Relaunch

**Last Updated:** 2026-12-15

> *Example brief written at project kickoff (Dec 2026), before discovery began. Compare to `project-context.md` to see how the project evolved.*

---

## One-Sentence Vision

**Wildgate Trail Club turns our loyalty program from a transactional points dispenser into a tiered experience program that makes our top customers feel like part of a community, not a database.**

## Overview

Our existing loyalty program ("Wildgate Rewards") is a flat 1%-back points program launched in 2019. Engagement has dropped each of the last three years, and customer research suggests the program no longer differentiates us from competitors. This project replaces it with a four-tier experience program that mixes points, early access, exclusive content, and partner experiences. Targeting full launch April 15, 2027.

## Business Context

- **Sponsor:** Priya Shah, VP Marketing
- **Problem we're solving:** Loyalty program engagement has dropped from 38% (2022) to 21% (2026). Top-decile customers (representing 41% of DTC revenue) report on NPS surveys that the program "doesn't feel like Wildgate." Cost-per-acquired-member has tripled.
- **Cost of not doing it:** Continued churn of high-value customers to competitors with stronger programs (REI Co-op, Backcountry). Loyalty incentives currently cost ~$1.2M/year and the LTV lift is no longer justifying it.
- **Success criteria:**
  - 40%+ of program members engaging at least monthly within 6 months of launch (vs. 21% baseline)
  - Top-tier (Pathfinder) members showing >25% LTV lift vs. matched non-program control by EOY 2027
  - NPS score from program members >55 by Q4 2027 (vs. 42 baseline)
  - Customer Service ticket volume related to program <2% of total tickets (was 6% in Q3 2026)

## Scope

### What We're Doing

Replacing the existing Yotpo-powered points program with a Smile.io-powered tier program that has four named tiers, mixing points, access, content, and partner experiences. Includes brand identity ("Trail Club"), full Klaviyo email program rebuild, account dashboard redesign, customer service training, and migration of 240,000 existing members.

### Must Have (v1 / MVP — April 15 launch)

1. Four-tier program with named tiers, defined progression rules, and dashboard
2. Migrated 240k existing members with no point loss
3. Email program with welcome flow, tier-up notification flow, and monthly statement
4. Refreshed account dashboard with tier status, progression, and benefits
5. Tier 3+ "exclusive product access" benefit live for at least three SKUs
6. Customer Service team trained on the program and the migration

### Should Have (after v1)

1. Retail POS integration so in-store members earn and redeem (Phase 2, Q3 2027)
2. Tier 4 (Pathfinder) "partner experiences" with Trail Republic
3. Mobile push notifications via Klaviyo's mobile SDK
4. Referral mechanic tied to program (one-tier-up reward)

### Out of Scope

- B2B / wholesale customer loyalty (separate program, not part of this work)
- Subscription product offering (raised, deferred to 2028)
- Multilingual program copy (English-only for v1; Spanish considered for Phase 3)
- Gamification features (badges, streaks) — researched, did not move into scope based on customer feedback

## Approach

Phased delivery: Discovery (Jan) → Design (Feb) → Build (early Mar) → Pilot (Mar 17 - Apr 7) → Public launch (Apr 15) → Post-launch optimization (May+).

### Key Decisions

#### Loyalty platform: Smile.io vs. extending Yotpo

**Decision:** Replace Yotpo with Smile.io.

| Option | Pros | Cons | Effort / Cost |
|---|---|---|---|
| Stay on Yotpo + custom build | No migration risk; existing data in place | Tier UI requires custom dev; admin tooling weak; vendor relationship has been rocky | Med |
| Migrate to Smile.io | Native tier support; better admin tooling; well-rated by similar brands; Shopify-native | Migration risk for 240k members; vendor lock-in; new contract | High |
| Build in-house | Full control; long-term cost | Distracts engineering from product; no operational support; fragile | High |

**Why this one:** The migration is one-time pain; the platform difference is ongoing leverage. Smile.io's tier and admin features save us 200+ hours of custom dev annually.
**Tradeoffs:** We're locked into Smile.io for 3 years (contract). Migration of 240k member balances has acknowledged data-quality risk (~3% manual reconciliation budgeted).

#### Tier model: Points-based vs. spend-based vs. behavior-based

**Decision:** Hybrid — spend-based progression (gets you in) + behavior-based perks (keeps you active).

**Why this one:** Pure points/spend feels transactional, which research said is the existing problem. Pure behavior is hard to administer at our scale. Hybrid lets us reward both who buys and who's engaged.

## Tools & Stack

- **Primary tools:** Asana (project tracking), Slack, Notion (program wiki), Figma
- **Tech stack:** Shopify Plus, Klaviyo, Salesforce Service Cloud, Tableau, Smile.io (new), Yotpo (sunsetting)
- **Vendors / partners:** Smile.io (loyalty platform), Trail Republic (partner experiences for Tier 4), Boulder Photo Co. (brand assets)
- **Internal systems touched:** Customer accounts, CRM, email, reporting, retail POS (Phase 2 only)

## Timeline & Milestones

| Milestone | Target Date | Notes |
|---|---|---|
| Kickoff | 2026-12-15 | This brief |
| Discovery complete | 2027-01-30 | Customer research, competitive analysis, tier modeling |
| Design complete | 2027-02-28 | Brand identity, tier benefits final, legal sign-off |
| Build complete | 2027-03-14 | Smile.io live in staging, Klaviyo flows built, site changes deployed |
| Pilot launch | 2027-03-17 | Employees + 500 invited customers |
| Public launch | 2027-04-15 | All 240k existing members migrated, public sign-up open |
| Phase 2 (retail POS) | 2027-Q3 | In-store earn/redeem |

## Resources & Budget

- **Team:** Marketing (1.0 FTE Maya, 0.5 FTE Tom, 0.3 FTE Wei, 0.2 FTE Sam), Creative (0.4 FTE Devon), Ecommerce (0.4 FTE Aiden), Merch (0.2 FTE Hannah)
- **Budget:** $185k for v1 (Smile.io: $48k/yr platform; Boulder Photo: $35k; Trail Republic seed inventory: $40k; outside counsel: $12k; misc: $50k)
- **External:** Smile.io implementation team (vendor-provided), Trail Republic, outside counsel

## Risks & Open Questions

| Risk / Question | Likelihood | Impact | Mitigation / Owner |
|---|---|---|---|
| Yotpo migration data quality issues | Med | Med | Aiden + 1 wk contractor budgeted for reconciliation |
| Trail Republic partner experiences may need state-by-state legal review | Med | Low (delays Tier 4 only) | Outside counsel doing 50-state review now |
| Customer Service training not deep enough for Tier 3+ exceptions | High | Med (CSAT risk) | Maya + CS Director expanding training before pilot |
| Tier benefits not differentiating enough vs. competitors | Low | High (program flop) | Discovery research validated tier model; pilot will catch this before public launch |
| Migration day load on Klaviyo / Shopify | Low | High | Smile.io + ecommerce team load-testing in week before public launch |

## Links & Resources

- **Tracker:** [Asana - Spring 2027 Loyalty Relaunch](#)
- **Wiki / docs:** [Notion - Wildgate Trail Club](#)
- **Designs:** [Figma - Trail Club brand identity](#)
- **Strategic context:** [Q4 2026 customer research deck](#)
