# PROJECTS — Shared Company Context

**Last Updated:** 2026-07-01

Unlike the `ABOUT ME/` folder (which you personalize), **`PROJECTS/` is shared company truth — the same for everyone at Webapper.** Each folder captures a project at an **onboarding altitude**: what it is, its current status, tech stack, key workflows, team/ownership, and where things live. When architecture, status, or ownership changes, update these as a team so everyone's Claude starts from the same accurate picture.

> **Operational context only.** These files intentionally exclude anything sensitive — dollar/SOW figures, specific security-vulnerability findings, credentials/secrets, AWS account IDs, acquisition-target/deal terms, and personal contact info. Those live in access-controlled Jira/Confluence/Drive. Keep it that way: this template gets cloned and forked.

## Active projects

| Project | Jira | Type | What it is |
|---------|------|------|-----------|
| **Herb Co** | `HERB` | Client (anchor) | Technical partner for Monterey Bay Herb Co. (Beckway portfolio); iBizFusion ColdFusion/MySQL ERP + web consolidation (AWS) & commerce migration (Azure) + retained support |
| **CloudSee Drive** | `CSD` | Internal product | Flagship SaaS — browser-based interface for Amazon S3 (search, sharing, RBAC); strategic bet to become Webapper's primary business |
| **Atlantic British** | `AB` | Client | Managed AWS hosting + ColdFusion e-commerce support for RoverParts.com (Land Rover/Range Rover parts) |
| **eRep** | `EREP` | Client | Managed AWS hosting for a ColdFusion/Lucee + SQL Server app stack (erep.com and related domains) |
| **Icon Media** | `IM` | Client | Managed AWS hosting for a Lucee/CFML multi-storefront e-commerce estate (wheel/tire brands) |

**Internal:** the `WEBA` (Webapper) Jira project holds internal work — the AI-transformation initiative and cross-client hosting governance (foundations audits, ADRs, credential migration). It's represented here via `ABOUT ME/our-process.md`, not as a client folder.

## Retired / concluded

| Project | Jira | Status |
|---------|------|--------|
| **VisionAST** | `VAST` | Winding down — customers migrating to another platform; Webapper infrastructure being decommissioned in 2026. Folder kept as historical reference. |
| **EDS** (Educational Data Services) | — | Closed/archived. A ColdFusion app Webapper formerly hosted; no active Jira project, only residual infrastructure monitoring. No folder — this line is the record. |

## How to use

- **Working on a project?** Tell Claude which one; it reads that folder's `project-context.md` (and `memory/` where present).
- **Adding a project?** Copy `TEMPLATES/project-context.md` into a new `PROJECTS/<Name>/` folder and fill it in at operational altitude. Add a row above. Propose it to the team so the shared picture stays consistent.
- **Structure:** active client/product folders carry `project-context.md` plus (for the deepest engagements) a `CLAUDE.md`, `mcp-decisions.md`, and a `memory/` directory. Lighter engagements may have just `project-context.md`.
