---
name: harness-setup
description: Apply the Webapper harness (CLAUDE.md, typed memory, retros, MCPs, verify-before-done) to a project. Use when starting a new project repo, when an existing repo has no harness, or when a session is going off the rails because there's no continuity scaffold. Trigger phrases - "set up the harness", "scaffold CLAUDE.md", "harness this project", "wire up memory and retros".
---

# Harness Setup

You are setting up the project-level harness defined in `HARNESS/`. The goal is a working `CLAUDE.md`, a typed memory directory, a retro file, the right MCPs installed, and a verified verify command — in that order. Don't claim done until each step is verified.

## Inputs you need before starting

Ask for these up front. If the user gives you a repo path, infer what you can and confirm.

1. **Project name and repo root path.**
2. **Stack** — frontend, backend, database, infra. Pull from `PROJECTS/<name>/project-context.md` if it exists.
3. **Verify command** — the single command that proves the codebase is in a good state. Common patterns: `npm run typecheck && npm test`, `pnpm tsc --noEmit && pnpm vitest run`, `make ci`, `pytest -q`. If unknown, ask.
4. **Approval-required areas** — code paths that need explicit approval before edits (auth, payments, migrations).
5. **Environment** — Cowork only, Claude Code only, or both. Hooks step is skipped for Cowork-only.

## Steps

### 1. Drop CLAUDE.md at repo root

Copy `HARNESS/CLAUDE.md.template` to `<repo root>/CLAUDE.md`. Fill in:

- Project name, last-updated date
- Stack overview, repos
- Ownership matrix (read from `PROJECTS/<name>/project-context.md` Team table if present)
- Verify command
- Approval-required areas
- Memory path (Cowork: `<repo>/memory/`; Claude Code: `~/.claude/projects/<slug>/memory/`)

**Verify:** Show the filled-in CLAUDE.md. Confirm with the user before moving on.

### 2. Create the memory directory

Cowork: `<repo>/memory/`. Claude Code: `~/.claude/projects/<slug>/memory/`. Choose one based on environment.

Seed with these starter files:

- `MEMORY.md` — index, copy from `HARNESS/memory/MEMORY.md.template` and fill in the project name + the three seed entries below.
- `project_overview.md` — one-paragraph what-and-why of the project. Pull from `project-context.md` if present.
- `reference_stack.md` — the full stack with versions and any non-obvious lib choices.
- `user_preferences.md` — start empty with a short comment explaining what goes here. The user (or Claude observing) fills this over time.

**Verify:** `ls memory/` shows the four files. `MEMORY.md` lists them.

### 3. Add the retro structure

Create `<repo>/docs/retros/`. Copy `HARNESS/retros/retro.template.md` to `<repo>/docs/retros/SETUP-YYYY-MM-DD-harness-bootstrap.md`. Fill it in for this setup session as a smoke test.

**Verify:** The retro exists, has been filled in, and CLAUDE.md's "Read the latest retro at session start" instruction now has something to point at.

### 4. Pick and install MCPs

Walk through `HARNESS/mcp-shortlist.md`. For each, ask the user:

- Is this project's stack served by this MCP? (e.g., is the data in Supabase, Postgres, or DynamoDB?)
- Do you already have an account / are you authenticated?
- Yes / no / skip.

Install the agreed-on set. For Cowork, this is via the cowork plugin / connector flow. For Claude Code, it's `claude mcp add` or the equivalent config.

**Verify:** Each installed MCP responds to a smoke-test call (e.g., `list_workspaces`, `search_files` against the project). Show the responses.

### 5. Establish or document the verify command

Three paths, in order of preference:

1. **Verify command exists.** Run it once and show the output. If it passes, exit code 0; the harness is verified-ready. If it fails, the project isn't in a clean state — fix that or capture the known-broken state in the retro and `MEMORY.md`.
2. **Verify command needs to be assembled** from existing tooling. The project has typecheck, tests, or lint but no single entry point. Propose a `verify.sh` or package.json `verify` script that wires them together. Get user approval before adding it; this is touching the project repo.
3. **No verify command exists** (legacy stack, no tests, no CI). **Don't fabricate one.** Inventing a `cfformat --check` or similar that adds no correctness signal is worse than admitting the gap. Document explicitly in `CLAUDE.md` under "Verify command":

   ```
   Status: not yet established. <one paragraph why>
   Candidates being evaluated: <list>
   Until verify exists: every "done" claim is conditional on <fallback verification>.
   ```

   Then add "establish a verify command" to the project's Phase 2 / improvement backlog.

**Verify:** One of three states is true and explicit:

- Verify command runs, exit code 0
- Verify command runs, fails for a documented and accepted reason
- Verify command does not exist; the gap is documented in CLAUDE.md and queued for follow-up

### 6. (Claude Code only) Wire hooks

If the user is using Claude Code, walk through `HARNESS/claude-code-extras/hooks.md`. The minimum set:

- `PostToolUse` on `Edit`/`Write` — auto-run the verify command
- `SessionStart` — load latest retro + MEMORY.md index
- `Stop` — auto-save retro

**Verify:** Edit a no-op character in a file, save, and confirm the hook fires.

### 7. (Optional) Wire slash commands

If the user wants the `/retro` and `/consult` commands, install from `HARNESS/claude-code-extras/slash-commands.md`.

## Done criteria

The harness is set up when all of the following are true. Read this list back to the user before claiming completion.

- [ ] `CLAUDE.md` exists at repo root and is filled in
- [ ] Memory directory exists with `MEMORY.md` index and three seed files
- [ ] At least one retro exists under `docs/retros/`
- [ ] MCP shortlist has been walked through and decisions are recorded
- [ ] Verify command exit code is 0 (or known-broken state is documented)
- [ ] (Claude Code only) Hooks are wired
- [ ] User has been shown a summary and confirmed

## What to avoid

- Don't paste the entire CLAUDE.md.template content unfilled. Either fill it or don't drop it.
- Don't install MCPs the user didn't approve. Walk through the shortlist and ask.
- Don't fabricate a verify command. If you don't know what verifies this codebase, ask.
- Don't declare done after step 5 if the verify command failed silently — that's exactly what verify-before-done is supposed to prevent.
