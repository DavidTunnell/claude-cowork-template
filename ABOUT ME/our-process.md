# Our Process

> **How to fill this out**
> This file describes how *your team* works. If you're filling out the template solo, write what you observe.
> If your team is adopting this template together, fill it in once and share. Re-read it whenever the process actually changes.
> Replace prompts and `[BRACKETS]` with your specifics. Drop sections that don't apply.
> Or in Cowork: **"help me fill out our-process.md"**.

**Last Updated:** YYYY-MM-DD

---

## How We Work (High-Level)

[One paragraph. What flavor of process does your team use? Agile (Scrum, Kanban, Scrumban), waterfall, project-based, campaign cycles, continuous flow, ad-hoc? What's the core philosophy behind the choice?]

<details>
<summary>Example (creative agency)</summary>

We run a project-based studio model. Each client engagement gets a small core team (creative director, designer, project manager) and a defined scope, timeline, and budget. Internal initiatives use light Kanban with a weekly review. The core philosophy is "fewer projects, deeper craft" — we'd rather do five projects exceptionally than fifteen adequately.
</details>

---

## Cadence

[How long are your iterations? When do they start and end? Is the team synchronized on a single rhythm, or do projects run independently?]

- **Iteration / cycle length:** [E.G., "2-WEEK SPRINTS, MON-FRI" OR "QUARTERLY OKRS" OR "PROJECT-BASED, NO FIXED LENGTH"]
- **Synchronization:** [DOES THE WHOLE COMPANY OPERATE ON ONE CALENDAR, OR DOES EACH TEAM RUN ITS OWN?]
- **Significant calendar events:** [QBRS, BOARD MEETINGS, RELEASE WINDOWS, ANNUAL PLANNING, ETC.]

<details>
<summary>Example</summary>

- **Iteration length:** 2-week sprints, synchronized across all engineering teams. Sprints run Monday-Friday across two weeks.
- **Synchronization:** All product and engineering work follows the same sprint calendar. Marketing runs on monthly campaign cycles.
- **Calendar events:** Quarterly business reviews (last week of quarter), monthly all-hands (first Thursday), annual planning in early November.
</details>

---

## Standard Ceremonies & Meetings

[Recurring meetings the team relies on. Include who attends, how often, and what they're for.]

| Ceremony | Cadence | Who | Purpose |
|---|---|---|---|
| [E.G., DAILY STANDUP] | [DAILY] | [TEAM] | [WHAT IT'S FOR] |
| | | | |
| | | | |

<details>
<summary>Example</summary>

| Ceremony | Cadence | Who | Purpose |
|---|---|---|---|
| Daily standup | Daily, 9:30am | Engineering team | What I did, what I'm doing, blockers. 15 min hard cap. |
| Sprint planning | Every other Monday | Engineering team + PM | Confirm sprint scope and assignments. |
| Sprint review / demo | Last day of sprint | Team + stakeholders | Walk through what shipped. Customer-facing demo if applicable. |
| Sprint retro | Last day of sprint, after demo | Engineering team only | What worked, what didn't, one thing we'll change. |
| Backlog refinement | Weekly, Wednesdays | PM + tech lead + designer | Groom upcoming work, estimate, clarify requirements. |
| 1:1s | Bi-weekly | Each manager + report | Career, feedback, blockers. |
</details>

---

## How Work Is Tracked

[Where does work live? How does something move from idea to "done"? What's the equivalent of a "ticket" in your system?]

- **Primary tracker:** [JIRA, ASANA, LINEAR, MONDAY, NOTION, TRELLO, ETC.]
- **Hierarchy:** [E.G., "EPIC > STORY > SUB-TASK" OR "INITIATIVE > PROJECT > TASK"]
- **Statuses (workflow):** [LIST THE STATES A TICKET MOVES THROUGH]
- **Estimation:** [HOW WORK IS SIZED, IF IT IS]
- **Definition of done:** [WHAT IT TAKES FOR SOMETHING TO BE CONSIDERED COMPLETE]

<details>
<summary>Example</summary>

- **Primary tracker:** Jira. Each project has its own board (e.g., `MKT`, `ENG`, `OPS`).
- **Hierarchy:** Epic > Story / Task / Bug > Sub-task. Sub-tasks should be ≤ 1 day of work.
- **Statuses:** New → Discovery → Confirmed → In Progress → Ready to Test → Testing Complete → Ready for Production → Done. Plus side states: Waiting on Customer, On Hold, Closed/Stale, Invalid.
- **Estimation:** Hours, with a confidence rating (Low / Medium / High / "No idea"). Every ticket must be estimated before it enters a sprint.
- **Definition of done:** Acceptance criteria met, tests passing, deployed to production, PM signed off, ticket closed with summary comment.
</details>

---

## Roles

[Who does what on a typical engagement or sprint. Include the responsibilities of each role, not just titles.]

- **[ROLE 1]:** [RESPONSIBILITIES]
- **[ROLE 2]:** [RESPONSIBILITIES]

<details>
<summary>Example</summary>

- **Project Manager (PM):** Owns the schedule, scope, and stakeholder communication. Runs ceremonies. Adds and removes tickets from sprints.
- **Tech Lead:** Owns architecture, code quality, and unblocking the team. Reviews critical PRs.
- **Engineers:** Own the work they pick up. Update tickets daily. Communicate blockers immediately. Write tests. Pair on tricky problems.
- **Designer:** Owns the user experience. Co-owns the spec with the PM. Reviews implementation against the design.
- **QA / Tester:** Owns regression. Writes test plans for new work. Sign-off needed before "Ready for Production."
- **Product Owner / Client liaison:** Owns priorities and the "why." Available within 24 hours for blocking questions.
</details>

---

## Communication Norms

[How does the team communicate, and what's expected? This prevents misalignment more than any tool ever will.]

- **Synchronous channels:** [E.G., SLACK, GOOGLE CHAT, TEAMS]
- **Asynchronous channels:** [E.G., EMAIL, DOCS, TICKETS]
- **Response time expectations:** [WHAT'S "NORMAL" AND WHAT'S "URGENT"]
- **How to escalate:** [WHEN AND HOW TO ESCALATE A BLOCKER]

<details>
<summary>Example</summary>

- **Sync:** Slack for fast back-and-forth, video calls for design reviews and difficult conversations. Default to async unless live really helps.
- **Async:** Email for external. Tickets for any decision that needs a paper trail. Internal docs for anything more than half a page.
- **Response times:** Slack DMs within a few hours during work hours. Tagged in a ticket: within 24 hours. Email: within a business day. After-hours: only urgent.
- **Escalation:** Blocker for >24 hours → tag PM. Blocker for >48 hours → tag tech lead and PM. Anything that risks the sprint goal → flag in standup *immediately*, don't wait.
</details>

---

## Tools

[The tools you use to actually run the work, with what each one is for. Helps Claude know which connector or surface to reach for.]

| Tool | Use For | Notes |
|---|---|---|
| [TOOL NAME] | [WHAT IT'S FOR] | [LINKS, ACCESS, SPECIFICS] |
| | | |

<details>
<summary>Example</summary>

| Tool | Use For | Notes |
|---|---|---|
| Jira | Ticket tracking, sprint boards | Atlassian connector for read/write; web for visual reports |
| Confluence | Wiki, runbooks, decisions | Atlassian connector for read/search/create |
| Slack | Real-time team chat | — |
| Google Workspace | Docs, sheets, calendar, email | Drive + Gmail + Calendar connectors |
| GitHub | Code, PRs, CI | Web + GitHub connector |
| Figma | Design files | Read-only access for non-designers |
| Notion | Internal wiki and runbooks | Notion connector |
</details>

---

## Conventions & Norms

[The unwritten rules that the team follows. Often the things new joiners trip over.]

- [CONVENTION OR NORM]
- [CONVENTION OR NORM]

<details>
<summary>Example</summary>

- **Tickets are the source of truth.** Decisions captured in chat must be summarized in the relevant ticket within 24 hours.
- **Comments on tickets while you work, even small updates.** Don't wait until "done" to write the first comment.
- **Scope creep gets a new ticket.** New work doesn't get added to an in-progress estimated ticket.
- **Time tracking matches the truth.** Don't pad. Don't shave. Hours go where the time actually went.
- **Commit messages reference the ticket key.** `MKT-123 Fix broken UTM tags in welcome email.`
- **No work goes in a sprint unassigned.** If it's not assigned, it's not in.
</details>

---

## Process Deviations

[Anything specific projects, clients, or workstreams do *differently* from the standard. Helps Claude avoid applying defaults where they don't apply.]

[LIST DEVIATIONS HERE, OR DELETE THIS SECTION IF EVERYTHING FOLLOWS THE STANDARD]

<details>
<summary>Example</summary>

- **Project Phoenix** runs on weekly cycles instead of 2-week sprints because the client requires weekly demos.
- **Internal R&D** doesn't use the ticket workflow. It's tracked in a Notion doc with quarterly reviews.
- **Production hotfixes** skip the discovery and estimation steps. They go straight from triage to in-progress.
</details>
