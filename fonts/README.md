# Brand typeface — At Hauss VAR

`AtHaussVARVF.woff2` (86 KB) — the variable master from Arillatype.Studio,
byte-for-byte as delivered.

## Weights are not on the usual 100s

Read the `fvar` named instances before assuming CSS weight numbers:

```
Air 100   Thin 190   Light 282   Retina 375   Regular 421
Medium 500   Semibold 585   Bold 665   Black 827   Super 900
```

**"Regular" is `wght: 421`, not 400.** `font-weight: 400` lands between Retina
and Regular and renders lighter than the design. `styles.css` exposes
`--wght-regular: 421` and `--wght-medium: 500` for this reason.

The `ital` axis is also non-standard — it runs 0–10, not 0–1. `font-style:
normal` in the `@font-face` pins it to 0.

## If you ever subset this file, do not narrow the wght axis

Clamping `wght` to just the 400–500 the page uses looks like free savings and
**breaks rendering silently**. Narrowing the range moves `fvar`'s default off
500, and Chrome then renders the tagline at the wrong weight: bolder, ~8px
wider, and wrapping to two lines. The font file itself measures correctly in
fontTools, so this only reproduces in a browser.

Pinning the unused `ital` axis and cutting coverage to printable ASCII is the
safe pair of optimisations — that alone takes 86 KB to about 25 KB.

## The metric-matched fallback

`styles.css` declares a second `@font-face` named `At Hauss Fallback` mapping to
local Helvetica/Arial with `size-adjust: 98.9%` and At Hauss's own vertical
metrics (`ascent-override: 95%`, `descent-override: 19%`, from upem 1000 /
ascender 950 / descender −190).

This is not cosmetic. The tagline is 403.2px of At Hauss inside a 404px column
but 407.6px of plain Helvetica — so without the adjustment, a slow font load
flashes a **two-line tagline** and then reflows. With it, the fallback measures
388px, stays on one line, and the baseline does not move when the real font
swaps in.

## Other delivered formats

The purchase also includes static `.otf` desktop files and static
`.woff`/`.woff2` per weight. Two statics (Regular + Medium, ~80 KB combined)
would be marginally smaller than the variable master but need two requests, and
the variable file lets `font-weight: 421` resolve natively.
