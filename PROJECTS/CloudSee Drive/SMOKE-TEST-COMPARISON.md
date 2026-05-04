# Smoke Test Comparison — Herb Co vs CloudSee Drive

**Last Updated:** 2026-05-04
**Test #1 target:** `PROJECTS/Herb Co/` — legacy ColdFusion, single-dev, no verify, client engagement
**Test #2 target:** `PROJECTS/CloudSee Drive/` — active dev, microservices, multi-env, full verify, Webapper-owned
**HARNESS/ versions tested:** v1.0 (Herb Co), v1.1 with §3 fixes applied (CSD)

## Headline

The harness handles both extremes. Herb Co stress-tested the "no verify, sensitive client repo" case; CSD stress-tested the "active dev, full pipeline, multi-package" case. Together they covered enough surface area that the remaining rough edges are minor and accumulating, not foundational.

**Recommendation: ship v1.2 with two small additions** (release-process as a memory extension, multi-package verify example in CLAUDE.md.template) and stop iterating on templates until a real session reveals new gaps.

## What v1.1 fixed (held up cleanly)

| Fix | Held up? | Evidence |
|-----|---------|----------|
| Verify command third path ("not yet established") | YES | Herb Co already used it; CSD didn't need it because verify exists |
| Memory seed minimum + extensions | YES | Both tests added 1–3 extensions beyond the minimum four |
| Client-engagement memory path callout | YES | Herb Co uses the client-engagement pattern; CSD uses standard `<repo>/memory/` if working in source repos. Both paths now documented. |
| MCP decisions in one-pass table | YES | CSD's `mcp-decisions.md` is faster to read and cleaner than Herb Co's per-MCP narrative version. Worth holding to. |
| "Non-trivial session" definition for retros | YES | Both bootstrap sessions clearly cleared the bar (artifacts produced, decisions made). Definition reads cleanly. |

## What CSD surfaced that Herb Co didn't

### Edge #7 — Per-package verify commands (LOW)

**What the v1.1 template assumes:** A single verify command at the project root.

**Reality:** Microservice / monorepo projects have a verify command **per package**. CSD's CLAUDE.md ended up listing two separate commands (frontend, backend service) with prose context.

**Fix for v1.2:** Add a multi-package example to `CLAUDE.md.template` next to the single-command example. Something like:

```
# Single-package projects:
npm run typecheck && npm test -- --run

# Multi-package projects (run per affected package):
# Frontend (in <frontend-repo>/):
npm run typecheck && npm test -- --run && npm run lint
# Backend service (in <backend-repo>/<service>/):
npm run typecheck && npm test -- --run && npm run lint
```

### Edge #8 — `project_release_process.md` is a valuable seed for active projects (LOW)

**What v1.1 lists as applicable extensions:** AWS, Jira, payment-systems, integrations.

**Reality:** CSD's release process (sprint cadence, branch flow, per-environment deploy targets, who-deploys-what) was substantial enough to deserve its own memory file. It would have been forced into either `project_overview.md` (too long) or `reference_stack.md` (wrong category) without that file existing.

**Fix for v1.2:** Add `project_release_process.md` to the applicable extensions list in `harness-setup.skill.md` step 2. Trigger condition: "for projects with non-trivial deploy flows (multiple environments, branch protection, PR review, scheduled deploys)."

## Side-by-side comparison

### Memory directory shape

| File | Herb Co | CSD | Notes |
|------|---------|-----|-------|
| MEMORY.md | YES | YES | Always |
| project_overview.md | YES | YES | Always |
| project_release_process.md | NO | YES | New extension category |
| reference_stack.md | YES | YES | Always |
| reference_aws_account.md | YES | YES | Both AWS-deployed |
| reference_jira_workflow.md | NO | YES | CSD has CSD project key; Herb Co's tracking is TBD |
| user_preferences.md | YES | YES | Always |

CSD ended up with 7 memory files, Herb Co with 5. Both sat above the v1.1 "minimum four" floor as expected.

### Verify command

