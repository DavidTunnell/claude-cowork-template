# ibizfusion.com Email Outage — Diagnosis & Quick-Fix Runbook

**Date:** 2026-06-09
**Author:** David Tunnell (diagnosis assisted by Claude)
**Severity:** P1 — inbound email to `@ibizfusion.com` failing
**Status:** Root cause confirmed. Fix is a DNS change at **Hostek** (not AWS).

---

## TL;DR

Someone added a **CNAME record at the zone apex** (`ibizfusion.com` root) pointing to the
web app's load balancer. A CNAME at a zone apex is illegal per DNS RFCs and causes the
nameserver to return that CNAME for **every** record type at the apex — including `MX` and
`TXT/SPF`. That masks the real mail records, so inbound mail can't find the mail server.

**The fix: delete the apex CNAME.** The MX, SPF, NS, and other apex records still exist in the
zone and will start resolving again the moment the CNAME is removed. This is a one-record
deletion — low risk for email, with one website side effect handled in Step 2.

---

## Confirmed facts (read-only diagnosis)

- DNS for `ibizfusion.com` is **still authoritative on Hostek** — `ns1.hostek.com` /
  `ns2.hostek.com` (confirmed at the `.com` registry). **DNS was never moved to Route 53.**
- Queried directly against `ns1.hostek.com`, the apex returns a CNAME for *every* type:

  ```
  ibizfusion.com.  300  IN  CNAME  ibizfusion-shared-1621946795.us-east-1.elb.amazonaws.com.
  ```
  (apex MX, TXT, A, and NS all return this same CNAME — the signature of an apex-CNAME conflict)

- **Why mail bounces:** sender looks up MX → gets the ELB (not a mail server) → falls back to
  the apex A → also the ELB (`107.20.55.129`, `18.213.242.194`) → web servers, no SMTP → mail fails.
- The mailbox is fine: `mail.ibizfusion.com` → `46.31.66.220` (Hostek/SmarterMail) resolves and is online.

### Answers to Adrian's questions
| Question | Answer |
|---|---|
| Were MX records migrated to a new zone? | No new zone — still Hostek. MX exists but is **shadowed by the apex CNAME**. |
| Intended mail routing? | `mail.ibizfusion.com` → `46.31.66.220` (Hostek/SmarterMail). Correct and online. |
| Do `mail` / `webmail` records exist? | `mail` **exists** (A→46.31.66.220). `webmail` **does not exist** — never created. |
| Were SPF/DKIM/DMARC migrated? | DKIM (3 SES CNAMEs), DMARC, and `mail-ses` SPF resolve fine. The **apex SPF is shadowed** by the apex CNAME and does not resolve. |

---

## The Fix (Hostek DNS panel)

### Step 1 — Delete the apex CNAME  ⬅ this restores email
In the Hostek DNS zone for `ibizfusion.com`, find the record(s) with:

- **Name:** blank / `@` / root
- **Type:** `CNAME`
- **Value:** `ibizfusion-shared-1621946795.us-east-1.elb.amazonaws.com`

**Delete every apex CNAME entry** (the export shows two duplicates). Do **not** touch the
`www`, `app`, or DKIM (`*._domainkey`) CNAMEs — those are subdomains and are correct.

> The moment this is removed, the apex `MX` (→ `mail.ibizfusion.com`) and apex `SPF TXT`
> stop being masked and resolve normally. Apex TTL is 300s, so propagation is ~5 minutes.

### Step 2 — Keep the bare-apex website working
Removing the apex CNAME means `http(s)://ibizfusion.com` (no `www`) no longer points at the ELB.
`www.ibizfusion.com` still works. Pick one, in order of preference:

1. **ALIAS / ANAME at apex (best, if Hostek offers it):** set an `ALIAS`/`ANAME` record at the
   root → `ibizfusion-shared-1621946795.us-east-1.elb.amazonaws.com`. Unlike a CNAME, an
   ALIAS resolves to A records for browsers **without** masking MX/TXT. Fixes the site *and*
   leaves email intact.
2. **Domain forwarding (clean):** use Hostek "domain forwarding / URL redirect" to 301
   `ibizfusion.com` → `https://www.ibizfusion.com`.
3. **Apex A records (stopgap):** add `A` records at the root → `107.20.55.129` and
   `18.213.242.194`. Works immediately but **fragile** — these are ELB IPs that can rotate.
   Acceptable only as a short-term bridge to the permanent fix below.

### Step 3 — Verify
After ~5 minutes:

```bash
dig +short MX  ibizfusion.com @8.8.8.8       # expect: 10 mail.ibizfusion.com.  (NOT the ELB)
dig +short TXT ibizfusion.com @8.8.8.8       # expect: v=spf1 mx a ip4:46.31.66.220 ...  (+ verification TXTs)
dig +short A   ibizfusion.com @8.8.8.8       # expect: ELB IPs (if ALIAS/A used) — must NOT be a CNAME
dig +short A   mail.ibizfusion.com @8.8.8.8  # expect: 46.31.66.220
```
Then send a test email to `adrian@ibizfusion.com` and confirm delivery in SmarterMail.

