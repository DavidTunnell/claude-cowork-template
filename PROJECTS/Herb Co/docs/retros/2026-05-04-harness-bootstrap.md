# Retro — 2026-05-04 — Harness Bootstrap (smoke test)

**Project:** Herb Co (iBizFusion)
**Session length:** ~30m (within a longer session that also built HARNESS/)
**Goal of the session:** Smoke-test the new HARNESS/ templates by applying harness-setup against PROJECTS/Herb Co/ to find rough edges before they harden.
**Outcome:** Achieved

## What happened

Built `HARNESS/` (templates, skill draft, MCP shortlist, verification patterns, Claude Code extras). Then ran the harness-setup procedure against the Herb Co project as a smoke test, using `project-context.md` as the input. Generated `CLAUDE.md`, four memory files (`MEMORY.md`, `project_overview.md`, `reference_stack.md`, `reference_aws_account.md`, `user_preferences.md`), this retro, and an MCP decisions doc. Skipped the "run verify command" step because iBizFusion has no verify command — captured this as the headline finding.

## Decisions made

- **Memory directory location for this project:** `PROJECTS/Herb Co/memory/` inside the claude-cowork-template repo, not inside the iBiz source mirror. Rationale: the source repo is sensitive client code; we don't drop scaffolding there without approval.
- **Five seed memory files** instead of three: `user_preferences`, `project_overview`, `reference_stack`, `reference_aws_account`, plus index. The skill draft says "three seed files"; for a project this dense, five is the right floor.
- **Verify command is documented as a known gap.** Rather than fabricate one, the project's CLAUDE.md says "not yet established" and lists candidates for Phase 2. This is the honest state.
- **The smoke test artifacts live alongside `project-context.md`,** not in a separate `harness/` subfolder. Rationale: they ARE the project's harness; nesting them adds a layer with no payoff.

## Files / commits / artifacts touched

- `PROJECTS/Herb Co/CLAUDE.md` — filled-in project CLAUDE.md from template
- `PROJECTS/Herb Co/memory/MEMORY.md` — index
- `PROJECTS/Herb Co/memory/project_overview.md`
- `PROJECTS/Herb Co/memory/reference_stack.md`
- `PROJECTS/Herb Co/memory/reference_aws_account.md`
- `PROJECTS/Herb Co/memory/user_preferences.md`
- `PROJECTS/Herb Co/docs/retros/2026-05-04-harness-bootstrap.md` — this file
- `PROJECTS/Herb Co/mcp-decisions.md` — MCP shortlist applied to this project
- `PROJECTS/Herb Co/SMOKE-TEST-REPORT.md` — findings from this exercise

No commits. Per session-wide instruction, nothing has been committed yet.

## Open threads

- [ ] Establish a Phase 2 verify command for iBizFusion. Candidates: cfformat --check, staging-environment smoke suite. Blocked on staging-env existence.
- [ ] Confirm with Patrick / Steven which MCPs we actually have access to for Beckway tooling (their Atlassian? their Slack? their email?). The mcp-decisions doc has placeholders that need confirmation.
- [ ] Move smoke-test artifacts into the actual iBiz repo when/if we get write access there in Phase 2. Until then, claude-cowork-template is the canonical home.
- [ ] Apply HARNESS/ to CloudSee Drive and VisionAST once Herb Co's harness has been used in anger and the rough edges from this retro are fixed.

## Feedback to bank in memory

These should become `feedback_*.md` entries in the project's memory dir, or in `ABOUT ME/` if user-wide.

- **Don't fabricate a verify command** when none exists. Document the gap and propose a path. (Project-wide: applies anywhere the legacy stack predates testing tooling.)
- **The skill draft says "three seed memory files."** Five was right for Herb Co. Update the skill or note the floor as project-dependent. (Skill-improvement feedback.)
- **The CLAUDE.md template's "Memory path" line** assumes either Cowork or Claude Code, but the Herb Co situation has memory in the cowork template repo while the source is in another local dir. The path resolution wasn't obvious from the template. (Template-improvement feedback.)

## Verify status at session end

The verify command from `CLAUDE.md`:

```
# Not yet established for iBizFusion (see CLAUDE.md "Verify command" section)
```

- **Exit code:** N/A
- **Notes:** Documented as a known gap. Phase 2 deliverable. The smoke test itself can't be "verified" in the conventional sense; instead, see `SMOKE-TEST-REPORT.md` for the structured pass/fail by section.

## Next session should start by

Read `SMOKE-TEST-REPORT.md` first. It lists the rough edges found. Decide which to fix in `HARNESS/` (template / skill changes) versus document as known-by-design constraints. Apply the fixes, then re-run the smoke test against a second project (CloudSee Drive is the natural choice — different stack, more mature dev practices, will surface a different set of issues than Herb Co did).
