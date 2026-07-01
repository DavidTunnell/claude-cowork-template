# MCP Decisions — Herb Co (iBizFusion)

**Last Updated:** 2026-07-01

Walking through `HARNESS/mcp-shortlist.md` with this project's context to decide what to install. Decisions are sticky — if access changes, update this file rather than changing course silently. (Operational rationale only; no account IDs, credentials, or confidential document links here.)

## Installed / will install

### Google Drive
- **Why:** Beckway's shared drive is the canonical home for project docs and deliverables. Without this MCP those are many manual fetches. (Migrating toward SharePoint as NP Nutra moves to M365 — revisit then.)
- **Status:** Authenticated and in use.

### AWS API MCP + AWS CLI v2
- **Why:** Webapper-managed AWS work (marketing-site consolidation, monitoring). Pairs with the `ama-*` IdC profile pattern.
- **Status:** Set up; CLI used via Desktop Commander per rule 33.

### Atlassian (Jira + Confluence)
- **Why:** The `HERB` Jira project and Confluence docs live in Webapper's Atlassian.
- **Status:** Installed.

### Bitbucket / source control
- **Why:** The internal AWS-access tooling and Webapper project repos. Webapper-controlled source control is being adopted for the new workstreams.
- **Status:** Confirm per engineer.

### Azure / Microsoft Graph
- **Why:** The commerce migration lands on Azure (App Service + Azure SQL MI) and the document backend moves to SharePoint/Graph.
- **Status:** Add as the migration workstreams ramp.

## Skipping (for now)

- **GitHub** — iBiz source isn't on GitHub; revisit if a new repo lands there.
- **Browser automation** — add when we're clicking through app UIs to validate post-change behavior (commerce migration).
- **Database MCPs** — no direct iBiz DB access from our environment today; revisit if read-only access is granted.
- **Sentry / monitoring MCPs** — add alongside real application monitoring.
- **Context7 / Firecrawl / Intercom** — not a fit for this project's current shape.

## Audit

| MCP | Decision | Last reviewed |
|-----|----------|--------------|
| Google Drive | Installed | 2026-07-01 |
| AWS API + CLI | Installed | 2026-07-01 |
| Atlassian | Installed | 2026-07-01 |
| Bitbucket / source control | Confirm per engineer | 2026-07-01 |
| Azure / Graph | Add as migration ramps | 2026-07-01 |
| GitHub | Skip (revisit on new repo) | 2026-07-01 |
| Browser automation | Add for commerce-migration UI validation | 2026-07-01 |
| Database MCPs | Skip until DB access | 2026-07-01 |
| Sentry / monitoring | Skip until monitoring exists | 2026-07-01 |
| Context7 / Firecrawl / Intercom | Skip | 2026-07-01 |
