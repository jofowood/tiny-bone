# tiny-bone
Tiny Bone Maker

# Tiny Bone Set

A single-page, no-backend web app where visitors design their own **Tiny Bone Set** — a wall-mounted piece built from a fixed collection of 17 original tiny bone images — and request it to be made.

Built as one static HTML file. No build step, no server, no database. Designed to be hosted directly on GitHub Pages.

## What it does

Visitors work with two things:

- **17 named specimens** (real small bones — the 8 carpals, 7 tarsals, plus the hyoid and sesamoid), each used at most once.
- **5 solid fill colors** (Cyan, Magenta, Yellow, Black, White), unlimited use.

These get placed onto a fixed **2-row × 17-column** grid. The only structural rule: whatever shape gets built has to end up a **solid rectangle** — any width from 1 to 17, either 1 or 2 rows tall, no notches or holes. Any empty cell that's blocking a clean rectangle is highlighted in red on the board so it's obvious what still needs to be filled.

The specimen tray is deliberately **image-only** — no names or numbers are shown to the visitor. That information is tracked internally and only surfaces in what the maker receives.

### Requesting a set

Once a shape is a valid rectangle, a visitor can request it. That:

1. Generates a unique, timestamp-based ID for the set (sorts chronologically as plain text — see [Design notes](#design-notes)).
2. Renders a cropped snapshot image of just the built shape, stamped with that ID.
3. Opens a form for name, email, and notes.
4. On submit, either opens the device's native share sheet with the image attached (where supported — mostly mobile), or falls back to a pre-filled email draft (the visitor needs to manually attach the downloaded image in this case, since a plain link can't attach files to an email).

There is **no payment processing and no order database** — everything here is about capturing the design and the visitor's contact details. Pricing, confirmation, payment, and delivery all happen afterward, manually, by the maker.

## Setup

1. Open `bone-assemblage.html` and find the config constants near the top of the `<script>` block.
2. Set `ORDER_EMAIL` to the real inbox that requests should go to.
3. Replace the placeholder artwork (see below).
4. Push to a GitHub repo and enable **Settings → Pages** to serve it — no build process required.

### Replacing the placeholder artwork

The 17 bone glyphs are currently a single generated SVG shape, tinted 17 different ways as a stand-in. Look for the `boneSVG()` function — it's commented with where to swap in real images. Replace it with an `<img>` tag pointing at your actual 17 files.

Each piece is treated as **60mm × 90mm, portrait only** (`PIECE_W_MM` / `PIECE_H_MM` near the top of the script). Real artwork should match that ratio; the grid, the exported image, and the mm measurements in the request record are all built around it.

## File structure

Everything lives in one file:

```
bone-assemblage.html
```

No dependencies, no package.json, no build tooling. It loads nothing external except the system font stack (`-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`) — there isn't even a webfont to fetch.

## Design notes

A few decisions worth knowing about if you're going to keep customizing this:

- **The set ID isn't a true sequential counter.** There's no shared backend to count against, so each ID is generated in the visitor's own browser from the date, time, and a few random characters (e.g. `AS-20260730-193045-7F3K`). It's guaranteed unique and sorts correctly in chronological order as plain text, which covers what actually matters for tracking real requests — it just isn't a clean "1, 2, 3…" tally.
- **Nothing persists.** All state lives in memory for the current page load. A refresh clears the board. There's no login, no saved drafts, no order history.
- **Removing a placed piece doesn't re-validate the rest of the shape.** It's possible to build a valid rectangle, remove a piece from the middle, and leave an invalid shape until you fill the gap back in — the "gaps in shape" indicator will just tell you where.
- **Piece weight assumption:** the physical size math (mm measurements sent to the maker) assumes every cell — bone or color fill — is the same footprint. It doesn't know or care about actual material weight.

## Known limitations

- Placeholder artwork needs to be swapped for the real 17 images before this is customer-facing.
- `ORDER_EMAIL` needs to be set to a real address.
- Email attachment of the generated image isn't automatic outside of devices that support the Web Share API — visitors on desktop will need to manually attach the downloaded PNG.
- No persistence, no accounts, no backend — by design, but worth knowing if requirements grow beyond what a static file can reasonably do.
