# Exporting All VisionAST (VAST) Jira Tickets — Runbook

**Goal:** Export every historical ticket in the VAST project (~3,742 as of 2026-06-16) into one full-detail CSV file, then zip it for sharing/archiving.

**Why CSV (all fields):** It's the only native Jira Cloud export that includes the full description **and every comment** (each comment lands in its own `Comment` column, in chronological order). That's the "full detail" you asked for.

**Volume check:** Jira Cloud raised the per-export cap from 1,000 to **10,000 work items** (March 2025). Your ~3,742 tickets fit in **a single export** — no batching required.

---

## Part A — Pull ALL historical tickets (not just the board's view)

> ⚠️ Don't export straight from Board 14. Boards apply a filter (often hiding old/Done items or certain types), so a board export can silently miss tickets. Use a clean project-wide query instead.

1. Go to **webapper.atlassian.net** and open the **VisionAST** project.
2. In the left sidebar, select **List** (the project's list view).
3. Replace/clear the filter so it returns everything. The simplest way: switch to JQL and enter:
   ```
   project = VAST ORDER BY created ASC
   ```
   (You should see a total of ~3,742 work items.)
   - *Alternative entry point:* top nav **Filters → Search work items**, paste the same JQL, run it.

## Part B — Run the export

4. Click the **More actions** menu — the **••• (three dots)** at the top-right of the list.
5. Choose **Export**.
6. Select **Export CSV (all fields)**.
   - If the menu also shows **Export Excel CSV (all fields)**, either works — both produce a `.csv`. Pick "all fields" so descriptions + comments are included.
7. For a set this size the export runs **asynchronously**: Jira shows progress and gives you a download link when it's ready (it may take a few minutes — large, all-fields exports are slow by design). Download the `.csv` (it lands in your **Downloads** folder).

## Part C — Zip it (Windows)

8. Open **File Explorer → Downloads**, find the exported `.csv`.
9. Zip it:
   - **Windows 11:** right-click the file → **Compress to ZIP file**.
   - **Windows 10:** right-click → **Send to → Compressed (zipped) folder**.
   - (Win11 alt: right-click → **Show more options → Send to → Compressed (zipped) folder**.)
10. Rename the zip to something like `VAST-all-tickets-2026-06-16.zip` and share/archive.

---

## Notes & gotchas

- **Opening the CSV cleanly:** Jira exports **UTF-8**. Older Excel can garble special characters. If you see odd characters, open via Excel's **Data → From Text/CSV** and choose **65001: Unicode (UTF-8)**, or open in Google Sheets.
- **Attachments are NOT included.** The CSV captures attachment **URLs**, not the files themselves. Jira Cloud can't bulk-download physical attachments natively — needs a Marketplace app if you require the actual files.
- **Most-complete alternative — XML:** If you want the absolute fullest fidelity in one file, the **••• → Export → Export XML** option dumps essentially every field/relationship. It's less human-readable than CSV but loses nothing. CSV (all fields) is the better choice for a team-readable reference.
- **If you ever exceed 10,000 tickets:** split by date range in JQL (e.g. `project = VAST AND created >= "2024-12-01" AND created < "2025-07-01"`), export each batch, then merge the CSVs.
- **Re-import:** This CSV can be re-imported into Jira (same or another site) via **Settings → System → External System Import → CSV**.

---

*Sources: Atlassian Support — "Export issues from Jira cloud in CSV format" (updated Feb 2026) and "Export over 10,000 work items in Jira Cloud."*
