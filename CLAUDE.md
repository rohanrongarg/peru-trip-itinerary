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
| `blend-lab.html` | Sandbox for comparing background *treatments*. Loads `index.html` in an iframe and injects CSS, so it never affects the live page. Measures worst-case contrast from the real photo pixels. |
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
  - Transport items name origin and destination ("Train from Ollantaytambo to Aguas Calientes"). Write `Aguas&nbsp;Calientes` so it never splits across lines.
  - Hotels use their real names; add "Hotel" only if the name lacks it.
- **Detailed itinerary** (day tabs): every stop has a `stop-time` (estimated ranges OK). Every booking has a `<details class="stop-details">` dropdown with non-sensitive facts plus **Booked by / Booked via / Ticket** rows; use "?" when unknown.
- **Background:** one **sharp** fixed photo, never blurred, barely dimmed (`--dim-text` 0.12). The cards and detail panels get only `--dim-card` 0.20 — enough to separate them, deliberately not enough to read as boxes over the photo. Legibility comes from the glyph halo in `.wrap { text-shadow }` — six stacked layers, the inner ones fully opaque, so the photo cannot show between letter strokes — note `<footer>` sits **outside** `.wrap` and carries its own copy, and `svg.chart text` uses `paint-order:stroke fill` instead, which is far stronger at label sizes. Secondary text is `--text-muted` `#E2DAC4` (lifted twice from `#B5AC92`, which disappeared into the photo). Do not lift it further — the next stop is the heading colour `#F0EAD6` and the hierarchy collapses. Opened Details panels use `--dim-panel` 0.44, deliberately deeper than the 0.20 cards, because they only exist while expanded. Nothing is keyed to a fixed pixel width, so phones and desktops match.
- **The background no longer meets the measured contrast bar, by the owner's explicit choice.** Against pale text the photo's highlights measure roughly 2.8-3.4:1 in the open and 4.2-5.0:1 (text) / 2.2-2.6:1 (muted, gold) in cards, versus the 3.83 target. Rohan compared the options side by side and chose the photo. **Do not "fix" this by darkening the page.** If legibility needs work, change the glyph halo, the text colours, or the photo — and use `blend-lab.html` to measure, never guess.
- **Preview sandbox:** `index.html?bg=<key>` loads an alternate photo with its own measured dim and shows a "Preview" badge; `backgrounds.html` links them. The live default is unaffected. To make a preview official, set `--bg-img` / `--dim-text` in `:root` and move the old photo into the sandbox map.

## Owner preferences

- Be simple and incremental; verify each change before the next.
- Push back with evidence when something is wrong: e.g. a date mismatch in a booking, a timing clash, a closed restaurant.
- Don't re-open rejected options listed in `NOTES.md`.
