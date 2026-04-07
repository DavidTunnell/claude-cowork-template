## Status Report: Webapper Projects — Week of 3/23/2026
**Author:** David Tunnell | **Date:** March 27, 2026

---

### Executive Summary

Strong week across both projects. CloudSee Drive shipped the Favorites feature to production and has already moved into the Tag Explorer epic, with backend APIs and frontend modal UI complete and three tasks now in QA. VisionAST completed 7 dealer onboardings and continued DX1 integration development, though two AWS alarms (TaskRunner healthy hosts and RDS high CPU) fired on Thursday and warrant monitoring. AI-assisted velocity continues to accelerate delivery but is complicating sprint estimation — the team is actively working on baking AI gains into future estimates.

### Overall Status

| Project | Status |
|---------|--------|
| CloudSee Drive | 🟢 On Track |
| VisionAST | 🟡 At Risk (infra alerts) |

---

## CloudSee Drive

### Accomplishments This Week

- **Favorites (CSD-508) shipped to production.** Users can now mark/unmark up to 10 favorite S3 objects and access them from the left panel sidebar. Backend, frontend, and database work all complete and deployed.
- **Tag Explorer backend and frontend making fast progress.** Backend APIs for Get Tags + Search by Tags (CSD-514) done. Frontend Tag Explorer Modal UI with tree view, search, and tag selection (CSD-515) done. Three frontend tasks now in QA.
- **Automated testing for AI Search complete (CSD-476).** Henry finished the Selenium/unit test buildout for AI-powered natural language search and moved it to Ready to Test. Assigned to David for review.
- **Spec documents updated.** Favorites spec (CSD-519) and general spec updates (CSD-429) both completed.
- **Reorder Action menu (CSD-520) in production testing.** Minor UX improvement moving through final validation with Daniela.

### In Progress

| Ticket | Item | Owner | Status | Notes |
|--------|------|-------|--------|-------|
| CSD-513 | AWS Tag Explorer (Epic) | Steven | Discovery | 60 hours estimated; scoped to AWS tags only, bidirectional |
| CSD-516 | Tag Search Execution + Untagged Search | Peter → Scott (QA) | Ready to Test | Peter mentioned Tag Explorer frontend is feature-complete |
| CSD-517 | Main Search Window UI (Tag Explorer Launch) | Peter → Scott (QA) | Ready to Test | |
| CSD-521 | UI Changes: Drive Header, Drive Count, Tag Styling | Peter → Scott (QA) | Ready to Test | Clarification thread active with Scott |
| CSD-518 | QA - Tag Explorer Automated Tests | Henry | Confirmed | Next up after current QA cycle |
| CSD-500 | Automated Regression Tests | Henry | Confirmed | |
| CSD-520 | Reorder Action menu | Daniela | Ready for Prod Testing | Max completed dev; Daniela validating |

### Key Decisions from Sprint Meeting (3/24)

- Tag Explorer scoped to **AWS tags only** — team agreed not to include other metadata fields to avoid user confusion.
- Roadmap through end of June: **Tag Explorer → Home Directory → Advanced Search.**
- Tag Explorer + Home Directory expected to fill through end of April.
- Patrick to prepare Advanced Search for discovery sprint and gather missing mockups by next Tuesday.
- Team acknowledged AI velocity gains are complicating sprint estimation; goal is to eventually bake AI velocity into estimates.

---

## VisionAST

### Accomplishments This Week

- **7 dealer onboardings completed.** Kyle handled Stivers Auto (CDK), Big Rock Powersports (Lightspeed), Southern Honda (DealerTrack/Motive), Genesis of Norman (DealerTrack/Motive), Mathews East (CDK), and Cold Springs RV service data fix. Ann onboarded 5 Boniface Auto locations (R&R) to SalesVision/APP.
- **BHS updates complete (VAST-3650).** Henry/Joy finished Linux server and RDS minor version updates with post-upgrade validation confirming stability.
- **PV Service error on Shawnee resolved (VAST-3655).** Kyle fixed the Home Tab service error.
- **R&R RO date data change (VAST-3613) ready to deploy.** Ann's work is testing complete and waiting for deployment.

