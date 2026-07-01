# Webapper Claude Cowork Template

**Last Updated:** 2026-03-26

## What This Is

This is Webapper's shared template for Claude Cowork. Instead of writing long prompts every session, Claude reads these files to understand who you are, how Webapper works, and what you expect. Short prompts, better output.

Every team member gets their own copy. You fill in your personal details, push it to your own repo, and point Cowork at your folder. Claude then knows your role, your voice, your projects, and your rules from the first message.

## Quick Start (5 Minutes)

1. **Clone this repo** to your machine
2. **Fill in your personal files** (see "What to Customize" below)
3. **Push to your own repo** (fork or create a new private repo)
4. **Open Claude Desktop** and click the **Cowork** tab
5. **Select your folder** when prompted, point it to your cloned `claude-cowork-template` folder
6. **Set your global instructions** (see below)

## Folder Structure

```
claude-cowork-template/
├── ABOUT ME/              — Personal scaffold (you customize) + shared Webapper context
│   ├── about-me.md        — Your role, background, stack, priorities
│   ├── my-voice.md        — Your tone, style, patterns by audience
│   ├── my-rules.md        — Webapper standards (shared) + your personal rules
│   ├── our-process.md     — Webapper Agile Guide (WAG) + how we work with AI (shared)
│   └── reference_webapper_aws_org.md — Webapper AWS Organization layout (accounts, SSO, delegation)
├── PROJECTS/              — Shared company truth: current projects, same for everyone
│   ├── README.md          — Project roster (active + retired) and Jira keys
│   ├── Herb Co/           — Anchor client (iBizFusion ERP + web/commerce migration)
│   ├── CloudSee Drive/    — Flagship internal SaaS product
│   ├── Atlantic British/  — Managed-hosting client (RoverParts.com)
│   ├── eRep/              — Managed-hosting client
│   ├── Icon Media/        — Managed-hosting client (multi-storefront e-commerce)
│   └── VisionAST/         — Retired / transitioning (historical reference)
├── TEMPLATES/             — Shared templates (project-brief, project-context, jira-ticket)
├── HARNESS/               — Shared Claude operating harness (memory, retros, verification, MCP shortlist)
├── .claude/skills/        — Shared skills (loop-engineering, adhd)
├── REFERENCE/             — Durable references (e.g. iDesign method primer)
├── OUTPUTS/               — Where Claude saves finished deliverables
└── README.md              — This file
```

## What to Customize vs. What's Shared

| File | Type | What to Do |
|------|------|------------|
| `about-me.md` | **Personal** | Fill in your name, role, stack, priorities. Keep the shared team/company sections current. |
| `my-voice.md` | **Personal** | Define your writing style. Best approach: give Claude 5-10 of your real emails/docs and ask it to fill this in for you. |
| `my-rules.md` | **Personal + Shared** | Webapper standards (rules 1-31) stay the same for everyone. Add your personal rules at the bottom. |
| `our-process.md` | **Shared** | Don't change this, it's the WAG. If the process changes, we update the template repo and everyone pulls. |
| `PROJECTS/*` | **Shared** | Don't change project context files unilaterally. These reflect the current state of each project and should stay consistent across the team. Update them as a team when architecture, active work, or team assignments change. |
| `TEMPLATES/*` | **Shared** | Same templates for everyone. Propose changes to the team. |

## Working With AI + Skills

Webapper is going **AI-native** — see `ABOUT ME/our-process.md` § *Working With AI at Webapper* for the direction and the Anthropic tools we standardize on (Claude Desktop, Claude Code, Cowork, and the API / Managed Agents).

- **`HARNESS/`** is the shared Claude operating harness: typed memory, session retros, verification patterns, and an MCP shortlist. Read `HARNESS/README.md` when bootstrapping a project.
- **`.claude/skills/`** holds shared skills:
  - **loop-engineering** — iterative, verifier-driven work loops
  - **adhd** — parallel divergent ideation across many cognitive frames, for open-ended architecture / design / naming decisions (gated; it's ~10 agent calls per run)

## Setting Global Instructions

Go to **Settings > Cowork > Edit Global Instructions** and paste something like this, customized for you:

```
I'm [Your Name], [Your Role] at Webapper Services. Read my ABOUT ME files before every task. Ask clarifying questions before executing. Show a plan before acting. Never delete without my approval.
```

## Filling In Your Voice File (The Easy Way)

The `my-voice.md` file is the hardest to fill in by hand. Here's the shortcut:

1. Gather 5-10 real writing samples: sent emails, Jira comments, Google Docs, Slack messages, specs
2. Start a Cowork session and paste them in
3. Ask: "Analyze these writing samples and fill in my ABOUT ME/my-voice.md file based on what you see"
4. Review what Claude produces and adjust anything that doesn't feel right

## Recommended Model Settings

- **Model:** Opus 4.8 (latest available)
- **Extended Thinking:** ON

## Keeping It Current

These files work best when they're accurate. Update them when:

- Your role, team assignments, or priorities shift (about-me.md)
- You discover new patterns that improve AI output (my-rules.md)
- Project architecture, active work, or team assignments change (project context files)
- The WAG process is updated (our-process.md, coordinated team-wide)

## How to Use Day-to-Day

- **Starting a new project?** Copy `TEMPLATES/project-brief.md` into a new subfolder under `PROJECTS/` and fill it in.
- **Documenting project architecture?** Copy `TEMPLATES/project-context.md` into the project subfolder. See the CloudSee Drive and VisionAST examples.
- **Writing Jira tickets?** Use `TEMPLATES/jira-ticket.md` or ask Claude to write one.
- **Need Claude to produce a file?** It saves to `OUTPUTS/` automatically (per my-rules.md rule #31).
- **Working on a specific project?** Tell Claude which project. It will read the relevant project-context.md.
