# Project Context: Atlantic British (RoverParts.com)

**Last Updated:** 2026-07-01
**Jira Key:** AB
**Type:** Client engagement — managed AWS hosting + ColdFusion application support

## Overview

Atlantic British Ltd. is a North American supplier of Land Rover and Range Rover parts and accessories (Clifton Park, NY; sister brand British Pacific in CA), running an e-commerce business primarily on **roverparts.com**. Webapper manages their AWS cloud infrastructure and supports their ColdFusion-based e-commerce platform — provisioning, monitoring, performance tuning, incident response, and periodic hosting health/security reviews. It's a long-running relationship (original AWS build and data migration date back to ~2022).

## Current Status

- **Phase:** Managed hosting / maintenance (steady state)
- **Recent work:** RDS MySQL performance tuning; resolved S3/CloudFront 403s that were suppressing Amazon Seller Central listings; virtual-host config cleanup.
- **Cross-cutting hosting governance** (tracked in the WEBA project): a scoped hosting/security "foundations" audit, and migrating hosting credentials into AWS Secrets Manager (off Confluence).
- **Cadence:** Ongoing support with recurring BHS (Basic Hosting Support) reviews; additional scoped work billed as AHS (Additional Hosting Support).

## Team

| Name | Role | Focus |
|------|------|-------|
| Henry Tran | Engineer | Hosting/infra, recent AB work |
| Joy Miller | Engineer / hosting facilitator | Reviews, client coordination |
| Steven Nguyen | Tech Lead (VTeam) | DB / CloudFront work |
| Peter Truong | Engineer | Foundations audit / runbooks |
| David Tunnell | Lead Software Architect | Technical oversight |

External: Atlantic British / RoverParts e-commerce team (client-side contacts by role).

## Architecture (high level)

- **Cloud:** AWS, Terraform-provisioned, accessed via cross-account IAM role
- **Edge/DNS:** Route 53; CloudFront CDN + S3 for image hosting (`images.roverparts.com`)
- **Compute:** EC2 web servers with auto-scaling
- **Database:** RDS MySQL
- **App layer:** Adobe ColdFusion (historically monitored via SeeFusion, moving toward CloudWatch)
- **Monitoring:** CloudWatch

## Key Workflows / Integrations

- Auto-scaling with post-scale server reconciliation; CloudWatch/auto-scaling health checks
- CloudFront/S3 image delivery feeding **Amazon Seller Central** via SP-API listing feeds
- Recurring **BHS reviews** — periodic hosting health & security checklist (shared Google Sheet)
- AWS cost monitoring; Route 53 / DNS management
- FTP-based deploy on web servers

## Where Things Live

- **Jira:** project `AB` (plus cross-cutting items in `WEBA`)
- **Infra:** AWS via Terraform
- **Docs:** hosting foundations audit template + runbooks in the Webapper ops repo; BHS review checklists and weekly sprint summaries in Google Drive
- **Process:** follows the WAG (`ABOUT ME/our-process.md`)

> Operational context only. AWS account IDs, credential locations, and specific security-audit findings are intentionally kept out of this template — see Jira/Confluence (access-controlled) for those.
