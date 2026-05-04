# reference_aws_account

**Last Updated:** 2026-05-04

**Summary:** The Herbco UAT AWS account, access pattern, and conventions. Use this anchor any time AWS work is involved on this project.

## Account

- **Account ID:** 797601398324
- **Account name:** Monterey Bay Herb Co / NP Nutra
- **Ownership:** Customer-owned (Herbco). Webapper has admin access via IdC, not as account root.
- **Region:** us-east-1
- **Access portal:** https://webapper-aws-sso.awsapps.com/start

## Access pattern (chained AssumeRole via IdC)

All access goes through Webapper IdC SSO; no Herbco-specific username/password.

- **Console:** AWS access portal → Applications tab → "Monterey Bay Herb Co / NP Nutra". One click. Session lasts 8 hours.
- **CLI:** chained AssumeRole via the `ama-*` profile pattern, sourced from `ama-mgmt`.

```
[profile ama-herbco-uat-ro]
source_profile = ama-mgmt
role_arn = arn:aws:iam::797601398324:role/anthropic-managed-agents-read-only
external_id = <from ~/dev/anthropic-managed-agents/external-id.secret>
region = us-east-1
output = json

[profile ama-herbco-uat-admin]
source_profile = ama-mgmt
role_arn = arn:aws:iam::797601398324:role/anthropic-managed-agents-admin
external_id = <from ~/dev/anthropic-managed-agents/external-id.secret>
region = us-east-1
output = json
```

- **Daily login:** `aws sso login --profile ama-mgmt`
- **Default for "looking around":** `--profile ama-herbco-uat-ro`
- **For deploys / IaC / Secrets writes:** `--profile ama-herbco-uat-admin`

## Provisioned team members (IdC)

`dtunnell`, `jmiller`, `pquinn`, `htran`, `snguyen`, `dcamargo`

## Logging

- **CloudTrail:** `herbco-cloudtrail-797601398324` — every action logged.
- **Root account:** locked. MFA via TOTP in 1Password. Never used day-to-day.

## Tooling repo

- **Anthropic Managed Agents repo:** https://bitbucket.org/cloudsee-drive/anthropic-managed-agents
- **External ID file:** `~/dev/anthropic-managed-agents/external-id.secret` (gitignored, never commit).
- **Profile generator:** `generate-aws-config.ps1 -Apply` from the repo root.

## Common gotchas

- "Unable to locate credentials" → `aws sso login --profile ama-mgmt`
- "SSO session expired" → same as above (8-hour sessions)
- "AccessDenied on AssumeRole" → check ExternalId in `~/.aws/config` against `external-id.secret`
- "Could not connect to endpoint" → add `--region us-east-1` or set in profile
