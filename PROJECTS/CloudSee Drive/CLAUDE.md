# CLAUDE.md

**Project:** CloudSee Drive
**Last Updated:** 2026-05-04

## Stack overview

CloudSee Drive is Webapper's flagship SaaS — a browser-based interface for Amazon S3 that replaces the AWS Console for non-technical users. Sub-second search across millions of objects (Fast Buckets / OpenSearch), file versioning, RBAC, secure sharing, activity monitoring, files up to 5 TB. Available via AWS Marketplace with same-day onboarding. Strategic goal: replace client services as Webapper's primary business. Full picture in `PROJECTS/CloudSee Drive/project-context.md`.

- **Frontend:** React, hosted on S3 + CloudFront — repo `csd-frontend` (Bitbucket)
- **Backend:** Node.js microservices on AWS Lambda (SAM CLI / CloudFormation) — repo `csd-backend` (Bitbucket)
- **Database:** DynamoDB (env-prefixed tables, `production_*` / `qa_*`)
- **Search:** OpenSearch (Fast Buckets index)
- **Shared SDK:** `@webapper/cloudsee-drive-sdk` (NPM)
- **Auth:** SSO-ready, Microsoft Entra ID for group management
- **CI/CD:** Jenkins (recently optimized with EFS mount)
- **Repos:** Bitbucket — `csd-frontend`, `csd-backend`

## Ownership matrix

| Area | Owner | Approval required for |
|------|-------|----------------------|
| Frontend (`csd-frontend`) | Peter Truong (primary), Max Nguyen | Major UI/UX changes; SSO/auth flows |
| Backend (`csd-backend`) | Max Nguyen, Steven Nguyen | API contracts; permissions logic |
| User API / RBAC | Steven Nguyen | Any permissions change |
| Storage API (S3 ops) | Steven Nguyen | Any change touching multipart, large files, or quota logic |
| Fast Bucket Service (OpenSearch) | Steven Nguyen | Index schema, ingestion pipeline |
| Free Trial / throttling | Steven Nguyen | Limit changes, marketplace integration |
| Jenkins pipeline | Henry Tran | Pipeline edits, build steps |
| Selenium / unit tests | Henry Tran | Test infrastructure changes |
| Manual QA | Daniela Camargo | Acceptance criteria, sign-off |
| Roadmap / prioritization | Patrick Quinn | Feature scope, sprint priority |
| AWS Marketplace + SEO | Scott Herring | Public-facing roadmap, marketplace listing changes |
| Architecture | David Tunnell | Cross-service decisions, AI integration patterns |

## Hard rules

These override anything else. Project-specific gates layered on top of `ABOUT ME/my-rules.md`.

1. **Verify before declaring done.** See "Verify command" below. Frontend changes additionally require a screenshot of the affected screen in a clean state.
2. **Atomic commits, one fix per commit.** WAG-style Jira ticket reference in the message: `[CSD-NNN] One-line summary`.
3. **Bugfixes capped at ~50 lines.** If it grows, propose a refactor as a separate ticket.
4. **Branching: `develop -> qa -> production`.** Never push directly to qa or production. PR review required for production merges.
5. **Don't break the SDK contract.** `@webapper/cloudsee-drive-sdk` is consumed by every microservice. Changes are versioned; breaking changes require coordinated deploy.
6. **Per-environment DynamoDB prefixes.** Code references `production_*` and `qa_*` tables explicitly. Don't hardcode environment names; read from config.
7. **Read a file before editing.** Always.
8. **Match the React + Lambda + DynamoDB pattern.** Functional components and hooks on the frontend. Stateless Lambdas on the backend. New services follow the existing microservice template (User API, Storage API, etc.).
9. **Stay in scope.** WAG sprint commitments. Adjacent improvements get logged as new tickets, not silently included.

## Verify command

The single command to check work before claiming completion. Verify happens **per-package** since the codebase is split across microservices.

```
# Frontend (in csd-frontend/)
npm run typecheck && npm test -- --run && npm run lint

# Backend service (in csd-backend/<service>/)
npm run typecheck && npm test -- --run && npm run lint

# Full check before merging to develop
# (Henry's Jenkins pipeline runs the full Selenium suite — wait for green)
```

For UI changes, also:

- **Run the dev server**, load the affected screen, confirm no console errors
- **Take a screenshot** with browser MCP and show it back
- **Click through the interaction** in the actual UI — verify happens in the browser, not just unit tests

For backend changes that touch DynamoDB or OpenSearch, also:

- **Run against the QA environment** (`qa_*` tables, `drive-qa.cloudsee.cloud`) before promotion to production
- **Confirm Selenium pass** on Jenkins after merge

If verification fails, treat it as a blocker. Roll back rather than patch forward.

## Memory

Persistent memory for this project lives at:

- **Cowork:** `PROJECTS/CloudSee Drive/memory/` (this repo)
- **Claude Code:** `~/.claude/projects/cloudsee-drive/memory/` if running CC against `csd-frontend` or `csd-backend`

Files use typed prefixes — see `HARNESS/memory/README.md`. Index is `MEMORY.md`. Read the latest retro at the start of every session: `PROJECTS/CloudSee Drive/docs/retros/<latest>.md`.

## Subagent dispatch

For this project specifically:

- **Cross-service refactors** (e.g., "where is `getUserPermissions` called?") → Explore subagent across both `csd-frontend` and `csd-backend` repos.
- **OpenSearch query construction** for Fast Bucket changes → general-purpose subagent with the natural-language search docs as reference.
- **AWS infrastructure investigation** (Lambda config, CloudFormation stacks, DynamoDB tables) → general-purpose subagent with `--profile ama-cloudsee` access.
- **Architectural calls** (e.g., "should this new feature be a new microservice or extend Storage API?") → multi-model consultation per `HARNESS/verification-patterns.md`.
- **AI coding feature exploration** (Claude Code primary tool) → main session, pulling Productboard / Jira context.

## Approval-required areas (project-specific)

Before editing or proposing changes in any of these, get explicit approval:

- `csd-frontend` SSO / auth integration (Microsoft Entra ID flow)
- User API permissions logic (role: Regular User / Admin / Owner Admin)
- Free Trial throttling configuration (1M index objects, 100K delta sync ops, 30-day trial limits)
- AWS Marketplace integration (subscriber detection, billing webhook handling)
- DynamoDB schema (any new attribute, any GSI change)
- CloudFormation production templates
- Jenkins pipeline definitions

## Project-specific notes

- **AWS access:** Use `ama-cloudsee` profile via IdC SSO. Region us-east-1.
- **CloudFront:** prod E3MYVWORHVNEPX, qa E21TCNFRHC2JD7, dev E18Q5AYZVNBZS7. Invalidate after frontend deploys.
- **Parameter Store:** `/cloudseedrive/appsetting/{environment}` for app config.
- **Sprint planning:** Tuesdays 2:30 PM CDT, weekly. WAG ticket workflow: New > Discovery > Confirmed > In Progress > Ready to Test > Testing Complete > Ready to Deploy > Ready for Production Testing > Done!
- **Productboard for prioritization** — Patrick pushes Productboard candidates into Jira Discovery. ICE scoring.
- **Public roadmap:** `cloudseedrive.com` shows Launched / In Progress / Planned. Don't reference internal-only feature names externally.
- **AI coding:** Actively using Claude Code for smaller features. Favorites was identified as a good candidate.
