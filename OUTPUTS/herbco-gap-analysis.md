# Herbco / iBizFusion — Gap Analysis
## Webapper Findings vs. Adrian's Internal Documentation

**Date:** April 7, 2026
**Prepared by:** David Tunnell, Lead Software Architect
**Purpose:** Compare what Adrian's documentation claims about the iBizFusion platform against what Webapper's independent static analysis and document review actually found. This informs the Thursday meeting agenda and Phase 1 deliverables.

---

## Executive Summary

Adrian has produced extensive, well-organized documentation of the iBizFusion platform. His understanding of the system architecture and business logic is clearly deep. However, his security and code quality assessments are significantly more optimistic than what our independent analysis reveals. The gaps fall into three categories: things he overstates, things he omits entirely, and things where his recent improvements haven't been applied system-wide yet.

---

## 1. Security Posture — Major Discrepancy

### What Adrian's docs say:
- "SQL queries use CFQUERYPARAM everywhere, ensuring strong protection against SQL injection"
- "Input escaping and sanitization patterns are consistently applied"
- "No known security incidents"
- URL parameter validation issues described as "low-severity security risk"
- Security section summary: strengths outweigh weaknesses

### What Webapper found:
- **221 unparameterized SQL queries** across the codebase
- **35 files** with ORDER BY SQL injection vulnerabilities
- **SQL injection via dynamic table and column names** (not just parameters)
- **30+ hardcoded production credentials** in source control (DB passwords, API keys, SMTP creds)
- **Stripe secret keys** in a web-accessible test file
- **CFMX_COMPAT (RC2-40 bit) encryption** still used for credit card data — a PCI DSS violation
- **1.5% CSRF coverage** across the application
- Missing security headers, session management weaknesses, directory traversal risks

### Assessment:
This is the most significant gap. Adrian's claim of universal CFQUERYPARAM usage is flatly contradicted by the static analysis finding 221+ unparameterized queries. There are two possible explanations: (1) Adrian is unaware of the full scope of legacy code, or (2) his documentation reflects aspirational state after recent remediation that hasn't reached all files yet.

His Q1 2026 commit log does show real security work — password hashing migration, CSRF on login forms, login throttling, API key signing, cookie security improvements. This suggests he's actively remediating but hasn't completed a systematic pass across the full 1,334-file codebase.

**Recommendation for Thursday:** Don't lead with "your docs are wrong." Instead, share the static analysis results and ask Adrian to walk through what he's already fixed vs. what's still outstanding. His reaction will tell us whether the documentation gap is awareness-based or positioning-based.

---

## 2. Code Architecture — Largely Accurate, But Understated Risk

### What Adrian's docs say:
- "Classic ColdFusion structure" with logical separation between UI (.CFM) and APIs (.CFC)
- "CF tags extensively" — keeps logic readable
- "Some duplicated logic" across modules
- "Additional modularization would improve maintainability"

### What Webapper found:
- `commonFunctions.cfm` is a massive god-file containing permissions, inventory management, order state machines, pricing, costing, commissions, audit trails, KPI calculations, and integration utilities — all in one file
- Business logic is inline in .cfm view files, not separated into a service layer
- Functions in commonFunctions.cfm depend on APPLICATION, SESSION, and Request scopes — making them non-portable and untestable in isolation
- No automated tests of any kind
- No CI/CD pipeline
- Direct production deployment with no code review or staging gate

### Assessment:
Adrian's description is technically accurate but significantly understates the architectural risk. Calling it "some duplicated logic" undersells the reality of a system where modifying `updateProductInventory` or `updateOrderStatus` can cascade through inventory, invoicing, commissions, and customer status in ways that are impossible to predict without deep institutional knowledge — knowledge that currently lives in one person's head.

The `commonFunctions.cfm` overview document is actually excellent — it's one of the best pieces of documentation Adrian produced. It clearly maps the high-impact functions and their dependencies. This is exactly the kind of knowledge transfer we need more of.

---

## 3. Infrastructure — Accurate, Recent Upgrade Already Done

### What Adrian's docs say:
- Successfully upgraded from Win Server 2012 / CF 2018 / MySQL 5.6 to Win Server 2025 / CF 2023 / MySQL 8
- Production cutover completed via SiteLock WAF routing
- Old server retained as rollback
- MySQL 8 upgrade required application-level query changes only (no schema changes)
- 171 commits Jan–Mar 2026 covering the upgrade work

### What Webapper found:
- Infrastructure upgrade plan and execution appear solid and well-documented
- Hostek VPS at $269.30/month — reasonable for current scale
- The IBIZSUITE → IBIZFUSION migration runbook is thorough and methodical
- Development machine running CF 2025 for early deprecation visibility — smart practice

