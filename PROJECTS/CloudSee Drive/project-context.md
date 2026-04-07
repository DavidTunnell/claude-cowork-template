# Project Context: CloudSee Drive

**Last Updated:** 2026-03-18

## Overview

[CloudSee Drive](https://www.cloudseedrive.com/) is Webapper's flagship SaaS product. It gives teams a browser-based interface for Amazon S3 storage, replacing the need for the AWS Console or technical expertise. Users can browse, search, upload, download, and manage files across S3 buckets with features like sub-second search across millions of objects (powered by Fast Buckets indexing), file versioning, RBAC permissions, secure file sharing, activity monitoring, and support for files up to 5TB. It's available through the AWS Marketplace with same-day onboarding. The strategic goal is for CloudSee Drive to replace client services revenue and become Webapper's primary business.

## Current Status

- **Phase:** Active Development
- **Current Sprint Focus:** Favorites feature (mark/unmark S3 objects as favorites, display in left panel), automated testing buildout
- **Last Major Release:** Free Trial v2 (re-enabled with throttling limits: 1M indexed objects, 100K delta sync ops, 30-day trial via AWS Marketplace), deployed to production March 2026
- **Next Milestone:** Tag Explorer epic (next main feature, in discovery/estimation), Favorites targeting March 2026 release
- **Release Cadence:** Goal of 1 feature release per month

## Team

| Name | Role | Focus Area |
|------|------|------------|
| David Tunnell | Lead Software Architect | Architecture, requirements, AI coding integration, sprint management |
| Peter Truong | Sr. Dev | Frontend development, CloudSee Drive primary dev, also helps with VisionAST onboardings |
| Max Nguyen | Jr/Mid Dev | Backend development, CloudSee Drive |
| Steven Nguyen | Tech Lead (VTeam) | Tech lead across all projects, backend, Free Trial implementation |
| Henry | SDET | Documentation, Selenium tests, unit tests, DevOps, Jenkins pipeline |
| Daniela Camargo | PM / Scrum Master / Manual Tester / SME | Product management, QA, domain expert for CloudSee |
| Patrick Quinn | CEO | Product direction, roadmap prioritization, business strategy |
| Scott Herring | COO | Marketing, sales, SEO, marketplace publishing, dashboard review |

## Architecture

### Tech Stack
- **Frontend:** React, hosted on S3 + CloudFront CDN (repo: Bitbucket `csd-frontend`)
- **Backend:** Node.js microservices deployed as AWS Lambda functions via SAM CLI / CloudFormation (repo: Bitbucket `csd-backend`)
- **Database:** DynamoDB (tables prefixed by environment, e.g. `production_User`)
- **Search:** OpenSearch (powers Fast Buckets indexing engine, natural language + voice search)
- **Shared SDK:** `@webapper/cloudsee-drive-sdk` on NPM (handles DB connections, CORS, auth)
- **Auth:** SSO-ready, Microsoft Entra ID integration for group management. Three user types: Regular User, Admin, and Owner Admin (one per account). Owner Admin is the only user who can add other Admins and change company info. When adding users, the Admin selects which AWS accounts and Collections (buckets) they can access.
- **CI/CD:** Jenkins (pipeline performance recently optimized with EFS mount)

### Backend Microservices
- **User API** - User management, auth, permissions
- **Storage API** - S3 operations, file management
- **Metric Service** - Dashboard and business metrics
- **Fast Bucket Service** - OpenSearch indexing engine
- **Task Manager** - Background job orchestration
- **Zipper Task** - Multi-file download/zip operations

### Environments

| Environment | URL | Branch | DynamoDB Prefix |
|-------------|-----|--------|-----------------|
| Production | https://drive.cloudsee.cloud | production | production_ |
| QA | https://drive-qa.cloudsee.cloud | qa | qa_ |
| Development | https://drive-test.cloudsee.cloud | develop | qa_ |
| UAT/Demo | https://drive-uat.cloudsee.cloud | - | - |

### Support Portal Environments

| Environment | URL |
|-------------|-----|
| Production | https://drive-support.cloudsee.cloud |
| UAT | https://drive-support-uat.cloudsee.cloud |
| Test | https://drive-support-test.cloudsee.cloud |

### AWS Infrastructure
- **Region:** us-east-1
- **Services:** S3, Lambda, API Gateway, CloudFront, DynamoDB, OpenSearch, SES (email), Systems Manager Parameter Store, CloudFormation, SQS
- **Frontend S3 Buckets:** `cloudsee-drive-frontend-mp-saas` (prod), `cloudsee-drive-frontend-qa` (qa), `cloudsee-drive-frontend-test` (dev)
- **API S3 Buckets:** `cloudsee-drive-api-stack-production`, `cloudsee-drive-api-stack-qa`, `cloudsee-drive-api-stack-test`
- **CloudFront Distributions:** E3MYVWORHVNEPX (prod), E21TCNFRHC2JD7 (qa), E18Q5AYZVNBZS7 (dev)
- **Parameter Store:** `/cloudseedrive/appsetting/{environment}`
- **Deployment:** SAM CLI (bat/sh scripts per environment per service)

## Active Work

### Current Epic: Favorites (CSD-508) - In Progress
Users can mark and unmark S3 objects as favorites and view them in the left panel. Subtasks:
- [CSD-509] Frontend: Object list mark/unmark favorites (In Progress, Peter + Max)
- [CSD-510] Frontend: Left panel display favorites (In Progress, Peter)
- [CSD-511] Backend: CRUD services for favorites (Ready to Test, Max)
- [CSD-512] Database: DynamoDB table for favorite objects (Done, Max)

### Next Up: AWS Tag Explorer (CSD-513) - To Do / Discovery
Browse, search, and filter S3 objects by tags within CloudSee Drive. Tree view of all available tags, single and multi-select search with And/Or/Not logic, leverages existing Fast Buckets index. Currently in discovery sprint for estimation by Steven. This is the highest priority next feature per the roadmap prioritization algorithm.

### Upcoming Roadmap
- Home Directory feature (planned to start April 1-15, parallel with Tag Explorer)
- Advanced Search (future)
- Full document content semantic search (phase 2 of AI, metadata-only done)

### Ongoing
- [CSD-429] Spec Document Updates (In Progress)
- [CSD-476] Automated Testing: Unit and Selenium Test Buildout (In Progress, Henry)

## Completed Epics (Recent History)

- **Free Trial v2 (CSD-486)** - Re-enabled free trial with throttling (1M index objects, 100K delta sync, 30-day AWS Marketplace trial). Support portal allows per-subscriber limit overrides. Shipped March 2026.
- **Natural-Language & Voice Search (CSD-467)** - LLM-powered natural language query to structured OpenSearch query. Voice input via speech-to-text (Whisper/OpenAI). Metadata-only search. Shipped February 2026.
- **Large Files Uploads (CSD-235)** - Multipart upload supporting files up to 5TB via Uppy + AwsS3Multipart in React. Resumable uploads, parallel parts. Shipped late 2025.
- **Fast Buckets (CSD-144)** - OpenSearch-based indexing engine for sub-second search across millions of S3 objects. SQS-based fan-out architecture with Lambda pre-processing. Foundation for all search features. Shipped 2024-2025.
- **Dashboard Metrics (CSD-163)** - Internal business metrics dashboard tracking demos, trials, logos, users with date-range filtering.

## Known Tech Debt (CSD-202) - Discovery

- Code quality and maintainability (refactor legacy code, remove dead code)
- Testing and coverage improvement (automate unit and integration tests)
- Process automation / CI/CD pipeline improvements
- Infrastructure as Code standardization
- Observability and monitoring (centralized logging, alerting)

## Release Cadence / How Code Gets to Production

- **Sprint Length:** 2 weeks (synchronized across all Webapper projects, per WAG)
- **Sprint Planning:** Weekly meetings, Tuesdays 2:30 PM CDT
- **Ticket Workflow:** New > Discovery > Confirmed > In Progress > Ready to Test > Testing Complete > Ready to Deploy > Ready for Production Testing > Done!
- **Deployment Process:** Code moves develop > qa > production. Each backend service (User API, Storage API, etc.) is deployed independently via SAM CLI scripts (bat for Windows, sh for Linux). Frontend deployed to S3 + CloudFront invalidation.
- **Who Deploys:** Developers deploy to QA. Production deploys require review. Daniela handles QA testing, then dev verifies on production, then Daniela/client performs UAT.
- **Release Strategy:** Feature-per-month cadence. Epics ship as complete units. No feature flags currently. Public roadmap at cloudseedrive.com shows Launched/In Progress/Planned.
- **Roadmap Prioritization:** Productboard with ICE scoring. Patrick pushes features from Productboard candidates into Jira Discovery sprint for estimation.

## Project Conventions

- **Ticket Format:** WAG-style Jira tickets with emoji section headers (see TEMPLATES/jira-ticket.md)
- **Branching:** develop -> qa -> production
- **Deployment:** SAM CLI deploy scripts (bat for Windows, sh for Linux) per service per environment
- **Sprint Cadence:** Weekly sprint planning meetings (Tuesdays 2:30 PM CDT)
- **Roadmap Management:** Productboard for prioritization (ICE scoring), public roadmap at cloudseedrive.com
- **AI Coding:** Actively using Claude Code for smaller features (Favorites was identified as a good candidate)
- **Testing:** Daniela handles manual QA, Henry handles Selenium/automated tests

## Key Links

- **Jira Project:** https://webapper.atlassian.net/browse/CSD
- **Confluence Space:** https://webapper.atlassian.net/wiki/spaces/CD1/overview
- **Confluence - General Info:** https://webapper.atlassian.net/wiki/spaces/CD1/pages/26542082/CloudSee+-+General+Information
- **Confluence - Infrastructure:** https://webapper.atlassian.net/wiki/spaces/CD1/pages/27000833/Infrastructure
- **Confluence - Deployment Spec:** https://webapper.atlassian.net/wiki/spaces/CD1/pages/26935312/Deployment+Specification
- **Google Drive (shared):** https://drive.google.com/drive/u/1/folders/0AGTN13Mq6DDWUk9PVA
- **Product Site:** https://www.cloudseedrive.com/
- **AWS Marketplace:** Available via AWS Marketplace (free trial + paid plans)
- **Application:** https://app.cloudsee.cloud/
- **Confluence - Sprint History:** https://webapper.atlassian.net/wiki/spaces/CD1/pages/171933697/Sprint+History
- **Confluence - User Credentials:** https://webapper.atlassian.net/wiki/spaces/CD1/pages/26443788/User+Credentials
- **Confluence - Production Credentials:** https://webapper.atlassian.net/wiki/spaces/CD1/pages/26443778/Production+Credentials
- **Confluence - Dev Credentials:** https://webapper.atlassian.net/wiki/spaces/CD1/pages/26804240/Development+Credentials
- **Confluence - Access Information:** https://webapper.atlassian.net/wiki/spaces/CD1/pages/26509319/CloudSee+Drive+Access+Information
- **Google Doc - Permissions Spec:** https://docs.google.com/document/d/1W6u5Zyg8cIp-0HhZZ1-Wy7sbqZMjR8ru2XtJFymaUFE
- **Frontend Repo:** Bitbucket `csd-frontend`
- **Backend Repo:** Bitbucket `csd-backend`
