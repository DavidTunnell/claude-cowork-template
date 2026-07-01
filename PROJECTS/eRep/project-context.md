# Project Context: eRep

**Last Updated:** 2026-07-01
**Jira Key:** EREP
**Type:** Client engagement — managed AWS hosting + ColdFusion/Lucee application support

## Overview

eRep (erep.com) is an external client whose web-application stack Webapper hosts and operates on AWS. The application tier runs Adobe ColdFusion 11 and Lucee 6 on Windows web servers behind an Application Load Balancer, backed by a SQL Server database. Webapper provides ongoing managed hosting: performance and security reviews, patching, database migrations, backups, and incident support. The estate serves several domains (erep.com, erep.work, cfhindsight.io, whatsmycvi.com).

## Current Status

- **Phase:** Managed hosting (active, healthy)
- **Recent work:** SQL Server 2016 → 2022 cutover completed (May 2026); AWS WAF deployed (resolved a March bot-attack issue); point-in-time DB restore validated (June 2026). All EREP-tracked tickets currently Done.
- **Active governance work** (in the WEBA project): hosting-foundations remediation (root MFA, ADRs, incident-response runbooks) in progress; Q2 BHS review completed.
- **Open client decisions:** migrate the Windows Server 2012 R2 hosts to Server 2022 over the next two quarters; Reserved Instance renewal; possible on-demand billing for an underused dev workstation.
- **Billing model:** BHS (Basic Hosting Support, included) vs AHS (Additional Hosting Support, billed).

## Team

| Name | Role | Focus |
|------|------|-------|
| Joy Miller | Hosting lead / reviewer | Client-facing, BHS reviews |
| Henry Tran | Engineer | Database & infra work |
| Peter Truong | Engineer | Foundations remediation |
| David Tunnell | Lead Software Architect | Oversight / reporting |

External: eRep — owner/decision-maker and their developer (client-side, by role).

## Architecture (high level)

- **Cloud:** AWS — EC2 (Windows Server 2012 R2 web servers), Application Load Balancer (manual scaling, no ASG)
- **Database:** RDS SQL Server 2022
- **App layer:** Adobe ColdFusion 11 + Lucee 6
- **Perimeter:** AWS WAF, ACM (TLS)
- **Ops:** CloudWatch, EBS snapshots, SSM

## Key Workflows / Integrations

- Quarterly **BHS reviews** with a customer-facing summary email
- SSM-driven fleet inventory / patch checks
- RDS point-in-time restore and database migrations
- WAF / TLS perimeter protection; EBS daily backups
- ADRs and incident-response runbooks maintained in the ops repo

## Where Things Live

- **Jira:** project `EREP`; active hosting items also tracked in `WEBA`
- **Ops repo:** `webapper-os` — `foundations/erep-audit.md`, `projects/erep/decisions/` (ADRs), `wiki/runbooks/`
- **Comms:** `aws@webapper.com` (alias `aws+erep@webapper.com`)
- **Process:** follows the WAG (`ABOUT ME/our-process.md`)

> Operational context only. AWS account IDs, credentials, security-audit specifics, and pricing are intentionally kept out of this template — see access-controlled Jira/Confluence.
