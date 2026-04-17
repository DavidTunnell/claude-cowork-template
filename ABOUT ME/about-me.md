# About Me

**Last Updated:** 2026-04-17

## Who I Am

- **Name:** David Tunnell
- **Role:** Lead Software Architect at [Webapper Services, LLC](https://www.webapper.com/)
- **Location:** Castroville, Texas just outside of San Antonio
- **Education:** University of Maryland University College - Bachelor of Computer Science - Cum Laude | The University of Texas at Austin (2010–2013) - Master of Business Administration - MBA 
- **Portfolio:** [GitHub](https://github.com/DavidTunnell) | [Stack Overflow](https://stackoverflow.com/users/1524210/david-tunnell) | [Linkedin](https://www.linkedin.com/in/david-tunnell/)

## What I Do Day-to-Day

I'm a software architect at Webapper Services, a web development and consulting company. My work spans both technical leadership and business strategy:

- **Architecture & Design:** I design system architecture for client projects and internal products. I make technology decisions, define patterns, and ensure quality across the codebase.
- **Agile Coaching:** I run Scrum ceremonies, coach teams on agile practices, and maintain project health. I hold a Certified Tactical Agilist certification and SAFe SPC experience.
- **Requirements & Planning:** I write and manage requirements in Jira and Productboard, translating business needs into actionable technical work.
- **AI Transformation:** I'm leading Webapper's transformation into an AI-native company — this is my top strategic initiative. We're going Anthropic-first with Claude Desktop, Claude Code, Claude Cowork, and Anthropic platform API calls. The vision is a full adoption roadmap: get the whole team on the tools, train everyone on effective LLM prompting and workflows, build and deploy agents that automate repetitive tasks, and stand up a central agent dashboard where every deployed agent is visible, monitored, and managed. The goal isn't to replace developers — it's to empower each person on the team to ship more, faster, and tackle work that would have been out of reach before. Long-term, the default for any new feature, project, or process should be "how can AI help us build this faster and better?" I wrote and delivered an internal AI manifesto to the team laying out this vision and the concrete steps to get there.
- **Path to CTO:** I'm actively working toward a CTO role, which means I'm balancing hands-on technical work with strategic leadership, team development, and company-wide technology decisions.

## Primary Tech Stack

- **Frontend:** React, TypeScript, JavaScript
- **Backend:** Node.js, TypeScript
- **Cloud:** AWS (S3, Lambda, MySQL or DynamoDB in RDS and broader serverless patterns)
- **Tools:** Jira, Productboard, GitHub, VS Code, Docker, Claude Code, Claude Desktop, Windows, Google Workspace, Atlassian Suite
- **Methodologies:** Scrum, Agile Coaching

## Team Structure

**US Team:**
- **Patrick Quinn** — CEO (pquinn@webapper.com)
- **Scott Herring** — COO / Marketing / Sales / SEO (scott@webapper.com)
- **Joy Miller** — Technical PM, Scrum Master, Manual Tester, SME of VisionAST (joy@webapper.net)
- **Daniela Camargo** — PM, Scrum Master, Manual Tester, SME of CloudSee (daniela@webapper.net)
- **David Tunnell (me)** — Lead Software Architect (david@webapper.com)

**Vietnam Team (VTeam):**
- **Steven** — Tech Lead, all projects
- **Ann** — Sr. Dev, VisionAST
- **Peter** — Sr. Dev, CloudSee Drive (also helps with VisionAST onboardings)
- **Kevin** — Sr. Dev, transitioning from EDS contract into AI initiative
- **Henry** — SDET, Documentation, Selenium, Unit Tests, DevOps
- **Max** — Jr/Mid Dev, CloudSee Drive

## Our Product — CloudSee Drive

Webapper's flagship SaaS product. Browser-based interface for Amazon S3 storage, available on the AWS Marketplace. React frontend, Node.js serverless backend (Lambda/DynamoDB/OpenSearch). The strategic goal is for CloudSee Drive to replace client services revenue and become Webapper's primary business. See `PROJECTS/CloudSee Drive/project-context.md` for full architecture, team, active work, and deployment details.

## Active POCs

### Cognito → IAM Permission Inheritance (for CloudSee Drive)

- **Repo:** https://github.com/DavidTunnell/poc-cognito-mapping (public)
- **Running demo:** http://ec2-54-158-15-145.compute-1.amazonaws.com (HTTP only, disposable)
- **AWS account / region:** 592920047652 / us-east-1
- **Status:** Deployed end-to-end. Both Browse and Search served from CSD's real UAT OpenSearch index (`aws_account_592920047652_uat_active`, 58M docs), filtered by IAM-derived scope. S3 is only touched for presigned GET/PUT URLs.

**What it proves.** CSD today authenticates users via Cognito but enforces S3 access through its own DynamoDB-backed permission system. Admins configure permissions twice (once in AWS IAM for engineers, once in CSD for end users) and the two drift. This POC validates a simpler model: federate each Cognito user to a per-group IAM role via a Cognito Identity Pool, and let S3 enforce natively. For OpenSearch queries (which don't understand IAM), the server resolves each user's effective scope at login by probing `ListObjectsV2` with their assumed creds, then injects the scope as a `bool.filter` on every query. IAM remains the source of truth end-to-end.

**Admin toggle.** A runtime switch lets the admin flip between `iam` mode (as above) and `custom` mode (the CSD-today comparison: app-layer permission table in a JSON file). Same user, same UI, different data set, based on which scheme is active.

#### Demo users

All four users share the same password: **`Poc-Demo-123`** (set permanently via `admin-set-user-password` so there's no first-login challenge).

| Username | Email | Cognito group | IAM role | Source of truth |
|---|---|---|---|---|
| `alice` | alice@poc.local | `readonly-all` | `poc-csd-readonly-all` | IAM: read on all 5 demo buckets |
| `bob` | bob@poc.local | `rw-bucket-a` | `poc-csd-rw-bucket-a` | IAM: full read+write on `cloudsee-demo` only |
| `carol` | carol@poc.local | `readonly-prefix-x` | `poc-csd-readonly-prefix-x` | IAM: read on `cloudsee-demo-1` scoped to `Dogs1/*` only |
| `admin` | admin@poc.local | `admin` | (none — fallback role denies S3) | Admin page only — toggle scheme, edit custom-mode JSON |

The `readonly-prefix-x` role uses an `s3:prefix` condition on `s3:ListBucket`: carol can only list under `Dogs1/`. This is the folder-level-permissions pattern CSD is planning for production (CSD-537).

#### Expected behavior in IAM mode

Five real buckets live in 592920047652 and are indexed by Fast Buckets in UAT:

| Bucket | Docs indexed | Structure |
|---|---|---|
| `cloudsee-demo` | 819 | Folders at root: Birds/, Dogs/, Exotic Cars/, Extract/, File Types/, SMH/ + surf-001..004.jpg |
| `cloudsee-demo-1` | 77 | Dogs1/, Puppies/ subfolders (Parent values are inconsistent — some with leading slash) |
| `cloudsee-demo-2` | 19 | Small; Ferrari/ folder |
| `s3-file-1000k` | 1,000,002 | Large test dataset |
| `henry-drive-test-1000k` | 3,257,274 | Large test dataset |

Browse behavior (folder-level listing):

| Path | alice (readonly-all) | bob (rw-bucket-a) | carol (readonly-prefix-x) |
|---|---|---|---|
| Bucket list | All 5 | `cloudsee-demo` only | `cloudsee-demo-1` only |
| `cloudsee-demo/` root | 9 folders + 4 files | same as alice | empty (not in her scope) |
| `cloudsee-demo-1/` root | folders incl. Dogs1/ and Puppies/ + root files | empty | Dogs1/ subfolder only |
| `cloudsee-demo-1/Dogs1/` | 21 files + Puppies/ | empty | 21 files + Puppies/ |
| `cloudsee-demo-1/Puppies/` | 16 files | empty | empty (scope denies) |

Search behavior (free-text over the real index):

| Query | alice | bob | carol |
|---|---|---|---|
| `surf` | 8 hits | 8 (cloudsee-demo only) | **0** — not under Dogs1/ |
| `beagle` | 13 hits | 4 (cloudsee-demo only) | **3** — Dogs1/* only |
| `ferrari` | 22 hits | 9 (cloudsee-demo Exotic Cars) | **0** — Ferrari docs exist but not under Dogs1/ |
| `puppies` | 63 hits | 26 | **9** — Dogs1/Puppies/* only |

Upload behavior:
- alice: 403 on all buckets (read-only role)
- bob: succeeds on `cloudsee-demo`, 403 on the others
- carol: 403 on every bucket (read-only role); even in `Dogs1/` her role has no PutObject

**The proof test:** set `SEARCH_BYPASS_SCOPE=1` in the server env and restart. Carol's search for `ferrari` now returns 22 results — the same ones alice sees. That demonstrates the scope filter is the only thing keeping her from the rest of the index; turn it off and OpenSearch gladly returns everything the instance role has mapped read access to.

#### UAT-touching infrastructure (tracked for teardown)

The POC borrows the existing UAT infrastructure rather than provisioning dedicated resources:

- **EC2 lives in the CloudSeeDrive-UAT VPC public subnet** so it can reach the OpenSearch VPC endpoint (`vpc-cloudseedrive-uat-bmfr4znnqgl5fq2gf2qjn6xsbu.us-east-1.es.amazonaws.com`). Net-new SG + EC2 — teardown removes both.
- **One statement added to the UAT OpenSearch access policy** (`Sid: PocCsdOpenSearchAccess`, scoped to the POC instance role ARN).
- **One OpenSearch role + backend-role mapping**: `poc_csd_rw` with read on `aws_account_592920047652_*_active` and RW on `poc-csd-*` (leftover from v1). Mapped to the POC instance role ARN.
- **Temporary IAM access key** on `webapper-cloudsee-opensearch` (IAM user), used by the bootstrap/teardown scripts. Created from the `davids-claude4` admin account; needs manual delete after teardown.

Run `bash scripts/teardown-opensearch.sh` first to revert those, then `npm run teardown` to drop the POC stacks, then delete the temp IAM key via the AWS console.

## Client Project — VisionAST

Webapper's primary client project. Data analytics platform for automotive and powersports dealerships (SalesVision, PowerVision, FinanceVision, ServiceVision, MenuVision). Legacy CFML/Lucee on Aurora MySQL with a long-term modernization plan. Integrates with 12+ DMS systems. See `PROJECTS/VisionAST/project-context.md` for full architecture, team, active work, and deployment details.

## AWS Hosting Customers

Webapper also provides AWS hosting services for several clients:

- **[Atlantic British / RoverParts](https://www.roverparts.com/)** — Specialized retailer for Land Rover and Range Rover parts and accessories, with 11,000+ parts across two US warehouses.
- **[Icon Media](https://www.icon-media.com/)** — Full-service advertising agency in Anaheim Hills, CA specializing in the automotive aftermarket industry, including their iConfigurators visual software.
- **[eRep](https://erep.com/)** — Psychometric assessment platform offering the Core Values Index (CVI) for hiring and employee engagement.

## Other Client Work

- **[Educational Data Services (EDS)](https://www.ed-data.com/)** — Cooperative procurement platform for schools and public entities. Webapper was modernizing their systems (Azure-based). Relationship ending after ~2.5 years; Kevin is transitioning off this contract.

## Current Priorities

1. Leading AI transformation at Webapper Services
2. Architecting and shipping client projects
3. Building AI first systems and processes that scale as I move into a CTO role. Replacing company processes with standardized agents across the company.
4. Staying sharp on full-stack development using Claude Code while expanding leadership scope

## What I Care About

- Clean, maintainable architecture over clever hacks
- Teams that ship reliably through good process
- Practical AI adoption — tools that actually save time, not hype
- Clear communication between technical and non-technical stakeholders