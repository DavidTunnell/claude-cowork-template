# MCP Shortlist

**Last Updated:** 2026-05-04

The MCPs worth installing on most software projects, with the rationale for each. Marked by environment: **C** = Cowork, **CC** = Claude Code, **B** = both.

Pick the ones whose data the project actually touches. Don't install everything — each MCP adds tool-search surface area and authentication overhead.

## Atlassian (Jira + Confluence) — B

For any project with Jira tickets and a Confluence space. Read tickets, transition status, comment, search by JQL/CQL, fetch Confluence pages by URL. Replaces "let me copy this Jira ticket into the chat" entirely.

**Use when:** the project tracks work in Jira.

## Slack / Teams — B

Read messages, search channels, send messages, fetch threads. Useful for "what was the decision in #engineering yesterday?" without leaving Claude.

**Use when:** the team's working conversation lives in Slack or Teams and you want Claude to be able to pull context from it.

## Google Drive / OneDrive / SharePoint — B

Search and read Docs, Sheets, Slides, PDFs. List folders. Useful for any project where the docs of record live in cloud storage rather than the repo.

**Use when:** project documentation lives in Google Drive, OneDrive, or SharePoint.

## GitHub or Bitbucket or GitLab — B

Read PRs, diffs, issues, files. Comment on PRs. Pick whichever your repos live on.

**Use when:** the project has a remote repo. Always.

## Browser automation (Chrome / Playwright) — B

Claude can navigate the deployed site, take screenshots, evaluate JavaScript, run a real interaction flow. Closes the loop on "did the change actually work in the browser." Replaces a chunk of manual QA.

**Use when:** any web application, especially during feature development. Critical for verify-before-done on UI changes.

## Cloud provider MCPs (AWS / GCP / Azure) — B

Run cloud-provider CLI calls through Claude. Pairs with whatever profile/credential pattern your team uses.

**Use when:** project deploys to a public cloud and you need ad-hoc CLI work during sessions (resource state, log queries, infra inspection). Combine with the appropriate profile or credential set; never paste keys into chat.

## Database MCP (project-specific) — B

Pick by stack. Don't install all of them.

| Stack | MCP |
|---|---|
| Postgres | postgres-mcp / supabase-mcp |
| MySQL | mysql-mcp |
| DynamoDB | covered by the AWS MCP |
| MongoDB | mongodb-mcp |
| BigQuery / Snowflake | provider-specific MCP |

**Use when:** the project's data is in that store and you need ad-hoc queries during work. Read-only credentials by default.

## Sentry / Datadog / observability — B

Read production errors, traces, and logs. Triage in-place instead of context-switching to the dashboard.

**Use when:** project has observability tooling wired up and you do active production support.

## Context7 — CC primarily

Pulls current library docs into context on demand. Kills the class of hallucination where Claude references a deprecated API based on training data. Especially valuable for fast-moving libraries (React, Next.js, AWS SDK v3, etc.).

**Use when:** working with libraries Claude's training data is likely stale on.

## Firecrawl / Browserbase — B

On-demand scraping of public web content. Pairs well with WebSearch when you need full-page content rather than snippets.

**Use when:** researching external docs, vendor product pages, competitor sites — anything WebFetch can't reach or where you need structured extraction.

## Customer support knowledge base (Intercom / Zendesk / HelpScout) — B

Read conversations and articles, search support content. Useful for projects with customer-facing support knowledge bases.

**Use when:** project has a support tool and you need to consult historical customer conversations or update help articles.

## What we don't install by default

These appear on a lot of "essential MCP" lists. We skip them unless the project specifically needs them.

- **Email MCPs (Gmail/Outlook)** — high noise-to-signal. Use only when the project genuinely runs through email and you've scoped it.
- **Calendar MCPs** — useful for scheduling skills, less so for engineering work. Add when it's the actual job.
- **General-purpose file-system MCPs** — pick *one* (e.g., Desktop Commander) and standardize, rather than stacking three that all do similar things.

## Installation

- **Cowork:** Use the connector / plugin install flow in the desktop app.
- **Claude Code:** `claude mcp add <name>` or edit the appropriate config. See the MCP-specific repo for exact install instructions.

## Audit table (fill this in per project)

Drop a copy of this in `mcp-decisions.md` next to the project's `CLAUDE.md`. Walk it once, decide, move on.

| MCP | Decision | Rationale |
|---|---|---|
| Atlassian | install / pending / skip | |
| Slack or Teams | install / pending / skip | |
| Cloud storage (Drive / OneDrive) | install / pending / skip | |
| Repo (GitHub / Bitbucket / GitLab) | install / pending / skip | |
| Browser (Chrome / Playwright) | install / pending / skip | |
| Cloud (AWS / GCP / Azure) | install / pending / skip | |
| Database | install / pending / skip | |
| Observability (Sentry / Datadog) | install / pending / skip | |
| Context7 | install / pending / skip | |
| Scraping (Firecrawl / Browserbase) | install / pending / skip | |
| Support KB (Intercom / Zendesk) | install / pending / skip | |