### In Progress

| Ticket | Item | Owner | Status | Notes |
|--------|------|-------|--------|-------|
| VAST-3408 | DX1 Integration (Epic) | Ann | In Progress | Core integration for PowerVision/FinanceVision |
| VAST-3653 | Onboard DX1 Pilot Locations | Ann | In Progress | Pilot locations identified |
| VAST-3628 | DX1 Parser - MajorUnitDeal/Summary | Ann | In Progress | |
| VAST-3629 | DX1 Parser - MajorUnitDeal/GetDealDetail | Ann | In Progress | |
| VAST-3626 | DX1 Importer - MajorUnitDeal/Summary | Ann | Ready to Test | |
| VAST-3627 | DX1 Importer - GetSaleDealDetail | Ann | Ready to Test | |
| VAST-3631 | DX1 Deal Deletion Service | Ann | Confirmed | |
| VAST-3608 | Review System Error Logs | Ann | In Progress | |
| VAST-3450 | Ranking Report Automated Testing | Kyle | In Progress | |
| VAST-3663 | Finance/Lease Deals for Boniface CDJR | Ann | Confirmed | New this week |
| VAST-3652 | Big Rock Powersports Changes | Kyle | Ready for Prod Testing | |
| VAST-3660 | 90 Day APP Files Being Sent Nightly | Kyle | Ready for Prod Testing | |

### Risks and Issues

| Risk/Issue | Impact | Mitigation | Owner |
|------------|--------|------------|-------|
| **VisionAST-TaskRunner-LowHealthyHosts ALARM** (3/26) | Task runner may have reduced capacity; could delay data imports | Monitor; verify auto-scaling recovered | Steven / Ann |
| **VisionAST-RDS Writer High CPU ALARM** (3/26) | Database performance degradation; could impact reporting for large dealer groups | Check for runaway queries; relates to ongoing need to downsize from 8xl after optimization (VAST-3441) | Steven / David |
| **Build failures in prod automation tests** (VAST-3659) | Automated regression suite unreliable | Marked INVALID for two of three; one To Do remains unassigned | Henry |
| **Ann is single-threaded on DX1** | All DX1 importer/parser work depends on one developer | No immediate mitigation; Peter available for onboarding support if needed | Joy / David |
| **Motive MIX v2 migration deadline (end of 2026)** | Must migrate all v1 APIs to v2; not yet started | Currently confirmed/queued for Ann after DX1 | Joy |

### Decisions Needed

| Decision | Context | Deadline | Recommended Action |
|----------|---------|----------|--------------------|
| Investigate VisionAST AWS alarms | Two alarms fired Thursday — TaskRunner healthy hosts and RDS high CPU | Monday 3/30 | Steven/Ann review CloudWatch metrics and confirm systems recovered; escalate if recurring |
| Assign VAST-3659 build failure | Unassigned To Do bug for prod automation test failure | Next sprint planning | Assign to Henry for triage |

---

## Next Week Priorities

**CloudSee Drive:**
1. Complete QA cycle on Tag Explorer frontend tasks (CSD-516, CSD-517, CSD-521)
2. Begin Tag Explorer automated test coverage (CSD-518)
3. Deploy Reorder Action menu to production (CSD-520)
4. David to review Henry's AI Search automated testing (CSD-476)

**VisionAST:**
1. Continue DX1 parser development (Ann)
2. Test and deploy DX1 importers (VAST-3626, VAST-3627)
3. Move onboarding tickets through production testing (Kyle)
4. Monitor AWS alarms — confirm TaskRunner and RDS recovered
5. Deploy R&R RO date data change (VAST-3613)

---

*Sources: Jira (CSD and VAST projects), Google Drive meeting notes, Gmail notifications including AWS CloudWatch alerts.*
