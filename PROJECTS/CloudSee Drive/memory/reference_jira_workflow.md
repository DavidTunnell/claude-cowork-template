# reference_jira_workflow

**Last Updated:** 2026-05-04

**Summary:** CSD's Jira project key, ticket lifecycle, and the Productboard-to-sprint flow.

## Project

- **Jira project key:** `CSD`
- **Jira URL:** https://webapper.atlassian.net/browse/CSD
- **Confluence space:** `CD1` — https://webapper.atlassian.net/wiki/spaces/CD1/overview

## Ticket workflow (WAG)

```
New > Discovery > Confirmed > In Progress > Ready to Test > Testing Complete > Ready to Deploy > Ready for Production Testing > Done!
```

Stage meanings:

- **New** — captured but unestimated. Fresh from Productboard or in-team intake.
- **Discovery** — under estimation by Steven or domain owner. Output: story points + scope.
- **Confirmed** — estimated, accepted into a sprint commitment.
- **In Progress** — assigned developer is working it.
- **Ready to Test** — dev complete, deployed to QA env.
- **Testing Complete** — Daniela has signed off on QA.
- **Ready to Deploy** — green light to push to production.
- **Ready for Production Testing** — deployed to production, dev verifies, then Daniela / client UAT.
- **Done!** — UAT passed. Don't transition to Done before UAT.

## Productboard → Sprint flow

1. **Productboard** is the prioritization layer. ICE scoring (Impact / Confidence / Ease).
2. **Patrick** reviews Productboard candidates and pushes the next batch into Jira Discovery.
3. **Steven** estimates Discovery tickets.
4. **Sprint planning** (Tuesdays 2:30 PM CDT) commits estimated tickets into the next sprint.
5. **Public roadmap** at `cloudseedrive.com` shows Launched / In Progress / Planned. Updated by Scott.

## Sprint cadence

- **Sprint length:** 2 weeks (synced with WAG across all Webapper projects)
- **Sprint planning:** Tuesdays 2:30 PM CDT, weekly
- **Cadence target:** 1 feature release per month

## Ticket conventions

- Title: short, action-oriented. "Add favorites left-panel display" not "Favorites work."
- Description: WAG-style sections with emoji headers (see `TEMPLATES/jira-ticket.md`).
- Commit messages reference the ticket: `[CSD-NNN] One-line summary`.

## Useful JQL filters

```
# My open CSD tickets
project = CSD AND assignee = currentUser() AND statusCategory != Done

# Sprint-current
project = CSD AND sprint in openSprints()

# Discovery queue
project = CSD AND status = Discovery

# Ready to Deploy (production deploy candidates)
project = CSD AND status = "Ready to Deploy"

# Tech debt epic and children
project = CSD AND ("Epic Link" = CSD-202 OR key = CSD-202)
```

## Confluence anchors

- General Information: https://webapper.atlassian.net/wiki/spaces/CD1/pages/26542082/CloudSee+-+General+Information
- Infrastructure: https://webapper.atlassian.net/wiki/spaces/CD1/pages/27000833/Infrastructure
- Deployment Specification: https://webapper.atlassian.net/wiki/spaces/CD1/pages/26935312/Deployment+Specification
- Sprint History: https://webapper.atlassian.net/wiki/spaces/CD1/pages/171933697/Sprint+History
- Permissions Spec: https://docs.google.com/document/d/1W6u5Zyg8cIp-0HhZZ1-Wy7sbqZMjR8ru2XtJFymaUFE
- Credentials (production / dev / user): in Confluence, access-controlled — not linked here
