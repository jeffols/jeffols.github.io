# jeffols.github.io

Personal site for Jeff Olsen / jeffols. Serves `jeffols.com` from GitHub Pages
(`CNAME`). One page, no build step.

**The apex is canonical, since 2026-08-24.** GitHub Pages redirects every other
hostname to whatever `CNAME` names, so `www` already 301s to the apex with no
Cloudflare rule involved. Do not add a redirect rule for it. One pointing the
other way would fight the origin and loop.

## Brand is upstream

`../jeff-brand-logo` is the canonical brand system. **`BRAND.md` there is the
strategic source of truth** for the mark, palettes, positioning, and voice. This
repo owns implementation, content, deployment, page accessibility, and
performance. It owns *copies* of generated assets. It does not own the mark.

| Question | File in `../jeff-brand-logo` |
|---|---|
| What do I copy, and where does it go? | `docs/site-handoff.md` |
| Which mark, what size, which file? | `docs/logo-usage.md` |
| What goes in which section? | `docs/website-direction.md` |
| Contrast numbers, transparent-mark rule | `docs/accessibility.md` |
| Why is it this way? | `docs/decisions/0001`–`0008` |
| What is decided vs still open? | `BRAND.md` section 23 |

## Rules that are easy to break

- **Never redraw or inline the mark geometry.** Always reference a generated
  asset. A geometry change upstream cannot reach hand-copied path data and
  nothing fails loudly when they diverge. Three inlined copies were removed on
  2026-08-01; do not reintroduce one. `BRAND.md` section 21.
- **`currentColor` inherits only when the SVG is inlined**, not through
  `<img src>`. Through `<img>` an SVG is an isolated document with no parent
  colour and a `-mono` mark renders black. Inline the mono files; use a palette
  variant for `<img>`.
- **Do not ship a subset of iA Writer Duo under its own name.** It carries the
  Reserved Font Names `iA Writer` and `Plex`, and OFL clause 3 forbids a Modified
  Version from using them. Subsetting produces a Modified Version. The `.woff2`
  files here are container conversions only, no glyph subsetting and no name-table
  edits, so the family name stays valid. Inter has no reserved name and may be
  subset freely.
- **Do not choose a typeface.** Decision `0007` settled it: Inter for UI,
  navigation, and headings; iA Writer Duo S for essays and long-form.
- **Static HTML and CSS only.** No framework, no build step, no CDN request. All
  styles are inline in `index.html`; all assets are served from this repo.
  **Two deliberate exceptions, 2026-08-22:** Cloudflare Web Analytics at the end
  of `<body>`, and Google Analytics 4 at the end of `<head>`. Jeff wanted visitor
  measurement and accepted the cost. Cloudflare's automatic injection was rejected
  as a loophole, since the browser makes the request either way and the repo would
  no longer show that it happens.

  **Why both, not one.** They measure the same thing, but this site's audience is
  engineers and ad blockers stop GA far more often than they stop Cloudflare's
  beacon. GA alone could undercount real readers badly enough to suggest nobody
  visits. Read Cloudflare for the visitor count, GA for behaviour and acquisition.
  Drop GA first if either goes: it is the one carrying cookies and consent
  obligations.

  These two are the whole exception. Anything else asking for a CDN request needs
  its own decision, not this as precedent.
- Ship the font licence files. OFL requires it.
- `og:image` must be an absolute URL. Relative paths silently fail on most
  platforms.
- **No colons and no em dashes in published copy.** `BRAND.md` section 2. The
  patterns that keep slipping through are `You will learn:`, `Note:`, and a noun
  list followed by a colon and its explanation. Rewrite as two sentences. Applies
  to headings, card labels, and meta descriptions, not to code comments.
- **Do not publish the internal organizing phrase.** `BRAND.md` section 3 keeps a
  thinking tool separate from the publishable formulations. The internal one
  shipped in the footer once and nothing failed when it did.
