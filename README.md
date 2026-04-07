# Claude Cowork Template

**Last Updated:** 2026-03-19

## What This Is

This is your master context folder for Claude Cowork. Instead of writing long prompts every session, Claude reads these files to understand who you are, how you work, and what you expect. Short prompts, better output.

## Folder Structure

```
claude-cowork-template/
├── ABOUT ME/
│   ├── about-me.md        — Role, company, team, stack, priorities
│   ├── my-voice.md        — Tone, style, patterns by audience, things to avoid
│   ├── my-rules.md        — 29 non-negotiable rules for every interaction
│   └── our-process.md     — Webapper Agile Guide (WAG) process overview
├── PROJECTS/
│   ├── CloudSee Drive/
│   │   └── project-context.md — Architecture, team, active work, deployment
│   └── VisionAST/
│       └── project-context.md — Architecture, team, active work, deployment
├── TEMPLATES/
│   ├── project-brief.md       — Template for kicking off new projects
│   ├── project-context.md     — Template for documenting project architecture and context
│   └── jira-ticket.md         — Template for writing structured Jira tickets
├── OUTPUTS/               — Where Claude saves finished deliverables
└── README.md              — This file
```

## How to Connect This to Cowork

1. Open the Claude desktop app.
2. Click the **Cowork** tab at the top.
3. When prompted to select a folder, point it to this `claude-cowork-template` folder.
4. Claude will now read your context files before every task.

## Recommended Model Settings

- **Model:** Opus 4.6
- **Extended Thinking:** ON
- Don't change these between sessions.

## Global Instructions (Set Once)

Go to **Settings > Cowork > Edit Global Instructions** and paste:

```
I'm David, Lead Software Architect at Webapper Services. Read my ABOUT ME files before every task. Ask clarifying questions before executing. Show a plan before acting. Never delete without my approval.
```

## How to Use This

- **Starting a new project?** Copy `TEMPLATES/project-brief.md` into a new subfolder under `PROJECTS/` and fill it in.
- **Documenting project architecture?** Copy `TEMPLATES/project-context.md` into the project subfolder. See the CloudSee Drive and VisionAST examples.
- **Writing Jira tickets?** Use `TEMPLATES/jira-ticket.md` or ask Claude to write one. It follows the template pattern.
- **Need Claude to produce a file?** It saves to `OUTPUTS/` automatically (per my-rules.md rule #29).
- **Working on a specific project?** Tell Claude which project. It will read the relevant project-context.md for architecture, team, and deployment details.

## Customizing

These files are meant to evolve. Update them as your role, stack, or preferences change. The more accurate your context files are, the less prompting you need to do.

Key files to keep current:
- `about-me.md` when your role, team, or priorities shift
- `my-rules.md` when you discover new patterns that improve AI output
- Project context files when architecture, team assignments, or active work changes
