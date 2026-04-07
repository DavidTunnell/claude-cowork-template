# CloudSee Drive: Automated Lead-to-Outreach Pipeline

**Date:** March 31, 2026
**Author:** David Tunnell (with Claude)
**Goal:** A system that automatically discovers leads, enriches them, drafts multi-channel outreach (email + LinkedIn + community), queues for human approval, then sends and tracks — running continuously with minimal manual effort.

---

## System Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                        LEAD DISCOVERY (Automated, runs daily)       │
│                                                                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐              │
│  │ Intent Signal │  │  Competitor  │  │  Community   │              │
│  │  Monitoring   │  │  Watchers    │  │  Listeners   │              │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘              │
│         └─────────────────┼─────────────────┘                      │
│                           ▼                                         │
│              ┌─────────────────────┐                                │
│              │  Raw Lead Queue     │                                │
│              └──────────┬──────────┘                                │
└─────────────────────────┼───────────────────────────────────────────┘
                          ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   ENRICHMENT & SCORING (Automated)                  │
│                                                                     │
│  Company data → Tech stack → Contact finding → ICP score            │
│                                                                     │
│  Score < threshold → Archive                                        │
│  Score ≥ threshold → Pass to outreach                               │
└─────────────────────────┼───────────────────────────────────────────┘
                          ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   OUTREACH GENERATION (Automated)                   │
│                                                                     │
│  For each qualified lead, AI drafts:                                │
│  • Personalized cold email (+ 2 follow-ups)                        │
│  • LinkedIn connection request + follow-up message                  │
│  • Community response (if lead came from a forum post)              │
│                                                                     │
│  Each draft includes: lead context, why they're a fit,              │
│  personalization notes, and the message itself                      │
└─────────────────────────┼───────────────────────────────────────────┘
                          ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   APPROVAL QUEUE (Human in the loop)                │
│                                                                     │
│  Scott/Patrick sees a daily digest:                                 │
│  • 5-15 leads with context cards                                    │
│  • Pre-written messages for each channel                            │
│  • One-click: Approve / Edit / Skip / Snooze                       │
│                                                                     │
│  Approved → auto-scheduled for send                                 │
│  Edited → saved and scheduled                                       │
│  Skipped → archived with reason                                     │
│  Snoozed → re-queued for next week                                  │
└─────────────────────────┼───────────────────────────────────────────┘
                          ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   SEND & TRACK (Automated after approval)           │
│                                                                     │
│  • Emails sent via warmed sending infrastructure                    │
│  • LinkedIn messages sent (semi-automated, see notes)               │
│  • Community posts published                                        │
│  • Opens, clicks, replies tracked                                   │
│  • Follow-ups auto-triggered on schedule                            │
│  • Replies flagged for human response                               │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Stage 1: Lead Discovery

Three automated funnels running on daily/hourly schedules.

### 1A. Intent Signal Monitoring

Finds companies showing signs they need CloudSee Drive before they start searching.

**Signals to watch:**

| Signal | What it means | How to capture |
|--------|--------------|----------------|
| Job postings mentioning S3, cloud storage, data migration | Company is scaling AWS storage | Trigify, or custom scraper on Indeed/LinkedIn Jobs API |
| Companies posting about AWS migration | They'll have S3 management needs soon | Google Alerts + Clay enrichment |
| Teams hiring "Cloud Engineer" or "DevOps" without a large eng org | Small team, big storage needs = ideal ICP | Trigify filtered by company size 50-500 |
| G2/Capterra reviews of S3 tools (Cyberduck, S3 Browser, etc.) | Actively using a competitor, may be dissatisfied | PhantomBuster scraper on review sites |

**Recommended tool:** Trigify ($150-300/mo) for job posting signals. It monitors hiring intent and exports leads with company + contact data. This alone generates 50-200 leads/week for a niche like S3 management tools.

**Custom alternative:** A scheduled Lambda that scrapes LinkedIn Jobs API or Indeed for keywords ("S3", "cloud storage manager", "AWS file management") and pipes results into your pipeline. Cheaper but more maintenance.

### 1B. Competitor Watchers

Finds people already using or evaluating competing tools.

