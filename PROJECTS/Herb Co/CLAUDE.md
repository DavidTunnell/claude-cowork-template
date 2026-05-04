# CLAUDE.md

**Project:** Herb Co (iBizFusion)
**Last Updated:** 2026-05-04

## Stack overview

Webapper is the technical team for Beckway's acquisition of Monterey Bay Herb Co., which is itself acquiring Thrive (~$23M nutraceutical ingredients supplier). Thrive runs on iBizFusion — a custom ColdFusion / MySQL ERP built over 20+ years by a single external developer (Adrian, Romania). We're in Phase 1 of a 6-month engagement: audit, stabilize, and then operationally support. Full project picture in `PROJECTS/Herb Co/project-context.md`.

- **Application:** ColdFusion 2023 (Adobe), dev machine on CF 2025
- **Database:** MySQL 8 (370+ tables, no published ERD)
- **OS / Hosting:** Windows Server 2025 on Hostek managed VPS
- **WAF:** SiteLock
- **Repos:** Adrian-controlled; Webapper engineers each keep a read-only local mirror at `<your-local-dev-path>/ibizfusion`. Migration to Webapper-controlled GitHub planned for Phase 2.

## Ownership matrix

| Area | Owner | Approval required for |
|------|-------|----------------------|
| All iBizFusion source | Adrian (sole dev) | Any direct change in production |
| `commonFunctions.cfm` | Adrian | Any change — god-file, cascade risk |
| Credit card encryption (CFMX_COMPAT) | Adrian + Webapper Phase 2 | Any change — PCI DSS-sensitive |
| Salesforce integration (REST API) | Adrian | Any change to bidirectional sync |
| Authorize.Net payment flow | Adrian | Any change — payments-sensitive |
| Hostek VPS / SiteLock WAF | Adrian | Any production infra change |
| AWS Herbco UAT account (797601398324) | David Tunnell (Webapper) | Adding/removing IdC users; non-routine IAM |
| Static analysis findings | Steven Nguyen (Webapper) | Remediation prioritization |
| Beckway-facing comms | David Tunnell + Patrick Quinn | Anything implying scope, timeline, or cost |

## Hard rules

These override anything else. Project-specific gates layered on top of `ABOUT ME/my-rules.md`.

1. **Verify before declaring done.** See "Verify command" below — and note its current limitation.
2. **Atomic commits, one fix per commit.** When we have our own GitHub repo (Phase 2), strictly enforced. For now (Phase 1, read-only against Adrian's repo) we don't commit; we propose changes via the prioritized remediation roadmap.
3. **Bugfixes capped at ~50 lines.** Especially in iBiz. The codebase has cascade risk; small changes blast smaller.
4. **Don't touch production directly.** Phase 1 is assessment, not active dev. Any change in iBiz goes through Adrian until we have a staging environment (Phase 2 deliverable).
5. **Don't touch approval-required areas.** Auth, payments, `commonFunctions.cfm`, the encryption layer. Period.
6. **Read a file before editing it.** Always.
7. **Match Adrian's existing patterns.** Even where they're not what we'd choose — Phase 1 is documentation, not refactoring. Phase 2 is when patterns shift.
8. **Stay in scope.** Phase 1 deliverable is the prioritized remediation roadmap. Don't drift into Phase 2 work.
9. **Communication tone with Adrian.** Collaborative assessment, not audit. See `project-context.md` § Project Conventions.

## Verify command

**Status: not yet established for iBizFusion.**

The legacy iBizFusion stack has no automated tests, no CI, no type checker, no linter setup. Verification today is:

```
# Adrian's process: manual UI testing on production
# Webapper's process: read-only static analysis (already delivered 2026-04-06)
```

**Remediation:** establishing a verify command is itself a Phase 2 deliverable. Candidates being evaluated:

- `cfformat --check` (formatting-only, low value but available)
- A staging-environment smoke test suite (target Phase 2, blocked on having a staging env)
- `git status` clean + manual UI smoke against staging (Phase 2 minimum bar)

**Until verify exists:** every "done" claim on iBiz changes is conditional on Adrian's manual confirmation. Document this explicitly in retros — don't pretend a change is verified when it isn't.

## Memory

Persistent memory for this project lives at:

- **Cowork:** `PROJECTS/Herb Co/memory/` (in the claude-cowork-template repo)
- **Claude Code:** `~/.claude/projects/herb-co/memory/` if running CC against your local iBiz mirror

Files use typed prefixes (`user_`, `project_`, `reference_`, `feedback_`) — see `HARNESS/memory/README.md`. Index is `MEMORY.md`. Read the latest retro at the start of every session: `PROJECTS/Herb Co/docs/retros/<latest>.md`.

## Subagent dispatch

For this project specifically:

- **Searching iBiz source** (the 1,334-file CF/CFC codebase) → always Explore subagent. Main session can't hold meaningful chunks of that codebase.
- **Cross-referencing static analysis findings to actual code** → general-purpose subagent with the static analysis PDF as a reference and the source mirror path as the search root.
- **Beckway PMO communications drafts** → main session, with `ABOUT ME/my-voice.md` loaded.
- **Architecture / migration calls** (e.g., "should we modernize CFMX_COMPAT now or in Phase 2") → multi-model consultation per `HARNESS/verification-patterns.md`.

## Approval-required areas (project-specific)

Before editing or proposing changes in any of these, get explicit approval:

- `commonFunctions.cfm` — god-file, change cascades unpredictably across inventory, invoicing, commissions, customer status
- Anything related to credit card data (CFMX_COMPAT encryption, tokenization flow, Authorize.Net integration)
- Anything in the Salesforce integration (REST API endpoints, sync logic)
- Hardcoded credentials cleanup — must be coordinated with Adrian and a parallel Secrets Manager / vault rollout to avoid downtime
- Production database schema (370+ tables, no ERD — every change is high-risk until we map it)

## Project-specific notes

- **AWS access:** Use IdC SSO. Profile pattern `ama-herbco-uat-ro` (read-only, default) and `ama-herbco-uat-admin` (writes). Account 797601398324, region us-east-1. CloudTrail to `herbco-cloudtrail-797601398324`. See `OUTPUTS/herbco-aws-uat/` for setup artifacts.
- **Source mirror:** `<your-local-dev-path>/ibizfusion` (read-only mirror of Adrian's repo). For active dev work on the same code, keep a parallel checkout at `<your-local-dev-path>/dev-main`.
- **SharePoint docs mirror:** `<your-local-dev-path>/ibizfusion/_drive_docs/iBizFusion Documentation (Sharepoint)`.
- **Drive folder:** https://drive.google.com/drive/u/0/folders/0AAU0auAeY4VQUk9PVA — full link list in `project-context.md`.
- **Don't open the Lucee-vs-Adobe ColdFusion question** with Adrian. Loaded topic; not Phase 1 scope.
- **Don't use "legacy" pejoratively** in any Adrian-facing comm. To him this is his life's work.
