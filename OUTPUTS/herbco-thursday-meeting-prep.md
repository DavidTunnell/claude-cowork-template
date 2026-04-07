# Thursday Meeting Prep — First Developer Meeting with Adrian
## April 10, 2026

**Meeting purpose:** First direct interaction with Adrian, the solo developer who built and maintains iBizFusion over ~20 years.
**Attendees:** David (Webapper), likely Patrick, possibly Joy, Adrian
**Tone target:** Collaborative assessment, not audit interrogation

---

## Context and Stakes

Adrian has produced 25+ detailed documents about the system. He executed a full infrastructure upgrade (Win Server 2025, CF 2023, MySQL 8) and pushed 171 commits in Q1 2026. He clearly cares about this system and has been actively improving it.

However, we were hired by the investors (Beckway) to do an independent assessment — which means Adrian may feel like he's being evaluated or second-guessed. How we handle this first meeting sets the tone for the entire engagement. If Adrian becomes defensive or guarded, we lose access to 20 years of institutional knowledge. If he feels respected and heard, he becomes our biggest asset for Phase 1.

---

## Recommended Agenda (60-90 minutes)

### 1. Introductions and Framing (10 min)
**Goal:** Set the right tone immediately.

Key messages to convey:
- We've read his documentation — it's thorough and well-organized (genuine compliment, and true)
- We're here to help strengthen the platform, not replace him
- Our assessment will document things clearly so the business can make informed investment decisions
- We want his input on prioritization — he knows what matters most operationally

**Avoid:** Any framing that implies "we're here to find what's wrong." Frame it as "we're here to build a clear picture together."

### 2. System Walkthrough — Adrian Drives (20-25 min)
**Goal:** Let Adrian show us the system on his terms first.

Ask him to walk through:
- The main application screens and workflows
- How a typical order flows from entry to fulfillment
- The Salesforce integration in action
- His development and deployment process
- Any areas he's most proud of or considers strongest

**Why this matters:** This builds rapport and gives us context that static analysis can't provide. It also reveals his mental model of the system — what he considers important vs. what he glosses over.

**Listen for:** Does he mention security practices? Does he talk about testing? How does he describe his deployment process?

### 3. Architecture Discussion (15-20 min)
**Goal:** Understand the practical architecture from someone who lives in it daily.

Questions to ask:
- "Walk us through how `commonFunctions.cfm` evolved — what prompted the consolidation?"
- "When you make a change to a high-impact function like `updateOrderStatus`, what's your process for verifying nothing else breaks?"
- "What's the relationship between the .CFM templates and the .CFC components in practice? Is there a pattern for when logic goes where?"
- "Are there areas of the codebase you avoid touching because the risk is too high?"
- "What would you change about the architecture if you could start over?"

### 4. Infrastructure and Recent Upgrades (10 min)
**Goal:** Acknowledge his recent work and understand current state.

Questions:
- "The MySQL 5.6 to 8 migration — how did you identify which queries needed updating? Was it systematic or as-you-find-it?"
- "What's the current backup strategy? How often, where stored, ever tested a restore?"
- "The SiteLock WAF — what does it actually protect against in practice?"
- "Is the old server still running as rollback, or has that been decommissioned?"

### 5. Security Discussion — Handle With Care (15-20 min)
**Goal:** Share our findings without making it adversarial.

**Approach:** Don't start with "we found 221 unparameterized queries." Instead:

1. Start with what he's doing right: "We can see the authentication hardening work you did in Q1 — password hashing migration, CSRF on login, login throttling. That's solid recent work."

2. Introduce the static analysis: "We ran a static analysis across the full codebase and want to walk through what it flagged. Some of this may be stuff you've already addressed or are aware of — help us understand the current picture."

3. Share findings as data, not accusations:
   - "The scanner flagged 221 queries without CFQUERYPARAM. Are some of these in legacy code paths that aren't actively used?"
   - "We found some hardcoded credentials — is there a plan for centralizing those?"
   - "The CFMX_COMPAT encryption on stored card data — is that still in active use, or is it legacy?"

4. Ask for his remediation priorities: "If you had to pick the three most critical things to fix first, what would they be?"

**Watch for:** His response tells us a lot:
- If he says "I know about most of those" — he's aware but resource-constrained
- If he says "the scanner is wrong, those are all parameterized" — we need to dig deeper with specific examples
- If he says "I didn't know about the credit card encryption issue" — he's being honest about blind spots

### 6. Collaboration and Access (10 min)
**Goal:** Establish what we need for Phase 1.

Request:
- Repository access (read-only is fine initially)
- Database schema export (no production data)
- ColdFusion Admin access (read-only) to see datasources, mappings, scheduled tasks
- A list of all scheduled tasks and their purposes
- Access to server logs (error logs, slow query logs)
- His availability for follow-up questions (how does he prefer to communicate?)

### 7. Wrap-up (5 min)
- Summarize what we heard
- Confirm next touchpoints
- Thank him for the documentation — reiterate that it's saving significant time

---

## Questions We Need Answered (But May Not Ask Directly)

These are things we need to figure out during the meeting, but asking them bluntly would be counterproductive:

1. **How defensive is Adrian about the codebase?** Does he view our engagement as a threat or an opportunity?
2. **How accurate is his self-assessment?** Does he know about the security gaps, or is he genuinely unaware?
3. **What's his appetite for change?** Is he open to new patterns (CI/CD, testing, cloud) or will he resist?
4. **What's his relationship with the business stakeholders?** Does he have allies who might push back on our recommendations?
5. **Is he a potential long-term collaborator or a transition risk?** Can we work with him on remediation, or will he need to be replaced?

---

## Things to NOT Do Thursday

- Don't use the word "legacy" as a pejorative — to Adrian, this is his life's work
- Don't lead with security findings — lead with appreciation for his documentation and recent improvements
- Don't promise specific outcomes to Adrian that Beckway hasn't approved
- Don't discuss Lucee vs. Adobe ColdFusion yet — that's a loaded topic for a ColdFusion developer
- Don't bring up "rewrite" or "replace" language — Phase 1 is assessment, not prescription
- Don't compare his work unfavorably to any standard — frame everything as "current state vs. target state"

---

## Pre-Meeting Prep Checklist

- [ ] Review the static analysis findings so you can reference specific file examples if needed
- [ ] Have the Phase 1 project plan ready to reference for what's coming next
- [ ] Prepare screen-share capability in case Adrian wants to demo
- [ ] Have the security remediation guide (Steven's doc) ready but don't share it yet — wait to see how the security conversation goes
- [ ] Brief Patrick on the "trust but verify" approach so messaging is aligned
- [ ] Set up the HERB Jira project with initial placeholder epics

---

## Post-Meeting Action Items (Pre-populate)

After the meeting, capture:
- [ ] Adrian's reaction to our engagement and perceived attitude
- [ ] Any security items he flagged as already addressed
- [ ] Access credentials and repository details
- [ ] His preferred communication channel and availability
- [ ] Any business context or constraints he raised that aren't in the documentation
- [ ] Updated risk assessment based on what we learned
