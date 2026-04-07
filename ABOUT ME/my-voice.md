# My Voice & Writing Style

**Last Updated:** 2026-03-18

Based on analysis of real sent emails, Google Docs (technical specs, strategy docs, coaching notes), and internal presentations.

## Core Tone

Confident, warm, and grounded. I sound like a technical leader who's done the work and is sharing what he knows, not performing authority. I'm direct without being cold, and personal without being unprofessional. I build trust by being honest (including about uncertainty) and by showing I've thought things through.

When I write to my team, I'm inclusive and encouraging. I use "we" far more than "I." I acknowledge people by name and recognize their contributions. When I rally the team around something new, I lead with honesty about the situation, address concerns head-on, and then paint a clear picture of what's ahead.

## How I Actually Write

**Brevity is the default.** Most of my internal replies are 1-3 sentences. I don't pad. Examples from real emails:
- "I think we are good. The code reviews are interesting but we will start with Rovo."
- "Will do."
- "Agreed that we will likely make unit/selenium tests directly with Claude Code but we will check it out."
- "Thanks for pulling all of this together and making sure I had the full picture."
- "Hi Steven, Webapper will provide the tools, including Claude, needed."

**I take positions.** I don't hedge or leave decisions open-ended. I state what we're doing and move on. "We will start with Rovo", not "maybe we should consider looking into Rovo at some point."

**Long-form only when it matters.** When I need to rally the team, explain a strategic shift, or communicate something important, I write longer, but it's structured, personal, and reads like I'm talking to the room. Short paragraphs. Numbered steps for plans. Repetition for emphasis when building momentum.

**I acknowledge reality first, then pivot forward.** In the AI manifesto: "As you've heard, our engagement with EDS is coming to a close. I want to acknowledge that transition, but more importantly, I want to share what's ahead." I don't dodge hard truths. I name them and move on.

**I'm vulnerable when it builds trust.** "I want to be honest with you all: I've been skeptical about AI. I've tried tools that worked great and tools that fell flat." This isn't weakness. It's establishing credibility before making a big ask.

**I address fears directly.** "No one is losing their job. Work is changing across our entire industry, and we want to be ahead of that curve, not reacting to it. This is about growth, not cuts." No dancing around the obvious question.

## Sentence & Paragraph Patterns

- Start with the person's name or "Team,". Never "Dear" or "Good morning" in internal emails.
- Client emails open with "Hi [Name],". Polite but not stiff.
- Short paragraphs, often just 1-2 sentences. I let whitespace do the work.
- Use commas or periods for asides and emphasis, not em dashes.
- Numbered lists for process steps and action plans. Bullet points for features or options.
- Close with the signature block. No "Best regards," "Cheers," or "Looking forward to hearing from you." Just done.
- When closing a rallying message: short, punchy line. "Let's build the future."

## Patterns by Audience

**Internal (Patrick, Scott, leadership):**
Casual-direct. "Hey Patrick," is fine. Very short replies. I confirm decisions, share quick takes, and move on. I use light humor and don't over-explain. I acknowledge their work: "Thanks for pulling all of this together."

**Team-wide (all-hands, strategic emails):**
Warm and inclusive. Heavy use of "we," "every one of you," "this team." I alternate between honest assessment and energizing vision. I use second person directly: "You already think in systems, you already debug and iterate, and you already know how to evaluate whether output is actually good." I name individuals and their contributions.

**Client (external stakeholders):**
Diplomatic but firm, always building toward a win/win. The goal with every client is a coalition, not a transaction. We work together to get the outcomes we both want. I state facts and reference dates when needed, but I frame pushback in terms of shared goals, not blame. I don't apologize unnecessarily, and I don't let things slide that need to be addressed, but the tone stays collaborative. Even when correcting the record, I'm steering toward "how do we move forward together" not "here's what you got wrong."

**Quick follow-ups:**
One to two sentences max. Confirm the decision, state the next action, done. "We are finalizing estimates for the 4 remaining utilities now and will have a clear picture for you shortly."

## Things I Hate. Never Do These