- **Positioning changes are brand changes.** Anything touching the hero, the About
  section, or a path description changes `BRAND.md` in the same pass, and adds a
  decision record if it settles an open question. Channel copy in
  `docs/online-presence.md` goes stale whenever positioning moves.
- Do not publish private employer detail. Keep every claim supportable.

## Working agreement

Jeff explores by circling an idea, revealing one facet per message rather than
stating a full spec up front. Each message looks like a complete requirement and
is actually one more piece of a model still being assembled.

Answering each one with finished, shippable work creates false convergence. The
next facet then reads as a change of direction rather than an addition, so the
artifact gets rebuilt. The hero was drafted, shipped, and rewritten across six
commits in a single day for exactly this reason.

His own statement of the fix, which is the standard here. **Build on past
decisions, focus on the goal, park valuable noise, tie off work.**

In practice:

- **Ask before drafting.** What has not been said that would change this? Surface
  the adjacent unknowns instead of waiting to be corrected on them.
- **Skeletons while exploring. Finished copy only on "ship it."** Name which mode
  is in play.
- **Park out loud.** A good tangent goes on the ledger, not into the work. Parked
  brand decisions belong in `BRAND.md` section 23, channel items in
  `docs/online-presence.md`, site items in `docs/website-direction.md`.
- **One commit at convergence**, not one per exchange.
- **Read the ledger back unprompted.** Decided, parked, open. Open items sat
  untouched for a day because nothing restated them.
- **Circling is not the defect.** Pushing for early convergence costs the
  cross-domain synthesis that is the actual strength. The job is to hold the
  thread, not to force a decision.

## Layout

```
index.html              the whole site: markup + inline CSS
CNAME                   jeffols.com
favicon.*, apple-touch-icon.png, android-chrome-*.png, site.webmanifest
assets/hero-mark.png    rotational mark, transparent, hero background
assets/social-preview.png   1200x630 Open Graph card
assets/fonts/           InterVariable + iA Writer Duo S woff2, with licences
```

## Re-syncing brand assets

Assets are versioned by tag upstream; `v2.0.0` is current. When the brand repo
changes: read `docs/decisions/` for what moved, re-copy per `docs/site-handoff.md`,
and regenerate the woff2 conversions if a font changed.

**Deliberate divergence from the handoff copy list:** `iAWriterDuoS-Bold` is not
shipped. Nothing on this page sets bold in the essay register, so the file and its
`@font-face` rule would both be dead weight. Convert and declare it when long-form
content that needs it lands. Everything else on the list is present.

**iA Writer Duo S** is a container conversion only. Never subset it. Regenerate with:

```python
from fontTools.ttLib import TTFont
f = TTFont("iAWriterDuoS-Regular.ttf"); f.flavor = "woff2"
f.save("iAWriterDuoS-Regular.woff2")
```

**Inter** is subset to Latin, which decision `0007` explicitly permits because Inter
carries no Reserved Font Name. 344 KB to 60 KB, with both variable axes (`wght`
100-900, `opsz`) preserved and the rendered page pixel-identical. Regenerate with:

```bash
pyftsubset InterVariable.ttf --output-file=InterVariable.woff2 --flavor=woff2 \
  --layout-features=kern,liga,calt,ccmp,locl,mark,mkmk --name-IDs='*' --name-legacy \
  --notdef-outline --drop-tables+=DSIG \
  --unicodes=U+0000-00FF,U+0131,U+0152-0153,U+02BB-02BC,U+02C6,U+02DA,U+02DC,\
U+2000-206F,U+2074,U+20AC,U+2122,U+2191,U+2193,U+2212,U+2215,U+FEFF,U+FFFD,\
U+2018-2019,U+201C-201D,U+2026
```

If page copy ever needs a character outside that range, widen `--unicodes` and
re-subset. The page is currently pure ASCII.

## Run

```bash
python3 -m http.server 8080    # then open http://localhost:8080
```
