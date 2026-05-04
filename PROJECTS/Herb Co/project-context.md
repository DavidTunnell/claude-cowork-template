# Project Context: Herb Co (iBizFusion)

**Last Updated:** 2026-05-04

## Overview

Webapper is the hands-on technical team for **Beckway**'s acquisition of **Monterey Bay Herb Co. ("Herbco")**, which is itself acquiring **Thrive**, a specialty nutraceutical ingredients supplier (~$23M revenue, ~58 staff, 1,000+ metric tons shipped annually). Thrive runs its entire operation on **iBizFusion** ("iBiz") — a custom monolithic ColdFusion / MySQL ERP built over 20+ years by a single external developer (Adrian) based in Romania. Beckway's 61-page Technical Due Diligence concluded that iBiz is both a strategic asset (it natively automates complex compliance workflows) and a technical liability (single-developer dependency, recently-upgraded but historically end-of-life stack, no staging environment, significant security debt). Our job is to **audit, stabilize, and then operationally support** the platform across a six-month engagement that is itself Phase 1 of Beckway's multi-year roadmap.

## Current Status

- **Phase:** Phase 1 — Technical Evaluation & Risk Validation (Month 1 of 6)
- **Current Sprint Focus:** Stand up Herbco UAT AWS account (797601398324) with chained-AssumeRole IdC access for the team; finalize the prioritized remediation roadmap from gap analysis; maintain weekly working cadence with Adrian.
- **Last Major Deliverable:** *iBizFusion Static Analysis* (2026-04-06) and *Webapper-vs-Adrian Gap Analysis* — flagged 221 unparameterized SQL queries, 30+ hardcoded credentials, CFMX_COMPAT (40-bit RC2) encryption on stored credit card data, 1.5% CSRF coverage, and the architectural risk of `commonFunctions.cfm` as a god-file.
- **Next Milestone:** Phase 1 deliverable — prioritized remediation roadmap to Beckway PMO (target end of Month 1). Phase 2 (stabilization & remediation) begins Month 2 with 2 FTE dropping to 1 FTE in Month 3.
- **Engagement Cadence:** 2 FTE in months 1–2, 1 FTE months 3–6. Pacific Time business-hours support model planned for Phase 3.

## Team

### Webapper (us)

| Name | Role | Focus Area |
|------|------|------------|
| David Tunnell | Lead Software Architect | Engagement lead, architecture, AWS / IdC access management, Beckway-facing comms |
| Patrick Quinn | CEO | Executive sponsor, client relationship, scope and prioritization |
| Steven Nguyen | Tech Lead (VTeam) | Static analysis, security review, remediation planning |
| Daniela Camargo | PM / QA / SME | Project management, QA strategy, knowledge capture |
| Henry Tran | SDET | Documentation, testing buildout, DevOps / pipeline |
| J. Miller | TBD | Provisioned in Herbco UAT AWS account |

### Beckway (client)

| Name | Role |
|------|------|
| Andrew Latypov | Director — primary contact |
| Raj Thangavelsamy | Partner |
| Chip Hyman | Managing Director |

### Herbco / Thrive (end users)

| Name | Role |
|------|------|
| Juan Pozzi | CFO |

### iBiz / Adjacent

| Name | Role |
|------|------|
| Adrian | Sole iBiz developer (Romania, external contractor) — 20+ years system knowledge, single point of failure |
| Mark | Independent Power BI / Azure consultant (~10–20 hrs/week) |

## Architecture

### Tech Stack (current, post-2026 Q1 upgrade)

- **Application:** ColdFusion 2023 (Adobe), with development machine on CF 2025 for early-deprecation visibility
- **Database:** MySQL 8 (370+ tables, no published ERD)
- **OS / Hosting:** Windows Server 2025 on Hostek managed VPS (~$269/month)
- **WAF:** SiteLock, used to route the production cutover during the recent upgrade
- **Pre-upgrade legacy** (now retired but referenced in older docs): CF 2018, MySQL 5.6, Windows Server 2012 R2 — all end-of-life. The upgrade was executed in Q1 2026 by Adrian (171 commits Jan–Mar). Old server retained as rollback.