| Project | Verify | How it appears in CLAUDE.md |
|---------|--------|----------------------------|
| Herb Co | None established | "Status: not yet established" block per v1.1 fix path #3 |
| CSD | Per-package npm scripts + Selenium on Jenkins | List of commands per package, with screenshot + click-through requirement for UI |

### MCP decisions

| MCP | Herb Co | CSD | Difference |
|-----|---------|-----|-----------|
| Atlassian | Pending (Beckway-side TBD) | Install (Webapper) | CSD has clear Webapper Atlassian access; Herb Co has uncertainty about Beckway access |
| Bitbucket | Will install | Install | Both — both projects in Bitbucket |
| Google Drive | Installed | Install | Beckway shared drive vs CSD permissions docs |
| AWS API + CLI | Installed | Install | Both AWS-heavy |
| Browser automation | Skip until Phase 2 | Install | CSD has live UI; Herb Co Phase 1 is read-only assessment |
| Slack | Likely install (Webapper) | Install (Webapper) | Same |
| Sentry | Skip until monitoring exists | Install if wired up | CSD might have Sentry; Herb Co definitely doesn't |
| Database MCPs | Skip until DB access | Skip (DynamoDB via AWS API) | Different reasons; same outcome |
| Context7 | Skip | Skip | Both stable stacks |
| Productboard | N/A | Pending (registry check) | CSD-specific |
| OpenSearch MCP | N/A | Pending (registry check) | CSD-specific |

### Approval-required areas

| Project | Areas |
|---------|-------|
| Herb Co | `commonFunctions.cfm`, credit card encryption, Salesforce integration, Authorize.Net, Hostek/SiteLock, AWS account, Beckway comms |
| CSD | SSO/auth, User API permissions, Free Trial throttling, Marketplace integration, DynamoDB schema, CloudFormation prod, Jenkins pipeline |

Both projects produced a substantive list. The CLAUDE.md template's "Approval-required areas" section is doing useful work in both.

## Net assessment

**v1.1 is production-ready for Webapper's project mix.** The remaining issues (Edges 7 and 8) are additions, not corrections — the existing structure is right; we just want to broaden the example coverage.

**Promote `harness-setup` from a skill draft to a real installable skill.** Two smoke tests have exercised every step except the live MCP install and hook wiring. Those steps need a live session to test, not another smoke test.

## Recommended v1.2 changes

Two small edits, no rewrites:

1. **`HARNESS/CLAUDE.md.template`** — add a multi-package verify example below the single-command one.
2. **`HARNESS/harness-setup.skill.md` step 2** — add `project_release_process.md` to the applicable extensions list with the trigger condition above.

Both are 5–10 line additions. Skip them if you'd rather hold v1.1 stable until a real session pushes back; the absences won't break anything.

## What's next (if you want to keep iterating)

- **Smoke test #3 — VisionAST.** Different shape again — Webapper SaaS, has client onboarding flow, Peter Truong helps with onboardings. Likely surfaces "client-onboarding workflow" as a needed extension or reveals nothing new and signals diminishing returns.
- **Live application** — apply v1.1 (or v1.2) to CSD by checking the CLAUDE.md into `csd-frontend` and `csd-backend`, and let it actually drive a real Claude Code session against those repos. That's where hooks, slash commands, and the verify rule get real use. This is the next 10x of value, not another smoke test.
- **Promote the skill.** Move `harness-setup.skill.md` into the user skills directory or wrap `HARNESS/` as a Cowork plugin. Either makes it invokable from any project context, not just this one.

## Files produced this session (smoke test #2)

```
PROJECTS/CloudSee Drive/
├── CLAUDE.md
├── SMOKE-TEST-COMPARISON.md      (this file)
├── mcp-decisions.md
├── project-context.md            (already existed)
├── docs/
│   └── retros/
│       └── 2026-05-04-harness-bootstrap.md
└── memory/
    ├── MEMORY.md
    ├── project_overview.md
    ├── project_release_process.md
    ├── reference_aws_account.md
    ├── reference_jira_workflow.md
    ├── reference_stack.md
    └── user_preferences.md
```

Plus, in `HARNESS/`, the v1.1 fixes from the previous step. v1.2 fixes (Edges 7 and 8) are queued but not applied yet.
