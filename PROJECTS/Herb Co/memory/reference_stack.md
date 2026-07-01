# reference_stack

**Last Updated:** 2026-07-01

**Summary:** iBizFusion's stack (post-upgrade) plus the platforms the newer workstreams touch. Use this to anchor questions like "what version of CF supports X" or "where does the commerce migration land."

## iBizFusion (core ERP)

- **Application:** Adobe ColdFusion 2023. Classic CF — `.cfm` for UI, `.cfc` for APIs, `<cftag>`-heavy.
- **God-file:** `commonFunctions.cfm` — permissions, inventory, order state machines, pricing, costing, commissions, audit trails, KPI calculations, integration utilities. Functions depend on APPLICATION/SESSION/Request scopes (non-portable, hard to test in isolation). Treat as high-cascade-risk.
- **Database:** MySQL 8 (upgraded from 5.6). 370+ tables, no published ERD. DDL changes are direct.
- **OS / Hosting:** Windows Server (upgraded off end-of-life). Managed VPS host with a third-party WAF.
- **Tests / CI:** none historically; no linter/formatter configured. Modern SDLC is being introduced with the new workstreams.

## New-workstream platforms

- **AWS** (marketing-site consolidation): Lightsail, Route 53, single WAF — consolidating NP Nutra sites (npnutra.com WordPress, NPNList) into Monterey's own account.
- **Azure** (commerce migration): App Service + Azure SQL Managed Instance for the Herbco storefront (ASP.NET + SQL Server), plus a Dynamics-GP-adjacent environment.
- **Microsoft 365 / SharePoint / Graph**: replacing iBiz's Google Drive document backend.

## Integrations (point-to-point, under-documented)

| System | Direction | Mechanism |
|--------|-----------|-----------|
| Salesforce CRM | Bidirectional | REST API |
| QuickBooks Online | One-way out | Manual journal entries |
| Google Drive → SharePoint | Reference | Docs (migrating to M365) |
| Lab Portals | Inbound | Manual data entry |
| Carrier APIs | Outbound | UPS, FedEx, DHL, USPS |
| e-Commerce | Bidirectional | REST API + HMAC-signed callbacks |
| NetNow Credit | One-way | Manual limit updates |
| Authorize.Net | Outbound | Tokenized payment flow |

## Security posture (high level)

The original codebase carried meaningful security debt typical of a long-lived single-developer ColdFusion app (input handling, credential management, stored-data encryption, CSRF coverage). A security code-review remediation effort (HERB-1) has addressed the bulk of it. **Specific findings, counts, and any credential details are tracked in access-controlled Jira/Confluence — not in this template.** Treat payments, auth, and the encryption layer as approval-required areas.

## What to confirm as work continues

- Backup/DR specifics for iBiz on AWS (DR documentation in progress)
- Monitoring/alerting coverage across iBiz + the new Azure/AWS environments
- Performance baselines
