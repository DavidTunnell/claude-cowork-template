# project_overview

**Last Updated:** 2026-07-01

**Summary:** eRep (erep.com) is a managed-AWS-hosting client. Webapper operates their ColdFusion 11 + Lucee 6 web stack (Windows EC2 behind an ALB, SQL Server DB) — reviews, patching, DB migrations, backups, incident support. Jira key `EREP`. Billing split across BHS (included) and AHS (billable).

## What's true today

- Active and healthy. SQL Server 2016→2022 migration done (May 2026); WAF deployed after a March bot attack; PITR restore validated (June 2026).
- Live work runs in the `WEBA` project: hosting-foundations remediation (root MFA, ADRs, runbooks) in progress; Q2 BHS review done.
- Open decisions: Windows Server 2012 R2 → 2022 migration, Reserved Instance renewal, dev-workstation billing.

## Who's involved

- **Webapper:** Joy Miller (hosting lead/reviewer), Henry Tran (DB/infra), Peter Truong (remediation); David Tunnell (oversight)
- **Client:** eRep owner/decision-maker + developer (by role)

## Keep top of mind

Steady managed-hosting relationship; the win is reliability plus clean quarterly BHS reviews. Operational-only here; sensitive identifiers live in access-controlled systems.
