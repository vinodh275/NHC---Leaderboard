NHC Leaderboard

A live leaderboard for the NHC trainer sales contest (July 1 – July 31), built as a single static HTML page and hosted on GitHub Pages. It pulls trainer performance straight from the "View" tab of the contest Google Sheet — no backend, no API key.

What it shows


Top 3 podium by % of target achieved
Contest-wide stats: trainers live, targets hit, overall % achieved, cash unlocked
Center-wise leaderboards with search, and filters by center / role (PT vs FM)
Progress tiers from "Just Getting Started" through "Outstanding" (150%+, ₹6,000 prize)


Live data

The page reads from Google's public data-export endpoint for the sheet, using a JSONP script-tag request (avoids CORS, needs no key):

https://docs.google.com/spreadsheets/d/<SHEET_ID>/gviz/tq?tqx=out:json;responseHandler:onSheetData&gid=<GID>&headers=1&range=A4:I500

On load it shows a cached snapshot instantly, then syncs live in the background. It auto-refreshes every 5 minutes, and there's a manual Refresh now button plus a "Last synced" timestamp.

Requirement: the sheet's sharing must stay set to "Anyone with the link can view". This is read-only — no one can edit the sheet through this page. If sharing is changed or the link breaks, the page shows a warning banner and falls back to the last data it successfully loaded, instead of failing silently.

Files

FilePurposeindex.htmlThe dashboard — rename nhc_leaderboard_dynamic.html to this so GitHub Pages serves it at the site root

Setup


Create (or reuse) a GitHub repo.
Add the dashboard file, named index.html.
Push to the main branch.
In the repo: Settings → Pages → Source → deploy from main (root).
GitHub gives you a live URL — that's what you share with trainers.


Updating for a new contest / month

Edit these constants at the top of the <script> block in index.html:

jsconst SHEET_ID = "...";   // the Google Sheet ID from its URL
const GID = "...";        // the tab's gid, from the sheet URL after #gid=
const HEADER_RANGE = "A4:I500";  // where the header row + data live in that tab

Column matching is by header name, not position — so as long as the "View" tab keeps these exact headers, row order or extra columns won't break anything:

Trainer_Id, Trainer_Name, Center_Name, Status, Designation, Tgt, Ach

Don't rename these headers or the tab without also updating the constants above — a mismatch just falls back to the last synced snapshot rather than crashing, but the page will stop updating live.

Prize tiers

RangeStatusPrize0%Just Getting Started—1–49%Building Momentum—50–79%Great Progress—80–99%Almost There—100–149%Target Achieved₹1,000150%+Outstanding₹6,000
