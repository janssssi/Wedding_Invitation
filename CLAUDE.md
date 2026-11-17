# Wedding Invitation — Carl & Rocelle

Mobile-first wedding invitation website. Plain HTML/CSS/JS, no build step, no framework.
Hosted on GitHub Pages from `main`: https://janssssi.github.io/Wedding_Invitation/

- **Couple:** Carl & Rocelle
- **Date:** Sunday, December 12, 2027 — ceremony 3:00 PM, reception until 10:00 PM (Manila time, +08:00)
- **Venue:** The Grand Garden, Manila, Philippines
- **RSVP deadline:** November 12, 2027

## Ground rules

1. **Never commit or push unless the user asks in that message.** Leave changes in the working tree and say what is uncommitted.
2. **Work in the main folder** `C:\Users\menes\source\repos\janssssi\Wedding_Invitation`, not in a worktree.
3. **No AI-generated images.** Artwork comes from Canva's *free* stock elements (or the couple's own photos). Code-based CSS/JS animation is fine.
4. **Keep every page a single self-contained HTML file** (inline `<style>` and `<script>`); only images, `music.mp3` and Google Fonts are external.
5. **Mobile first.** Content column is `--col: 460px`; check every change at phone size (375×812) before calling it done.
6. **Respect `prefers-reduced-motion`.** Every new animation needs an off switch in the `@media (prefers-reduced-motion:reduce)` block.
7. **Line endings are CRLF** in the HTML files — keep them when editing with scripts.
8. **Don't break the RSVP:** the Google Form IDs in `CONFIG.rsvp` must stay exactly as they are unless the user gives new ones.

## Files

| Path | What it is |
|---|---|
| `index.html` | **Main page** — embossed white paper + earth tones (ivory, champagne, beige, taupe, mocha, gold). Wax-seal opening with corner paper bouquets, oval photo frame. |
| `index-botanical.html` | **Alternative design** for comparison — ivory + sage + blush, line-drawn botanicals, full-screen envelope opening, watercolor bands. Same `<script>` logic and element IDs as `index.html`. |
| `photos/` | `couple.jpg` (oval frame), `story-1..3.jpg`, `attire-1.jpg` (her), `attire-2.jpg` (him), `venue.jpg`. **Must be committed** — the live site can't show anything that isn't in the repo. |
| `art/` *(optional)* | Floral/watercolor art exported from Canva, mainly for the botanical page. |
| `cover.jpg` | Link-preview (Open Graph) image. |
| `music.mp3` | Background music; starts when the guest opens the invitation. |

Never add `photos/` (or `art/`) to `.gitignore` — that once made every image disappear on the live site.

## Page structure (both pages, same order and IDs)

1. **Opening** `#opening` — envelope / wax seal `#openBtn`, "Skip intro" `#skipBtn`, guest greeting `#dear`
2. **Announcement** `#announcement` — names, couple photo
3. **Countdown** `#countdown`
4. **Details** `#details` — ceremony & reception
5. **Our Story** `#story` — three moments with photos
6. **Entourage** `#entourage` — replace the `[bracketed]` placeholders
7. **Dress code** `#dresscode` — earth-tone swatches + attire images
8. **Reminders** `#reminders`
9. **RSVP** `#rsvp` — form `#rsvpForm`, posts to Google Forms (no-cors)
10. **Location** `#location` — venue photo + Google Maps link
11. **Closing** `#closing`

The "Add to calendar" feature was removed on purpose — don't bring it back unless asked.

## Editing the content

All event data lives in the `CONFIG` object at the top of the `<script>` in each page:

```js
const CONFIG = {
  couple, weddingStart, weddingEnd,   // ISO times with +08:00
  venue, mapsUrl,                     // paste the real Google Maps share link
  rsvp: { formId, entries: { name, attending, count, meal, message } }
};
```

- If you change `CONFIG` (or any shared text) in one page, make the same change in the other.
- Personalized links: `index.html?to=Maria%20Santos` shows "Dear Maria Santos" on the envelope.
- Use the `on(id, type, fn)` helper for listeners so a missing element never crashes the script.

## Design conventions

- **Fonts:** Cormorant Garamond (serif), Jost (sans), Pinyon Script (names and headings).
- **Colours** are CSS variables in `:root` — restyle by changing those, not hard-coded values.
- **Decorations** are inline SVG defined once in a hidden `<defs>` block and reused with `<use href="#...">`.
  Mirror a decoration *inside* the SVG (`transform="translate(W 0) scale(-1 1)"`), not with CSS `transform`, because `.reveal` already uses `transform`.
- **Scroll reveal:** add class `reveal`; an IntersectionObserver adds `.in`.
- **Line-art "draw" effect** (botanical page): class `draw` on an SVG.
- **Ambient motion:** falling petals (a shower on the seal tap, then a few while `#announcement` is visible), swaying corner blooms, breathing sprigs. Use the individual `rotate` / `scale` / `translate` CSS properties so they don't fight `transform`.
- **Images:** always set the real pixel `width`/`height` attributes, `loading="lazy"` below the fold, and meaningful `alt` text.
  The attire images are full-length figures on white, shown with `object-fit: contain` in a white frame — don't switch them to `cover` (it crops heads and shoes).
- **Watercolor bands** on the botanical page use `--art`: `<div class="painting" style="--art:url(art/garden-1.jpg)">`; without it a CSS placeholder painting shows.

## Getting art from Canva

The Canva connector can't search Canva's stock library, so:
1. The user makes a Canva design (e.g. "Wedding art") and adds **Free** elements (watercolor frames, floral corners, garden scenes), one per page.
2. Find it with Canva `search-designs`, export each page as PNG (transparent background where useful) or JPG.
3. Save into `art/`, keep files web-sized (≤ 1600 px wide, ideally < 400 KB), wire them into the page.

## Testing

Images use relative paths, so `file://` previews won't load them. Serve the folder:

```bash
python -m http.server 8765 --bind 127.0.0.1
```

Open `http://127.0.0.1:8765/` (and `/index-botanical.html`) at 375×812 and check:
- the envelope opens (seal tap and Skip intro); the music button works
- every image loads (no 404s), the countdown runs, the RSVP form validates
- no console errors; with reduced motion all content is still visible

Stop the server when done.

## Publishing (only when asked)

Commit on `main` and push; GitHub Pages redeploys in about a minute. Put new files in `photos/` or `art/` in the same commit as the HTML that uses them.
