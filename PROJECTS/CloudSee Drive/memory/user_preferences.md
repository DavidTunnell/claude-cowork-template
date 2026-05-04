# user_preferences

**Last Updated:** 2026-05-04

**Summary:** David's CSD-specific preferences. Layered on top of `ABOUT ME/my-rules.md` and `ABOUT ME/my-voice.md`.

## Communications

- **PR / commit messages:** WAG style — `[CSD-NNN] One-line summary` followed by why-focused description. Don't pad. No "as discussed" filler.
- **Sprint planning comms:** factual, terse. Daniela / Patrick are direct; match the energy.
- **Customer-facing roadmap updates:** check with Scott before any change to `cloudseedrive.com` content.
- **Internal comms about Free Trial / pricing:** check with Patrick. Marketplace integration has business implications beyond engineering.

## Decision-making

- **AI coding boundaries:** actively using Claude Code for smaller features. Favorites was a good candidate. New features should be evaluated case-by-case for AI-coding fit before commitment.
- **Don't propose architectural rewrites** without a path through Productboard / Discovery. Adjacent improvements get logged as new tickets, not silently included.
- **Tech debt vs. new features:** explicit tradeoff. CSD-202 is the tech-debt epic. Don't let new feature work paper over debt items.

## Scope discipline

- One ticket = one fix. Atomic commits. Branch off `develop`, named `[CSD-NNN]-short-description`.
- ~50 lines per bugfix. Past that, propose a refactor as a separate ticket.
- No silent scope expansion. If a fix turns out to require touching adjacent code, raise it before committing.

## Workflow

- Use Desktop Commander, not the sandbox (rules 32–33).
- AWS work uses `--profile ama-cloudsee` via IdC SSO.
- Frontend UI changes require a screenshot of the affected screen — not just unit tests passing (CLAUDE.md verify command).
- Backend changes that touch DynamoDB / OpenSearch get exercised in QA env before production promotion.
- All approval-required areas in `CLAUDE.md` are gates, not suggestions (rule 40).

## Empty-but-reserved

(Add to this file as preferences emerge during CSD work. Keep entries one-line and dated.)
