# MCP Shortlist

**Last Updated:** 2026-05-04

The MCPs worth installing on most Webapper projects, with the rationale for each. Marked by environment: **C** = Cowork, **CC** = Claude Code, **B** = both.

Pick the ones whose data the project actually touches. Don't install everything — each MCP adds tool-search surface area and authentication overhead.

## Atlassian (Jira + Confluence) — B

For any project with Jira tickets and Confluence space (most Webapper projects). Read tickets, transition status, comment, search by JQL/CQL, fetch Confluence pages by URL. Replaces "let me copy this Jira ticket into the chat" entirely.

**Use when:** project has a Jira project key (CSD, HERB, AB, VAS, etc.).

## Slack — B

Read messages, search channels, send messages, fetch threads. Useful for "what was the decision in #engineering yesterday?" without leaving Claude.

**Use when:** the project has team activity in Slack and you want Claude to be able to pull context from it.

## Google Drive — B

Search Drive, read Docs/Sheets/Slides/PDFs, list folders. Especially useful for client-shared drives like the Beckway Herb Co drive (`0AAU0auAeY4VQUk9PVA`).

**Use when:** project documentation lives in Google Drive — which is most non-engineering content at Webapper.

## GitHub or Bitbucket — B

Read PRs, diffs, issues, files. Comment on PRs. For Webapper, default to **Bitbucket** since most repos are there (`csd-frontend`, `csd-backend`, `anthropic-managed-agents`). GitHub MCP is fine for OSS / external repos.

**Use when:** the project has a remote repo. Always.

## Browser automation (Chrome / Playwright) — B

Claude can navigate the deployed site, take screenshots, evaluate JavaScript, run a real interaction flow. Closes the loop on "did the change actually work in the browser." Replaces a chunk of manual QA.

**Use when:** any web application, especially during feature development. Critical for verify-before-done on UI changes.

## AWS API MCP — B

Run AWS CLI calls through Claude. Pairs with the `ama-*` profile pattern from `my-rules.md` rule 33. For projects with AWS infra (CloudSee, VisionAST, Herb Co UAT).

**Use when:** project deploys to AWS. Combine with the right profile (`ama-cloudsee`, `ama-herbco-uat-admin`, etc.).

## Database MCP (project-specific) — B

Pick by stack. Don't install all of them.

| Stack | MCP |
|-------|-----|
| Supabase / Postgres | supabase-mcp or postgres-mcp |
| DynamoDB | AWS API MCP covers this |
| MySQL | mysql-mcp |
| Smartsheet | Smartsheet MCP (already used at Webapper) |

**Use when:** the project's data is in that store and you need ad-hoc queries during work.

## Sentry — B

Read production errors and issues from chat. Triage in-place instead of context-switching to the dashboard.

**Use when:** project has Sentry wired up and you do active production support (CloudSee, VisionAST, Herb Co Phase 3).

## Context7 — CC primarily

Pulls current library docs into context on demand. Kills the class of hallucination where Claude references a deprecated API based on training data. Especially valuable for fast-moving libraries (React, Next.js, AWS SDK v3).

**Use when:** working with libraries Claude's training data is likely stale on.

## Firecrawl — B

On-demand scraping of public web content. Pairs well with WebSearch when you need full-page content rather than snippets.

**Use when:** researching external docs, vendor product pages, competitor sites — anything WebFetch can't reach or where you need structured extraction.

## Intercom — B

Read conversations and articles, search support content. Useful for projects with customer-facing support knowledge bases (CloudSee Drive's support portal).

**Use when:** project has Intercom and you need to consult historical customer conversations or update help articles.

## What we don't install by default

These appear on a lot of "essential MCP" lists. We skip them unless the project specifically needs them.

- **Email MCPs (Gmail/Outlook)** — high noise-to-signal. Use only when the project genuinely runs through email and you've scoped it.
- **Calendar MCPs** — useful for scheduling skills, less so for engineering work. Add when it's the actual job.
- **File-system MCPs (Desktop Commander excluded)** — Desktop Commander is the one we standardize on (see rule 32). Don't double up.

## Installation

- **Cowork:** Use the connector / plugin install flow in the desktop app.
- **Claude Code:** `claude mcp add <name>` or edit the appropriate config. See the MCP-specific repo for exact install instructions.
