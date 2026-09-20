# Peru trip itinerary site

Static GitHub Pages site for the Peru trip, Sep 23–29, 2026 (Rohan, Ashwin, Rishi).

- Live: https://rohanrongarg.github.io/peru-trip-itinerary/
- Repo: `rohanrongarg/peru-trip-itinerary` (public), deploys from `main`.
- **Read `NOTES.md` first.** It's the trip brain: bookings ledger, decisions, open items, conflicts, rejected options. Keep it current: when a fact changes, update `NOTES.md` in the same commit.

## Security: this repo and the site are PUBLIC

Never commit or publish:
- confirmation codes, booking references, order numbers, e-ticket numbers, PINs
- passport numbers or other ID details
- surnames or full names (first names are fine, including in "Booked by" rows)
- booking/manage URLs that contain IDs (e.g. GetYourGuide `/booking/<id>`)
- email addresses or other secrets

Business phone numbers, hotel and restaurant addresses, flight numbers, train services, times, and prices are fine.

Sensitive references go only in `PRIVATE.md`, which is git-ignored and exists only on Rohan's Mac. Before every commit, grep the diff for codes and surnames.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole site (inline CSS/JS). This is what Pages serves. |
| `peru-boys-trip-itinerary_V3.html` | Must be **byte-identical** to `index.html`. After every edit: `cp index.html peru-boys-trip-itinerary_V3.html`. |
| `backgrounds.html` | Gallery for previewing alternate background photos. |
| `blend-lab.html` | Sandbox for trying styling options against the live page. Loads `index.html` in an iframe and injects CSS (and optionally a little JS), so it never affects the published site, and measures worst-case contrast from the real photo pixels. Repoint it at whatever question is open — it currently holds day-subline options; the background-treatment looks were removed once the background was settled. |
| `maps.html` | Per-day map, `maps.html?day=0..6`. Leaflet + OpenStreetMap tiles (no API key, nothing to leak), numbered pins and a line through the day's stops, plus an "Open in Google Maps" button. Linked from each day heading in `index.html` via `.day-map`. |
| `bg-*.jpg`, `thumbs/` | Background photos (Unsplash) and their gallery thumbnails. Live: `bg-classic.jpg`. |
| `NOTES.md` | Trip facts and decisions (non-sensitive). |
| `PRIVATE.md` | Git-ignored. Booking refs and contacts. Never commit. |

## Workflow

1. Edit `index.html` in small, targeted changes. Assert that each find/replace matched exactly once.
2. `cp index.html peru-boys-trip-itinerary_V3.html`
3. Commit with a descriptive message and push to `main`. Pushing is expected for site changes, since the owner views the live site.
4. Verify the live site (Pages takes ~1 min): `curl -s "https://rohanrongarg.github.io/peru-trip-itinerary/?cb=$RANDOM" | grep -q "<new text>"` in a retry loop.
5. For visual changes, preview locally first (`uv run --no-project python -m http.server 8765`) and check desktop plus a ~390px phone width.
6. Tell the owner to hard-refresh (Cmd+Shift+R) to see changes.

## UI conventions

- **At a glance** (overview cards): three cards per row; 11.5px item text. The owner calls this section the **AAG**.
  - Status dots: `status-booked` green = booked, `status-needed` red = still to book, `status-na` grey = walk-up / nothing to book.
  - Links are **invisible**: `<a href="..." target="_blank" rel="noopener">` around the item text. The CSS `.overview-item a{ color:inherit; text-decoration:none; }` keeps them identical to plain text. Never blue, underlined, or with icons. Link official sites (Wikipedia for landmarks).
  - Day sublines (the "Lima → Cusco → …" route lines) are bright gold `#E6BE63` at weight 600 — not `--gold` `#C9A24A`, which is too dark against the photo.
  - Day headings ("Sat 9/26") are **jump links**: `<a href="#tabs" class="day-jump" data-day="N">` wrapped inside the `<h3>`. Clicking one activates that day's tab and scrolls to `#tabs`. `data-day` is the zero-based index into `.day-tab` / `.day-panel`, so **if a day is ever added or reordered, renumber them**. Styled invisible like the item links via `.overview-card h3 a{ color:inherit; text-decoration:none; }`.
  - Transport items name origin and destination ("Train from Ollantaytambo to Aguas Calientes"). Write `Aguas&nbsp;Calientes` so it never splits across lines.
  - Hotels use their real names; add "Hotel" only if the name lacks it.
