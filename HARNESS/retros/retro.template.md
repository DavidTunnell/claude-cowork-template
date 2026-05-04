# Retro — [YYYY-MM-DD] — [topic]

> Save as `docs/retros/YYYY-MM-DD-topic.md` in the project repo.
> The next session reads the latest file in this folder for continuity.
> Keep it short: ~150 lines max. If a session generates more, split into multiple retros.
>
> **When to write a retro:** after any session that produced an artifact, made a decision,
> or surfaced a finding worth referencing later. Skip the retro for pure Q&A or one-tool
> lookups — too many retros drown the signal as much as too few.

**Project:** [Project Name]
**Session length:** [approx, e.g., 2h]
**Goal of the session:** One sentence — what was the user trying to accomplish.
**Outcome:** Achieved | Partially achieved | Not achieved

## What happened

Two-to-five sentence narrative. What did we try, what worked, what didn't. Stick to facts.

## Decisions made

Bulleted list of decisions that future sessions need to honor. Each one with a one-line rationale.

- [Decision] — [why]
- [Decision] — [why]

## Files / commits / artifacts touched

- `path/to/file.ts` — [what changed]
- Commit `[hash]` — [what shipped]
- Artifact `[name]` — [what it is, where it lives]

## Open threads

Things that are unfinished or deferred. Future sessions should pick these up.

- [ ] [thread] — [next action]
- [ ] [thread] — [next action]

## Feedback to bank in memory

Anything Claude got wrong or right that should change behavior next time. These typically become `feedback_*` memory files. One line per item.

- [Behavior to repeat or avoid]
- [Behavior to repeat or avoid]

## Verify status at session end

The verify command from `CLAUDE.md`:

```
[verify command]
```

- **Exit code:** [0 / non-zero]
- **Notes:** [if non-zero, why, and is this the project's responsibility or a known-broken state being deferred]

## Next session should start by

One paragraph or a short list. What's the first move next time someone (Claude or human) opens this project. Pin the resume context here so we don't lose minutes re-orienting.
