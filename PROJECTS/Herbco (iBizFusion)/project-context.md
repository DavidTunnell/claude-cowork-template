# Project Context: Herbco (iBizFusion ERP)

**Last Updated:** 2026-04-07

## Overview

Herbco is Webapper's internal project name for the iBizFusion ERP assessment and modernization engagement. iBizFusion is a ~20-year-old custom ColdFusion ERP platform used by NP Nutra, a B2B wholesaler of natural ingredients for the dietary supplements and functional foods industries. Webapper was hired by Beckway (the investor group) to conduct an independent technical assessment of the platform, identify risks, and recommend a modernization path. Phase 1 is a 4-week discovery and assessment engagement.

**Client company:** NP Nutra (npnutra.com) — operating as "Thrive" in Beckway's project codename
**Parent/Buyer entity:** Monterey Bay Herb Co. (241 Walker Street, Watsonville, CA 95076)
**Product:** iBizFusion ERP (ibizfusion.com)
**Engagement sponsor:** Beckway (PE operating company managing the M&A integration)
**Existing developer:** Adrian (solo developer in Romania who built and maintains the system)
**CFO (Buyer side):** Juan Pozzi, Monterey Bay Herb Co.

### M&A Context
This is a post-acquisition engagement. Monterey Bay Herb Co. is the acquiring entity; NP Nutra/iBizFusion ("Thrive") is the target. Beckway is the PE operating company managing due diligence and integration. Beckway commissioned a prior ERP due diligence report ("Thrive: ERP Due Diligence & Integration Considerations", March 23, 2026, DRAFT) that assessed applications, integrations, infrastructure, vendor model, and support structure. **Beckway's assessment explicitly excluded code-level audits and IT/cyber due diligence** — that is precisely the gap Webapper is now filling.

### Buyer's Technology Stack (Monterey Bay Herb Co.)
| Component | Technology | Status |
|-----------|-----------|--------|
| ERP | Microsoft Dynamics GP 2016 | End of mainstream support (July 2021), extended support only |
| CRM | SalesPad (GP add-on) | Deeply coupled to GP database, custom "Purchasing Advisor" module |
| Database | SQL Server 2016 on Windows Server 2016 | Needs upgrade to SQL 2022 + Win Server 2022 |
| Middleware | "Atlas" (custom ASP.NET) | Resource-intensive, batch updates every 15 min, no real-time inventory |
| E-commerce | Custom ASP.NET storefront (Version 8) | No longer supported, basic checkout only |
| AP Automation | AP Smart (CloudX) | Integrated with GP 2016, available to Thrive as early win |
| Reporting | Azure SQL + Power BI | Managed by independent consultant "Mark", ~10-20 hrs/week |

## Current Status

- **Phase:** Discovery / Assessment (Phase 1 — 4 weeks)
- **Current Focus:** Source code analysis, security review, infrastructure assessment, environment setup
- **Kickoff Date:** Week of April 7, 2026
- **First Developer Meeting:** Thursday, April 10, 2026 (with Adrian)
- **Key Milestone:** Phase 1 deliverables due ~May 2, 2026
- **Jira Project:** HERB (webapper.atlassian.net)

## Team

| Name | Role | Focus Area |
|------|------|------------|
| Patrick Quinn | CEO / Engagement Lead | Client relationship (Beckway), project plan, deliverables |
| David Tunnell | Lead Software Architect | Technical assessment, security analysis, architecture recommendations |
| Steven Nguyen | Tech Lead | Security remediation guide, code review, infrastructure planning |
| Joy Miller | Technical PM | Schedule, process, coordination |
| Adrian (client-side) | Solo Developer / Maintainer | System knowledge, codebase access, infrastructure access |

## Architecture

### Tech Stack (Current — Post-Upgrade)
- **Frontend:** ColdFusion (.cfm templates) with HTML, JavaScript, CSS, jQuery, Bootstrap
- **Backend:** Adobe ColdFusion 2023 Enterprise (recently upgraded from CF 2018)
- **Database:** MySQL 8 (recently upgraded from MySQL 5.6), charset utf8mb4
- **Infrastructure:** Single VPS at Hostek (Windows Server 2025, IIS)
- **WAF:** SiteLock (routes traffic to Hostek VPS)
- **Domain:** ibizfusion.com (production), ibizsuite.com (staging/testing)

