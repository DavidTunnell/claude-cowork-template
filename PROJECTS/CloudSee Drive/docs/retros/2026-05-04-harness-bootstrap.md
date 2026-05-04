# Retro — 2026-05-04 — Harness Bootstrap (smoke test #2)

**Project:** CloudSee Drive
**Session length:** ~25m (within a longer session that also fixed HARNESS/ v1)
**Goal of the session:** Run smoke test #2 against CSD to surface the second class of harness issues — projects with active dev, real verify command, multiple environments, mature Jira workflow.
**Outcome:** Achieved

## What happened

Built `PROJECTS/CloudSee Drive/{CLAUDE.md, memory/, docs/retros/, mcp-decisions.md}` from the v1.1 templates (post-Herb-Co fixes). Used the project-context.md as primary input, with `HARNESS/CLAUDE.md.template` and the four-or-more memory-seed pattern from the updated skill. Real verify command exists (per-package typecheck + tests + lint + Selenium on Jenkins); documented per CLAUDE.md.

The v1.1 fixes from the Herb Co retro all held up:

- **Verify command third path** wasn't needed here — CSD has a real one. Wrote it per-package since the codebase is multi-service.
- **Memory seed extensions** (`reference_aws_account.md`, `reference_jira_workflow.md`) used as suggested by the updated skill. Both were the right call.
- **Client-engagement memory path callout** wasn't needed — CSD is a Webapper-owned repo, so `<repo>/memory/` would apply if working in `csd-frontend` or `csd-backend` directly. The `PROJECTS/CloudSee Drive/memory/` location works as the project-docs anchor in claude-cowork-template.
- **MCP decisions table format** worked well. Single pass, table at the bottom mirroring the shortlist. Faster than per-MCP Q&A.

## Decisions made

- **Five seed memory files** for CSD: `MEMORY.md`, `project_overview.md`, `project_release_process.md` (added because release process is non-trivial here), `reference_stack.md`, `reference_aws_account.md`, `reference_jira_workflow.md`, `user_preferences.md`. That's seven total. The "minimum four plus extensions" guidance from v1.1 fits.
- **Verify command is per-package, not one project-wide command.** The microservices architecture means `npm run typecheck && npm test --run` in each repo. Captured this in CLAUDE.md as a list.
- **Browser automation MCP install is the right call** for CSD. Frontend changes need screenshot + click-through verification. Without it, CLAUDE.md's verify rule for UI work isn't actionable.
- **Sentry MCP marked "install if wired up"** rather than yes/no. If it's not instrumented, the MCP has nothing to read. Captured as a confirmation needed.

## Files / commits / artifacts touched

- `PROJECTS/CloudSee Drive/CLAUDE.md`
- `PROJECTS/CloudSee Drive/memory/MEMORY.md`
- `PROJECTS/CloudSee Drive/memory/project_overview.md`
- `PROJECTS/CloudSee Drive/memory/project_release_process.md`
- `PROJECTS/CloudSee Drive/memory/reference_stack.md`
- `PROJECTS/CloudSee Drive/memory/reference_aws_account.md`
- `PROJECTS/CloudSee Drive/memory/reference_jira_workflow.md`
- `PROJECTS/CloudSee Drive/memory/user_preferences.md`
- `PROJECTS/CloudSee Drive/mcp-decisions.md`
- `PROJECTS/CloudSee Drive/docs/retros/2026-05-04-harness-bootstrap.md` — this file

No commits. Same posture as the rest of the session.

## Open threads

- [ ] Confirm Sentry instrumentation state on CSD before installing the MCP.
- [ ] Check MCP registry for Productboard and OpenSearch MCPs; install if available and useful.
- [ ] Run the smoke-test MCP calls listed in `mcp-decisions.md` once the install pass is done. They're written to be runnable.
- [ ] Decide whether VisionAST gets a harness-bootstrap session next, or if we hold off until Herb Co produces real session-to-session continuity wins worth replicating.

## Feedback to bank in memory

- **`project_release_process.md` was a valuable addition** for CSD specifically, not in the suggested-extensions list in the v1.1 skill. Consider adding "release process" to the applicable extensions in `harness-setup.skill.md` for projects with non-trivial deploy flows.
- **Per-package verify commands** are common enough (any monorepo or multi-service project) that the `CLAUDE.md.template` might benefit from a multi-package example alongside the single-command example.
- **The MCP decisions table approach** (single pass, full table, iterate by exception) was clearly better than the original per-MCP Q&A. Worth holding to.

## Verify status at session end

The verify command from `CLAUDE.md`:

```
# Frontend (in csd-frontend/)
npm run typecheck && npm test -- --run && npm run lint

# Backend service (in csd-backend/<service>/)
npm run typecheck && npm test -- --run && npm run lint
```

- **Exit code:** N/A — these weren't run in this session because we didn't touch the actual `csd-frontend` or `csd-backend` repos. The smoke test was scoped to the project-docs harness in claude-cowork-template, not to live code.
- **Notes:** When/if a real CSD session uses this CLAUDE.md, the verify command should run against the affected package and exit 0 before "done" claims.

## Next session should start by

Read `SMOKE-TEST-COMPARISON.md` (paired with this retro). It compares Herb Co (legacy, no verify) and CSD (active, full verify) outcomes and recommends what to do with `HARNESS/` next. The decision point: ship v1.2 with the two new lessons (release-process extension, multi-package verify example) and stop iterating until a real session reveals more, or apply VisionAST as smoke test #3.
