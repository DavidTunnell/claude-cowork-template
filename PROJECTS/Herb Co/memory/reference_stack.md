# reference_stack

**Last Updated:** 2026-05-04

**Summary:** iBizFusion's full stack with versions, post-Q1-2026 upgrade. Use this to anchor questions like "what version of CF supports X" or "is feature Y available in MySQL 8."

## Application

- **ColdFusion 2023** (Adobe). Production. Adrian also runs CF 2025 on his dev machine for early-deprecation visibility.
- **Architecture:** classic CF — `.cfm` for UI, `.cfc` for APIs, `<cftag>`-heavy.
- **Files:** ~1,334 source files (per static analysis 2026-04-06).
- **God-file:** `commonFunctions.cfm` — permissions, inventory, order state machines, pricing, costing, commissions, audit trails, KPI calculations, integration utilities. Functions depend on APPLICATION/SESSION/Request scopes (non-portable, untestable in isolation).
- **Tests:** none.
- **CI/CD:** none. Direct production deployment.
- **Linter / formatter:** none configured.

## Database

- **MySQL 8** (post Q1 2026 upgrade from 5.6). Adrian completed application-level query compatibility changes; no schema changes required.
- **Tables:** 370+. No published ERD.
- **Migration tooling:** none. DDL changes are direct.

## OS / Hosting

- **Windows Server 2025** (post Q1 2026 upgrade from Win 2012 R2).
- **Host:** Hostek managed VPS, ~$269/month.
- **WAF:** SiteLock — used to route the production cutover during the upgrade.
- **Old server:** retained as rollback, not yet decommissioned (per most recent meeting prep).

## Integrations (point-to-point, undocumented end-to-end)

| System | Direction | Mechanism |
|--------|-----------|-----------|
| Salesforce CRM | Bidirectional | REST API |
| QuickBooks Online | One-way out | Manual journal entries |
| Google Drive | Reference | QA docs, CoAs, FSVP |
| Lab Portals | Inbound | Manual data entry |
| Carrier APIs | Outbound | UPS, FedEx, DHL, USPS |
| eCommerce (CF-based, ~99% complete) | Bidirectional | REST API + HMAC-signed callbacks |
| WooCommerce (alternative path, in progress) | Bidirectional | REST API + HMAC + nonce |
| NetNow Credit | One-way | Manual limit updates |
| Authorize.Net | Outbound | Tokenized payment flow (correctly implemented per static analysis) |

## Security stack (as found, not as desired)

- **CFQUERYPARAM coverage:** ~85% (static analysis flagged 221 unparameterized queries; 35 ORDER BY injection vectors)
- **Stored card data encryption:** CFMX_COMPAT (RC2 40-bit) — PCI DSS violation
- **CSRF coverage:** ~1.5% across the app (Adrian added on login forms in Q1)
- **Hardcoded credentials in source:** 30+ locations (DB, API, SMTP)
- **Stripe keys:** secret keys in a web-accessible test file (flagged for immediate cleanup)
- **Auth:** session-based, password hashing migrated to modern algorithm in Q1 2026
- **Login:** throttling added in Q1 2026

## What we don't know yet

- Backup strategy details (verified or not? how often? where stored?)
- Disaster recovery plan (none documented — Phase 1 finding)
- Monitoring / alerting — brief mention of email alerts and slow-query logs, no systematic monitoring
- Performance baselines (none documented)