### Architecture Pattern
- **No framework** — classic page-per-action ColdFusion
- .CFM files contain UI, JavaScript, HTML, and inline SQL queries
- .CFC components handle APIs and integration logic
- `commonFunctions.cfm` is a massive shared utility/business-logic layer (formatting, permissions, inventory, pricing, orders, commissions, audit trails)
- Business logic is tightly coupled to views — no service layer separation
- CF tags used extensively (not CFScript)

### Environments
| Environment | URL | Notes |
|-------------|-----|-------|
| Production | ibizfusion.com | Single VPS, Win Server 2025, CF 2023, MySQL 8 |
| Staging | ibizsuite.com | Same Hostek, used for upgrade testing |
| Development | Adrian's local machine | Running CF 2025 for early deprecation visibility |
| QA | None | No QA environment exists |

### Key Integrations
| System | Purpose | Method |
|--------|---------|--------|
| Salesforce | CRM, lead management, customer sync | Bi-directional API (CF → Salesforce, Salesforce → CF) |
| Authorize.Net | Invoice payments | Accept hosted tokenization (no raw card data on server) |
| PayPal | Payments | Direct integration |
| Stripe | Payments | API integration (secret keys found in web-accessible test file) |
| FedEx/UPS/USPS | Shipping | API integration |
| Google Drive | Document storage | API with OAuth tokens |
| Dropbox | Document storage | API integration |
| WordPress/WooCommerce | eCommerce (in progress) | REST API + HMAC-signed callbacks |
| QuickBooks | Financial reporting | Data sync for accounting |

### Core Business Processes (5 Key Flows)
1. **Lead to Order** — Salesforce CRM → customer conversion → AM/CSR coordination → iBiz order entry
2. **Demand to Fulfill** — Order entry → inventory check → fulfillment request → shipping department
3. **Order to Cash** — Invoice generation → payment collection (bank/PayPal/card) → application to open invoices
4. **Procure to Pay** — Purchase orders → supplier payments → lot-based receiving
5. **Record to Report** — All transactions in iBiz → financial data synced to QuickBooks

### Key Business Logic
- **Lot-based inventory:** Each product shipment gets a unique lot with its own cost, dates, and lab results
- **Landed cost:** Calculated per lot (base price + freight + testing + customs + brokerage), drives margin calculations, commissions, and financial reporting
- **Tiered pricing:** Dynamic B2B wholesale pricing based on quantity tiers with SKU-specific pallet sizes
- **Pack-size constraints:** Orders must be in multiples of pack size
- **Samples:** Special small-quantity orders from sub-lots, different rules than regular orders

## Codebase Profile

- **Total files:** ~2,800
- **CFML files:** ~1,334
- **Age:** ~20 years of continuous development
- **Version control:** Git repository (171 commits Jan-Mar 2026, mostly by Adrian)
- **Tests:** None (no unit tests, no integration tests, no automated testing)
- **CI/CD:** None (no pipeline, manual deployments)
- **Package manager:** None
- **Documentation:** Minimal inline; Adrian has created extensive external docs in Google Drive

## Security Assessment (Webapper Static Analysis — April 6, 2026)

