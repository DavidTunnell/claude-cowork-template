# reference_aws_account

**Last Updated:** 2026-05-04

**Summary:** CSD's AWS account, profile, and the per-environment resources you need to know.

## Account & access

- **Profile:** `ama-cloudsee` (chained AssumeRole via `ama-mgmt`, IdC SSO)
- **Region:** us-east-1
- **Daily login:** `aws sso login --profile ama-mgmt`
- **Default for browsing:** `--profile ama-cloudsee` (admin-level — be careful)

If a read-only variant is needed (e.g., for production debugging), confirm with Patrick / Steven before adding it. Same `ama-*` chained pattern as Herb Co's UAT setup.

## CloudFront distributions

| Environment | Distribution ID | URL |
|-------------|----------------|-----|
| Production | `E3MYVWORHVNEPX` | https://drive.cloudsee.cloud |
| QA | `E21TCNFRHC2JD7` | https://drive-qa.cloudsee.cloud |
| Dev | `E18Q5AYZVNBZS7` | https://drive-test.cloudsee.cloud |

After every frontend deploy, invalidate the matching distribution.

## S3 buckets

| Use | Production | QA | Dev |
|-----|-----------|-----|-----|
| Frontend hosting | `cloudsee-drive-frontend-mp-saas` | `cloudsee-drive-frontend-qa` | `cloudsee-drive-frontend-test` |
| API artifacts | `cloudsee-drive-api-stack-production` | `cloudsee-drive-api-stack-qa` | `cloudsee-drive-api-stack-test` |

## DynamoDB

- **Naming:** environment-prefixed. Production tables start with `production_`, QA tables with `qa_`. Dev shares `qa_` prefix.
- **Schema changes:** approval-required. New attributes or GSIs need explicit sign-off.

## Parameter Store

- **Path:** `/cloudseedrive/appsetting/{environment}`
- Use SSM Parameter Store for app config. Don't hardcode environment-specific values.

## SQS + OpenSearch (Fast Buckets)

- SQS-based fan-out for indexing pipeline
- OpenSearch cluster powers the Fast Buckets index
- Lambda pre-processors before ingestion

## SES (email)

- Used for application notifications, support emails
- Same account; check sender address conventions before adding new flows

## Common gotchas

- **"Unable to locate credentials"** → `aws sso login --profile ama-mgmt`
- **Wrong environment after deploy** → check the script's hardcoded environment + the table prefix it's wired to
- **CloudFront not serving new content** → forgot to invalidate after S3 sync
- **DynamoDB ResourceNotFoundException** → table name missing the env prefix