---

## Do NOT touch (these records are correct)
`mail` A → 46.31.66.220 · `www` / `app` CNAME → ELB · `*._domainkey` DKIM CNAMEs → SES ·
`_dmarc` TXT · `mail-ses` MX + TXT · apex `MX`, apex `SPF TXT`, apex verification TXTs
(google-site-verification, globalsign, sitelock), `ftp`, `server`, `www.npnutra`.

---

## Permanent fix (recommended follow-up) — validated against existing HERB zones
A read-only inspection of HERB Co (797601398324) confirms the right pattern is **already in
production** for the sibling domains `ibizfusion-app.com` and `ibizfusion-uat.com`: their apex is
an **`A` ALIAS → the `ibizfusion-shared` ALB**, coexisting cleanly with mail records. Replicate
that for `ibizfusion.com`:

1. Create a Route 53 public hosted zone `ibizfusion.com` in 797601398324.
2. **Apex `A` ALIAS → `dualstack.ibizfusion-shared-1621946795.us-east-1.elb.amazonaws.com`**
   (add a `*` wildcard A ALIAS too if you want to match the app zones). ALIAS coexists with
   MX/TXT — this is exactly what a raw CNAME cannot do.
3. **Keep mail on Hostek — do NOT copy the app domains' SES mail records.** Carry these over from
   the Hostek zone: `MX` → `mail.ibizfusion.com` (pri 10), `mail` A → `46.31.66.220`, apex SPF
   `v=spf1 mx a ip4:46.31.66.220 include:spf.ezhostingserver.com -all`, `_dmarc` TXT, the DKIM
   selector CNAMEs/TXT, and the `mail-ses` MX/TXT.
4. Carry over the rest: `www`, `app`, `ftp`, `server`, `www.npnutra`, and the verification TXTs.
5. Repoint the registrar's nameservers from `ns1/ns2.hostek.com` to the new Route 53 NS set.
6. Verify MX/SPF/site, then retire the Hostek zone.

> `ibizfusion.com` mail lives on **Hostek/SmarterMail** (46.31.66.220); the app domains use
> **Amazon SES**. Keep them distinct during the migration.

---

## Appendix — Read-only AWS access to HERB Co (797601398324)
The AWS MCP currently authenticates as `arn:aws:iam::592920047652:user/davids-claude` (the
Webapper/CloudSee account) and **cannot reach 797601398324** (AssumeRole denied; the
`ibizfusion-shared` ELB is not in 592920047652). To let Claude inspect the HERB account
read-only, do one of:

**Option A — cross-account role + auto-assuming profile (recommended)**
1. In **797601398324**, create role `ClaudeReadOnly`:
   - Trust: `arn:aws:iam::592920047652:user/davids-claude` (action `sts:AssumeRole`)
   - Permissions: AWS managed `ReadOnlyAccess` (or narrower `ViewOnlyAccess`)
2. In **592920047652**, allow `davids-claude` to `sts:AssumeRole` on
   `arn:aws:iam::797601398324:role/ClaudeReadOnly`.
3. On your machine, add to `~/.aws/config`:
   ```
   [profile herbco]
   role_arn = arn:aws:iam::797601398324:role/ClaudeReadOnly
   source_profile = default
   region = us-east-1
   ```
4. Tell Claude the profile name (`herbco`); it will run `aws --profile herbco ...` read-only.

**Option B — simplest:** add a read-only IAM user's credentials for 797601398324 as a named
profile `[herbco]` in `~/.aws/credentials`, and share the profile name.

> Note: this MCP blocks `aws configure`, but `--profile` on normal service commands is fine,
> provided the profile exists where the MCP server runs. If `--profile` isn't honored, the
> fallback is to point the MCP's default credentials at a read-only principal in 797601398324.

---

## Appendix B — HERB Co (797601398324) read-only inspection · 2026-06-09
Performed read-only (describe/list/get only) via the `claude-access` IAM user.

- **No `ibizfusion.com` hosted zone exists in Route 53** — the production domain is entirely on
  Hostek. Route 53 zones present in the account: `ibizfusion-app.com`, `ibizfusion-uat.com`,
  `mbsc-cloud.com`.
- **`ibizfusion-shared` is an internet-facing Application Load Balancer**; listeners are
  **HTTP:80 and HTTPS:443 only — no SMTP/25.** This is why pointing the apex at it kills mail:
  delivery to the apex IPs hits a web ALB with no mail listener and fails.
- `ibizfusion-app.com` and `ibizfusion-uat.com` already use **apex `A` ALIAS → the ALB** with
  **Amazon SES** mail records — the proven template for the permanent fix above (but note
  `ibizfusion.com` mail must remain on Hostek, not SES).
