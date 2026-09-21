# Provincial Noticeboard — Buckinghamshire Freemasons

A static noticeboard app with six tabs — **Vacancies · News · Provincial Events ·
Community Events · Lodges · Map** — all driven by one Excel workbook. The site
reads `bucks-board.xlsx` from GitHub at runtime, so updating content never
triggers a Netlify build.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole app — tabs, styling, filters, fetch logic (logo embedded as base64) |
| `manifest.json` | PWA manifest for home-screen installation |
| `apple-touch-icon.png`, `icon-192.png`, `icon-512.png` | Logo icons for home screen and favicon |
| `bucks-board.xlsx` | The data. Sheets: **Vacancies**, **News**, **Provincial Events**, **Community Events**, **Lodges**, **Meta** (last-updated date), **Instructions** |

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
   - **Lodges** — one row per lodge (name, secretary, address, next meeting date,
     Installation Yes/No, ceremony, special events, meeting-night formula). Shown as
     a sortable/searchable table in the Lodges tab, and as venue markers on the Map
     tab (lodges sharing an address are grouped into one marker).
   - **Meta** — update the Last Updated date in B1 (shown in the app header).
2. Save and close Excel, then in GitHub Desktop: **Commit to main → Push origin**
   (or drag the file into the repo on github.com). The app updates on next load.

There is no separate admin screen or PIN — updating the Lodges sheet is the same
push-to-GitHub workflow as every other tab.

## Behaviour notes

- Fetches are cache-busted and the app auto-refetches when returning to the
  foreground after >60s, so stale home-screen sessions self-correct.
- All spreadsheet values are HTML-escaped before rendering; booking/news links are
  only rendered if they start with http(s).
- Tab counts show open vacancies, visible news items, upcoming events in each
  category, and total lodges listed.
- **Map tab**: built the first time a visitor opens it (not on every page load), to
  avoid unnecessary calls to OpenStreetMap. It geocodes each unique venue address
  (not each lodge individually) via OpenStreetMap's Nominatim service, staggered
  0.3s apart to stay within its usage limits — with 237 lodges across 18 venues,
  that's 18 lookups, not 237. This needs the site to be live on the internet (not
  opened as a local file) to work, same reason as the main data fetch.
- All rows in the workbook are fictional examples — delete them before going live.
- Recommended: enable two-factor authentication on both GitHub and Netlify.
