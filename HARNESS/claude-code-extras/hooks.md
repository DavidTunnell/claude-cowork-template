# Hooks (Claude Code only)

**Last Updated:** 2026-05-04

Hooks let Claude Code run a script at fixed lifecycle points. The harness uses them to automate the janitor work — auto-loading memory, auto-running verify, auto-saving retros — so the model doesn't have to remember and the user doesn't have to nag.

Hooks are configured in Claude Code's settings (typically `~/.claude/settings.json` or per-project `.claude/settings.json`). Check the current Claude Code docs for the exact path and schema; the patterns below are what each hook is *for*.

## Lifecycle points

| Hook | Fires when | Best use |
|------|-----------|----------|
| `SessionStart` | Beginning of a session | Load latest retro and `MEMORY.md` index |
| `PreToolUse` | Before any tool call | Block dangerous commands; log what's about to happen |
| `PostToolUse` | After any tool call | Auto-run verify on `Edit`/`Write`; auto-commit on success |
| `PreCompact` | Before context compaction | Snapshot session state into a retro |
| `Stop` | End of session | Save final retro; update `MEMORY.md` Last Updated |

## Highest-value hooks (install these first)

### 1. SessionStart — load the latest retro

Goal: every session starts with continuity. The model should never have to ask "what were we working on?"

```bash
#!/usr/bin/env bash
# .claude/hooks/session-start.sh
# Print the latest retro so the model picks it up in the initial context.
RETRO_DIR="docs/retros"
LATEST=$(ls -1t "$RETRO_DIR"/*.md 2>/dev/null | head -1)
if [ -n "$LATEST" ]; then
  echo "=== Latest retro: $LATEST ==="
  cat "$LATEST"
fi
echo "=== MEMORY.md ==="
cat memory/MEMORY.md 2>/dev/null || echo "(no memory yet)"
```

### 2. PostToolUse — auto-run verify after edits

Goal: every meaningful edit is verified before the next step. Removes the temptation to skip verify "just this once."

```bash
#!/usr/bin/env bash
# .claude/hooks/post-tool-use.sh
# Runs after Edit and Write. Runs the project's verify command and shows output.
TOOL_NAME="$1"
case "$TOOL_NAME" in
  Edit|Write|MultiEdit)
    if [ -f "verify.sh" ]; then
      ./verify.sh
    elif [ -f "package.json" ] && grep -q '"typecheck"' package.json; then
      npm run typecheck
    fi
    ;;
esac
```

The `verify.sh` convention is recommended: a single project-level script that runs whatever combination of typecheck / test / lint the project needs. CLAUDE.md's verify command points at this script.

### 3. Stop — auto-save retro

Goal: every non-trivial session leaves a retro. The next session starts with continuity (see hook #1).

```bash
#!/usr/bin/env bash
# .claude/hooks/stop.sh
# At session end, write a retro from session metadata.
DATE=$(date +%Y-%m-%d)
TOPIC="${1:-untitled}"
RETRO_FILE="docs/retros/${DATE}-${TOPIC}.md"
mkdir -p docs/retros
# Use the harness retro template as the seed; the model fills it in during the session.
cp HARNESS/retros/retro.template.md "$RETRO_FILE" 2>/dev/null || true
echo "Retro stub: $RETRO_FILE"
```

In practice, the retro is written *during* the session by the model (or by `/retro` — see slash-commands.md) and the hook just nudges if no retro was written.

## Lower-value but worth it

### PreToolUse — block approval-required areas

If `CLAUDE.md` has an "Approval-required areas" section, a `PreToolUse` hook can intercept tool calls that touch those paths and require explicit user override.

```bash
#!/usr/bin/env bash
# .claude/hooks/pre-tool-use.sh
TOOL_NAME="$1"
TARGET="$2"
case "$TOOL_NAME" in
  Edit|Write|MultiEdit)
    case "$TARGET" in
      *src/auth/*|*src/payments/*|*infra/prod/*)
        echo "BLOCKED: $TARGET is in an approval-required area. Get explicit user approval before retrying."
        exit 1
        ;;
    esac
    ;;
esac
exit 0
```

### PreCompact — snapshot before context loss

When Claude Code compacts the conversation to free context, you lose the full session log. A `PreCompact` hook can dump the current state to a retro so nothing important evaporates.

```bash
#!/usr/bin/env bash
# .claude/hooks/pre-compact.sh
DATE=$(date +%Y-%m-%d-%H%M)
echo "Compacting at $DATE — saving snapshot."
# Append a snapshot section to today's retro
RETRO_FILE="docs/retros/$(date +%Y-%m-%d)-snapshot.md"
echo -e "\n## Compaction snapshot $DATE\n" >> "$RETRO_FILE"
```

## Installation pattern

For each hook:

1. Drop the script under `.claude/hooks/<hook-name>.sh` in the project repo.
2. `chmod +x` it.
3. Register in `.claude/settings.json` (check current Claude Code docs for exact schema).
4. Restart the Claude Code session so the new hook config loads.
5. Verify by triggering the hook intentionally (e.g., make an edit, watch verify run).

## Failure modes to watch for

- **Hook scripts that hang** block the entire tool flow. Always set timeouts on long-running commands.
- **Hook scripts that fail silently** make Claude think something verified that didn't. Always exit non-zero on failure and let Claude Code surface it.
- **Hook scripts that depend on shell state** break when the working directory isn't what you expect. Always `cd` to repo root at the top of the script.
- **Hooks that run heavy commands on every keystroke** kill the session. `PostToolUse` running a 30-second test suite on every edit is a recipe for rage-quitting. Scope to the specific tool names that warrant it.