- **Each day heading carries a "Map" pill** (`.day-head-row` > `.day-heading` + `.day-map`) linking to `maps.html?day=N`. `N` is the same zero-based index as the `day-jump` links, so **renumber both together** if a day is ever added or reordered.
- **`maps.html` never uses a Google API key** — the repo is public. It renders OpenStreetMap tiles via Leaflet and hands off to Google only through a plain `google.com/maps/dir/?api=1` URL built from **place names and addresses**, not coordinates, so Google resolves the real places even though the pin coordinates are hand-placed approximations. Google's URL API caps waypoints at 9; the button says so when a day is trimmed.
- **The map is a bonus layer, never a dependency.** If Leaflet or its tiles fail, `body.no-map` shows an explanation and the numbered stop list and Google button still work. Keep that fallback.
- **Detailed itinerary** is headed **"Day by day"** (mirrors "At a glance", same `.overview-heading` / `.overview-sub` classes). Its subline doubles as the swipe hint.
- **The day-tab row scrolls sideways on narrow screens and iOS hides its scrollbar**, so nothing signals it. A gradient fade reads as a smudge over the photo, so the hint rides the row's own underline: `.tabs-scroll i`, a gold thumb whose width is the visible fraction and whose left tracks `scrollLeft`, shown only when `.tabs-wrap` has `.scrollable`. `activate(i)` also centres the chosen tab — **instantly via `scrollLeft`, never `behavior:'smooth'`**, which is unreliable on a nested scroller.
- **Detailed itinerary** (day tabs): every stop has a `stop-time` (estimated ranges OK). Every booking has a `<details class="stop-details">` dropdown with non-sensitive facts plus **Booked by / Booked via / Ticket** rows; use "?" when unknown.
- **iOS Safari tints its status bar and toolbar from the page canvas, not from the photo.** `<meta name="theme-color">` and `--canvas` are both `#474840`, sampled from the photo's own top and bottom edges, so the chrome blends instead of reading as a black band. **Keep the two in step**, and don't confuse `--canvas` with `--bg` (`#1B1D18`), which fills the timeline dot and stays dark.
- **Background:** one **sharp** fixed photo, never blurred, barely dimmed (`--dim-text` 0.12) and brightened by a white veil *under* the dim (`--lift` 0.05, a plain background layer). The fixed layer is sized `top:-80px; height:calc(100lvh + 160px)` — **never `inset:0`**, which sizes to iOS Safari's layout viewport. Just as important: **never `overflow:clip` on `html`/`body`** — unlike `hidden`, `clip` clips fixed-position descendants, which cropped this layer and left a black band behind iOS Safari's toolbar. Use `body{overflow-x:hidden}` if stray horizontal overflow ever needs guarding. Also **never put `filter` or `backdrop-filter` on `body::before`** — it was tried and iOS Safari stopped painting the fixed layer, blacking out the bottom of the screen while scrolling. Brightening always costs text contrast, so re-measure if you change it. The cards and detail panels get only `--dim-card` 0.20 — enough to separate them, deliberately not enough to read as boxes over the photo. Legibility comes from the glyph halo in `.wrap { text-shadow }` — six stacked layers, the inner ones fully opaque, so the photo cannot show between letter strokes — note `<footer>` sits **outside** `.wrap` and carries its own copy, and `svg.chart text` uses `paint-order:stroke fill` instead, which is far stronger at label sizes. Muted text is weight **500** — readability comes from stroke weight, never from darkening it (the glyph halo is near-black, so darker text loses contrast). **Never stack `opacity` on muted text**; `.day-tab .p` had `opacity:0.75` that silently cancelled three colour lifts. `.stop p` uses the primary `--text` cream, not the muted tone, being the actual reading copy. Secondary text is `--text-muted` `#E2DAC4` (lifted twice from `#B5AC92`, which disappeared into the photo). Do not lift it further — the next stop is the heading colour `#F0EAD6` and the hierarchy collapses. Stop descriptions sit on a faint `--dim-copy` 0.13 panel — **lighter than the 0.20 cards on purpose**; its 12px padding is cancelled by a `-12px` left margin so the copy stays flush with its title, and 12px stays clear of the 22px `.stops` gutter. The stop *title* gets no panel. Opened Details panels use `--dim-panel` 0.44, deliberately deeper than the 0.20 cards, because they only exist while expanded. Nothing is keyed to a fixed pixel width, so phones and desktops match.
- **The background no longer meets the measured contrast bar, by the owner's explicit choice.** Against pale text the photo's highlights measure roughly 2.8-3.4:1 in the open and 4.2-5.0:1 (text) / 2.2-2.6:1 (muted, gold) in cards, versus the 3.83 target. Rohan compared the options side by side and chose the photo. **Do not "fix" this by darkening the page.** If legibility needs work, change the glyph halo, the text colours, or the photo — and use `blend-lab.html` to measure, never guess.
- **Preview sandbox:** `index.html?bg=<key>` loads an alternate photo with its own measured dim and shows a "Preview" badge; `backgrounds.html` links them. The live default is unaffected. To make a preview official, set `--bg-img` / `--dim-text` in `:root` and move the old photo into the sandbox map.

## Owner preferences

- Be simple and incremental; verify each change before the next.
- Push back with evidence when something is wrong: e.g. a date mismatch in a booking, a timing clash, a closed restaurant.
- Don't re-open rejected options listed in `NOTES.md`.