### Application Architecture

- Classic ColdFusion structure: `.cfm` for UI, `.cfc` for APIs, `<cftag>`-heavy
- `commonFunctions.cfm` is a "god file" — permissions, inventory, order state machines, pricing, costing, commissions, audit trails, KPI calculations, and integration utilities all in one. Functions depend on APPLICATION / SESSION / Request scopes (non-portable, untestable in isolation).
- No automated tests of any kind. No CI/CD. Direct production deployment with no staging gate.
- Adrian's `commonFunctions.cfm` overview document is one of the strongest pieces of internal docs and maps high-impact functions and their dependencies.

### Integration Landscape

iBiz sits at the center of a web of integrations — none of which are documented end-to-end:

| System | Direction | Mechanism |
|--------|-----------|-----------|
| Salesforce CRM | Bidirectional | REST API |
| QuickBooks Online | One-way out | Manual journal entries |
| Google Drive | Reference | QA docs, CoAs, FSVP |
| Lab Portals | Inbound | Manual data entry |
| Carrier APIs | Outbound | UPS, FedEx, DHL, USPS |
| eCommerce shop (CF-based, ~99% complete) | Bidirectional | REST API + HMAC-signed callbacks |
| WooCommerce (alternative path, in progress) | Bidirectional | REST API + HMAC + nonce |
| NetNow Credit | One-way | Manual limit updates |
| Authorize.Net | Outbound | Tokenized payment flow (correctly implemented) |

### Webapper-Managed Environments

| Environment | Notes |
|-------------|-------|
| Herbco UAT (AWS) | Account `797601398324`, customer-owned, set up May 2026. Webapper admin via IdC AssumeRole (`ama-herbco-uat-ro`, `ama-herbco-uat-admin` profiles). Region us-east-1. CloudTrail to `herbco-cloudtrail-797601398324`. Root locked, MFA via 1Password TOTP. |
| Production (Hostek) | Owned by Adrian / Herbco. We have read-only and limited admin access by request. Single VPS, single developer. |

## Active Work

### Phase 1 — Technical Evaluation & Risk Validation (current month)

- AI-assisted static code analysis across the full 1,334-file codebase (delivered)
- Live environment validation: repo vs. production drift
- Database schema audit (370+ tables)
- Security review: hardcoded credentials, API vulnerabilities, CFMX_COMPAT card-data encryption, CSRF coverage
- Scheduler / job audit
- Integration validation (Salesforce, payments, carriers)
- Documentation review across Adrian's 25+ SharePoint docs
- **Deliverable:** Prioritized remediation roadmap for Beckway PMO

### Phase 2 — Stabilization & Remediation (Months 2–3, 2 FTE → 1 FTE)

- Security hardening: credential rotation, secrets management, API sanitization, CFMX_COMPAT migration
- Database hardening: schema constraints, engine fixes
- Scheduler stabilization: error handling, monitoring
- Email / notification routing cleanup
- SDLC implementation: GitHub workflow, PR process, staging environment, validation gate
- Documentation updates

### Phase 3 — MSP Operational Support (Months 4–6, 1 FTE)

- Pacific Time business-hours support
- Ticketing system (taxonomy, SLAs)
- Application + database + scheduled-job monitoring
- Change management through GitHub
- Periodic operational reporting

## Top Risks

