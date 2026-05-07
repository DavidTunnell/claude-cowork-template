# Claude Cowork Template

A drop-in context folder for Claude Cowork. Instead of writing long prompts every session, Claude reads these files at the start of every task to understand who you are, how you work, and what you expect. **Short prompts in, better output out.**

This template is designed to work for **anyone, in any company, doing any kind of work** — engineers, marketers, project managers, analysts, attorneys, designers, founders, operations leads. It's not software-specific (the one section that is, `HARNESS/`, is clearly marked optional).

You can fill it in **manually** by editing the markdown files, or **via AI interview** by asking Claude in Cowork: *"help me fill out about-me.md"*. Most people end up doing a mix.

---

## Quick Start (about 10 minutes)

1. **Clone or download this repo** to your machine.
2. **Point Claude Cowork at the folder** as a working folder. Open the Cowork tab in the Claude desktop app, start a new task, select this folder when prompted.
3. **Set your global instructions** (one-time, in Settings → Cowork → Edit Global Instructions). Suggested baseline:

    ```
    Read my ABOUT ME files before every task. Ask clarifying questions before executing.
    Show a plan before acting. Never delete or overwrite without my approval.
    Save deliverables to the OUTPUTS folder.
    ```

4. **Fill out the four ABOUT ME files.** In Cowork, start with: *"help me fill out about-me.md"*. Then move to `my-rules.md`, `my-voice.md`, `our-process.md`. Or just open them and edit by hand. Either works.
5. **Optional: add your projects.** Copy `TEMPLATES/project-context.md` into a new folder under `PROJECTS/` for each ongoing project, account, or initiative you want Claude to know about.

That's it. Every future Cowork session pointed at this folder will read the context and use it.

---

## Folder structure

```
claude-cowork-template/
├── ABOUT ME/
│   ├── about-me.md         — Who you are, role, team, tools, priorities
│   ├── my-voice.md         — Tone, style, patterns by audience, things to avoid
│   ├── my-rules.md         — Default rules + your customizations
│   └── our-process.md      — How your team works (cadence, ceremonies, tools)
├── PROJECTS/
│   ├── README.md           — How to populate this folder
│   └── Example Project/    — Fully filled-out non-software example you can imitate
├── TEMPLATES/
│   ├── project-context.md  — Template for documenting an ongoing project
│   ├── project-brief.md    — Template for kicking off a new project
│   └── jira-ticket.md      — Template for writing structured tickets
├── HARNESS/                — Optional. Software/coding-specific scaffolding for project repos.
├── OUTPUTS/                — Where Claude saves deliverables you want to find later
└── README.md               — This file
```

---

## How to use it day-to-day

- **Starting work on something?** Just describe what you need. Claude already knows your role, tools, voice, and rules.
- **Working on a specific project?** Tell Claude which one. It will read the project-context file for that project (team, stack, status, decisions) before doing anything.
- **Starting a new project?** Copy `TEMPLATES/project-brief.md` into a new folder under `PROJECTS/` and use it to define the project.
- **Getting an output you'll want to keep?** Claude saves to `OUTPUTS/` so things don't get buried in chat.
- **Something feels off?** Open the relevant markdown file (your voice, your rules, the project context) and edit. The next session will pick up the change.

---

## Filling it out: manual vs. AI-interview

Each file in `ABOUT ME/` opens with a "How to fill this out" callout. The pattern is the same:

- **Manual:** Open the file, replace each `[BRACKETED_PLACEHOLDER]` with your info, expand or delete the example block, save.
- **AI-interview:** In Cowork, say *"help me fill out [filename]"*. Claude will ask you questions one at a time and update the file as you go. Faster than staring at blanks.

Some files (especially `my-voice.md`) are easier to write *from* something than *toward* something. The trick: have Claude read your real writing first.

> *Connect to my email and read my last 50 sent messages. Then propose patterns for my-voice.md based on how I actually write. Show me the diff before saving.*

That kind of prompt produces a voice file that actually sounds like you, not generic advice.

---

## Recommended model settings

Use the strongest available Claude model and turn on **Extended Thinking** for any non-trivial task. Don't change models between sessions in the same project — context handoff is cleaner with consistency.

---

## Customizing this template

This is a starting point, not a final destination. Edit anything. Add files. Delete sections. Reorganize.

The most common customizations:

- Adding domain-specific files under `ABOUT ME/` (e.g., `my-design-system.md` for designers, `my-research-methods.md` for researchers, `my-clients.md` for consultants)
- Adding a `PRIVILEGED/` or `LEGAL/` folder for sensitive content with a clear "approval required" note in `my-rules.md`
- Replacing `OUTPUTS/` with a deeper structure (e.g., `OUTPUTS/{YYYY-MM-DD-topic}/`) once you've done enough work to know what you save

Treat this template the way you'd treat a notebook: useful when it reflects how *you* think, not how the original author thought.

---

## Sharing with your team

If a few people on your team want to use the same starting point:

1. Each person clones the template into their own folder.
2. Each person fills out their personal `ABOUT ME/` files separately. Voice and rules are personal; they shouldn't be shared.
3. Optionally share `our-process.md` and `PROJECTS/*` if your team works on the same projects. One person can fill these in and send the markdown to others, who paste them into their own folders.

The template is designed to be easy to fork. The personal files (`about-me.md`, `my-voice.md`, `my-rules.md`) and the shared files (`our-process.md`, `PROJECTS/*`) live together but are independent.

---

## License

This template is provided as-is to help anyone bootstrap a useful Claude Cowork context folder. Adapt it however you want.
