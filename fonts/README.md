# Brand typeface — At Hauss VAR

`AtHaussVARVF.woff2` (86 KB) — the variable master as delivered by
**Arillatype.Studio**, byte-for-byte unmodified. Licensed under their
All-inclusive EULA v1.1.

## Do not subset, instance, or otherwise modify this file

The EULA's *General restrictions* are explicit:

> You must not modify, adapt, translate, reverse engineer, decompile,
> disassemble, edit, reformat, alter, or otherwise attempt to discover the
> source code of the Fonts or the designs embodied therein. This includes you
> must not create derivative works of the Fonts.

Subsetting with `pyftsubset`, pinning an axis with `fontTools`' instancer, or
re-compressing the woff2 all produce a derivative work and breach this. *Term*
says the contract terminates automatically on breach, and Arillatype reserves
the right to charge for a licence covering actual usage per infringement.

So the 86 KB ships whole. That is the cost of the licence terms, not an
oversight — do not "optimise" it.

## What the licence does allow

*All-inclusive licence scope* grants a non-exclusive, non-transferable,
perpetual, worldwide licence covering "any kind of project developed by the
licensee on its behalf… desktop, **web**, app, game, or broadcast", and lists
`.woff`/`.woff2` among the delivered formats. Self-hosting via `@font-face` is
squarely within it — no separate webfont licence is needed.

Two constraints worth carrying forward:

- **"You must not use the Fonts in projects for other entities."** Confirm the
  licensee named at checkout is the entity that owns this site. If the licence
  was bought under one company and SecondShelf is a separate legal entity, that
  needs sorting before launch.
- **Redistribution.** Serving the file to browsers is inherent to the licensed
  web use. Publishing it in a public repository is not — keep the repo private.
  Sending copies to a contractor is permitted only on the licensee's behalf, and
  they must agree to the EULA first and destroy their copies afterwards.

## Weights

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

## Other delivered formats

The full purchase also includes static `.otf` desktop files and static
`.woff`/`.woff2` per weight. Two statics (Regular + Medium, ~80 KB combined)
would be marginally smaller than the variable master but need two requests, and
the variable file lets `font-weight: 421` resolve natively. Either is compliant
as long as the files are used unmodified.