### Critical Findings (C1-C6)
- **C1:** 30+ hardcoded production credentials in source control (DB passwords, API keys, SMTP credentials)
- **C2:** SQL injection via dynamic table and column names
- **C3:** 221 unparameterized SQL queries (despite Adrian's docs claiming "CFQUERYPARAM everywhere")
- **C4:** ORDER BY SQL injection in 35 files
- **C5:** Weak credit card encryption using CFMX_COMPAT (RC2-40 bit) — PCI DSS violation
- **C6:** Stripe secret keys in web-accessible test file

### High Findings (H1-H8)
- Missing input validation on URL/form parameters
- No CSRF protection (1.5% coverage)
- Session management weaknesses
- Directory traversal risks
- Unrestricted file upload
- Information disclosure via error messages
- Insecure direct object references
- Missing security headers

### Medium Findings (M1-M4)
- Outdated JavaScript libraries
- Verbose error output
- Missing rate limiting
- Insecure cookie configuration

### Remediation Roadmap (Steven's Guide)
- **Phase 1 (Immediate):** Credential rotation, Stripe key removal, critical SQL injection fixes
- **Phase 2 (2 weeks):** CFQUERYPARAM remediation, input validation framework
- **Phase 3 (30 days):** CSRF implementation, session hardening, file upload restrictions
- **Phase 4 (90 days):** AWS Secrets Manager migration, comprehensive security headers, monitoring

## Gap: Adrian's Documentation vs. Webapper Findings

Adrian's internal technical debt documentation presents a significantly more optimistic security posture than what our static analysis revealed. Key discrepancies:

| Topic | Adrian's Docs Say | Webapper Found |
|-------|-------------------|----------------|
| SQL injection | "CFQUERYPARAM everywhere", "fully parameterized" | 221 unparameterized queries, 35 ORDER BY injection files |
| Security posture | "No known security incidents" | 30+ hardcoded credentials, Stripe keys exposed |
| URL validation | "Low-severity security risk" | Active SQL injection and parameter tampering vectors |
| Credit card handling | Uses Authorize.Net tokenization (correct) | Also has legacy CFMX_COMPAT encryption for stored card data |
| CSRF | Not mentioned | 1.5% coverage across application |
| Overall assessment | "Manageable" tech debt, "incremental" fixes | Multiple critical/high severity findings requiring immediate action |

**Important context:** Adrian has been actively improving security (171 commits in Q1 2026 include auth hardening, CSRF on login, password hashing migration, API key signing). The documentation may reflect aspirational state or recent improvements not yet applied across the full codebase.

## Infrastructure Recommendations (Webapper Proposed)

- **Migrate to AWS:** EC2 (Windows) + RDS MySQL 8 + S3 for documents
- **Add CI/CD pipeline:** Automated testing, deployment gates
- **Secrets management:** AWS Secrets Manager (Steven designed a SecretManagement.cfc component)
- **Monitoring:** CloudWatch, centralized logging, alerting
- **Environments:** Separate dev/QA/staging/production
- **Licensing consideration:** Evaluate Lucee (open source) vs. Adobe ColdFusion (current)

## Phase 1 Project Plan (Patrick's 4-Week Plan)

### Week 1: Kickoff & Source Code Analysis
- Project kickoff and onboarding
- Source code walkthrough with Adrian
- Architecture mapping
- Static analysis review

### Week 2: Live Environment & Security Review
- Live environment walkthrough
- Security and infrastructure deep-dive
- Performance baseline
- Credential audit

### Week 3: Documentation & Environment Setup
- Review existing documentation
- Set up Webapper dev environment (local + AWS)
- Knowledge transfer sessions with Adrian

### Week 4: Synthesis & Deliverables
- Gap analysis report
- Risk assessment
- Modernization roadmap
- Phase 2 proposal
- Final presentation to Beckway

### Milestones
- **M1:** Onboarding Complete (end of Week 1)
- **M2:** Discovery Complete (end of Week 3)
- **M3:** Phase 1 Complete (end of Week 4)

## Active Work

### Current Epic(s)
- **Phase 1 Discovery & Assessment** — All tasks currently "Not Started", kickoff this week

### Upcoming
- Finalize Phase 1 deliverables
- Present recommendations to Beckway
- Phase 2 proposal (remediation and modernization execution)

## Beckway's Strategic Roadmap ("Stabilize & Federate")

Beckway's March 2026 TDD recommended a three-phase approach with the strategic verdict: **"Retain & Stabilize"** iBizFusion.

### Phase 1: Immediate Stabilization (Months 1–7) — MANDATORY
**Objective:** Remove end-of-life and single-point-of-failure risk, establish a supportable platform baseline.
- **Thrive side:** New Windows Server 2022, upgrade MySQL → 8.0 (iBiz developer refactors legacy SQL), upgrade ColdFusion → 2025, extend Power BI to consolidate GP + iBiz financials
- **Buyer side:** Approve GP 18.8 + SQL 2022 upgrade (~3-year runway), reject Atlas "flat file" workaround and invest in proper hardware/scaling, execute retention agreement for Adrian
- **Status as of April 2026:** Adrian has already completed Win Server 2025, CF 2023, and MySQL 8 upgrades — ahead of Beckway's timeline. Code-level security work (Webapper's scope) was NOT part of Beckway's Phase 1.

