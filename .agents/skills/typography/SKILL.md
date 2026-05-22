---
name: typography
description: Choose fonts, build type systems, and set type beautifully for the web. Use when the user needs to pick fonts, pair typefaces, build a type scale, improve readability, or set up web font loading. Trigger on "font", "typeface", "typography", "type scale", "font pairing", "readability", "line height", "font loading", or "what font should I use".
---

# Web Typography

This skill covers the full arc of web typography: choosing fonts, pairing them, building a scale, setting readable body text, loading fonts efficiently, and the specific considerations for professional service sites.

## Choosing Fonts

### What makes a font right for a context

Ask these questions before picking:

1. **Tone** — Formal or approachable? Clinical or warm? Authoritative or friendly? A psychologist's site needs warmth and trust; a law firm needs formality.
2. **Role** — Display (headlines, large sizes) or text (body copy, small sizes)? A font that's beautiful at 72px can be unreadable at 16px.
3. **Distinctiveness** — Does it stand out from competitors while still fitting the industry? Avoid fonts so common they feel invisible.
4. **Optical size** — Some fonts have optical sizing variants (`wght` axis or separate opsz axis) that improve rendering at small sizes.

### Fonts to use (and why)

**For warmth + professionalism (healthcare, therapy, counselling):**
- **Playfair Display** (display) + **Source Serif 4** (body) — editorial authority, warm serif
- **Cormorant Garamond** (display) + **Lato** (body) — refined, human, contrast between styles
- **Fraunces** (display) + **DM Sans** (body) — distinctive optical display font, clean modern sans for body
- **Libre Baskerville** (display + body) — classic, trustworthy, readable at body sizes

**For clean minimalism (tech, consultancy, portfolio):**
- **Cabinet Grotesk** + **Satoshi** — both from Fontshare; clean, humanist, distinctive
- **Neue Haas Grotesk** / **Aktiv Grotesk** — professional Swiss-style grotesque
- **Inter** (only if you need maximum neutrality and screen optimisation) — overused but genuinely excellent for UI-heavy interfaces

**For editorial character:**
- **Recoleta** (display) + **Nunito** (body) — friendly, rounded, inviting
- **Melodrama** (display) + **General Sans** — bold display with clean body
- **Sentient** + **Switzer** — both Fontshare; geometric warmth

### Where to find good fonts (free)

| Source | Best for | Notes |
|---|---|---|
| **Fontshare** (fontshare.com) | High-quality, feels paid | Free for web; self-host |
| **Google Fonts** | Widest selection | Self-host via fontsource.org to avoid GDPR/privacy issues |
| **Bunny Fonts** | Google Fonts, privacy-safe | EU-hosted Google Fonts alternative |
| **Font Squirrel** | Fully licensed for commercial use | Wide range, download and self-host |
| **The League of Moveable Type** | Open-source quality fonts | Small but curated |

## Font Pairing

### The contrast principle

Pairs work when there is **clear contrast** on at least one axis:

| Axis | Example |
|---|---|
| Structure: serif + sans-serif | Playfair Display + Source Sans |
| Weight: light display + regular body | Thin headline + Regular body |
| Personality: expressive display + neutral body | Decorative heading + clean paragraph |
| Era: classic + modern | Old-style serif + geometric sans |

**Avoid pairing two fonts that are similar** — two humanist sans-serifs, two geometric serifs. They'll clash without contrasting.

### Reliable pairing patterns

```css
/* Pattern 1: Editorial serif display + humanist sans body */
--font-display: 'Playfair Display', Georgia, serif;
--font-body: 'Source Sans 3', system-ui, sans-serif;

/* Pattern 2: Expressive variable + neutral system */
--font-display: 'Fraunces', serif;
--font-body: 'DM Sans', sans-serif;

/* Pattern 3: Single typeface family, weight contrast only */
--font-display: 'Cabinet Grotesk', sans-serif; /* 800 weight */
--font-body: 'Cabinet Grotesk', sans-serif;    /* 400 weight */
```

Single-family pairings (one typeface, different weights) are always safe and often elegant.

## Type Scale

Use a modular scale — each step multiplies the previous by a fixed ratio. Common ratios:

| Ratio | Name | Use case |
|---|---|---|
| 1.125 | Major Second | Tight, dense UIs |
| 1.25 | Major Third | Most websites |
| 1.333 | Perfect Fourth | Editorial, spacious |
| 1.5 | Perfect Fifth | Large display-heavy |

**Fluid type with `clamp()`** — scales smoothly between viewport sizes:

```css
:root {
  /* clamp(min, preferred, max) */
  /* preferred: viewport-relative value that hits min at ~375px and max at ~1280px */

  --text-xs:   clamp(0.75rem,  0.7rem + 0.25vw,  0.875rem);
  --text-sm:   clamp(0.875rem, 0.8rem + 0.375vw, 1rem);
  --text-base: clamp(1rem,     0.9rem + 0.5vw,   1.125rem);
  --text-lg:   clamp(1.125rem, 1rem + 0.625vw,   1.25rem);
  --text-xl:   clamp(1.25rem,  1.1rem + 0.75vw,  1.5rem);
  --text-2xl:  clamp(1.5rem,   1.3rem + 1vw,     2rem);
  --text-3xl:  clamp(1.875rem, 1.5rem + 1.875vw, 2.5rem);
  --text-4xl:  clamp(2.25rem,  1.75rem + 2.5vw,  3.5rem);
  --text-5xl:  clamp(3rem,     2.25rem + 3.75vw, 5rem);
}
```

