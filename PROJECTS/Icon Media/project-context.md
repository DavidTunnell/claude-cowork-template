# Project Context: Icon Media

**Last Updated:** 2026-07-01
**Jira Key:** IM
**Type:** Client engagement — managed AWS hosting + Lucee/CFML e-commerce maintenance

## Overview

Icon Media (also known as "Iconfigurators") is a long-running Webapper hosting and maintenance client running a Lucee/CFML e-commerce platform on AWS. Webapper operates their production and QA infrastructure and handles ongoing ops: DNS and email records, WAF geo/IP rules, SSL certificates, SEO/crawler issues, and production incident response. The estate hosts many wheel/tire storefront domains (e.g., schottwheels.com, uswheel.com, iconvehicledynamics.com, 1886wheels.com).

## Current Status

- **Phase:** Mature managed services (steady state) — the large majority of tracked work is complete.
- **Recent work:** Google/crawler indexing issues (waiting on customer); production load-balancer (ELB → ALB) alerting cleanup. A 2025 "System Upgrades" epic completed April 2026 (QA rebuild + SQL Server 2022 upgrade + Lucee 6 migration).
- **Cross-cutting governance** (in the WEBA project): retroactive hosting ADRs and credential migration off Confluence into AWS Secrets Manager.
- **Cadence:** Ongoing support with recurring BHS reviews.

## Team

| Name | Role | Focus |
|------|------|-------|
| Joe Lopez | Engineer (primary) | Most IM hosting/maintenance work |
| Joy Miller | Project facilitator / reporter | Coordination, reviews |
| Henry Tran | Engineer | Infra / AMI / JVM tuning |
| Donald Quitangon | Support engineer | Ongoing tickets |
| Steven Nguyen | Tech Lead (VTeam) | Support / escalations |
| William Koenigslieb | Engineer | AWS / infra |
| Peter Truong | Engineer | ADRs / governance (WEBA) |
| David Tunnell | Lead Software Architect | Technical oversight |

External: Icon Media client staff (client-side infra contact by role).

## Architecture (high level)

- **Cloud:** AWS — EC2 auto-scaling groups (custom AMIs), ELB/ALB, S3, AWS WAF, Route 53
- **Web tier:** Windows/IIS front end proxying via AJP to Apache Tomcat
- **App engine:** Lucee CFML (migrating 5.4.x → 6)
- **Database:** Microsoft SQL Server (upgraded to 2022)
- **Monitoring:** SeeFusion (JVM/CF)

## Key Workflows / Integrations

- SnipCart checkout webhooks; ClearSale and Snap Finance (payment/fraud) integrations
- Email deliverability management (DKIM/TXT records; per-store send subdomains)
- WAF IP allow-listing and country geo-unblocking
- QA ↔ Production sync for platform upgrades
- Recurring BHS hosting reviews

## Where Things Live

- **Jira:** project `IM`; related governance items in `WEBA`
- **Docs:** Confluence (Webapper space) for hosting docs/ADRs; Google Sheets/Docs for review checklists and weekly sprint summaries
- **Code/ADRs:** `projects/iconmedia` path in the ops repo
- **Process:** follows the WAG (`ABOUT ME/our-process.md`)

> Snapshot as of 2026-07-01. Account IDs, credentials, allow-list entries, and detailed findings live in access-controlled Jira/Confluence.