### Phase 2: Operational Bridges (Months 8–15)
**Objective:** Replace legacy front-end components, implement iPaaS + CRM consolidation.
- Replace custom ASP.NET storefront with SaaS commerce (Shopify Plus or BigCommerce B2B)
- Replace Atlas middleware with iPaaS (Celigo or eOne SmartConnect) for real-time inventory and order routing
- Evaluate integrated CRM + Marketing suite (HubSpot) to consolidate Salesforce + SalesPad
- Build lightweight WMS ("Bin Location") capability in iBiz

### Phase 3: Unified Future (Months 15+)
**Objective:** Migrate both businesses to a single cloud ERP.
- Formal ERP selection — Microsoft Dynamics 365 Business Central is the leading candidate (fits Buyer's Microsoft stack, scales to $100M+)
- Buyer migrates first (simpler financial mapping), then Thrive rebuilds compliance/QA workflows as Power Apps + BC extensions
- Legacy data archived to Data Lake; only opening balances + key reference data migrated to BC

### Beckway's Process Maturity Scores (1–5 Scale)
| Process Area | Execution Maturity | System Enablement |
|-------------|-------------------|-------------------|
| Order-to-Cash (Sales) | 3.1 | 2.8 |
| SIOP / Planning | 3.0 | 3.0 |
| Procure-to-Pay (P2P) | 2.8 | 2.8 |
| Quality Management (QM) | 2.8 | 2.8 |
| Inventory & Warehouse (WM) | 3.0 | 2.8 |
| Production / Blending / Repack | 1.0 | 1.0 |
| Fulfillment (Shipping) | 3.5 | 3.0 |
| Finance & Accounting | 2.7 | 2.6 |
| Master Data Management (MDM) | 2.8 | 3.0 |
| Integrations & Reporting | 2.7 | 2.0 |

**Key gaps:** Production/blending scored lowest (1.0) — no formal production orders, WMS, or barcoding. Finance reporting is fragmented across iBiz AR subledger, QuickBooks G/L, and Excel roll-forwards.

### Beckway's Top-5 Strengths & Top-5 Gaps
**Strengths:** Lot-level traceability, data-driven supply planning, strong QA/lab testing, clear pricing logic, consistent Salesforce usage for pipeline.
**Gaps:** Fragmented data model (iBiz + QB + Excel), heavy manual workarounds across entire value chain, limited system integration (SF→iBiz→QB), no workflow automation for core controls, overuse of Google Drive/spreadsheets as systems of record.

### Risk Mitigation: Outsourced Support (Beckway's Provider Recommendations)
Beckway identified four provider categories to reduce key-person risk:
1. **Legacy ColdFusion Upgrade Specialists** — boutique firms for CF 2018→2023/2025, MySQL 5.6→8.0, Phase 1-2 refactoring
2. **Enterprise MSP (24/7 Operations)** — managed services provider for monitoring, incident response, routine maintenance, structured onboarding playbook to capture Adrian's tribal knowledge
3. **Offshore Refactoring Pool** — cost-efficient build team for high-volume repetitive code changes under specialist direction
4. **Strategic ColdFusion Audit Partner (Optional)** — senior CF consultancy for independent code/security audits (this is essentially Webapper's role)

## Known Tech Debt (Comprehensive)

### Critical (Address Immediately)
- Hardcoded credentials throughout codebase
- SQL injection vulnerabilities (221+ queries)
- Weak credit card encryption
- Exposed API keys in web-accessible files
- No CSRF protection

### High Priority
- No automated testing of any kind
- No CI/CD pipeline
- No separate QA/staging environment
- No secrets management
- Single point of failure (one VPS, one developer)
- `commonFunctions.cfm` is a god-file with massive coupling

### Medium Priority
- Inline business logic in view files
- Duplicated logic across modules (costing, notifications, reporting)
- Legacy/unused files in codebase
- Minimal inline documentation
- No formal API documentation or versioning
- No monitoring beyond email alerts and slow-query logs

### Strategic
- ColdFusion licensing costs and talent availability
- Single-vendor hosting dependency (Hostek)
- No disaster recovery plan
- No cloud readiness
- UI modernization needed long-term

## Release Cadence / How Code Gets to Production

Currently: Adrian commits to Git, deploys manually to the single Hostek VPS. No code review, no automated testing, no staging gate. Changes go directly to production.

**Webapper recommendation:** Establish proper SDLC with code review, automated testing, staging environment, and deployment gates before any remediation work begins.

## Key Links

- **Jira:** webapper.atlassian.net (project: HERB)
- **Google Drive:** [Herbco shared folder](https://drive.google.com/drive/u/0/folders/0AAU0auAeY4VQUk9PVA)
- **Production:** ibizfusion.com
- **Staging:** ibizsuite.com
- **Hosting:** Hostek (cp.hostek.com)
- **GitHub:** github.com/npnutra-tech/ibizfusion (CODE ESCROW ONLY — not Adrian's working repo)

### GitHub Escrow Note
The GitHub repo (npnutra-tech/ibizfusion) has only 6 commits and 2 contributors (ibizfusion, beckway-andrew). The initial commit message references "Schedule 2.5(j)" — this is a code escrow deposit per the acquisition agreement, NOT Adrian's day-to-day development repository. The code is from Jan/Feb 2026 and does NOT reflect the recent CF 2023 + MySQL 8 upgrade. Adrian's actual working repo is separate and we don't yet have access.

### Disaster Recovery Status
**No DR/failover plan exists.** Single Hostek VPS, no tested backup restoration, no failover runbook, no documented RTO/RPO. This is a known risk flagged by both Beckway and Webapper.

## Documentation Index (Google Drive)

### iBizFusion Documentation (Sharepoint) — 13 docs
Core system documentation written by Adrian. Key docs:
- Technical Debt Overview and Modernization Plan (Adrian's self-assessment)
- Technical Overview and Audit Documentation
- Platform Overview and Operational Processes
- Existing Process Flows for 5 Key Areas
- Salesforce Bi-Directional Integration
- Infrastructure Upgrade Plan
- Upgrade and Technical Status (includes Q1 2026 progress — 171 commits)
- commonFunctions.cfm Shared Library Overview
- Landed Cost Management
- Centralized Maintenance Scheduler
- Enhancing NP Nutra's Digital Strategy (eCommerce analysis)
- IBIZSUITE → IBIZFUSION migration runbook
- Balance Sheet Explained

### Archive/eCommerce Documentation — 9 docs
WordPress/WooCommerce integration work (in progress):
- WooCommerce Integration Strategy
- eCommerce API
- eCommerce Implementation Recovery & Alignment Plan
- Proposed Working Protocol (iBiz ↔ WordPress)
- Week 1 PHP/CF deliverables
- NP Nutra B2B Customer Registration
- NP Nutra Website QA Review
- Proposal for CF-based eCommerce shop

### Development/Enhancement — 1 doc
- Security Risk and Remediation Summary (Steven/Webapper)

### Root Folder (PDFs/HTML — not readable via Drive MCP)
- Webapper Proposal to Beckway
- Thrive TDD and ERP Feasibility (redacted)
- Directional Scope for iBiz
- Static Analysis Results (PDF)
- Team Onboarding Checklist (HTML)
- Phase 1 Visual Plan (HTML)
- Phase 1 Project Plan (Google Sheets)
- iBiz Visual Walkthrough (HTML)
- iBiz Project Briefing (HTML)
- Status Update Template (PPTX)