### Assessment:
This is an area where Adrian's work and documentation are strong. The infrastructure upgrade was planned and executed competently. The migration runbook could serve as a template for future work. Our AWS migration recommendation is a strategic improvement (redundancy, DR, CI/CD), not a correction of a failure.

**Key nuance for the team:** Adrian already did the hard work of MySQL 5.6 → 8 query compatibility. That's a significant effort that shouldn't be dismissed. Our recommendation to move to AWS is about operational maturity (CI/CD, monitoring, DR), not about the current stack being broken.

---

## 4. eCommerce Integration — In Progress, Mixed Quality

### What Adrian's docs say:
- 99% complete CF-based eCommerce shop exists, integrated with iBiz ERP
- Alternative WordPress/WooCommerce integration also in progress
- REST API + HMAC-signed callbacks designed for WP ↔ iBiz communication
- Customer registration flow with draft queue and human validation
- Detailed week-by-week sprint plans for PHP and CF sides

### What Webapper found:
- The eCommerce documentation is extensive (9 separate docs in the archive folder)
- The "Enhancing NP Nutra's Digital Strategy" doc includes AI-generated recommendations that accurately identify Shopify's limitations for their lot-based, pack-size, tiered-pricing model
- The WooCommerce integration strategy shows thoughtful security design (HMAC, nonce, signing secrets)
- However, the "Proposed Working Protocol" disclaimer admits all code examples are AI-generated and "not tested in all environments"

### Assessment:
The eCommerce work represents active development that may be paused or deprioritized during our engagement. Worth understanding the current state but not a Phase 1 priority. The REST API work Adrian did here could be valuable for the future modernization path.

---

## 5. Business Logic Documentation — Genuinely Valuable

### What Adrian documented well:
- **Process Flows for 5 Key Areas** — Clean, accurate mapping of Lead-to-Order, Demand-to-Fulfill, Order-to-Cash, Procure-to-Pay, Record-to-Report
- **Landed Cost Management** — Detailed explanation of lot-based costing, automatic vs. manual allocation, and reporting impact
- **Balance Sheet** — How financial calculations derive from operational data
- **commonFunctions.cfm overview** — Maps all high-impact functions with dependencies and safe modification practices
- **Salesforce integration** — Bi-directional sync documentation

### Assessment:
This documentation is a significant asset. Adrian clearly understands the business domain deeply. These docs save us weeks of discovery work. They should be verified against the actual codebase during our review, but they provide an excellent starting point.

---

## 6. Key Omissions — What Adrian's Docs Don't Address

The following topics are absent or barely mentioned in Adrian's documentation:

1. **Credit card data handling** — The Authorize.Net tokenization flow is documented (and is correct), but the legacy CFMX_COMPAT encryption for stored card data is never mentioned. This is the most dangerous omission from a compliance perspective.

2. **CSRF protection** — Not mentioned in any document. Our analysis found 1.5% coverage. Adrian's Q1 commits show he added CSRF to login forms, but the rest of the app is unprotected.

3. **Secrets management** — Hardcoded credentials appear in 30+ locations. No documentation addresses this. No mention of environment variables, secrets vaults, or credential rotation.

4. **Testing strategy** — No mention of any testing approach (unit, integration, manual, regression). The entire test strategy appears to be "Adrian manually checks things work."

5. **Disaster recovery** — No DR plan documented. Single VPS, single developer, no automated backups verification process.

6. **Bus factor** — Adrian is the single point of knowledge and access. His documentation partially mitigates this but doesn't address the operational risk of one-person dependency.

7. **Monitoring and alerting** — Brief mention of email alerts and slow-query logs, but no systematic monitoring, no uptime checks, no performance baselines.

---

## Summary: Trust but Verify

Adrian's documentation demonstrates deep system knowledge, genuine pride in his work, and active investment in improvement (171 commits in Q1 alone). The business logic documentation is excellent and genuinely valuable for our assessment.

The security claims, however, cannot be taken at face value. The gap between "CFQUERYPARAM everywhere" and "221 unparameterized queries" is too large to be a rounding error. This doesn't necessarily mean Adrian is being dishonest — it may mean his self-assessment predates the static analysis tooling we used, or that he's documenting the standard he's working toward rather than current state.

**Thursday meeting approach:** Lead with respect for his work and documentation quality. Use the static analysis as a shared fact base rather than an accusation. The goal is to make Adrian a collaborator in the remediation, not a defensive adversary.
