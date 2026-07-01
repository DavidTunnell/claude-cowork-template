# project_overview

**Last Updated:** 2026-07-01

**Summary:** Icon Media ("Iconfigurators") is a managed-AWS-hosting client running a Lucee/CFML e-commerce platform that serves many wheel/tire storefront domains (schottwheels.com, uswheel.com, iconvehicledynamics.com, …). Webapper runs prod + QA infra and ongoing ops. Jira key `IM`.

## What's true today

- Mature, steady state — most tracked work is Done. Recent: crawler/indexing (waiting on customer), ELB→ALB alerting cleanup.
- 2025 "System Upgrades" epic completed April 2026: QA rebuild + SQL Server 2022 + Lucee 6.
- Stack: AWS (EC2 ASG w/ AMIs, ELB/ALB, S3, WAF, Route 53), Windows/IIS → AJP → Tomcat, Lucee CFML, SQL Server 2022, SeeFusion.
- Governance (ADRs, Secrets Manager credential migration) tracked in `WEBA`.

## Who's involved

- **Webapper:** Joe Lopez (primary), Joy Miller (facilitator), Henry Tran (infra), Donald Quitangon & Steven Nguyen (support), William Koenigslieb (AWS); David Tunnell (oversight)
- **Client:** Icon Media staff (by role)

## Keep top of mind

High-volume multi-storefront hosting; SEO/indexing and checkout/payment integrations matter most. Operational-only in this template.
