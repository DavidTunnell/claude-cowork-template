# Spec: Automated Transcript-Derived Memory

**Last Updated:** 2026-05-31
**Status:** Draft — design only, not yet implemented
**Owner:** David Tunnell
**Related:** `HARNESS/memory/`, `HARNESS/retros/retro.template.md`, `HARNESS/claude-code-extras/hooks.md`, `ABOUT ME/my-rules.md` (rules 14, 24, 38), `ABOUT ME/our-process.md` (Daily Pulse stack)

## What this is

A plan to automate the memory loop the harness already runs by hand. Today, continuity between sessions depends on discipline: rule 38 says write a retro after non-trivial sessions, the next session reads the latest retro, and `feedback_*` files grow from those retros. It works when followed and silently degrades when a session ends under deadline and nobody writes the retro.

"Transcript-derived memory" is the category name for the fix: a separate process reads the session transcript *after* the work is done and writes durable artifacts the next session loads. We already produce the artifacts (retros, typed memory files) and already have the destination (the `memory/` dir and `docs/retros/`). This spec is about making the capture → summarize → load loop fire automatically instead of relying on the model and the user to remember.

It does **not** propose adopting an external tool. The capture stays local and repo-native by default. A third-party SaaS that streams full session transcripts (prompts, tool I/O, file contents) to its own cloud is incompatible with our client-confidentiality posture and rule 14 (secrets and sensitive data never leave controlled infrastructure). That constraint is the spine of this spec.

## Why now

- The retro discipline is the single most-skipped harness rule because it lands at the end of a session when attention is lowest.
- `HARNESS/claude-code-extras/hooks.md` already sketches the hook scripts. This spec turns those sketches into a defined, installable feature of the Webapper OS with acceptance criteria, rather than leaving them as illustrative snippets.
- The CTO-track ambition — standardized agents and shared knowledge across the company — needs a memory layer that is automatic and eventually team-wide. This is the foundation for that, built in the right order so we don't skip straight to a shared store we can't secure.

## Design principles

1. **Local and repo-native by default.** Artifacts are markdown in the project's `memory/` and `docs/retros/`. Nothing leaves the machine unless a later phase explicitly and deliberately introduces controlled sync.
2. **Curated, not accumulated.** The blog failure mode "now you need a memory system for your memory" is real. Keep the typed-prefix convention, the one-topic-per-file rule, and the 200-line cap. Automation feeds the curation; it does not bypass it.
3. **Re-summarize from source, not from summaries.** To avoid summary drift (a Xerox of a Xerox), consolidation reads raw retros/transcripts where practical rather than summarizing prior summaries.
4. **Fail loud.** A skipped capture or a broken hook must surface, never fail silently. A session that *thinks* it has continuity but doesn't is worse than one that knows it's starting cold.
5. **Environment honesty.** Cowork is primary and has no hooks; Claude Code is secondary and does. The two phases below are deliberately different because the primitives are different.

## Phase 1 — Automate the local loop (Claude Code)

**Goal:** every non-trivial Claude Code session starts with continuity and ends with a saved retro, without the user nagging or the model remembering.

**Scope:**

- Promote the three highest-value hooks from `claude-code-extras/hooks.md` into a real, installable set under a project's `.claude/hooks/`:
  - `SessionStart` — load the latest retro and `memory/MEMORY.md` into initial context.
  - `Stop` — ensure a retro exists for the session; nudge or stub from `retros/retro.template.md` if one wasn't written.
  - `PreCompact` — snapshot session state into a dated retro before context is lost.
- A `verify.sh` convention check so `PostToolUse` verify (already specced) and the memory hooks share one install path.
- An install routine (extend `harness-setup.skill.md`) that wires the hooks into a project one at a time, in the order hooks.md recommends — not all at once.

**Out of scope:** any network calls, any cross-project or cross-user sharing.

**Deliverables:**

- `.claude/hooks/session-start.sh`, `stop.sh`, `pre-compact.sh` finalized (current versions in hooks.md are illustrative; these become tested, error-handled scripts per rule 6).
- A short "Memory hooks" section added to the project `CLAUDE.md.template`.
- Update to `harness-setup.skill.md` covering hook installation.

**Acceptance criteria:**

- Starting a session in a wired project prints the latest retro and `MEMORY.md` with no prompting.
- Ending a non-trivial session leaves a retro in `docs/retros/` following the template.
- A forced compaction writes a snapshot retro rather than losing state.
- Each hook handles its failure states (missing dir, no retros yet, empty memory) without throwing or silently no-op'ing — it reports what it did or could not do.

## Phase 2 — Cowork parity (no hooks)

**Goal:** the same continuity in Cowork, which is our primary environment and has no lifecycle hooks.

**Scope:**

