# The Second Rising — Buckinghamshire Freemasons

A static noticeboard app with eight tabs — **Vacancies · News · Provincial Events ·
Community Events · Lodges · Lodge Events · Locations · Links** — all driven by one
Excel workbook. The site reads `bucks-board.xlsx` from GitHub at runtime, so
updating content never triggers a Netlify build.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole app — tabs, styling, filters, fetch logic (logo embedded as base64) |
| `manifest.json` | PWA manifest for home-screen installation |
| `apple-touch-icon.png`, `icon-192.png`, `icon-512.png` | Logo icons for home screen and favicon |
| `bucks-board.xlsx` | The data. Sheets: **Vacancies**, **News**, **Provincial Events**, **Community Events**, **Lodges**, **Links**, **Meta** (last-updated date), **Instructions** |

## One-time setup

1. **Create a public GitHub repo for the data** (e.g. `bucks-board-data`), clone it with
   GitHub Desktop, and save `bucks-board.xlsx` inside the cloned folder. Keeping the
   data in its own repo — separate from the site — means updates never trigger a build.
2. **Push once**, then get the raw URL: open the file on github.com, click **Raw**,
   copy the address (`https://raw.githubusercontent.com/USER/REPO/main/bucks-board.xlsx`).
3. **Paste it into `index.html`** — replace `PASTE_RAW_GITHUB_URL_HERE` in `CONFIG.DATA_URL`.
4. **Deploy** `index.html`, `manifest.json` and the three icon PNGs to Netlify
   (drag-and-drop is fine; there's no build step).

Until `DATA_URL` is set, the app shows built-in sample content with an amber banner.

## Update workflow (the central person)

1. Edit `bucks-board.xlsx` in the cloned folder:
   - **Vacancies** — one row per role; only Status = Open rows are shown.
   - **News** — one row per item, shown newest first; Link is optional; Show = No retires an item.
   - **Provincial Events** / **Community Events** — one row per event, shown soonest
     first; Booking Link becomes the "Book now" button; past events disappear
     automatically. Enter dates as real dates.
   - **Lodges** — one row per lodge (name, secretary, email, address, next meeting
     date, Installation Yes/No, ceremony, special events, meeting-night formula).
     This one sheet feeds THREE tabs:
       - **Lodges** — full sortable/searchable directory table, with the
         secretary's name and email shown per lodge.
       - **Lodge Events** — a chronological feed of upcoming meetings only (like
         the Events tabs), soonest first; past meetings drop off automatically.
       - **Locations** — a simple list of venues, with lodges sharing the same
         address grouped under one entry. No interactive map and no external
         mapping service — just addresses.
   - **Links** — one row per link (WhatsApp group, Facebook page, website, etc).
     Category groups links together under a heading on the Links tab (e.g. every
     "WhatsApp Groups" row appears together). Show = No hides one without deleting it.
   - **Meta** — update the Last Updated date in B1 (shown in the app header).
2. Save and close Excel, then in GitHub Desktop: **Commit to main → Push origin**
   (or drag the file into the repo on github.com). The app updates on next load.

There is no separate admin screen or PIN anywhere in the app — every tab updates
through this same spreadsheet-and-push workflow.

## Using a Google Sheet instead of GitHub (optional)

The app can read live from a Google Sheet, which lets several people edit in the
browser with no commit/push step.

1. Upload `bucks-board.xlsx` to Google Drive, open it, and choose
   **File → Save as Google Sheets** so it becomes a native Google Sheet.
2. **File → Settings → Locale: United Kingdom** (so dates read day/month/year).
3. Keep the tab names exactly as they are (Vacancies, News, Provincial Events,
   Community Events, Lodges, Links, Meta). Real dates in date columns.
4. **Share → General access → Anyone with the link → Viewer.** Add your editors by
   email as **Editor**. (Note: anyone with the link can read the sheet, including
   the secretary emails on the Lodges tab.)
5. Copy the Sheet ID from the address bar — the long code between `/d/` and `/edit`.
6. In `index.html`, paste it into `GOOGLE_SHEET_ID` near the top of the script,
   save, commit/push (or redeploy). To go back to the Excel/GitHub route, clear it.

When `GOOGLE_SHEET_ID` is filled in it takes priority over `DATA_URL`.

## Behaviour notes

- Fetches are cache-busted and the app auto-refetches when returning to the
  foreground after >60s, so stale home-screen sessions self-correct.
- All spreadsheet values are HTML-escaped before rendering; booking/news/link URLs
  are only rendered if they start with http(s).
- Tab counts: open vacancies, visible news items, upcoming events per category,
  total lodges listed, upcoming lodge meetings, venues listed, and visible links.
- No external services are called anywhere in the app (the old Map tab's live
  OpenStreetMap geocoding has been removed) — everything renders from the one
  spreadsheet fetch.
- All rows in the workbook are fictional examples — delete them before going live.
- Recommended: enable two-factor authentication on both GitHub and Netlify.
