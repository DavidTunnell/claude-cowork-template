---
name: loop-engineering
description: "Design and run an autonomous agent loop for a task instead of hand-prompting it. Invoke as /loop-engineering <task>. Turns a goal into a trigger -> maker -> verifier -> memory loop with cost and safety guardrails."
disable-model-invocation: true
---

# Loop engineering — scaffold a loop on demand

You were invoked deliberately. Your job is **not** to do the task by hand. Your
job is to build the smallest loop that does the task, wire in a verifier, and run
it. Cost and risk compound across iterations, so prefer the simplest loop that
meets the goal.

The task to loop on is whatever follows the command (`$ARGUMENTS`). If it's
empty, ask the user for the task and its definition of "done" before continuing.

Work through the steps below, then present the plan (Step 7) and wait for
approval before launching anything that writes at scale, schedules, or spends.

## Step 1 — Define "done" (set the goal)

- Restate the task in one line and state a **verifiable** stopping condition.
- If "done" can't be checked by a command, a test, or an inspectable artifact,
  make it checkable first — that check becomes the verifier's rubric.
- Set the bar with `/goal <condition>` so the loop doesn't quit at a soft
  completion point (the main cause of "it said it was done but wasn't").

## Step 2 — Pick the trigger (which tier)

- **One-shot, now** → just run it once in this session; no scheduling.
- **Repeats while you work, needs session/branch/working-dir state** → Tier 1:
  `/loop [interval] <prompt-or-/command>` (omit the interval to let Claude
  self-pace). You can loop a slash command, not just a prompt.
- **Overnight / hands-off / runs on a fresh clone of main** → Tier 2:
  `/schedule` (a routine on Anthropic's cloud; coarser interval; survives the
  laptop closing).
- **Massive one-shot fan-out across many files** → Tier 3: `/batch <change>`
  (one isolated git-worktree subagent per unit).

Rule of thumb: needs your local working directory → Tier 1; runs cleanly on a
fresh clone → Tier 2. Prefer **event triggers** (hooks, CI, webhooks) over tight
polling — a loop that wakes every minute to find nothing still costs tokens.

## Step 3 — Isolate the work

Anything parallel or risky runs in its own **git worktree** so agents don't
collide and `main` stays clean. `/batch` does this for you; for manual fan-out,
give each maker subagent its own worktree.

## Step 4 — Split maker from verifier (the load-bearing rule)

- **Maker** subagent implements: makes the change, runs `<TEST_CMD>`, returns a
  diff plus the result.
- A **separate verifier** subagent checks it — different instructions, ideally a
  stronger or different model. It sees only the rubric and the artifact, never
  who produced it or the maker's reasoning. Use `references/verifier-prompt.md`.
- A model grading its own output is too generous; this split is what makes an
  unattended run trustworthy.
- **Verify against reality**, not vibes: real tests, a built/driven app, a
  browser for web, a simulator for mobile. If the check didn't actually run,
  the work is not verified.

## Step 5 — Persist state (memory)

- Write progress to a durable file so the next iteration or run resumes instead
  of restarting: e.g. `.claude/loop-state/<task-slug>.md` for in-session work,
  or a committed `.claude/inbox/*.md` / `.claude/audit/*.md` for cross-tier
  handoff (Tier 2 routines run on a fresh clone, so the **only** durable handoff
  is files in the repo: routines write, loops read).
- Over time, distill corrections you keep making into `CLAUDE.md`.

## Step 6 — Guardrails (do not skip)

Cost:

- Gate with a cheap model: a quick "is there any work?" check on `<CHEAP_MODEL>`
  before escalating to `<STRONG_MODEL>`. Don't run your top model on an empty
  queue every few minutes.
- Set an explicit token budget per run; cap iterations and fan-out width.
- Keep a stable prompt prefix across iterations so prompt caching applies.
- Run the verifier on what actually ships, not on every micro-step.

Safety:

- Auto-approve only **reversible** actions. Require explicit human approval for
  irreversible ones: deploys, deletes, force-pushes, data migrations, or sending
  anything to an external party. Never delete without approval.
- **Quarantine untrusted input**: a read-only reader subagent (no privileged
  tools) digests external content (tickets, web pages, emails, third-party API
  output) into a structured summary; a separate actor subagent acts only on that
  summary and never sees the raw text. This removes most prompt-injection risk.
- Keep the loop interruptible (Esc) and make sure it stops on its goal
  condition.

## Step 7 — Present the plan, then run

Output a compact plan, then wait for go-ahead:

- Goal / stop condition (and the `/goal` you'll set)
- Tier + trigger (and interval)
- Isolation (worktrees?)
- Maker (model, tools) and Verifier (model, rubric)
- Memory file path
- Token budget + iteration/fan-out caps
- Approval gates (which actions pause for a human)

After approval, create the needed files/commands and start the loop. While it
runs, emit one terse status line per iteration — no verbosity.

If a single context starts creeping past the job's natural size, split it into
focused subagents with isolated state rather than letting one context drift
(constraints get lost across compaction).

## References

- Patterns, failure modes, job→pattern map, tier model: `references/patterns.md`
- Adversarial verifier system prompt: `references/verifier-prompt.md`

## Calibration (read once)

Built on Claude Code primitives confirmed in the official docs: `/goal`, `/loop`
(plus the `.claude/loop.md` default prompt), `/schedule` routines, `/batch` with
git worktrees, subagents in `.claude/agents/`, and `CLAUDE.md` memory. Command
names and limits vary by version — check `/help`. Fill the placeholders before
first use: `<TEST_CMD>`, `<BUILD_CMD>`, `<CI_CMD>`, `<STRONG_MODEL>`,
`<CHEAP_MODEL>`.
