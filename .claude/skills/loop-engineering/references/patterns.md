# Patterns & failure modes (load on demand)

## Three failure modes — diagnose before you design

- **Agentic laziness** — does part of the job, calls the rest "handled."
  Fix: `/goal` + loop-until-done; add a counter/synthesizer that verifies count.
- **Self-preferential bias** — grades its own work favorably. A maker can't be a
  fair verifier of itself. Fix: a separate verifier with fresh eyes on a
  different model, seeing only the rubric and the artifact.
- **Goal drift** — fidelity decays over many turns and across compaction
  ("don't do X" quietly disappears at turn 47). Fix: split into focused
  subagents with isolated state; re-assert constraints each stage.

Shortcut: drift → fan-out; self-preference → adversarial verification;
open-ended → loop-until-done; hard-to-score → tournament.

## Six harness patterns

1. **Classify-and-act** — a router decides the task type and dispatches to
   specialists. Use for triage, support, intake.
2. **Fan-out-and-synthesize** — split into N pieces, run parallel agents, merge
   at a barrier. Use for migrations, bulk scoring, bulk extraction.
3. **Adversarial verification** — every maker gets a fresh-eyes skeptic. The
   verifier sees only the rubric and the artifact, not who produced it.
4. **Generate-and-filter** — produce N candidates, kill the failures, return the
   survivors. Forces late commitment, after every option has been challenged.
5. **Tournament** — pairwise comparison until a winner. For taste-based work;
   comparative judgment beats absolute scoring. Keep the bracket in loop code.
6. **Loop-until-done** — keep going until a stop condition fires (no new
   findings, zero errors, theory verified). Use for flake hunts, root-cause.

Real loops compose two to four of these.

## Job → pattern stack

- **Migrations / refactors**: fan-out (one agent per callsite) → adversarial
  verification → loop-until-done.
- **Deep research**: fan-out (parallel searches) → verify each claim →
  synthesize one cited report.
- **Draft / claim verification**: identify claims → one verifier per claim →
  meta-verifier checks source quality.
- **Sort or rank 1000+ items**: tournament (pairwise / bracket).
- **Root-cause investigation**: generate theories from disjoint evidence → panel
  of verifiers and refuters → loop until one survives.
- **Triage at scale**: classify-and-act → dedupe against existing items →
  fix or escalate (pair with `/loop`).
- **Design / naming / UI choices**: generate-and-filter (5–20 options) →
  tournament with a rubric.

## Tier model — where the loop lives

- **Tier 1 — `/loop`**: runs in an open session; needs local state; dies when
  the session closes; ~1-minute minimum interval.
- **Tier 2 — `/schedule` routines**: run on Anthropic's cloud on a fresh clone
  of `main`; survive the laptop closing; ~1-hour minimum. The only durable
  handoff is files committed to the repo — routines write, loops read.
- **Tier 3 — `/batch`**: massively parallel one-shot fan-out across isolated
  worktrees; job-bound.

Compose them: a routine writes findings to a file → a loop reads and acts on
them → spawns a batch for a big job → results feed the next cycle.