Use the **Utopia fluid type calculator** (utopia.fyi) to generate these values — specify your min/max viewport and min/max font size for each step.

## Readable Body Text

Body text readability is the most important and most neglected part of web typography.

```css
body {
  font-size: clamp(1rem, 0.9rem + 0.5vw, 1.125rem); /* 16–18px */
  line-height: 1.6;       /* 1.5–1.8 for body; never below 1.4 */
  max-width: 65ch;        /* 60–75ch is the optimal line length */
  font-weight: 400;
  letter-spacing: -0.01em; /* slight tightening for display fonts; 0 for body */
  font-optical-sizing: auto;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
}

h1, h2, h3 {
  line-height: 1.1;       /* Tighter for headings */
  letter-spacing: -0.02em; /* Tighten large type */
  text-wrap: balance;     /* Prevents awkward single-word last lines */
}

p {
  text-wrap: pretty;      /* Avoids orphans in paragraph text */
}
```

**Critical readability rules:**
- Body text: 16–18px minimum, never smaller
- Line length: 60–75 characters (use `max-width: 65ch` on the text container)
- Line height: 1.5–1.8 for body; 1.1–1.3 for headings
- Never use light weight (100–300) for body copy — use 400 or 500
- Avoid ALL CAPS for body text; use sparingly for labels/captions

## Font Loading

Bad font loading causes layout shift and flash of unstyled text (FOUT). Do it correctly:

```html
<head>
  <!-- 1. Preload the fonts you'll use above the fold -->
  <link rel="preload" href="/fonts/heading.woff2" as="font" type="font/woff2" crossorigin>
  <link rel="preload" href="/fonts/body.woff2" as="font" type="font/woff2" crossorigin>
</head>
```

```css
/* 2. Declare @font-face before any usage */
@font-face {
  font-family: 'Heading';
  src: url('/fonts/heading.woff2') format('woff2');
  font-weight: 700;
  font-style: normal;
  font-display: swap;       /* Show fallback immediately; swap when loaded */
}

@font-face {
  font-family: 'Body';
  src: url('/fonts/body-400.woff2') format('woff2');
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}

/* 3. Tune fallback metrics to reduce layout shift on swap */
@font-face {
  font-family: 'Body-fallback';
  src: local('Georgia');
  size-adjust: 103%;
  ascent-override: 90%;
  descent-override: 22%;
}

body {
  font-family: 'Body', 'Body-fallback', Georgia, serif;
}
```

Use the **Fallback Font Generator** (screenspan.com/blog/font-fallback) to find the right `size-adjust` and metric overrides for your specific font.

**Subset your fonts.** A full Latin font is 100–400KB. A subset with only the characters you use is 10–30KB. Use **glyphhanger** or **pyftsubset**, or download pre-subsetted files from Fontshare.

**Only declare the weights you use.** Each weight is a separate file. If you only use 400 and 700, don't declare 300, 500, 600, 800, 900.

## Variable Fonts

Variable fonts encode multiple weights/styles in one file, often smaller than two separate static files.

```css
@font-face {
  font-family: 'Fraunces';
  src: url('/fonts/fraunces-variable.woff2') format('woff2-variations');
  font-weight: 100 900;        /* Declare the full range */
  font-style: normal;
  font-display: swap;
}

h1 {
  font-weight: 800;            /* Any value in the declared range */
  font-variation-settings: 'SOFT' 100, 'WONK' 1; /* Custom axes if available */
}
```

Check available axes at **v-fonts.com** or **fonts.google.com/variablefonts**.

## Typography for Professional Service Sites

Trust-building sites (healthcare, legal, therapy, consultancy) have specific needs:

- **Serifs signal authority and permanence** — a serif heading font increases perceived expertise and trustworthiness compared to a sans-serif at the same size
- **Warmth comes from humanist letterforms** — avoid geometric sans-serifs (Futura, Circular) which feel cold; prefer humanist (Gill Sans, Lato, Source Sans) or old-style serifs
- **Don't compromise readability for aesthetics** — patients and anxious visitors will abandon hard-to-read text; body copy at 17–18px with 1.6 line-height is non-negotiable
- **Contrast is accessibility** — text/background contrast ≥ 4.5:1 is both a legal requirement and better for older readers common in healthcare contexts
- **Avoid decorative fonts for body** — cursive, handwritten, display fonts are for single words or short headings only

## Quick Reference

```css
:root {
  /* Fonts */
  --font-heading: 'Playfair Display', Georgia, serif;
  --font-body: 'Source Sans 3', system-ui, sans-serif;

  /* Scale (Major Third × fluid) */
  --text-sm:   clamp(0.875rem, 0.83rem + 0.23vw, 1rem);
  --text-base: clamp(1rem,     0.95rem + 0.28vw, 1.125rem);
  --text-lg:   clamp(1.25rem,  1.16rem + 0.43vw, 1.5rem);
  --text-xl:   clamp(1.563rem, 1.41rem + 0.76vw, 2rem);
  --text-2xl:  clamp(1.953rem, 1.72rem + 1.17vw, 2.5rem);
  --text-3xl:  clamp(2.441rem, 2.02rem + 2.11vw, 3.5rem);

  /* Readability */
  --leading-tight: 1.1;
  --leading-snug:  1.3;
  --leading-normal: 1.6;
  --leading-loose:  1.8;
  --measure: 65ch;
}
```
