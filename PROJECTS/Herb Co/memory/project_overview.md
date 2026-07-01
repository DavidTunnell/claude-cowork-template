# project_overview

**Last Updated:** 2026-07-01

**Summary:** Webapper is the technical team for Beckway's PE-led acquisition of Monterey Bay Herb Co., which is itself acquiring Thrive (~$23M nutraceutical ingredients supplier). Thrive runs entirely on iBizFusion — a custom monolithic ColdFusion / MySQL ERP built over 20+ years by a single external developer (Adrian, Romania). Our 6-month engagement has three phases: Phase 1 (Month 1, 2 FTE) is technical evaluation and risk validation, ending in a prioritized remediation roadmap to Beckway PMO. Phase 2 (Months 2–3, 2 FTE → 1 FTE) is stabilization and remediation. Phase 3 (Months 4–6, 1 FTE) is Pacific Time business-hours MSP support. We currently operate in Phase 1.

## Why this matters

iBizFusion is both a **strategic asset** (natively automates complex compliance workflows the business depends on) and a **technical liability** (single-developer dependency, security debt — 221 unparameterized SQL queries, 30+ hardcoded credentials, CFMX_COMPAT encryption on stored card data, no staging, no CI). Beckway's TDD called the developer-in-Romania pattern a "Code Island." Beckway's longer roadmap eventually migrates the business to Microsoft Business Central as a single cloud ERP — but that's Phase 3 of *their* multi-year plan, not ours. Our job is to leave iBiz in a state that makes that migration easier, not harder.

## Who's involved

- **Beckway (client):** Andrew Latypov (Director, primary contact), Raj Thangavelsamy (Partner), Chip Hyman (MD)
- **Herbco / Thrive:** Juan Pozzi (CFO)
- **iBiz:** Adrian (sole external developer), Mark (Power BI / Azure consultant, 10–20 hrs/wk)
- **Webapper:** David Tunnell (engagement lead), Patrick Quinn (CEO sponsor), Steven Nguyen (tech lead, static analysis), Daniela Camargo (PM/QA), Henry Tran (SDET), J. Miller

## What's true today

- **Engagement expanded (2026-07-01):** iBiz stack upgrade complete; initial security remediation (`HERB-1`) largely closed; now running web consolidation (AWS) + commerce migration (Azure) (`HERB-129`, ~Sept cutover), SharePoint/M365 migration (`HERB-121`), and retained ERP support + Azure/AWS monitoring.

- AWS Herbco UAT account stood up (797601398324, customer-owned). Six Webapper team members provisioned via IdC SSO.
- Static analysis delivered (2026-04-06). Gap analysis comparing Adrian's docs vs. our findings exists in OUTPUTS history.
- Q1 2026 Adrian completed major upgrade: Win 2012 R2 / CF 2018 / MySQL 5.6 → Win 2025 / CF 2023 / MySQL 8. 171 commits Jan–Mar.
- No staging environment yet. Standing one up is a Phase 1 prerequisite for Phase 2 work.

## What to keep top of mind

Pace and tone matter. Beckway PMO is watching. If we nail Phase 1 + the MSP transition, this becomes a long-term relationship across Beckway's portfolio. If we stumble, we lose the client.