| # | Risk | Severity | Notes |
|---|------|----------|-------|
| 1 | **Single Developer Dependency (Adrian)** | Critical | 20+ years of institutional knowledge in one head. DB access restricted to one IP. TDD calls this a "Code Island." Our entire ramp-up depends on knowledge transfer from him. |
| 2 | **Security Posture Gap** | High | Adrian's docs claim "CFQUERYPARAM everywhere" but static analysis found 221 unparameterized queries, 35 ORDER BY injection vectors, 30+ hardcoded prod credentials in source control, Stripe secret keys in a web-accessible test file, and CFMX_COMPAT (40-bit RC2) encryption on stored card data — a PCI DSS violation. |
| 3 | **Integration Fragility** | High | Six+ point-to-point integrations, all undocumented. Each is a potential ticket source we'll have to triage in Phase 3. |
| 4 | **No Staging Environment** | High | Production processes live orders for a $23M business. Cannot safely remediate without a test environment. Standing up UAT is a Phase 1 prerequisite. |
| 5 | **Adjacent-System Scope Creep** | Medium | We own iBiz, but problems will surface in Salesforce, QuickBooks, carriers — every "iBiz isn't working" ticket lands on us without clear scope boundaries. |

## Known Tech Debt (for the prioritized roadmap)

- 221 unparameterized SQL queries; 35 ORDER BY injection vectors
- 30+ hardcoded production credentials in source control (DB, API, SMTP)
- CFMX_COMPAT encryption on stored credit card data (PCI DSS violation)
- 1.5% CSRF coverage across the app (Adrian added it on login forms only)
- `commonFunctions.cfm` god-file
- No automated tests, no CI/CD, no PR review
- No staging environment — production is the only environment
- No DR plan; single VPS; backup restore process never tested
- No systematic monitoring, uptime checks, or performance baselines
- Bus-factor of one (Adrian)

## Bigger-Picture Roadmap (Beckway, multi-year)

- **Phase 1 (Months 1–7):** Stabilize & federate — upgrade iBiz stack + GP, federated financial reporting. *We operate here.*
- **Phase 2 (Months 8–15):** Operational bridges — replace legacy eCommerce/Atlas, implement iPaaS, CRM unification.
- **Phase 3 (Months 15+):** Unified future — migrate to Microsoft Business Central as a single cloud ERP.

**Mindset:** Don't over-invest in legacy patterns. Stabilize and document. Every change should make the eventual Business Central migration easier, not harder.

## Release Cadence / How Code Gets to Production

Phase 1 is assessment, not active development on iBiz, so the current "release cadence" is Adrian's existing ad-hoc process — direct production commits, no PR review, no staging.

