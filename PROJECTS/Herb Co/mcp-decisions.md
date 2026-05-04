# MCP Decisions — Herb Co (iBizFusion)

**Last Updated:** 2026-05-04

Walking through `HARNESS/mcp-shortlist.md` with this project's specific context to decide what to install. Decisions are sticky — if access changes, update this file rather than changing course silently.

## Installed / will install

### Google Drive

- **Why:** Beckway's shared drive (`0AAU0auAeY4VQUk9PVA`) is the canonical home for Phase 1 deliverables, the project briefing, the static analysis PDF, the directional scope, the Phase 1 plan spreadsheet, the team onboarding checklist, and the SharePoint mirror. Without this MCP, those are 17 separate manual fetches.
- **Status:** Already authenticated and proven (used to populate `project-context.md`).

### AWS API MCP + AWS CLI v2

- **Why:** Webapper-managed UAT account 797601398324. Active work in CloudFormation, IAM, S3, CloudWatch / CloudTrail, Secrets Manager. Pairs with the `ama-herbco-uat-{ro,admin}` profile pattern.
- **Status:** Set up. CLI used directly via Desktop Commander per rule 33.

### Bitbucket

- **Why:** The Anthropic Managed Agents repo (`bitbucket.org/cloudsee-drive/anthropic-managed-agents`) holds the AWS profile generator and IdC tooling. Webapper's project repos are also in Bitbucket.
- **Status:** Should install. Currently unconfirmed whether installed.

## Decision pending — confirm with Patrick / Steven

### Atlassian (Jira + Confluence)

- **Why we'd want it:** Webapper has its own Atlassian (CSD, VAS Jira projects). The "HERB" Jira project mentioned in meeting prep would live there.
- **Status:** Confirmed Webapper has Atlassian. **Pending:** is there a Beckway Atlassian we also need access to, or does all PM tracking happen in Webapper's? Confirm before installing a Beckway-side instance.

### Slack

- **Why we'd want it:** Internal Webapper Slack for project comms. Beckway likely uses different tooling (possibly Microsoft Teams).
- **Status:** Webapper Slack — install. **Pending:** Beckway-side comms channel TBD; flag if it's not Slack.

## Skipping

### GitHub

- **Why skip:** Adrian's iBizFusion source is not in GitHub. Webapper's repos are in Bitbucket. No active GitHub repos in scope for Herb Co.
- **Reconsider when:** Phase 2 migration to Webapper-controlled source control. If we choose GitHub over Bitbucket for the new repo, install then.

### Browser automation (Chrome / Playwright)

- **Why skip:** Phase 1 is assessment, not active dev. We're not deploying changes to an iBiz UI we'd verify visually. Static analysis is read-only.
- **Reconsider when:** Phase 2 begins UI changes, or when we need to actually click through the app to validate behavior post-change.

### Database MCPs

- **Why skip:** MySQL 8 is on Hostek VPS, restricted to Adrian's single IP. We don't have direct DB access from our environment.
- **Reconsider when:** Webapper gets read-only DB access (likely Phase 2). Then add a MySQL MCP.

### Sentry

- **Why skip:** No Sentry instrumented in iBizFusion. Production monitoring is "Adrian manually checks things." Can't read what isn't there.
- **Reconsider when:** Phase 2 introduces application monitoring. Sentry / Datadog / similar would be installed at the same time.

### Context7

- **Why skip:** ColdFusion 2023 / MySQL 8 are stable, well-documented. The `lucee` / `cfwheels` / similar fast-moving CF ecosystem libraries aren't in scope. Context7's value is for fast-moving libs where training data goes stale, which doesn't fit here.
- **Reconsider when:** Phase 2/3 introduces Node.js / serverless / modern web stack alongside iBiz.

### Firecrawl

- **Why skip:** Most external research needed for Herb Co (TDD docs, vendor specs) is in the Drive folder we already have access to. WebFetch covers the rare external lookup.
- **Reconsider when:** Specific need for structured extraction from public web docs.

### Intercom

- **Why skip:** Herb Co isn't a SaaS product with an Intercom-style support knowledge base. Customer support flows through internal tools.
- **Reconsider when:** Never, for this project. Different shape of work.

## Audit

| MCP | Decision | Last reviewed |
|-----|----------|--------------|
| Google Drive | Installed | 2026-05-04 |
| AWS API + CLI | Installed | 2026-05-04 |
| Bitbucket | Will install | 2026-05-04 |
| Atlassian (Webapper) | Likely install | 2026-05-04 |
| Atlassian (Beckway) | Pending | 2026-05-04 |
| Slack (Webapper) | Likely install | 2026-05-04 |
| Slack (Beckway-side) | Pending | 2026-05-04 |
| GitHub | Skip | 2026-05-04 |
| Browser automation | Skip until Phase 2 | 2026-05-04 |
| Database MCPs | Skip until DB access | 2026-05-04 |
| Sentry | Skip until monitoring exists | 2026-05-04 |
| Context7 | Skip | 2026-05-04 |
| Firecrawl | Skip | 2026-05-04 |
| Intercom | Skip | 2026-05-04 |