**Sources:**
- G2 and Capterra reviews of Cyberduck, S3 Browser, Mountain Duck, Filezilla Pro, DragonDisk
- AWS Marketplace reviews of competing S3 management tools
- Twitter/X mentions of competitor tools + frustration keywords ("slow", "broken", "alternative", "looking for")

**How it works:** PhantomBuster ($56/mo) scrapes reviewer profiles from G2/Capterra. Clay enriches them with company data and email. Claude API classifies each as high/medium/low fit based on review content + company profile.

### 1C. Community Listeners

Finds people actively asking questions that CloudSee Drive answers.

**Platforms to monitor:**
- Reddit: r/aws, r/devops, r/sysadmin, r/cloud
- Stack Overflow: tags [amazon-s3], [aws], [cloud-storage]
- AWS re:Post
- Hacker News (Show HN and Ask HN about storage/file management)

**How it works:** A scheduled script (could be a Lambda running every 4 hours) uses each platform's API or RSS feed. Claude API classifies each post: "Is this person describing a problem CloudSee Drive solves?" If yes, it drafts a helpful response and queues it for approval.

**Important:** Community responses must lead with genuine help, not sales pitches. The AI prompt should be tuned to answer the question first, mention CloudSee Drive only when it's actually relevant, and never sound like marketing copy.

---

## Stage 2: Enrichment & Scoring

Every raw lead gets enriched automatically before a human ever sees it.

### Enrichment Stack

| Data point | Source | Why it matters |
|-----------|--------|---------------|
| Company name, size, industry | Clay / Apollo / Clearbit | ICP filtering |
| Tech stack (do they use AWS?) | BuiltWith / Wappalyzer via Clay | Disqualifies non-AWS companies |
| Contact name, title, email, LinkedIn | Apollo / Hunter.io / Clay | Outreach targeting |
| Company news (funding, growth, migration) | Clay / Google News API | Personalization hooks |
| Existing S3 usage indicators | Job postings, GitHub repos, tech blog posts | Buying intent signal |

### ICP Scoring Model

Each lead gets a score from 0-100 based on weighted criteria:

| Criteria | Weight | Scoring |
|----------|--------|---------|
| Uses AWS (confirmed) | 25 | Yes = 25, Likely = 15, Unknown = 5, No = 0 |
| Company size 50-500 employees | 20 | In range = 20, 500-2000 = 10, <50 or >2000 = 5 |
| Industry with heavy file needs | 15 | Media/Legal/Healthcare/Finance = 15, Engineering = 10, Other = 5 |
| Signal strength | 20 | Active search/review = 20, Job posting = 15, Community post = 10, Lookalike = 5 |
| Contact is decision-maker | 10 | VP/Director/CTO = 10, Manager = 7, IC = 3 |
| Recency of signal | 10 | <7 days = 10, <30 days = 7, <90 days = 3 |

**Threshold:** Score ≥ 50 → passes to outreach generation. Score 30-49 → added to nurture list. Score < 30 → archived.

---

## Stage 3: Outreach Generation

For each qualified lead, Claude API generates personalized messages across all three channels.

### Email Sequence (3-touch)

The AI gets a prompt with the full lead context (company, contact, signal source, industry, ICP score breakdown) and generates:

**Email 1 — The Hook (Day 0)**
Personalized to their specific signal. If they came from a job posting about S3 migration, the email talks about that. If from a competitor review, it addresses the specific complaint they had.

**Email 2 — The Value (Day 4)**
Shares a relevant use case or feature that maps to their situation. Links to a specific part of cloudseedrive.com or a case study.

**Email 3 — The Close (Day 9)**
Short, direct. "Still relevant? Here's a free trial link. Takes 5 minutes to connect your first bucket."

### LinkedIn Message (2-touch)

**Connection Request:** One sentence referencing something specific about them or their company. No pitch.

**Follow-Up (after connection accepted):** Brief, value-first message. Mentions CloudSee Drive in context of their work, offers the free trial.

### Community Response (1-touch, only for community-sourced leads)

A genuine, helpful response to their forum post that answers the question and mentions CloudSee Drive as one option (not the only one). Structured to be upvoted, not reported.

### Prompt Engineering

The outreach generation prompt should include:

