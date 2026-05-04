# Slash Commands (Claude Code only)

**Last Updated:** 2026-05-04

Slash commands turn the harness's recurring patterns into one-keystroke workflows. They live in `.claude/commands/` (project-scoped) or `~/.claude/commands/` (user-scoped). Each command is a markdown file whose contents become the prompt when the command is invoked.

The three worth installing first.

## /retro — write the session retro

Project file: `.claude/commands/retro.md`

```
Write a retro for this session using the structure in HARNESS/retros/retro.template.md.
Save it to docs/retros/YYYY-MM-DD-<short-topic>.md. Pick the date from today and the topic from the
session's actual goal. Fill in every section honestly — no padding, no exaggerating outcome.

Pay special attention to:
- "Decisions made" — capture every commitment, not just the big ones
- "Feedback to bank in memory" — these become feedback_*.md files
- "Verify status at session end" — paste the actual command output, not a summary
- "Next session should start by" — make it specific enough that someone with no context
  can pick up cleanly

After writing the retro, propose any feedback_*.md updates to the memory directory.
```

Usage: `/retro` at end of session, or whenever a logical chunk of work wraps up.

## /consult — multi-model consultation panel

Project file: `.claude/commands/consult.md`

```
Run a multi-model consultation on the question I'm about to describe.

Process:
1. Restate the question in your own words to confirm you understand it.
2. Identify the constraints I haven't stated explicitly but that matter.
3. Dispatch a Plan subagent for an implementation approach.
4. If a Gemini or other-model MCP is available, ask the same question there.
5. Compare the answers. Tell me where they agree (high confidence), where they
   diverge (needs investigation), and what each model's weak point is.
6. Don't pick a winner. Show me the full picture and let me decide.

Use this for: architecture decisions, security model design, migration plans,
choosing between technologies, anything where a wrong answer is expensive to undo.
```

Usage: `/consult` followed by the question. Trigger any time the answer isn't obvious and the cost of being wrong is high.

## /verify — run verification and show output

Project file: `.claude/commands/verify.md`

```
Run the verify command from CLAUDE.md (or verify.sh if it exists). Show me the full
output, not a summary. If it passes, confirm exit code 0 and the test/typecheck counts.
If it fails, show the failure verbatim and propose the smallest possible fix — do not
patch forward.

After verify, look at the latest commit (or staged changes) and tell me whether the
work in this session is now in a verified-done state or whether something else still
needs to happen.
```

Usage: `/verify` whenever you need a clean status check. Useful as a "do I commit this?" gate.

## Lower-priority but useful

### /resume — load the latest retro

```
Read docs/retros/<latest>.md and memory/MEMORY.md. Tell me in 5-10 lines what was
the state at the end of the last session and what we said the next session should
start by doing. Then ask me what I want to do today.
```

Useful if `SessionStart` hook isn't wired up — gives you the same effect on demand.

### /memory-update — append a feedback file from this session

```
Look at what just happened in this session. Identify any "feedback_*" worthy lessons
— things I said I prefer, things you got wrong and corrected, patterns we settled on.
Propose 1-3 feedback files to write to the project's memory directory. Show me the
proposed content; I'll approve before you write.
```

Useful periodically to keep memory current without writing a full retro.

## Installation

1. Create `.claude/commands/` in the project repo (or `~/.claude/commands/` for user-scoped).
2. Drop each command as `<name>.md`.
3. Restart the Claude Code session if the commands aren't picked up automatically.
4. Test by typing `/<name>` at the prompt.

## Failure modes

- **Commands that re-state CLAUDE.md** add noise. Keep them short — they layer on top of CLAUDE.md, they don't replace it.
- **Commands that try to do too much** in one prompt. `/retro` writing the retro AND updating memory AND committing AND pushing is one command too many. Keep each command to one job.
- **Commands that hard-code paths** that don't exist in this project. The retro template assumes `docs/retros/` and `HARNESS/`. If the project doesn't have those, the command fails confusingly. Check before installing.
