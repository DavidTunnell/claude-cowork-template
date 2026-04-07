# Webapper Agile Guide (WAG) - Process Overview

**Last Updated:** 2026-03-18
**Source:** WAG v4.0 (January 23, 2025)

## What This Is

The WAG is Webapper's internal guide to how we build and deliver software. It defines our scrum process, sprint structure, ticket standards, roles, and ceremonies. Every project at Webapper follows this process unless a specific deviation is noted in the project's context file.

## Core Philosophy

High quality software comes from strong process and clarity. Clarity requires effort and discipline. Requirements must be described before programming, and with enough detail to prevent blockers. Tickets are the "one source of truth" (OSOT) for all work.

## Sprint Structure

- **Sprint Length:** 2 weeks, synchronized across all Webapper projects
- **Sprint Naming:** "{SPRINT START DATE}/{SPRINT END DATE} - {Customer}" (e.g., "03-11-26 / 03-25-26 Cloudsee Drive")
- **Cadence Goal:** Everything happens during a sprint: planning, standups, review, retrospective

## Ceremonies Schedule

- **Daily Standup (USTeam):** All team members submit asynchronous standups using our proprietary tool [Daily Pulse](https://dailypulse.webapper.com/). Each person answers: What did I do yesterday? What am I working on today? Any blockers? Highlights/big wins? Daily Pulse also provides a team standup board with filtering by member and date, AI-powered analysis of standups and Jira tickets (via AWS Bedrock + OpenSearch), Jira integration with sprint metrics (active/blocked/completed tickets, hours tracking, sprint progress), smart ticket summaries, auto-labeling, release notes generation, and a Weekend Stories feature for team bonding. Architecture: React frontend (Vite/Tailwind/shadcn), Python FastAPI on AWS Lambda (SAM) with two services (api + orchestry for Jira sync), MySQL, OpenSearch (vector embeddings for semantic search), AWS Bedrock (Claude Sonnet for AI features).
- **Sprint Touchpoint (VTeam + USTeam):** Once per month after the All-Hands meeting
- **Sprint Planning & Backlog Refinement:** USTeam has weekly meetings per project. Only scrum masters should add/remove tickets from sprints.
- **Sprint Review / Demo:** Once per sprint with all available stakeholders including the customer. Can be replaced with a written summary or email report. We will want to create an AI agent to send this progress email to customers each sprint.

## Ticket Workflow (Standard Statuses)

1. **New** - New requirement (bug, feature, or enhancement)
2. **Discovery** - Scrum master works with clients on requirements. Dev team asks questions and provides estimates.
3. **Confirmed** - Ticket is understood, estimation complete
4. **In Progress** - Developer is actively working, updates ticket daily
5. **Ready to Test** - Deployed to QA server, scrum master tests. Failure goes back to In Progress.
6. **Testing Complete - Ready to Deploy** - QA passed, ready for production
7. **Ready for Production Testing** - Deployed to prod, developer verifies, then scrum master and/or client performs UAT. Failure goes back to In Progress.
8. **Done!** - Requirements fully delivered

Additional statuses: Waiting on Customer (blocked on external party), On Hold (paused for any reason), Closed/Stale (no longer relevant), Invalid (duplicate or false report)

## Plan Levels (Ticket Hierarchy)

- **Epic** - Large-scale objective spanning multiple sprints. Should consist of multiple Issues (Story, Task, Bug). Should not remain open indefinitely. 
- **Issue (Story, Task, Bug)** - Medium scale, under an epic. Stories detail specific functionality focusing on user value. Should contain Sub-tasks or a checklist if complex.
- **Sub-task** - Smallest unit. Specific, well-defined, achievable in a short timeframe. Should typically take 8 hours or less.

## Estimation

- Every ticket must be estimated before being added to a sprint
- An educated guess is better than no estimate
- **Estimate Confidence** levels:
  - No Earthly Idea: only a guess, ticket not well understood
  - Low: ~25% accurate (could take up to 3x longer)
  - Medium: ~50% accurate (could take up to 2x longer)
  - High: ~75%+ accurate (could take up to 25% longer)

## Sprint Capacity Planning

- Sprint Planning Spreadsheet tracks estimates vs. actual hours, planned vs. unplanned work
- All effort counts: Discovery, Testing, Regression Test Building, Studying, Cross-Training, Documentation, and Development
- No ticket should be added to a sprint if it isn't assigned

## Sprint Monitoring & Reporting

Daily and sprint-level monitoring happens through a combination of Jira boards, dashboards, and reports:

- **Active Sprint Boards:** Monitored daily. Shows current sprint tickets, statuses, assignees, and workflow columns.
  - [CSD Board](https://webapper.atlassian.net/jira/software/c/projects/CSD/boards/12)
  - [EDS Board](https://webapper.atlassian.net/jira/software/c/projects/EDS/boards/10)
  - [VAST Board](https://webapper.atlassian.net/jira/software/c/projects/VAST/boards/14)
  - [Webapper Board](https://webapper.atlassian.net/jira/software/c/projects/WEBA/boards/8)
- **Jira Dashboards:**
  - [Webapper Sprint Planning - SumUp](https://webapper.atlassian.net/jira/dashboards/10171) — Per-project sprint gadgets showing Issue Type, Key, Summary, Priority, Assignee, Status, Original Estimate (summed), and Estimate Confidence. Powered by per-project sprint filters (CSD `10015`, EDS `10016`, VAST `10121`)
  - [All Projects - Open Tickets](https://webapper.atlassian.net/jira/dashboards/10002) — Cross-project view of all open tickets. [Saved Filter](https://webapper.atlassian.net/issues/?filter=10012)
- **Jira Reports (per board):**
  - [Sprint Retrospective Report](https://webapper.atlassian.net/jira/software/c/projects/CSD/boards/12/reports/sprint-retrospective) — Completed vs. incomplete work per sprint
  - [Burndown Chart](https://webapper.atlassian.net/jira/software/c/projects/CSD/boards/12/reports/burndown-chart) — Remaining work over time within a sprint
- **Ticket Review Dashboards:**
  - [Monday Ticket Review](https://webapper.atlassian.net/jira/dashboards/10002)
  - [My Open Tickets](https://webapper.atlassian.net/jira/dashboards/10039?maximized=10229)
  - [Stale Tickets](https://webapper.atlassian.net/issues/?filter=10013)

### JQL Filters (for AI agent / Cowork automation)

These are the underlying JQL queries that power the dashboards above. Use these to replicate dashboard data programmatically via the Atlassian connector.

**Current Sprint Tickets - CSD** (`filter=10015`): [View](https://webapper.atlassian.net/issues/?filter=10015)
```jql
sprint in openSprints() AND project = "CloudSee Drive"
```

**Current Sprint Tickets - EDS** (`filter=10016`): [View](https://webapper.atlassian.net/issues/?filter=10016)
```jql
sprint in openSprints() AND project = "Educational Data Services"
```

**Current Sprint Tickets - VAST** (`filter=10121`): [View](https://webapper.atlassian.net/issues/?filter=10121)
```jql
sprint in openSprints() AND project = VisionAST
```

**All Projects - Open Tickets** (`filter=10012`): [View](https://webapper.atlassian.net/issues/?filter=10012)
```jql
(status NOT IN ("Done", "INVALID", "STALE") AND
((project IN ("CSD", "EDS", "VAST") AND sprint IN openSprints()) OR
(project NOT IN ("CSD", "EDS", "VAST")))) AND
(labels IS EMPTY OR labels NOT IN (EDSInternalDev))
```

**Stale Tickets** (`filter=10013`): [View](https://webapper.atlassian.net/issues/?filter=10013)
```jql
(updated <= -7d
 AND statusCategory NOT IN (Done, "To Do")
 AND status NOT IN (Discovery, CONFIRMED))
AND NOT (project = VAST AND status IN ("Waiting on Customer", "On Hold"))
AND NOT (project = VAST AND sprint IN ("Waiting for Completion"))
AND NOT (
  project IN (EDS, CSD, VAST)
  AND sprint IS EMPTY
)
AND assignee != 712020:c63bb606-bdb7-4ce5-87e3-c79bc35875fb
```

**Future Vision:** Replace manual dashboard checks and report reviews with AI agents that automatically pull sprint metrics, generate summaries, flag risks, and deliver insights. Current interim approach: Claude Cowork uses the JQL filters above via the Atlassian connector for structured data, and Claude in Chrome for visual reports (burndown charts, sprint retrospectives) that don't have clean API equivalents.

## Roles

### Scrum Masters (USTeam)
- Only people who should add/remove tickets from sprints
- Groom/curate backlogs consistently (daily/weekly)
- Plan sprints and respond to all questions within 24 hours
- Oversee Estimate Confidence and Estimate fields
- Work with clients to fill the Product Owner role

### Developers
- Update tickets daily (comments, status, hours)
- Keep ticket list matching weekly swim lane
- Communicate blockers immediately, tag USTeam members in tickets
- Do not self-assign tickets without discussing with scrum master
- Respond to all questions within 24 hours
- Goal: finish the work defined for each sprint

## Ticket Rules

- **Comments:** Always add comments as you work, even incremental updates
- **Scope Creep:** If scope is added to an in-progress estimated ticket, new work goes in a new ticket
- **Time Tracking:** Keep Total Time Worked up-to-date. Hours must match Harvest.
- **Git Commits:** Use format `CSD-123 - Your commit message here` (Jira project key prefix) to link commits to tickets
- **Bug Format:** Developer output must include Root Cause and Solution
- **Tags:** Tag tickets with helpful meta information (Bug, Bad Data, Next Sprint, etc.)
- **Followers:** Add followers and use @mentions for notifications

## Code Review / PR Process

Not yet mature. The goal is to establish a formal PR review workflow and leverage Atlassian Rovo AI to assist with code reviews. Branch strategy and PR conventions are defined per-project in the project context files. This section will be expanded as the process matures.

## Key Ticket Metrics

- **Velocity:** Move tickets toward closure as quickly as possible while maintaining quality
- **Accuracy:** All ticket fields as accurate as possible
- **Recency:** Tickets as up-to-date as possible
- **Formula:** High Velocity + High Quality = Great Customer Experience

## Current Manual Sprint Transition Process (Project Manager Task)

1. Screenshot the project in the "Webapper Sprint Planning - SumUp" dashboard
2. Paste screenshot at the top of the "Completed Sprints" page in the appropriate Confluence space
3. Navigate to the current sprint in Backlog view
4. Move any incomplete tickets to the next sprint by changing the Sprint field
5. Click "Complete sprint"
6. Click "Start sprint" for the new sprint

## Confluence (Wiki)

- Central shared repository of information describing customers and their systems
- Avoid duplicate information across pages, use page hierarchy for clarity
- Use formatting and link to external tools (Google Docs, Sheets, Miro) when needed
- In the future our goal is to **Never store access credentials in Confluence**

## Tools & Claude Access Methods

Use MCP connectors when available (faster, structured data). Fall back to Chrome MCP for visual/interactive content, or Desktop Commander for CLI/API access. Tools without any MCP use Chrome or Desktop Commander.

| Tool | URL | MCP Connector | Chrome | Desktop Commander | When to use which |
|------|-----|:---:|:---:|:---:|---|
| **Jira** | [webapper.atlassian.net](https://webapper.atlassian.net/) | ✅ Atlassian | ✅ | — | **MCP first** for JQL queries, reading/creating/updating issues, searching. **Chrome** for dashboards, sprint boards, visual reports (burndown, retrospective), and anything in gadgets/iframes. |
| **Confluence** | [webapper.atlassian.net/wiki](https://webapper.atlassian.net/wiki/) | ✅ Atlassian | ✅ | — | **MCP first** for reading/searching/creating pages via CQL. **Chrome** for embedded macros or page trees. |
| **Bitbucket** | [bitbucket.org/cloudsee-drive](https://bitbucket.org/cloudsee-drive/workspace/repositories) | ❌ | ✅ | ✅ | **Desktop Commander first** for git operations on cloned repos (`C:\Users\david\dev\`): `git log`, `git diff`, `git pull`, read source files. **Chrome** for PRs, repo browsing, and pipelines. See instructions below. |
| **Google Drive** | — | ✅ Drive MCP | ✅ | — | **MCP first** for searching and fetching Google Docs. **Chrome** for Sheets, Slides, or complex formatting. |
| **Gmail** | — | ✅ Gmail MCP | ✅ | — | **MCP first** for searching, reading, and drafting emails. **Chrome** for complex threads or attachments. |
| **Google Calendar** | — | ✅ GCal MCP | ✅ | — | **MCP first** for listing events, finding free time, creating events. **Chrome** for visual calendar views. |
| **Productboard** | [webapper.productboard.com](https://webapper.productboard.com/) | ❌ | ✅ | — | **Chrome only.** ICE scoring for CloudSee Drive roadmap. |
| **Harvest** | [webapper.harvestapp.com](https://webapper.harvestapp.com/) | ❌ | ✅ | — | **Chrome only.** Time tracking. |
| **Daily Pulse** | [dailypulse.webapper.com](https://dailypulse.webapper.com/) | ❌ | ✅ | — | **Chrome only.** Proprietary async standup + sprint intelligence tool. |
| **Weekdone** | [weekdone.com](https://weekdone.com/) | ❌ | ✅ | — | **Chrome only.** Quarterly OKRs. |
| **Google Chat** | [chat.google.com](https://chat.google.com/) | ❌ | ✅ | — | **Chrome only.** Primary team communication. No MCP available. |

### Bitbucket Access via Desktop Commander

Repos are hosted at [bitbucket.org/cloudsee-drive](https://bitbucket.org/cloudsee-drive/workspace/repositories). No MCP available. Use Desktop Commander (Windows CMD) for git and Bitbucket REST API operations.

**Local repos** are cloned to `C:\Users\david\dev\`. To read code, check history, or make changes:
```cmd
cd C:\Users\david\dev\{repo-name}
git log --oneline -20
git diff
git pull
git status
```

**To clone a repo not yet local:**
```cmd
cd C:\Users\david\dev
git clone git@bitbucket.org:cloudsee-drive/{repo-name}.git
```

**Bitbucket REST API** (for PRs, branches, repo info without cloning):
```cmd
curl -u david@webapper.com https://api.bitbucket.org/2.0/repositories/cloudsee-drive
curl -u david@webapper.com https://api.bitbucket.org/2.0/repositories/cloudsee-drive/{repo-name}/pullrequests
```
Note: API calls require authentication. If app passwords are configured, Desktop Commander can use them. Otherwise, use Chrome to browse PRs and pipelines.