# Smoke Test Report — HARNESS/ applied to Herb Co

**Last Updated:** 2026-05-04
**Test target:** `PROJECTS/Herb Co/`
**Source:** `HARNESS/` v1 (built same session)
**Tester:** David (via Claude in Cowork)

## Summary

The harness is structurally sound. Six rough edges surfaced, none are blocking, all are fixable in the templates without major rework. Net: **ship it**, with the fixes in §3 prioritized for the next pass.

## 1. Step-by-step status

| Step | What | Result |
|------|------|--------|
| 1 | Drop CLAUDE.md at repo root | PASS — template filled cleanly from `project-context.md`. ~150 lines, in budget. |
| 2 | Create memory directory + seed files | PASS with caveat — needed 5 seeds, not 3 (see edge #2) |
| 3 | Add retro structure | PASS — `docs/retros/2026-05-04-harness-bootstrap.md` written |
| 4 | Pick MCPs from shortlist | PASS — `mcp-decisions.md` written; 3 install, 2 pending, 7 skip |
| 5 | Confirm verify command runs | **FAIL — but in a known way** (see edge #1). iBiz has no verify command. Documented as gap. |
| 6 | Wire hooks (Claude Code only) | SKIPPED — Cowork-primary project |
| 7 | Slash commands (optional) | SKIPPED — Cowork-primary project |

## 2. What worked well (don't change)

- **Five-prefix memory convention** (`user_`, `project_`, `reference_`, `feedback_`, plus index). Filing the project-context content into typed memory was natural — it almost wrote itself once the prefixes were available.
- **Approval-required areas** as a CLAUDE.md section. For Herb Co, this section practically writes itself from the risk table in `project-context.md`. For any project with documented risks, this maps cleanly.
- **Subagent dispatch defaults section** in CLAUDE.md. Project-specific guidance ("for this project, use Explore against the iBiz mirror") is exactly the kind of thing that prevents drift when the model has to decide which tool to use.
- **MCP shortlist with `Use when:` and `Reconsider when:`** framing. Walking it produced a real decision per MCP, not a dump-everything install. Every "skip" had a clear "what would change my mind" condition.
- **Retro template's "Next session should start by" closer.** Forces explicit handoff. Without it, retros drift into "what happened" without a usable starting point for next time.

## 3. Rough edges (prioritized fixes)

### Edge #1 — Verify command assumption (HIGH priority)

**What the template assumes:** Every project has a verify command. Step 5 of harness-setup.skill.md says "Verify command exit code is 0, or the retro explicitly captures why it isn't."

**Reality:** Legacy stacks (ColdFusion + MySQL 5.6 → 8, no tests, no CI, no linter) often have no verify command. iBizFusion is the canonical example. Inventing one (`cfformat --check` is formatting-only and adds zero correctness signal) is worse than admitting the gap.

**Fix:** In `harness-setup.skill.md` step 5, change the language from "confirm the verify command runs" to "establish or document the verify command." Add a third path: "no verify command exists yet — document the gap and add to the project's Phase 2 backlog." In `CLAUDE.md.template`, add a "Status: not yet established" example next to the Verify command block.

### Edge #2 — "Three seed files" undersells it (MEDIUM)

**What the template assumes:** Three seed memory files cover most projects (`MEMORY.md`, `project_overview.md`, `reference_stack.md`).

**Reality:** For Herb Co (mature project, AWS-heavy, sensitive client comms), five was the floor: add `reference_aws_account.md` and `user_preferences.md`. Other projects might need different seeds (e.g., CloudSee Drive would want `reference_aws_account.md` + `reference_jira_workflow.md`).

**Fix:** In `harness-setup.skill.md`, change "Seed with these starter files" from a fixed list of three to a recommended-minimum list with explicit "add these if applicable" extensions: AWS, Jira, payment-systems, integrations.

### Edge #3 — Memory path resolution unclear (MEDIUM)

**What the template assumes:** `<repo>/memory/` for Cowork or `~/.claude/projects/<slug>/memory/` for Claude Code. One project, one repo, one memory dir.

**Reality:** For client work, the project's *docs and harness* live in one repo (claude-cowork-template) while the *source* lives in another local dir (`C:\Users\End-Dream-Office\dev\ibizfusion`). Where does memory go? The template doesn't address it.

**Fix:** Add a "client-engagement pattern" callout in `CLAUDE.md.template` and `HARNESS/memory/README.md`: when the source repo isn't yours, memory lives alongside the project's harness folder (e.g., `PROJECTS/<name>/memory/`), and CLAUDE.md goes in *both* locations or only in the harness folder.

### Edge #4 — Skill draft is opinionated about parallelization (LOW)

**What the skill says:** Step 4 walks through the MCP shortlist *with the user* asking yes/no for each.

**Reality:** Going one-by-one for ~14 MCPs is slow and breaks flow. In practice, I batched the decisions into one `mcp-decisions.md` doc and presented the table for review. Better UX.

**Fix:** Update step 4 to: "Walk the shortlist in one pass and produce a `mcp-decisions.md` table. User reviews the table; iterate by exception."

### Edge #5 — Top-level retro location not obvious (LOW)

**What the template assumes:** Retros at `<repo>/docs/retros/`.

**Reality:** For client-engagement projects with the harness folder pattern, this becomes `PROJECTS/<name>/docs/retros/`. The template doesn't surface this case explicitly.

**Fix:** Same callout as edge #3 — add a "client-engagement pattern" note showing the alternate path.

### Edge #6 — No "what counts as non-trivial" guidance for auto-retro (LOW)

**What rule 38 says:** "Auto-retro after non-trivial sessions." 

**Reality:** "Non-trivial" is doing a lot of work. Is a 10-minute lookup a session? A 30-minute exploration? A 2-hour deep work session? Too many retros become as bad as too few — they drown the signal.

**Fix:** Add a one-line definition to rule 38 or to `retro.template.md`: "Non-trivial = a session that produced an artifact, made a decision, or surfaced a finding worth referencing later. If the session was pure Q&A or one-tool lookups, skip the retro."

## 4. What I'd do differently next time

- **Smoke-test against a project with a working verify command** as the second test (CloudSee Drive is the obvious choice — it has typecheck, tests, Selenium). That'll exercise step 5 properly and surface a different class of issues than Herb Co did.
- **Have CLAUDE.md and project-context.md cross-link explicitly** in their headers. They're the two key load-on-session-start files; the relationship between them shouldn't be implicit.
- **Add a "harness-applied" badge/note to `project-context.md`** so a future session can immediately see "this project has a harness — read CLAUDE.md and memory/ before doing anything."

## 5. Files produced this session

```
PROJECTS/Herb Co/
├── CLAUDE.md
├── SMOKE-TEST-REPORT.md          (this file)
├── mcp-decisions.md
├── project-context.md            (already existed)
├── docs/
│   └── retros/
│       └── 2026-05-04-harness-bootstrap.md
└── memory/
    ├── MEMORY.md
    ├── project_overview.md
    ├── reference_stack.md
    ├── reference_aws_account.md
    └── user_preferences.md
```

Plus, in HARNESS/, no edits yet — the fixes from §3 are queued for the next pass.

## 6. Recommendation

**Promote `HARNESS/` v1 to "use it."** The structure is right. Apply the §3 fixes when you next touch the templates — none are urgent, all are minor. Then run smoke test #2 against CloudSee Drive to flush out the second class of issues (active dev, real verify command, multiple environments).
