# HARNESS

**Last Updated:** 2026-05-04

The harness is the *thin layer around the model* that determines whether Claude is useful or noisy. Memory, rules, verification, retros, MCPs, subagent dispatch — that's the harness. The model itself is held constant; only the harness changes.

This folder collects the templates and conventions that make the harness consistent across projects. Reach for it when:

- You're starting a new project repo and need a working `CLAUDE.md` and memory layout.
- A session ends and you need to capture a retro that the next session can read.
- You're picking which MCPs to install for a project (and why).
- You want a verification recipe that prevents "done"-but-not-actually-done outcomes.

## What's in here

- **`CLAUDE.md.template`** — drop into the root of a project repo, fill the placeholders. This is the file that loads every session and gives Claude the stack overview, ownership map, hard rules, verify command, and approval-required areas.
- **`memory/`** — typed-memory convention with prefixes (`user_`, `project_`, `reference_`, `feedback_`) and an index template. Use this so the project's memory dir is browsable instead of a swamp.
- **`retros/retro.template.md`** — the structure for `docs/retros/YYYY-MM-DD-topic.md`. The next session reads the latest retro for continuity. No re-briefing.
- **`mcp-shortlist.md`** — recommended MCPs with the rationale for each, marked by environment (Cowork, Claude Code, both).
- **`verification-patterns.md`** — what "verify before declaring done" actually looks like in practice. Frontend, backend, AWS, infra, content.
- **`harness-setup.skill.md`** — skill draft that walks through applying this folder to a new project. Install via the skill-creator or copy into your user skills directory.
- **`claude-code-extras/`** — the Claude-Code-only annex (hooks, slash commands). These don't apply to Cowork.

## Universal vs. environment-specific

Most of the harness applies in both **Cowork** and **Claude Code**:

- `CLAUDE.md` at repo root — both
- Typed memory files — both (path differs)
- Retros — both
- MCPs — both
- Subagent dispatch — both
- Verification rules — both

A few things only exist in Claude Code:

- Hooks (`PreToolUse`, `PostToolUse`, `SessionStart`, `PreCompact`, `Stop`)
- Slash commands at the CLI
- The `~/.claude/projects/<project>/memory/` path layout

These are isolated to `claude-code-extras/` so the rest of the harness is portable.

## Order of operations when applying to a new project

1. Copy `CLAUDE.md.template` to the project repo root and fill it in.
2. Create the project's memory directory and seed it from `memory/` templates. Start with `MEMORY.md`, `project_overview.md`, and `reference_stack.md`.
3. Pick MCPs from `mcp-shortlist.md` and install them.
4. Set the verify command in `CLAUDE.md` and confirm it runs cleanly once before any work.
5. (Claude Code only) Wire hooks from `claude-code-extras/hooks.md` if you want auto-save / auto-tsc behavior.
6. Run a small task end-to-end and write the first retro to bootstrap continuity.

## Source

The structure is adapted from a community-written Claude Code harness post and pruned to what actually translates to Webapper's environment (Cowork primary, Claude Code secondary). Specific tooling claims from the source post have been kept where they generalize and dropped where they were too tied to one project's stack.