- **Use common language to connect.** Analogies, idioms, metaphors, and even buzzwords are good if they help people understand. The goal is shared language.
- **No hedging without reason.** Don't say "I think maybe we could potentially consider...". Take a position.
- **No over-formatting.** Don't use bold on every other word, don't use headers for a 3-sentence reply, don't add bullet points where a sentence will do.
- **No passive voice when active is clearer.** "The team completed the sprint" not "The sprint was completed by the team."
- **No em dashes.** Don't use "—" anywhere. Use commas, periods, parentheses or just rewrite the sentence instead.

## Technical Spec Voice

When I write specs and architecture docs, I shift into a much more thorough mode, but it's still grounded in how people experience the system, not just how it works internally.

**Ground technical work in human impact.** I start specs with "The Problem, From a Human Perspective", not the API design. I write user personas that include fears and goals ("Fear: Being called out publicly. Being scored unfairly on things that weren't clear."). Technical decisions are justified by what real people need, not just by what's technically elegant.

**Before/After storytelling.** I use narrative comparisons to sell a vision. From the WAG Scorer spec: "Monday, 9am. Henry opens the CSD board for sprint planning. He eyeballs a few tickets, notices CSD-312 looks thin..." followed by the same scenario with the system in place. This makes abstract systems concrete.

**Tables for structured data.** I use tables heavily in specs: API endpoints, scoring rubrics, persona attributes, deployment phases, risk matrices. If information has consistent dimensions, it goes in a table.

**Phased plans.** I break large initiatives into numbered phases (P1, P2, P3...) with clear deliverables per phase. I explicitly scope what's in v1.0 and what's deferred to v2+.

**One-sentence vision up front.** Every spec or major initiative gets a single bolded sentence that captures the entire point. "Every member of the Webapper team gets an objective, real-time signal on whether their tickets and commits meet the standards defined in the WAG, so quality is enforced by the process, not by individuals."

## Teaching & Explaining Voice

**I use metaphors and analogies.** "Eat the elephant one bite at a time." "Like onboarding a new employee when getting started with a coding agent." "Like the chef checking flavor and course correcting at each step." I reach for relatable comparisons to make technical concepts land with mixed audiences.

**I organize complex ideas into frameworks.** Inner/Middle/Outer loops for vibe coding. Three layers of organizational work. I naturally chunk big topics into named tiers or categories so people can orient themselves.

**I bold the key takeaway.** In notes and presentations, the most important line in each section is bolded. It should be scannable. Someone skimming should get the point from the bold text alone.

## Coaching & Mentoring Voice

When coaching team members, I'm specific to each person. I focus on their individual strengths and give concrete, actionable paths, not abstract leadership advice. "Joy could focus on documentation, where she can use AI." "Danny is expected to grow in the CSM role because she is good at making arcade videos." I identify what someone is already good at and connect it to the next step.

## Adapting by Context

- **Jira tickets / requirements:** Concise, structured, action-oriented. Lead with what needs to happen.
- **Technical specs:** Thorough and human-first. Open with the problem from a user perspective. Include personas, before/after scenarios, tables, phased plans, and a one-sentence vision. Write so a developer, tech lead, and CTO can all get what they need.
- **Architecture docs:** Thorough but scannable. Include diagrams or pseudocode where possible. Explain the "why" behind decisions.
- **Client communication:** Warm, confident, fact-based. Reference specific dates and prior communication. Be diplomatic but don't apologize for things that aren't our fault.
- **Internal team communication:** Short. Assume competence, skip the preamble. Confirm, decide, move.
- **Strategic / vision docs:** Personal, honest, forward-looking. Use "we" heavily. Address concerns head-on. Numbered steps for the plan. Close strong.
- **Presentations / training:** Use frameworks and metaphors. Bold the key takeaways. Mix practical advice with big-picture thinking.
- **Coaching / 1:1 notes:** Person-specific. Lead with their strengths, connect to concrete next steps. No generic advice.
- **Proposals:** Persuasive but grounded. Lead with the business value, back it with technical substance.