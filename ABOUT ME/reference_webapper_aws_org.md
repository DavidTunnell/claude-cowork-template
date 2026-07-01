# reference_webapper_aws_org

**Last Updated:** 2026-05-20
**Verified via:** `aws organizations`, `aws sso-admin`, `aws ds` (profiles `ama-mgmt`, `ama-webapper-security-ro`)

**Summary:** Webapper's AWS Organization layout — which account owns what, who's the delegated admin for what, and where SSO actually lives. Recorded after a misconception came up that the Security account "houses SSO" (it doesn't).

## AWS Organization

| Field | Value |
|-------|-------|
| Org ID | `o-drasi0t4se` |
| Feature set | ALL |
| Management (master) account | **857110876011** — Webapper Services LLC |
| Master account email | pquinn@webapper.com |
| Enabled policy types | SERVICE_CONTROL_POLICY |

The management account naming convention is the tell: every other account uses an `aws+<alias>@webapper.com` forwarding alias, but the org master uses Patrick's actual mailbox.

## IAM Identity Center (SSO)

**Anchored in the management account 857110876011, NOT in Security.** SSO admin is not delegated.

| Field | Value |
|-------|-------|
| Instance ARN | `arn:aws:sso:::instance/ssoins-722322317848def1` |
| Owner account | **857110876011** (Webapper Services LLC) |
| Identity Store ID | `d-90677545a4` (Identity Center's built-in directory) |
| Status | ACTIVE |
| Created | 2020-04-07 |
| User portal | `webapper-aws-sso.awsapps.com` |
| Identity source | Identity Center built-in (NOT Active Directory) |
| Auto-managed SAML provider | `arn:aws:iam::857110876011:saml-provider/AWSSSO_d7e8c46486839835_DO_NOT_DELETE` |

If 469454037877 (Security) were lost, **SSO would keep working** — the instance, identity store, and SAML provider all live in the management account.

Verified by:
- `aws sso-admin list-instances` → OwnerAccountId 857110876011
- `aws organizations list-delegated-administrators --service-principal sso.amazonaws.com` → empty
- `aws ds describe-directories --profile ama-webapper-security-ro` → empty (no AD in Security to be the source)

## Delegated administrators

**Webapper Security (469454037877)** is the delegated admin for the org's security tooling — delegated since 2020-07-23:

| Service | Delegated since |
|---------|----------------|
| `guardduty.amazonaws.com` | 2020-07-23 |
| `access-analyzer.amazonaws.com` | 2020-07-23 |
| `macie.amazonaws.com` | 2020-07-24 |

Losing 469454037877 **would** break org-wide GuardDuty, IAM Access Analyzer, and Macie. So Patrick's "this account is really important" instinct is correct — he just attributed the importance to the wrong service (SSO). The right framing: Security is the **security tooling hub**, not the SSO hub.

Verified by `aws organizations list-delegated-services-for-account --account-id 469454037877`.

## Dormant Simple AD in management account (flag for cleanup discussion)

A Simple AD directory has been sitting in 857110876011 since 2018 with no detectable consumers:

| Field | Value |
|-------|-------|
| Directory ID | `d-90672ea7af` |
| DNS name | `ad.webapper.com` |
| Short name | `adwebapper` |
| Alias | `webapper` (sign-in URL: `webapper.awsapps.com`) |
| Type | SimpleAD (Small) |
| Account | 857110876011 |
| VPC | `vpc-10b98c75` (Webapper_Public_VPC, 10.0.0.0/16) |
| DNS IPs | 10.0.0.154, 10.0.2.119 |
| Launched | 2018-07-16 |
| SsoEnabled | false |

**This is NOT the SSO identity source** — Identity Center uses `d-90677545a4` (different ID), and `SsoEnabled` is false on this one.

What I checked for active consumers (all empty):
- WorkSpaces directories: none registered
- Workspaces using this directory: none
- FSx file systems in account: none
- AD trusts: none
- Tags: none
- Event topics (SNS): none
- Cross-account sharing: not possible (Simple AD doesn't support it)

What I did NOT check (would need separate investigation):
- EC2 instances domain-joined to it
- Whether anything on the network is still resolving `ad.webapper.com` or hitting 10.0.0.154 / 10.0.2.119

**Estimated cost:** Simple AD Small is ~$36/mo, so this has burned ~$3,400+ since 2018.

**Recommendation:** before any cleanup, ask Patrick what this directory was for and whether anything still consumes it. Do not delete without confirmation.

## Useful commands (from a profile that can hit the management account, e.g. `ama-mgmt`)

```
# SSO instance and ownership
aws sso-admin list-instances

# Org master / SSO delegation
aws organizations describe-organization
aws organizations list-delegated-administrators --service-principal sso.amazonaws.com

# Security account's delegated services
aws organizations list-delegated-services-for-account --account-id 469454037877

# Directory Service (the Simple AD)
aws ds describe-directories
aws ds describe-trusts --directory-id d-90672ea7af
```
