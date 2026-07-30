# SecondShelf — coming soon page

A static one-page site built from Figma
[SecondShelf Concepts › Desktop - 3](https://www.figma.com/design/5wqYOKU4Ipl9qGFPNpHDo5/SecondShelf-Concepts?node-id=10-1521)
(node `10:1521`).

No build step and no dependencies. `index.html`, `styles.css`, and `assets/`
do the work, plus ~90 lines of inline JavaScript for the cursor effect — so
any static host will serve it as-is.

Total page weight: **134 KB** — 86 KB brand font, 32 KB phone mockup, 9 KB
logo, 7 KB HTML and CSS. The font dominates and cannot be reduced; see
[`fonts/README.md`](fonts/README.md).

## Preview locally

```sh
python3 -m http.server 8000
# → http://localhost:8000
```

Opening `index.html` directly via `file://` also works, but a server is closer
to production.

## The brand font

At Hauss VAR, self-hosted from `fonts/`.

Nothing needed converting to SVG. "SecondShelf" is *outlined vector* in the Figma
file, not live text, so `assets/logotype.svg` carries no font data and the
wordmark never depended on the typeface. Only the tagline and the "Coming soon"
label use it, and they stay as real HTML text.

Two things in [`fonts/README.md`](fonts/README.md) before you touch the type:

- **"Regular" is `wght: 421`, not 400.** This family's named weights don't sit on
  the usual 100s, so `font-weight: 400` renders lighter than the design.
  `styles.css` exposes `--wght-regular` / `--wght-medium`.
- **If you subset it, don't narrow the `wght` axis.** It silently breaks
  rendering in Chrome — the file still validates, so it only reproduces in a
  browser.

## Deploy to a new domain

Pick one — all three are zero-config for a site this shape.

**Cloudflare Pages** — needs no config file at all.

```sh
npx wrangler pages deploy . --project-name=secondshelf
```

**Vercel** — `vercel.json` is included.

```sh
npx vercel --prod
```

**Netlify** — `netlify.toml` is included.

```sh
npx netlify deploy --prod --dir=.
```

Then point the domain at it: add the domain in the host's dashboard and create
the DNS record it gives you — an `A`/`ALIAS` record on the apex plus a `CNAME`
on `www`. All three issue the TLS certificate automatically once DNS resolves,
usually within a few minutes.

## Domain

The live domain is `secondshelf.shop`, set in four places: `<link rel="canonical">`
and `og:url` / `og:image` in `index.html`, the `Sitemap:` line in `robots.txt`,
and `<loc>` in `sitemap.xml`. `og:image` must stay absolute or link previews
render blank.

DNS is on GoDaddy nameservers pointing at Vercel. If the apex ever falls back to
a GoDaddy parking page while `www` works, the cause is GoDaddy's Forwarding
being switched on — it manages the apex `A` records and recreates its own
(`76.223.105.230` / `13.248.243.5`, reverse-DNS `awsglobalaccelerator.com`) even
after you delete them. Remove the forwarding rule first, then point `@` at
Vercel's `76.76.21.21`.

## Assets

| File | Notes |
| ---- | ----- |
| `assets/phones.webp` | The two-phone mockup, exported from Figma frame `16:1933` at 4× (1274×1680). Primary. |
| `assets/phones.jpg` | Fallback for browsers without WebP, at 2×. **Flattened onto `#111`** — see caveat below. |
| `assets/logotype.svg` | Mark + wordmark, assembled from the Figma vector layers. Contains no font data. |
| `assets/mark.svg` | Mark alone. |
| `assets/favicon.svg`, `assets/apple-touch-icon.png` | Mark on a `#111` tile. |
| `assets/og.jpg` | 1200×630 social card. |

### If you change the page background

`phones.jpg` has no alpha — it is flattened onto `#111` so it can be a 42 KB
JPEG instead of a 308 KB PNG (palette-quantising the PNG banded the gradient
badly). That is invisible while `--bg` stays `#111`, but change the background
and the fallback will show a dark rectangle. WebP browsers — ~97% of traffic —
are unaffected either way. To re-flatten, re-export frame `16:1933` from Figma
at 4× and composite it onto the new colour.

## Fidelity

Verified by pixel-diffing a 1440×1024 render against the Figma reference:
**mean difference 0.56/255, with 0.92% of pixels differing by more than 8/255.**
The content bounding box matches within 1px, the copy column starts at x=717 in
both, and the tagline's rendered ink spans x=718–1119 in both — so glyph advance
widths match Figma exactly.

What is left is sub-pixel: 1px of text-baseline rounding (browsers and Figma
position baselines differently inside a line box) and 1px of SVG rasterisation
on the wordmark.

One intentional departure from the Figma node: the "Coming soon" pill's 5%-white
stroke is **removed** by request. Its 1px was absorbed into the padding
(12 → 13), so the pill still measures Figma's 46px and the copy column stays
centred where the design puts it.

Also verified: with the webfont deliberately 404'd, the metric-matched fallback
still holds the tagline on one line, so there is no reflow on a slow load.

Layout is checked at 320 / 390 / 430 / 600 / 768 / 860 / 1024 / 1440 px with no
horizontal overflow at any width.

## The entrance

Two beats, ~2.8s total, with a deliberate hold between them.

**Beat one** — the phone mockups and the lockup (wordmark + tagline together)
fade up out of a slight blur. The lockup sits optically centred at this point,
as though the pill were not there.

**Beat two** — after beat one settles (~1.1s) the composition holds still for
about 0.8s, then the lockup rises 39px into its final position while
"Coming soon" fades in below it. 39px is exactly half of the pill plus its gap, which is what
makes beat one read as centred.

| Element | Effect | Delay | Duration | Blur from |
| ------- | ------ | ----- | -------- | --------- |
| Phone mockups | fade | 0.04s | 1.0s | 8px |
| Lockup | fade | 0.12s | 1.0s | 5px |
| Lockup | lift | 1.90s | 0.8s | — |
| Pill | fade | 2.10s | 0.7s | — |

No positional movement in beat one — the lift is the only transform, and it is
what gives the reveal its structure. Tune the `--blur-from` / `--dur` /
`--delay` values on the three selectors in `styles.css`, and the `39` in the
`lift` keyframe if you change the pill's size or gap.

`prefers-reduced-motion: reduce` disables all four animations.

## Cursor effect

Nothing is visible until the pointer moves. There is no glow or wash layer — the
dots are the whole effect, so the softness comes purely from the mask fading them
out toward its edge.

**Trail.** Eight reveal points chase each other in a chain (point 1 follows the
cursor, point 2 follows point 1, and so on at `0.17` per frame), so the tail lags
progressively and reads as a trail rather than a single spotlight. They are
stacked as eight `mask-image` layers whose alphas add up — 0.95 down to 0.08 — so
the trail also dims along its length. The whole list lives in one custom
property, `--trail`, which both the dot layer and the sparkle layer reuse.
`POINTS` in the script must match the number of mask layers.

**Sparkle.** 9 absolutely-positioned dots twinkle in and out on random
durations (2.6–5.2s) and random delays, and each spends the last 30% of its
cycle fully dark, so only one or two are lit at once. Each one
repositions on `animationiteration`, scattered uniformly over the reveal disc.
They are snapped to the dot lattice — note the half-cell offset: `radial-gradient`
centres its dot in each tile, so the dots sit at 7px, 21px, 35px… not 0, 14, 28.
Snapping to multiples of `--cell` puts every sparkle in the gap between four
dots, which is wrong; `snap()` in the script accounts for it. The sparkle layer
carries the same `--trail` mask, so sparkles only show where the light is.

The inline JS only writes custom properties (`--x1`…`--y8`), toggle
`--glow`, and position the sparkle elements; all rendering is CSS. The rAF chain
stops once the trail settles, and the twinkle animations are
`animation-play-state: paused` until the `.is-on` class is set.

Knobs on `.grid` in `styles.css`: `--cell` (dot pitch, 14px desktop / 12px
mobile) and `--spot` (reveal radius, 185px). Dot colour and the sparkle size sit
on `.grid::after` and `.sparks span`. Dots are drawn with
`radial-gradient(colour 0.9px, transparent 1.4px)` — the 0.5px gap between stops
keeps the edge soft instead of aliased, so change both numbers together.

The effect is skipped entirely when `(hover: hover)` does not match and when
`prefers-reduced-motion: reduce` is set. Since nothing renders without a
pointer, touch devices get the plain background and no listeners.

### One caveat with the WebP fallback

The dots render behind the phone mockups, and `phones.webp` has an alpha channel
so dots show through around the bezels. The `phones.jpg` fallback is flattened
onto `#111`, so on browsers without WebP (~2–3%) the dots are clipped to a
rectangle when the cursor passes over the mockups. Fixing it properly needs a
transparent fallback, which means a 308 KB PNG — not worth it for that share of
traffic.

## How the scaling works

`styles.css` keeps Figma's raw pixel values as literals and scales them as a
group through one custom property, `--u` — "one design pixel". It is capped at
`1px`, so the design never blows up past 100%, and shrinks below that to fit
narrow viewports. Two breakpoints only:

- **≥ 860px** — side by side, as designed. `--u` derives from the 802.42px the
  two columns need.
- **< 860px** — stacked, phones above the lockup. `--u` derives from the copy
  column alone, and the mockups ease to 78% to keep everything above the fold.

This is why there is no per-breakpoint retuning of type sizes or gaps: change
`--u` and the whole composition moves together, holding Figma's proportions.

## Making the pill do something

`.badge` is deliberately inert, matching the design. If you later want it to
capture interest, swap the `<p class="badge">` for an anchor or a form — the
styles need no change:

```html
<a class="badge" href="mailto:hello@YOUR-DOMAIN.com">Get notified</a>
```

Add `text-decoration: none` and a `:hover` state if you do.
