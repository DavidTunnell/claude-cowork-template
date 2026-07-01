# Project Context: Herb Co (iBizFusion)

**Last Updated:** 2026-07-01
**Jira Key:** HERB
**Type:** Client engagement — anchor client (technical services, coordinated via Beckway)

## Overview

Webapper is the hands-on technical team for **Monterey Bay Herb Co. ("Herbco" / NP Nutra)**, a Beckway portfolio company. The heart of the business runs on **iBizFusion ("iBiz")** — a custom monolithic ColdFusion / MySQL ERP built over 20+ years by a single external developer (Adrian, based in Romania). iBiz is both a strategic asset (it natively automates complex compliance and operations workflows) and a legacy liability (single-developer dependency, historically end-of-life stack, security debt). Our engagement began as a time-boxed audit/stabilize/support effort and has since **expanded** into hosting consolidation, a commerce migration, and retained operational support.

## Current Status

- **Phase:** Active and expanding (retained technical partner)
- **Done / stabilized:** The iBiz stack upgrade is complete (ColdFusion, MySQL, and Windows Server all moved off end-of-life versions). The initial security code-review remediation epic (HERB-1) is largely closed.
- **Active workstreams:**
  - **Monterey Web Consolidation (AWS) & Commerce Migration (Azure)** — `HERB-129` (scope confirmed and kicked off late June 2026; in discovery/grooming). Phase 1 consolidates the NP Nutra marketing sites (npnutra.com WordPress, NPNList) into Monterey's own AWS account (Lightsail, Route 53, single WAF) and retires the prior third-party hosting/WAF stack. Phase 2 lifts-and-shifts the Herbco storefront (ASP.NET + SQL Server) off its current host onto Monterey's Azure tenant (App Service + Azure SQL Managed Instance), targeting a cutover around September 2026. Includes an M&A "tuck-in" playbook and CI/CD for the client's developer.
  - **SharePoint / Microsoft Graph integration** — `HERB-121` (discovery): replace iBiz's Google Drive document backend with SharePoint as NP Nutra moves from Google Workspace to Microsoft 365.
  - **Retained support & ops** — ongoing ERP bug fixes (payments, reporting, PO/landed-cost), plus Azure/AWS monitoring (including a Dynamics-GP-adjacent Azure environment) and DR documentation for iBiz on AWS.

## Team

### Webapper (us)

| Name | Role | Focus |
|------|------|-------|
| David Tunnell | Lead Software Architect | Engagement lead, architecture, Beckway-facing comms |
| Patrick Quinn | CEO | Executive sponsor, client relationship, scope |
| Steven Nguyen | Tech Lead (VTeam) | Code review, remediation, migration engineering |
| Daniela Camargo | PM / QA / SME | Project management, QA strategy |
| Henry Tran | SDET | Documentation, testing, DevOps / pipeline |
| Joy Miller | PM / hosting | Hosting coordination, reviews |

### External

| Party | Role |
|-------|------|
| Beckway (Andrew Latypov, Director) | Sponsor / coordination — primary contact; partners engaged by role |
| Monterey Bay Herb Co. (Juan Pozzi, CFO) | Client / end users |
| Adrian | Sole external iBiz developer (20+ yrs system knowledge — key knowledge-transfer dependency) |
| Mark | Independent Power BI / Azure consultant (part-time) |

## Architecture (high level)

- **iBizFusion ERP:** Adobe ColdFusion 2023, MySQL 8 (370+ tables, no published ERD), Windows Server — classic CF (`.cfm` UI, `.cfc` APIs). A large `commonFunctions.cfm` acts as a "god file" (permissions, inventory, pricing, commissions, audit, KPIs). No automated tests / CI historically.
- **New workstreams touch:** AWS (Lightsail, Route 53, WAF) for marketing-site consolidation; Azure (App Service, Azure SQL MI, plus a Dynamics-GP-adjacent environment) for the commerce migration; SharePoint / Microsoft Graph for document management.

### Integration landscape (iBiz, high level)

Salesforce CRM (bidirectional), QuickBooks Online (out), Google Drive (docs — migrating to SharePoint), lab portals (inbound), carrier APIs (UPS/FedEx/DHL/USPS), an e-commerce path (REST + HMAC), NetNow credit, and a tokenized Authorize.Net payment flow. Most integrations are point-to-point and under-documented — treat each as a potential support surface.

## Known Tech Debt (high level)

Legacy single-developer dependency; historically end-of-life stack (now upgraded); security debt from the original codebase (remediation tracked in Jira — specifics live in access-controlled systems, not here); no staging environment historically; limited automated testing/CI; integration fragility. The multi-year direction is to stabilize and document iBiz while standing up modern hosting and SDLC, making any eventual ERP replatform easier rather than harder.

## Release Cadence / How Code Gets to Production

- Follows the WAG (`ABOUT ME/our-process.md`); 2-week sprints.
- **Target SDLC** (being implemented as the engagement matures): Webapper-controlled source control (GitHub), PR review required, `dev → staging → production` with a staging validation gate before any production change. Production deploys require client sign-off for non-routine changes.
- Historically iBiz changes were direct-to-production via Adrian; new workstreams adopt the PR + staging model.

## Project Conventions

- **Tone with Adrian:** collaborative assessment, not audit interrogation. Lead with appreciation for his documentation; share findings as shared facts. Don't use "legacy" pejoratively, and don't open the Lucee-vs-Adobe ColdFusion conversation.
- **Tone with Beckway:** speed, thoroughness, clear comms — this relationship is a foothold for portfolio-wide work.
- **AWS access:** Webapper IdC SSO with the `ama-*` chained-profile pattern (see `memory/reference_aws_account.md`). Account-specific IDs and any secrets live in access-controlled systems, never in this template.
- **Don'ts:** don't promise outcomes to Adrian that Beckway hasn't approved; don't touch payments/auth without explicit approval.

## Key Links

- **Jira:** project `HERB` (webapper.atlassian.net)
- **Docs:** Beckway shared Google Drive + iBiz SharePoint (access-controlled — links live in Jira/Confluence, not here)
- **Process:** the WAG (`ABOUT ME/our-process.md`)

> **Operational context only.** Dollar/SOW figures, specific security-vulnerability findings, credentials, AWS account IDs, and confidential due-diligence documents are intentionally excluded from this template. They live in access-controlled Jira/Confluence/Drive.
