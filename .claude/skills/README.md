# Skills (optional)

Skills are reusable, self-contained capabilities Claude can invoke by name (e.g. `/adhd`). They're **optional** — delete this folder if you don't need it, or add your own.

Two examples are included so you can see the shape:

- **adhd** — parallel divergent ideation. Explores an open-ended problem from several different "cognitive frames" at once, then scores and converges on the strongest options. Useful for architecture, naming, positioning, strategy, or any decision where the obvious answer might be wrong. General-purpose (not tied to software), but it's expensive (~10 model calls per run), so it's gated to high-stakes, open-ended questions.
- **loop-engineering** — a pattern for iterative, verifier-driven work loops. Software/coding-oriented; most useful for engineering teams.

## Adding your own

Create `.claude/skills/<name>/SKILL.md` with YAML frontmatter and instructions:

```markdown
---
name: my-skill
description: One or two sentences on what it does and when Claude should use it.
---

# My Skill

The steps or method Claude should follow when this skill is invoked.
```

Keep each skill focused on a single capability. Put any supporting files in a `references/` subfolder next to `SKILL.md`.
