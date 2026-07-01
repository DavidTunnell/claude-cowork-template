# project_overview

**Last Updated:** 2026-07-01

**Summary:** Atlantic British (RoverParts.com) is a Land Rover / Range Rover parts e-commerce client. Webapper manages their AWS infrastructure and supports a ColdFusion e-commerce platform — hosting, monitoring, performance tuning, and incident response. Long-running, steady-state managed-hosting engagement. Jira key `AB`.

## What's true today

- Steady-state managed hosting/maintenance. Recent work: RDS MySQL tuning, CloudFront/S3 403 fix affecting Amazon Seller Central listings.
- Stack: AWS (Terraform, cross-account IAM), Route 53, CloudFront + S3 (`images.roverparts.com`), auto-scaling EC2, RDS MySQL, Adobe ColdFusion.
- Hosting governance (foundations audit, Secrets Manager credential migration) tracked in the `WEBA` project.

## Who's involved

- **Webapper:** Henry Tran, Joy Miller, Steven Nguyen, Peter Truong; David Tunnell (oversight)
- **Client:** Atlantic British / RoverParts e-commerce team (by role)

## Keep top of mind

Steady-state hosting client — value is reliability, fast incident response, and clean recurring BHS reviews. Operational-only in this template; sensitive infra identifiers live in access-controlled Jira/Confluence.
