# Memory Directory Convention

**Last Updated:** 2026-05-04

Persistent memory for a project. Files are typed by prefix so the directory stays browsable as it grows. The index lives in `MEMORY.md` and points at every file in here with a one-line description.

## File path

- **Cowork:** `<repo>/memory/`
- **Claude Code:** `~/.claude/projects/<project-slug>/memory/`

Pick one and stick with it for a given project. If you switch environments later, copy the directory over.

## Naming convention

Every file uses one of these prefixes. The prefix is the type; what comes after is the topic.

| Prefix | What goes here |
|--------|----------------|
| `user_` | Preferences, voice, working style, gotchas the *user* has stated. Personal-to-the-user, not project-specific. (Often duplicated from `ABOUT ME/` for portability.) |
| `project_` | Facts about the project itself. Architecture, conventions, sprint cadence, deployment process, business context. |
| `reference_` | Stable technical facts the user wants Claude to anchor on. Stack with versions, library choices, infra topology, third-party API contracts. |
| `feedback_` | Things Claude got wrong (or right) in past sessions and how to avoid repeating the mistake (or repeat the win). Grows from retros. |

Examples:

- `user_preferences.md`
- `user_voice.md`
- `project_overview.md`
- `project_release_process.md`
- `reference_stack.md`
- `reference_aws_accounts.md`
- `feedback_dont_use_class_components.md`
- `feedback_always_use_aws_profile_ama_cloudsee.md`

## Rules of thumb

- **One topic per file.** If a file is hitting 200+ lines, split it. Browsability beats completeness.
- **Title and one-line summary at the top of every file.** This is what `MEMORY.md` quotes back.
- **Last Updated date at the top.** Stale memory is worse than missing memory.
- **Append `feedback_` files from retros.** Don't write feedback inline in project files; keep the signal separate.
- **Don't put secrets here.** Memory is read by every session. Secrets go in `.env`, AWS Secrets Manager, or 1Password — never in markdown.

## Loading order

When a session starts, the typical load order is:

1. `MEMORY.md` (always)
2. `project_overview.md` (always)
3. `reference_stack.md` (always)
4. The latest `feedback_*` files relevant to the current task
5. The latest entry in `docs/retros/` (always)

`user_*` files are loaded if the user has them outside the project (often in `ABOUT ME/`) so the project copy is optional.

## What memory is *not*

- Not a journal. Don't log every session here. That's what `docs/retros/` is for.
- Not a tasklist. That's `TASKS.md` (productivity plugin) or Jira.
- Not source-of-truth code documentation. That stays in the repo's actual docs.
- Not a backup of `project-context.md`. The two should reference each other but not duplicate.