- Define the manual/prompted equivalent of each Phase 1 hook:
  - *SessionStart* → a documented "load latest retro" step the global instructions or a session-opening skill performs.
  - *Stop* → an end-of-session retro step, ideally a `/retro`-style skill the user invokes, or a standing instruction in `my-rules.md`.
  - *PreCompact* → guidance to snapshot to a retro when a Cowork chat grows large (rule 24 already says start a clean session with a summary; this formalizes where that summary lands).
- A decision on whether a lightweight skill can automate the retro-write in Cowork given the available tools, vs. keeping it a prompted convention.
- Path reconciliation: `HARNESS/memory/README.md` already defines Cowork vs. Claude Code memory paths and the client-engagement path under `PROJECTS/<name>/memory/`. Confirm the automated loop respects all three.

**Out of scope:** team sharing; any infrastructure.

**Deliverables:**

- A "Cowork memory loop" doc (or section) describing the prompted equivalents.
- Optional: a session-open and session-close skill in the user skills directory.
- Any edits needed to `my-rules.md` so the loop is a standing rule, not a per-session ask.

**Acceptance criteria:**

- A Cowork session can be opened with continuity from the last retro using a single documented step.
- The retro-write at session end is either automated by a skill or codified as a rule, and lands in the correct per-environment path.

## Phase 3 — Self-hosted team memory (future, gated)

**Goal:** shared knowledge across the Webapper team — one brain for every agent — without sending client work to a third party.

**Why build, not buy:** a consultancy touching VisionAST, CloudSee, and hosted clients (RoverParts, eRep, Icon Media) cannot route session transcripts through an external SaaS. We already run the building blocks in Daily Pulse: OpenSearch for vector embeddings and AWS Bedrock (Claude Sonnet) for AI features. A self-hosted, client-scoped team memory on that stack is defensible to clients and consistent with rule 14.

**Scope (high level — to be refined into its own spec before any build):**

- A consolidation step that reads project retros/memory and produces team-visible, **non-confidential** summaries (decisions, patterns, reusable skills) into a shared store.
- Hard data-classification boundary: client code, secrets, and identifiable client data never enter the shared store. Only generalizable engineering knowledge and internal-process artifacts do. This boundary is the gating requirement and needs explicit definition and sign-off before implementation.
- Scoping model: per-client isolation vs. a shared internal-only corpus. Default assumption is internal/cross-client knowledge is shared; per-client material stays in that client's isolated space.
- Retrieval via the existing OpenSearch + Bedrock path so we reuse infra and don't stand up a parallel system.

**Out of scope for this doc:** implementation. Phase 3 is named here so Phases 1 and 2 are built in a direction that can feed it, but it requires its own spec, a data-classification policy, and multi-model review per rule 39 before any code.

**Gating requirements (all must be met before Phase 3 starts):**

- A written data-classification policy defining exactly what may and may not enter shared memory.
- Confirmation that the design routes nothing client-confidential off a controlled boundary.
- Explicit approval per rule 40 (this touches the same risk class as auth/payments/prod).

## Failure modes to design against

Pulled from the transcript-derived-memory failure list and mapped to our mitigations:

| Failure mode | Our mitigation |
|---|---|
| Summary drift (Xerox of a Xerox) | Principle 3: re-summarize from raw retros where practical; cap memory files and prune. |
| Hallucinated structure (invented "decisions") | Retro template is fixed; consolidation extracts signals, doesn't invent sections; verify per rule 34. |
| Volume — too many summaries | Typed prefixes + `MEMORY.md` index + 200-line cap + retro-skip for pure Q&A (rule 38). |
| Pipeline brittleness — silent hook failure | Principle 4: hooks report what they did; no silent no-ops. |
| Cost — an LLM call every session | Local retros are model-written in-session (already happening), not a separate billed call; Phase 3 reuses Bedrock we already pay for. |
| Over-engineering | Ship Phase 1 hooks one at a time; resist multi-stage pipelines until the simple loop proves out. |

## Open questions

- Can a Cowork skill reliably auto-write the end-of-session retro, or does it stay a prompted convention? (Phase 2 decision.)
- Should `PreCompact` snapshots be a separate file or append to the day's retro? (hooks.md currently does a separate `-snapshot.md`.)
- For client engagements where the source repo isn't ours, retros live under `PROJECTS/<name>/`. Does the `SessionStart` hook need a path-resolution step to find them, or is that Cowork-only?
- Phase 3 only: per-client isolated stores vs. one internal corpus — which is the default, and who owns the classification policy?

## Suggested sequencing

1. Phase 1, `SessionStart` hook only. Highest single-hook value (continuity on open). Prove it on one project.
2. Phase 1, `Stop` then `PreCompact`. Close the loop.
3. Phase 2 Cowork parity, since Cowork is primary — do this before any team work.
4. Phase 3 only after the data-classification policy exists and is approved.

Don't wire everything at once. Each hook is a script that can break and add friction; introduce them one at a time (per hooks.md).
