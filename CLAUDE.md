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

- **At a glance** (overview cards): three cards per row; 11.5px item text.
  - Status dots: `status-booked` green = booked, `status-needed` red = still to book, `status-na` grey = walk-up / nothing to book.
  - Links are **invisible**: `<a href="..." target="_blank" rel="noopener">` around the item text. The CSS `.overview-item a{ color:inherit; text-decoration:none; }` keeps them identical to plain text. Never blue, underlined, or with icons. Link official sites (Wikipedia for landmarks).
  - Transport items name origin and destination ("Train from Ollantaytambo to Aguas Calientes"). Write `Aguas&nbsp;Calientes` so it never splits across lines.
  - Hotels use their real names; add "Hotel" only if the name lacks it.
- **Detailed itinerary** (day tabs): every stop has a `stop-time` (estimated ranges OK). Every booking has a `<details class="stop-details">` dropdown with non-sensitive facts plus **Booked by / Booked via / Ticket** rows; use "?" when unknown.
- **Background:** a fixed photo darkened behind the text column by a black overlay (`--dim-text`), with a full-height blurred copy fading in from the sides. There is no content box. `--dim-text` is the *measured* minimum for worst-case text contrast at a 15%-relaxed WCAG target (owner's choice), computed over the brightest blurred pixels at four viewport sizes. **If you change the photo, re-measure.** Don't guess.
- **Preview sandbox:** `index.html?bg=<key>` loads an alternate photo with its own measured dim and shows a "Preview" badge; `backgrounds.html` links them. The live default is unaffected. To make a preview official, set `--bg-img` / `--dim-text` in `:root` and move the old photo into the sandbox map.

## Owner preferences

- Be simple and incremental; verify each change before the next.
- Push back with evidence when something is wrong: e.g. a date mismatch in a booking, a timing clash, a closed restaurant.
- Don't re-open rejected options listed in `NOTES.md`.