```
You are drafting outreach for CloudSee Drive, a browser-based interface for Amazon S3 storage.

LEAD CONTEXT:
{enriched lead data}

SIGNAL SOURCE:
{how we found them and what triggered the signal}

RULES:
- Never lie or exaggerate features
- Reference something specific to THIS person/company — generic = delete
- Keep emails under 100 words
- LinkedIn connection requests under 30 words
- No buzzwords: "leverage", "synergy", "cutting-edge", "game-changer"
- Sound like a helpful peer, not a sales robot
- Always include the free trial angle (30 days, no credit card via AWS Marketplace)
- For community posts: answer the question FIRST, mention CloudSee only if relevant
```

---

## Stage 4: Approval Queue

This is where Scott or Patrick spends 15-20 minutes per day.

### Daily Digest Format

Delivered every morning via email (or a simple web dashboard). Each lead gets a card:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEAD: Sarah Chen, VP Engineering @ DataFlow Inc.
SCORE: 78/100 | SOURCE: Job posting (S3 Data Engineer role)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
COMPANY: 120 employees, Series B, healthcare data analytics
WHY THEY FIT: Confirmed AWS user, scaling S3 storage, no internal
tools team, industry with compliance file management needs

📧 EMAIL DRAFT:
"Hi Sarah — saw DataFlow is hiring an S3 Data Engineer.
When teams hit that inflection point, managing access for
non-technical teammates becomes a real pain. We built CloudSee
Drive to solve exactly that — browser-based S3 with RBAC and
sub-second search across millions of objects. Free 30-day trial
on AWS Marketplace if it's worth a look."

💼 LINKEDIN DRAFT:
"Hi Sarah — DataFlow's work in healthcare analytics looks
fascinating. Would love to connect."

[✅ Approve All] [✏️ Edit] [⏭️ Skip] [⏰ Snooze 1 week]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Implementation Options for the Approval Queue

**Option A — Email-based (simplest):**
Daily digest email with approve/skip links. Each link triggers a webhook that moves the lead to "send" or "archive." Fastest to build, lowest friction.

**Option B — Spreadsheet-based:**
Google Sheet where each row is a lead. Scott marks a column "Approve" / "Skip" / "Edit." A scheduled script reads the sheet and processes approved leads. Easy to build, familiar interface.

**Option C — Simple web dashboard:**
React app (you already have the stack) with the card layout above. Most polished, but most effort to build. Consider this for v2.

**Recommendation:** Start with Option A (email digest). Migrate to Option C once volume justifies it.

---

## Stage 5: Send & Track

Once approved, messages are auto-sent and tracked.

### Email Sending

**Tool: Instantly ($30-97/mo)**
Handles cold email at scale with built-in warmup, deliverability optimization, and reply detection. Connects to any email account. Tracks opens, clicks, and replies.

**Why not send from SES directly?** Cold email from your main domain risks deliverability. Instantly manages separate sending domains, warms them up, and rotates across accounts. Worth the cost.

**Setup:** Create 2-3 secondary domains (e.g., cloudseedrive.io, getcloudsee.com) with forwarding to the main domain. Instantly warms them over 2 weeks, then starts sending.

### LinkedIn Sending

**Semi-automated approach:** Full LinkedIn automation violates ToS and gets accounts banned. Instead:

