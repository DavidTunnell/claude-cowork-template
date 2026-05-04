# Claude Code Extras

**Last Updated:** 2026-05-04

Everything in this folder applies **only when running Claude Code** (the CLI). Cowork doesn't expose the same primitives, so these don't carry over.

If you're working in Cowork, you can ignore this folder. The rest of `HARNESS/` (CLAUDE.md, memory, retros, MCPs, verification patterns) is what you need.

## What's here

- **`hooks.md`** — `PreToolUse`, `PostToolUse`, `SessionStart`, `PreCompact`, `Stop`. Auto-save memory, auto-run verify on edits, sync state before context compaction. The "harness does the janitor work" part of the post.
- **`slash-commands.md`** — `/retro`, `/consult` (multi-model panel), `/verify`. The shortcuts that turn the patterns elsewhere in `HARNESS/` into one-keystroke workflows.

## When to set this up

The minute the verify-before-done rule starts feeling like manual overhead. Hooks make it automatic — you can't forget to run typecheck if `PostToolUse` runs it for you on every edit.

## When to skip it

- Cowork-only workflow. Not applicable.
- Short-lived experiments where you don't care about session continuity.
- Projects you only touch once. The setup time isn't worth it.

## Order of operations

1. Get the rest of `HARNESS/` working without hooks first. Make sure `CLAUDE.md`, memory, and retros are doing their job manually.
2. Add `SessionStart` hook to auto-load latest retro. This is the highest-value single hook.
3. Add `PostToolUse` hook on `Edit`/`Write` to auto-run verify. This is the second.
4. Add `Stop` hook to auto-save retro. Set-and-forget continuity.
5. Add slash commands as wanted.

Don't try to wire all of it on day one. Each hook is a script that can break and add friction; introduce them one at a time.