**Phase 2 target state** (what we're implementing):

- **Sprint Length:** 2 weeks (synchronized with WAG)
- **Source Control:** Migrate from current arrangement to Webapper-controlled GitHub
- **Workflow:** New > Discovery > Confirmed > In Progress > Ready to Test > Testing Complete > Ready for Production Testing > Done!
- **Deployment:** dev → staging (UAT) → production. PR review required. Staging validation required before any prod change.
- **Who Deploys:** Webapper engineers; production deploys require Beckway sign-off for non-routine changes.
- **Express-Lane Addendum:** A WAG addendum exists for fast-turnaround Adrian-led changes that don't fit the full SDLC — see `herbco-express-lane-wag-addendum`.

## Project Conventions

- **Communication tone with Adrian:** Collaborative assessment, not audit interrogation. Lead with appreciation for his documentation; share static-analysis as shared facts, not accusations. Don't use "legacy" pejoratively or bring up "rewrite" / "replace" language in Phase 1.
- **Communication tone with Beckway:** Speed, thoroughness, and clear comms. Beckway PMO is watching; this engagement is a foothold for portfolio-wide work if executed well.
- **AWS / IdC access:** All access via Webapper IdC SSO (`webapper-aws-sso.awsapps.com`). Profile pattern `ama-herbco-uat-{ro,admin}` matching the existing `ama-*` pattern (CloudSee, VisionAST, Atlantic British). External ID stored in `~/dev/anthropic-managed-agents/external-id.secret` (gitignored).
- **Don'ts:** Don't promise specific outcomes to Adrian that Beckway hasn't approved. Don't open the Lucee-vs-Adobe ColdFusion conversation yet.

## Key Links

### Google Drive (Beckway shared drive)

- **Project root:** https://drive.google.com/drive/u/0/folders/0AAU0auAeY4VQUk9PVA
- **iBiz Project Briefing (HTML):** https://drive.google.com/file/d/1JT557dPVxqFMTnrA8CcoiB_CdCMetDIq/view
- **iBiz Visual Walkthrough (HTML):** https://drive.google.com/file/d/1-41zrY-EGSkyI5Z1YC0_gqWZR8_VDsI_/view
- **Phase 1 Project Plan (Sheets):** https://docs.google.com/spreadsheets/d/1dZzjYqX_5yidl5A7lqs4cozYgYfN5K2Z-SwjKSsjMUc
- **Phase 1 Visual Plan (HTML):** https://drive.google.com/file/d/1D9y5lkiyDfi2-dcl-J_CMSCv6pUgSsFd/view
- **Team Onboarding Checklist (HTML):** https://drive.google.com/file/d/1RhJgPc_RCFyn6Q8mDBhQ6YsoFcSgWcOY/view
- **iBizFusion Static Analysis (PDF, 2026-04-06):** https://drive.google.com/file/d/1gfUgm1AzIJsnfe3zqa4fLsjDskmfKlkD/view
- **Directional Scope for iBiz (PDF, 2026-03-23):** https://drive.google.com/file/d/1KlJxdiVLRv_KUHiBgA0rCbpGeuys7s5l/view
- **Beckway Proposal — Goals & Objectives (PDF):** https://drive.google.com/file/d/10_t9MSQp-9gezTxnEiof7wHr_IQK7S_d/view
- **Thrive TDD & ERP Feasibility (PDF, redacted):** https://drive.google.com/file/d/1BvHrjHIcrmYeQT3CNlgEhYyXL4TFhfCV/view
- **Webapper vs RedHelm — Dynamic GP 2018 Migration (Doc):** https://docs.google.com/document/d/1j0HMxUOwiOSn0ovE-9CjaG8PR-4SNd1taQs6659ditY
- **AWS — Herb Co — Quick Start (Doc):** https://docs.google.com/document/d/1oUPq-MdRZ8WGX0R3J7gEAw3JUQbor93R0zzGjPCofko
- **Express-Lane WAG Addendum (Doc):** https://docs.google.com/document/d/1x5WO4XKCxo6nI9KndGJtyyOBLlDZeVAzJ0Uux91hZio
- **Thrive iBiz — Status Update Template (Slides):** https://docs.google.com/presentation/d/1ixmiIS547KZlxb5HzYLAB99uTip4pczTdBGQs3lb7J8
- **iBizFusion Documentation (SharePoint mirror):** https://drive.google.com/drive/folders/1t6gsRtlnk9sKSC65rwBWGSfzGDILzKcO
- **Meeting Recordings folder:** https://drive.google.com/drive/folders/1RGR9NZAVf6l_7Pu-oQhdgJaeng7nxEXm
- **Development folder:** https://drive.google.com/drive/folders/1eiuT-Qhe7oJvXtwtllQZG-v9cyKG-Dmc

### Local Working Copies

- **iBiz source mirror:** `C:\Users\End-Dream-Office\dev\ibizfusion`
- **Active dev branch:** `C:\Users\End-Dream-Office\dev\dev-main`
- **SharePoint docs mirror:** `C:\Users\End-Dream-Office\dev\ibizfusion\_drive_docs\iBizFusion Documentation (Sharepoint)`
- **Anthropic Managed Agents repo (AWS access tooling):** https://bitbucket.org/cloudsee-drive/anthropic-managed-agents

### AWS

- **Access portal:** https://webapper-aws-sso.awsapps.com/start
- **Herbco UAT account:** `797601398324`
- **Region:** us-east-1
- **CloudTrail:** `herbco-cloudtrail-797601398324`
- **Profiles:** `ama-herbco-uat-ro`, `ama-herbco-uat-admin` (chained via `ama-mgmt`)
