# project_overview

**Last Updated:** 2026-05-04

**Summary:** CloudSee Drive is Webapper's flagship SaaS — a browser-based interface for Amazon S3 storage that replaces the AWS Console for non-technical users. Sub-second search across millions of objects (Fast Buckets / OpenSearch), file versioning, RBAC, secure sharing, activity monitoring, files up to 5 TB, AWS Marketplace listing with same-day onboarding. Strategic role: replace client services revenue and become Webapper's primary business.

## Why this matters

CSD is the strategic bet. Client services (the WAG project work) funds the company today; CSD is what we want to be when we grow up. Every roadmap call ultimately ladders up to "does this move the SaaS toward replacing the consultancy." Patrick is the product owner; Daniela is PM/QA; Steven is tech lead across all of Webapper but VTeam-focused on CSD; Peter and Max are the day-to-day devs.

## What's true today (Q2 2026)

- **Free Trial v2** shipped (March 2026): re-enabled with throttling — 1M indexed objects, 100K delta sync ops, 30-day trial via AWS Marketplace.
- **Natural-Language & Voice Search** shipped February 2026: LLM-powered NL → OpenSearch query, voice via Whisper. Metadata-only.
- **Current sprint:** Favorites (CSD-508) — mark/unmark S3 objects as favorites, left-panel display. In progress (Peter + Max).
- **Next up (Discovery):** Tag Explorer (CSD-513) — browse/filter S3 objects by AWS tags. Estimated by Steven, blocking on capacity.
- **Cadence target:** one feature per month to production.

## Architecture in one paragraph

React frontend on S3 + CloudFront (per-environment distribution), Node.js Lambdas behind API Gateway organized as microservices (User API, Storage API, Metric Service, Fast Bucket Service, Task Manager, Zipper Task), DynamoDB for primary data with environment-prefixed tables, OpenSearch for the Fast Buckets indexing engine, SAM CLI / CloudFormation for IaC, Jenkins for CI/CD with EFS optimization. SDK (`@webapper/cloudsee-drive-sdk`) handles cross-service concerns like DB connections, CORS, auth.

## Who's involved

- **David Tunnell** — Lead Software Architect, AI integration, sprint management
- **Steven Nguyen** — VTeam tech lead, primary backend, Free Trial implementation
- **Peter Truong** — Senior Dev, primary frontend
- **Max Nguyen** — Junior/Mid Dev, backend
- **Henry Tran** — SDET, Selenium + unit tests + Jenkins pipeline
- **Daniela Camargo** — PM / Scrum Master / Manual Tester / SME — domain expert for CSD
- **Patrick Quinn** — CEO, product direction, roadmap
- **Scott Herring** — COO, marketing/SEO/marketplace publishing

## What to keep top of mind

This is a **public SaaS product**, not internal tooling. The marketplace listing and the public roadmap (`cloudseedrive.com`) are sales surfaces. Anything that affects the customer-visible feature set or pricing has marketplace implications. Daniela owns sign-off; check before promising customer-facing changes.
