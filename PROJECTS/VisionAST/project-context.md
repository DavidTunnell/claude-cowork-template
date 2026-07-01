# Project Context: VisionAST  *(RETIRED / TRANSITIONING — historical reference)*

**Last Updated:** 2026-07-01

> **Status:** This engagement is winding down. VisionAST's customers are migrating to another platform, and Webapper's involvement is concluding in 2026 (infrastructure downsizing began 2026-07-01; the remaining work is DMS importer maintenance through the final customer transfers). This file is kept as **historical reference** for a long-running client relationship — it is not an active project. The details below describe the system as it operated during the active engagement.

## Overview

[VisionAST](https://www.visionast.com/) was, for years, Webapper's longest-running client relationship — a data analytics platform for automotive and powersports dealerships that turns DMS (dealership management system) data into real-time profitability reporting. The product suite includes SalesVision (variable ops reporting), PowerVision (powersports-specific), FinanceVision (F&I analytics), ServiceVision (service department metrics), and MenuVision (F&I video/reporting via the iTapMenu partnership). It integrates with 12+ DMS systems, runs on AWS, and provides unified single sign-on across multi-location dealer groups.

## Current Status

- **Phase:** Winding down / transitioning away from Webapper — customers migrating to another platform (~75% migrated as of late June 2026)
- **Remaining work:** DMS importer maintenance through the final customer/location transfers (early July 2026); infrastructure decommission/downsizing (initial downsizing completed 2026-07-01)
- **Historical focus (during active engagement):** DX1 DMS integration for PowerVision/FinanceVision, Motive MIX v2 API migration, Fortellis Deals API migration (CDK requirement), performance tuning for large-scale users

## Team

| Name | Role | Focus Area |
|------|------|------------|
| Joy Miller | PM / Scrum Master / Manual Tester / SME | Product management, QA, domain expert, customer liaison, onboardings |
| Steven Nguyen | Tech Lead (VTeam) | Architecture, all projects oversight |
| Ann Nguyen | Sr. Dev | Primary developer, DMS importers/parsers, DX1 integration |
| Kyle Harrington | VisionAST Dev (client-side) | Motive APIs, Fortellis integration, CDK holdback, ranking reports, automated testing |
| Henry Tran | SDET | BHS updates, Selenium tests, documentation, DevOps |
| Peter Truong | Sr. Dev | Assists with onboardings, previously explored automating onboardings |
| David Tunnell | Lead Software Architect | Architecture oversight, modernization planning, AWS cost optimization |

### VisionAST Client Team

Client-side operations contacts were maintained in access-controlled systems (not reproduced in this template).

## Architecture

### Current Tech Stack (Legacy)
- **Frontend:** Angular (legacy client application)
- **Backend:** ColdFusion/Lucee (CFML), running on AWS Linux
- **Database:** Amazon Aurora MySQL (RDS), migrated from SQL Server
- **Infrastructure:** AWS EC2 with Auto Scaling (api-prod group), load balanced
- **FTP:** AWS Transfer Family (visionast-ftp S3 bucket) for DMS data ingestion (R&R, DealerVault)
- **Reporting:** DealerVault automated reports (daily Lagging Dealer Reports)

### Planned Modern Stack (Rewrite/Strangler Pattern)
- **Frontend:** React
- **Backend:** Node.js, AWS Lambda (serverless)
- **Database:** Aurora Serverless v2 (read-only replica for reporting scale)
- **Architecture:** Microservices, API-first, modular
- **Strategy:** Strangler Pattern (gradual replacement starting from edge components)
- **Status:** Business case written (2025), discovery phase not yet started

### DMS Integrations
VisionAST integrates with 12+ Dealer Management Systems. Key integrations:
- **CDK** (largest DMS partner, transitioning from PIPs to Fortellis Deals APIs)
- **Motive/Automate/Autosoft** (MIX API v1 migrating to v2 by end of 2026)
- **DX1** (new powersports DMS, currently being integrated)
- **LightSpeed** (powersports DMS)
- **R&R** (via AWS FTP/S3)
- **DealerVault** (data aggregation, automated reporting)
- Each DMS requires its own importer (data ingestion) and parser (data transformation) pair

### Product Suite
| Product | Description | Primary Data |
|---------|-------------|-------------|
| SalesVision | Variable ops reporting for auto dealerships | Sales deals, F&I products, gross profit |
| PowerVision | Powersports-specific reporting | Unit deals, powersports metrics |
| FinanceVision | F&I analytics and production reporting | Finance products, penetration rates, per-deal metrics |
| ServiceVision | Service department metrics | RO data, parts, labor hours |
| MenuVision | F&I video/reporting (iTapMenu partnership) | Menu presentation data |
| AutoPayPlus/APP | Payment protection product tracking | APP enrollment and performance |

### Environments

| Environment | Details |
|-------------|---------|
| Production | EC2 Auto Scaling group (api-prod), Aurora MySQL, separate AWS account (ID in access-controlled systems) |
| Test | test2 user (Master Agent with access to ALL locations, used for large quarterly reports) |

### AWS Infrastructure
- **Account:** separate from Webapper main account (ID in access-controlled systems)
- **Region:** us-east-1 (assumed)
- **Auto Scaling:** api-prod group (scales up at 10:00 UTC, down at 23:07 UTC daily)
- **Database:** Aurora MySQL (RDS), previously 8xl instance (cost optimization in progress to downsize)
- **FTP:** AWS Transfer Family with S3 backend (visionast-ftp bucket)
- **Alerting:** VisionAST-API-Prod-COMPOSITE-HighHealthyHosts (email alerts when only 1 server healthy)
- **AMI:** Custom auto-scale AMI maintained for EC2 instances

## Active Work

### Current Epic: DX1 Integration (VAST-3408) - In Progress
Writing importer/parser for the DX1 powersports DMS for PowerVision and FinanceVision data. Note: DX1 does not provide "pack" data or Deal Type. Primary dev: Ann Nguyen.
- [VAST-3653] Onboard DX1 Pilot Locations (In Progress, Ann)
- [VAST-3630] Write Importer for Customer Info (Confirmed, Ann)
- [VAST-3628] Write Parser for v1 MajorUnitDeal/Summary (Confirmed, Ann)
- [VAST-3632] Importer and Parser Alerts and Monitor (Confirmed, Ann)

### In Progress
- [VAST-3650] VisionAST BHS Updates (Henry Tran)
- [VAST-3608] Review System Error Logs and Address Anything Needed (Ann Nguyen)
- [VAST-3450] Ranking Report Automated Testing (Kyle Harrington)
- [VAST-3424] Technical Debt Review/Planning (Joy Miller)

### Upcoming / Waiting
- **Motive MIX v2 API Updates (VAST-3542)** - Confirmed, Ann Nguyen. Motive requires all v1 APIs updated to v2 by end of 2026.
- **Fortellis Deals APIs Integration (VAST-3181)** - Waiting on customer. CDK requiring migration from PIPs to Fortellis. Currently in estimation phase.
- **Customized Deals Page (VAST-3285)** - Discovery. New customizable Deals page with selectable metrics.
- **Performance Improvements (VAST-3441)** - To Do. Goal: optimize enough to downsize from 8xl database to reduce costs.
- **Automated Test Coverage (VAST-3448)** - To Do. Focus on 4 most complex reports: F&I Summary, Ranking Report, Production Recap Report, Unit Report.

### Stale (Backlog, not actively worked)
- Automate Onboardings (VAST-2929)
- "Gross Only Product" category addition (VAST-2498)
- New Metrics for ServiceVision (VAST-2279)
- Custom Reports for Cable Dahmer (VAST-3025)
- Pacesetter Dashboards/Reports (VAST-3216)

## Completed Epics (Recent History)

- **Unqualified Category for PowerVision (VAST-3043)** - Added unit/vehicle type categorization for unqualified deals
- **Performance Tuning for test2 user (VAST-3243)** - Optimized Master Agent queries that access ALL locations for quarterly reporting

## Modernization Vision (Long-Term)

A full business case was written in 2025 proposing a complete platform rewrite using the Strangler Pattern:

**Why:** The legacy CFML/Lucee monolith has accumulated significant tech debt. The permission system is broken and complex. Feature updates cause side effects across the suite. Performance is limited by the monolithic architecture.

**Proposed Architecture:** Serverless microservices (AWS Lambda + Node.js), React frontend, Aurora Serverless v2, RBAC permissions, modular product suite where SalesVision/PowerVision/FinanceVision/ServiceVision can be independently updated.

**Approach:** Strangler Pattern in three phases: (1) Start on the edge with isolated components like auth, permissions, data import. (2) Build up the codebase with new microservices running parallel to legacy. (3) Gradually decommission legacy modules as new services are validated.

**Prerequisites:** Discovery phase and Prototype/PoC phase needed before full development. Neither has started yet.

**Current Action:** AWS cost optimization review (WEBA-89) examining potential savings for VisionAST infrastructure, with Joy Miller reviewing AI-generated cost analysis.

## Known Tech Debt (VAST-3424) - In Progress

- Permission system is broken and complex (top priority for rewrite)
- Legacy CFML/Lucee codebase limits performance and agility
- Monolithic architecture with interdependencies between product modules
- Limited automated test coverage
- Database costs too high (8xl instance needs to be downsized after performance optimization)
- Importer/parser failure alerting needs improvement (VAST-2066)

## Project Conventions

- **Ticket Tracking:** Jira project VAST (previously Assembla, many tickets reference old Assembla IDs)
- **Sprint Cadence:** Part of broader Webapper sprint cycle
- **Onboardings:** New dealership onboardings handled by Joy (PM) with dev support from Ann and Peter
- **DMS Pattern:** Each DMS integration follows the importer/parser pattern (importer pulls data, parser transforms it to VisionAST's data model)
- **Reporting:** DealerVault sends automated daily Lagging Dealer Reports to VisionAST operations team
- **AWS Alerts:** Auto Scaling notifications forwarded to visionast@webapper.com

## Key Links

- **Jira Project:** https://webapper.atlassian.net/browse/VAST
- **Confluence Space (VisionAST):** https://webapper.atlassian.net/wiki/spaces/VisionAST
- **Confluence Space (VisionAST Team Portal):** https://webapper.atlassian.net/wiki/spaces/VTP
- **Confluence - System Summary:** https://webapper.atlassian.net/wiki/spaces/VisionAST/pages/33783810/System+Summary
- **Confluence - System Infrastructure:** https://webapper.atlassian.net/wiki/spaces/VisionAST/pages/34242573/System+Infrastructure
- **Confluence - AWS Servers:** https://webapper.atlassian.net/wiki/spaces/VisionAST/pages/34045957/AWS+Servers
- **Confluence - AWS Alerting:** https://webapper.atlassian.net/wiki/spaces/VisionAST/pages/254574594/AWS+Application+Alerting
- **Confluence - Troubleshooting:** https://webapper.atlassian.net/wiki/spaces/VisionAST/pages/33783820/Troubleshooting
- **Confluence - Developer Setup (API):** https://webapper.atlassian.net/wiki/spaces/VisionAST/pages/34078735/Developer+Setup+-+API+CF+Site
- **Confluence - Developer Setup (Angular):** https://webapper.atlassian.net/wiki/spaces/VisionAST/pages/33587205/Developer+Setup+-+Angular
- **Confluence - Auto-Scale AMI:** https://webapper.atlassian.net/wiki/spaces/Webapper/pages/294354967/Update+the+Auto-Scale+AMI
- **Product Site:** https://www.visionast.com/
- **Google Drive (shared):** https://drive.google.com/drive/folders/0AG2fcGEjT6eOUk9PVA
- **Business Case Doc:** https://docs.google.com/document/d/12y-sA4gznR3E0x7Y9vIpzv7_o7KZKnv9GRdxDMljn5Y