- AI generates the message and copies it to clipboard
- A browser extension (or Clay's LinkedIn integration) opens the right profile
- Human clicks "Send" — but the message is pre-written

This keeps the human-in-the-loop requirement you already wanted for approvals, and the 5 seconds per send is worth not getting Scott's LinkedIn banned.

**Alternative:** Dripify or Expandi ($99/mo) automate LinkedIn within "safe" limits. They work until they don't. Use at your own risk.

### Community Posting

**Manual with AI assist.** Community responses get approved, then Scott posts them from his personal accounts. This must stay human — automated community posting gets detected and destroys credibility.

### Tracking & Follow-Up

| Event | Automated action |
|-------|-----------------|
| Email opened but no reply | Send Email 2 on Day 4 |
| Email 2 opened, no reply | Send Email 3 on Day 9 |
| Email replied | Flag for Scott, pause sequence |
| LinkedIn connection accepted | Queue follow-up message for approval |
| LinkedIn message replied | Flag for Scott |
| No engagement after full sequence | Move to "nurture" list, re-engage in 60 days |
| Trial signup detected | Trigger trial nurture sequence (separate flow) |

---

## Recommended Tool Stack

| Layer | Tool | Cost/mo | Role |
|-------|------|---------|------|
| Intent signals | Trigify | $150-300 | Job posting monitoring |
| Competitor scraping | PhantomBuster | $56 | G2/Capterra/LinkedIn scraping |
| Enrichment & orchestration | Clay | $150-500 | The glue — enriches, scores, triggers |
| AI drafting | Claude API | ~$20-50 | Message generation, classification |
| Cold email | Instantly | $30-97 | Sending, warmup, tracking |
| LinkedIn | Manual + Clay | $0 | AI drafts, human sends |
| Community monitoring | Custom script | $0 (Lambda) | Reddit/SO/re:Post monitoring |
| CRM / tracking | HubSpot Free or Google Sheets | $0 | Lead status tracking |

**Total estimated cost: $400-1,000/month**

At CloudSee Drive's price point, one converted customer likely pays for 6-12 months of this entire stack.

---

## Build Sequence

### Week 1-2: Foundation
- Sign up for Clay + Instantly
- Set up 2-3 secondary sending domains, start warmup
- Build the ICP scoring model in Clay
- Write the Claude API prompt templates for email/LinkedIn/community
- Set up a Google Sheet as the initial CRM

### Week 3-4: First Channel (Email)
- Configure Trigify → Clay pipeline for job posting signals
- Build the enrichment waterfall in Clay
- Generate first batch of leads, manually review quality
- Set up the email approval digest (even if it's just a formatted email from Clay)
- Approve and send first 20-30 emails
- Measure: deliverability, open rate, reply rate

### Month 2: Add LinkedIn + Competitor Signals
- Add PhantomBuster for G2/Capterra scraping
- Build LinkedIn message drafting into the pipeline
- Scott starts sending LinkedIn messages from AI-drafted copy
- Add competitor review signals as a lead source

### Month 3: Add Community + Optimize
- Deploy the community monitoring Lambda (Reddit, SO, re:Post)
- Build the community response approval flow
- Analyze what's working: which signals produce the best leads? Which messages get replies?
- Refine ICP scoring based on actual conversion data
- Consider building the web dashboard (Option C) if volume justifies it

---

## Key Metrics to Track

| Metric | Target (initial) | Why it matters |
|--------|-----------------|---------------|
| Leads discovered/week | 30-50 | Is discovery casting a wide enough net? |
| Leads scoring ≥ 50/week | 10-20 | Is the ICP filter right? |
| Approval rate | > 70% | If Scott skips most leads, scoring needs tuning |
| Email open rate | > 40% | Subject lines and deliverability |
| Email reply rate | > 5% | Message quality and targeting |
| LinkedIn accept rate | > 25% | Profile targeting |
| Trial signups from outreach | 2-5/month | The number that actually matters |
| Outreach → paid conversion | Track from month 2 | The number that matters most |

---

## What This Doesn't Cover (Yet)

- **Trial nurture sequences** — once someone starts a trial, that's a separate automated flow based on product usage. See the earlier brainstorm doc (#2) for that concept.
- **Inbound from content/SEO** — this pipeline is for outbound. Scott's SEO work feeds a different funnel.
- **Paid ads** — could layer in later as a signal source (who clicked an ad but didn't sign up → feed into outreach).
- **Partner outreach** — similar pipeline structure but different messaging and targeting. Could reuse 80% of this infrastructure.

---

## Open Questions for Patrick/Scott

1. **Who owns the daily approval queue?** Scott seems natural, but does Patrick want visibility?
2. **Do we have secondary domains available?** Need 2-3 for cold email warmup.
3. **What's Scott's current LinkedIn activity level?** Determines how much LinkedIn outreach volume is safe.
4. **Is there a CRM in place, or are we starting from scratch?** HubSpot Free would work, or we can start with a Sheet.
5. **What's the CloudSee Drive monthly price point?** Need this for ROI modeling on the tool spend.
