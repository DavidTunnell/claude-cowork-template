# CLAUDE.md

**Project:** Herb Co (iBizFusion)
**Last Updated:** 2026-07-01

## Stack overview

Webapper is the technical team for Monterey Bay Herb Co. ("Herbco" / NP Nutra), a Beckway portfolio company. The business runs on iBizFusion — a custom ColdFusion / MySQL ERP built over 20+ years by a single external developer (Adrian, Romania). What began as an audit/stabilize/support engagement has expanded into hosting consolidation, a commerce migration, and retained support. Full picture in `PROJECTS/Herb Co/project-context.md`.

- **iBiz app:** Adobe ColdFusion 2023
- **iBiz database:** MySQL 8 (370+ tables, no published ERD)
- **iBiz hosting:** Windows Server on a managed VPS, third-party WAF
- **New workstreams:** AWS (Lightsail/Route 53/WAF), Azure (App Service + Azure SQL MI), Microsoft 365 / SharePoint / Graph
- **Repos:** iBiz source is Adrian-controlled (engineers keep a read-only local mirror); Webapper-controlled source control is being adopted for the new workstreams.

## Ownership matrix

| Area | Owner | Approval required for |
|------|-------|----------------------|
| iBizFusion source | Adrian (sole dev) | Any direct change in production |
| `commonFunctions.cfm` | Adrian | Any change — god-file, cascade risk |
| Credit-card data handling / encryption | Adrian + Webapper | Any change — payments/PCI-sensitive |
| Salesforce integration | Adrian | Any change to bidirectional sync |
| Authorize.Net payment flow | Adrian | Any change — payments-sensitive |
| iBiz VPS / WAF | Adrian | Any production infra change |
| AWS / Azure managed environments | David Tunnell (Webapper) | IdC user changes; non-routine IAM/infra |
| Web consolidation / commerce migration | Steven Nguyen + David Tunnell | Cutover steps, DNS changes |
| Beckway-facing comms | David Tunnell + Patrick Quinn | Anything implying scope, timeline, or cost |

## Hard rules

These override anything else. Project-specific gates layered on top of `ABOUT ME/my-rules.md`.

1. **Verify before declaring done.** See "Verify command" below — and note its current limitation on the legacy iBiz app.
2. **Atomic commits, one fix per commit.** Enforced in Webapper-controlled repos (the new workstreams). Against Adrian's iBiz repo we work read-only and propose changes.
3. **Bugfixes capped at ~50 lines.** Especially in iBiz — cascade risk means small changes blast smaller.
4. **Don't touch production directly.** iBiz changes route through Adrian until a staging gate exists. New-workstream changes go through PR + staging.
5. **Don't touch approval-required areas.** Auth, payments, `commonFunctions.cfm`, the encryption layer. Period.
6. **Read a file before editing it.** Always.
7. **Match existing patterns.** In iBiz, match Adrian's conventions even where they're not what we'd choose.
8. **Stay in scope.** Confirm which workstream a task belongs to; don't drift across them silently.
9. **Communication tone with Adrian.** Collaborative assessment, not audit. See `project-context.md` § Project Conventions.

## Verify command

**Legacy iBiz app:** no automated tests, no CI, no type checker/linter historically. Verification on iBiz is Adrian's manual UI testing plus Webapper read-only review; a staging smoke-test suite is being introduced with the new SDLC. Until a real verify gate exists on iBiz, every "done" claim on iBiz changes is conditional on manual confirmation — say so explicitly in retros.

**New workstreams (AWS/Azure/commerce):** use the target repo's verify command (typecheck/tests/lint as applicable) plus a staging validation before any production cutover.

## Memory

Persistent memory for this project lives at:

- **Cowork:** `PROJECTS/Herb Co/memory/` (this repo)
- **Claude Code:** `~/.claude/projects/herb-co/memory/` if running CC against your local iBiz mirror

Files use typed prefixes (`user_`, `project_`, `reference_`, `feedback_`) — see `HARNESS/memory/README.md`. Index is `MEMORY.md`. Read the latest retro at the start of every session.

## Subagent dispatch

- **Searching iBiz source** (large CF/CFC codebase) → always an Explore subagent; the main session can't hold meaningful chunks of it.
- **Cross-referencing findings to code** → general-purpose subagent with the source mirror as the search root.
- **Beckway comms drafts** → main session with `ABOUT ME/my-voice.md` loaded.
- **Architecture / migration calls** → multi-model consultation per `HARNESS/verification-patterns.md`.

## Approval-required areas (project-specific)

Get explicit approval before editing or proposing changes in:

- `commonFunctions.cfm` (god-file; cascades across inventory, invoicing, commissions, customer status)
- Anything touching credit-card data (the encryption layer, tokenization, Authorize.Net)
- The Salesforce integration
- Credential cleanup (must be coordinated with Adrian + a parallel secrets-management rollout)
- Production database schema (370+ tables, no ERD — high-risk until mapped)
- DNS / cutover steps for the web consolidation and commerce migration

## Project-specific notes

- **AWS/Azure access:** Webapper IdC SSO + `ama-*` chained profiles. See `memory/reference_aws_account.md` (no account IDs/secrets are stored in this template).
- **Source mirror:** keep a read-only local mirror of the iBiz source (substitute your own path, e.g. `<your-local-dev-path>/ibizfusion`).
- **Don't open the Lucee-vs-Adobe ColdFusion question** with Adrian — loaded topic, not in scope.
- **Don't use "legacy" pejoratively** in any Adrian-facing comm.
