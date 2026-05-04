# reference_stack

**Last Updated:** 2026-05-04

**Summary:** CSD's full stack. Use this anchor for "what library does CSD use for X" or "what AWS service runs Y."

## Frontend

- **Framework:** React (functional components, hooks, ES6+)
- **Build:** standard React tooling (verify exact bundler against `csd-frontend` package.json — most recent state has been webpack-based)
- **Hosting:** S3 + CloudFront per environment
- **Repo:** Bitbucket `csd-frontend`
- **Branch flow:** `develop -> qa -> production`

## Backend (microservices)

| Service | Responsibility |
|---------|---------------|
| User API | User management, auth, permissions |
| Storage API | S3 operations, file management |
| Metric Service | Dashboard and business metrics |
| Fast Bucket Service | OpenSearch indexing engine |
| Task Manager | Background job orchestration |
| Zipper Task | Multi-file download/zip operations |

- **Runtime:** Node.js Lambda
- **Routing:** AWS API Gateway in front of Lambda
- **IaC:** AWS SAM CLI / CloudFormation
- **Repo:** Bitbucket `csd-backend`
- **Architecture:** SQS-based fan-out for Fast Bucket indexing; pre-processing Lambdas before OpenSearch ingestion

## Shared SDK

- **Package:** `@webapper/cloudsee-drive-sdk` on NPM
- **Concerns:** DB connections, CORS, auth — anything cross-service
- **Versioning:** SemVer. Breaking changes require coordinated consumer updates.

## Data layer

- **Primary DB:** DynamoDB
- **Naming convention:** environment-prefixed table names (`production_User`, `qa_User`, etc.)
- **Aurora MySQL** — used for relational use cases per general Webapper architecture rule, but DynamoDB is the default for CSD itself.

## Search

- **OpenSearch** powers Fast Buckets — sub-second search across millions of S3 objects
- **Voice / NL search** (CSD-467, shipped Feb 2026) — Whisper / OpenAI for speech-to-text → LLM converts to OpenSearch query → metadata-only search

## Auth

- **Pattern:** SSO-ready
- **Provider:** Microsoft Entra ID for group management
- **User types:** Regular User, Admin, Owner Admin (one per account)
  - Owner Admin — only user who can add other Admins or change company info
  - Admin — adds users, selects which AWS accounts and Collections (buckets) they can access

## CI/CD

- **Pipeline:** Jenkins (recently optimized with EFS mount)
- **Tests:** Selenium (Henry's domain) + unit tests per package
- **Owner:** Henry Tran

## AWS infrastructure

- **Region:** us-east-1
- **Services in use:** S3, Lambda, API Gateway, CloudFront, DynamoDB, OpenSearch, SES (email), Systems Manager Parameter Store, CloudFormation, SQS
- **Parameter Store path:** `/cloudseedrive/appsetting/{environment}`
- **Deploy mechanism:** SAM CLI deploy scripts (bat for Windows, sh for Linux), per service per environment

## Multipart / large file upload

- **Library:** Uppy + AwsS3Multipart in React (frontend)
- **Backend:** matching multipart logic per Storage API
- **Capability:** files up to 5 TB, resumable uploads, parallel parts
- **History:** shipped late 2025 (CSD-235)

## Observability

- Production logs flow through CloudWatch
- No Sentry or Datadog currently — flagged as tech debt (CSD-202)
- Monitoring / alerting / centralized logging are in the tech-debt epic backlog

## What we don't have

- **Type checking:** TypeScript adoption in progress; not all services typed yet. Confirm per package.
- **Test coverage:** Selenium-heavy, growing unit test footprint. CSD-476 is the active testing buildout epic.
- **Feature flags:** none currently. Releases are big-bang within feature scope.
- **Centralized logging / tracing:** part of tech-debt epic, not yet implemented.
