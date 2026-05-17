---
name: color
description: Choose color palettes, apply color theory, and build color systems for websites. Use when the user needs to pick colors, create a palette from scratch, set up CSS color tokens, understand color psychology, or check contrast. Trigger on "color palette", "color scheme", "what colors should I use", "color theory", "brand colors", "color tokens", "oklch", or "color psychology".
---

# Color for the Web

This skill covers choosing colors from scratch, building a coherent palette, encoding it as CSS custom properties, and applying color psychology for professional contexts.

## Before Picking Colors

Answer three questions first:

1. **What feeling should the site create?** — Trust, warmth, calm, energy, authority, playfulness? Colors are the fastest way to set emotional tone.
2. **Who is the audience?** — Anxious patients? Young professionals? Business clients? Color perception varies by age, culture, and context.
3. **What is the brand already?** — Existing logo, photography, or visual materials constrain the palette. Extract the dominant color from the logo and build from there.

## Color Psychology for Professional Sites

| Color family | Associations | Good for |
|---|---|---|
| **Blue** (cool, mid-saturation) | Trust, calm, professional, safe | Healthcare, finance, tech, legal |
| **Green** (natural, desaturated) | Growth, health, calm, reassurance | Mental health, wellness, environment |
| **Warm neutrals** (beige, sand, stone) | Approachable, grounded, human | Therapy, coaching, healthcare |
| **Terracotta / dusty rose** | Warmth, empathy, femininity | Counselling, women's health, beauty |
| **Deep navy or charcoal** | Authority, seriousness, expertise | Legal, consultancy, finance |
| **Warm white / cream** | Clean, open, non-clinical | Healthcare wanting to avoid hospital feel |

**Avoid for trust-building sites:** aggressive reds (urgency/alarm), pure black backgrounds (harsh), neon or highly saturated colors (frivolous), purple gradients (overused generic AI aesthetic).

## Building a Palette

A practical website palette has 5 layers:

```
Brand primary    — the main recognizable color (used for CTAs, links, accents)
Brand secondary  — a supporting color (used for section backgrounds, hover states)
Neutrals         — grays or warm-tinted whites/blacks for text and backgrounds
Semantic         — success (green), warning (amber), error (red), info (blue)
Surface          — page background, card background, subtle dividers
```

### Step 1: Start with one anchor color

Pick the primary color that defines the brand. Useful starting points:
- Extract from the logo
- Choose based on color psychology for the industry
- Use a hue that competitors don't (differentiation)

### Step 2: Build tints and shades

For each anchor color, generate a scale from light to dark (10 steps is standard):

```css
/* Using OKLCH — perceptually uniform, predictable lightness steps */
:root {
  --teal-50:  oklch(97% 0.03 195);
  --teal-100: oklch(93% 0.05 195);
  --teal-200: oklch(86% 0.08 195);
  --teal-300: oklch(77% 0.11 195);
  --teal-400: oklch(67% 0.13 195);
  --teal-500: oklch(56% 0.14 195);  /* anchor / mid */
  --teal-600: oklch(46% 0.13 195);
  --teal-700: oklch(38% 0.11 195);
  --teal-800: oklch(29% 0.08 195);
  --teal-900: oklch(20% 0.05 195);
  --teal-950: oklch(13% 0.03 195);
}
```

Use the **OKLCH color picker** (oklch.com) to build perceptually-even scales. OKLCH is far better than HSL for this: equal numeric lightness steps actually look equal to the eye.

### Step 3: Choose the relationship (harmony)

| Harmony | Formula | Feel |
|---|---|---|
| **Monochromatic** | Same hue, vary L and C | Calm, cohesive, sophisticated |
| **Analogous** | Adjacent hues (±30°) | Natural, harmonious, low contrast |
| **Complementary** | Opposite hue (±180°) | High energy, strong contrast |
| **Split-complementary** | One color + two adjacent to its complement | Balance of energy and harmony |
| **Triadic** | Three evenly spaced hues (120° apart) | Vibrant, playful |

For professional service sites: **monochromatic or analogous**. They feel calm and unified. Reserve complementary for CTAs only (a warm accent against a cool primary).

### Step 4: Define semantic and surface colors

```css
:root {
  /* Semantic — use standard hue conventions */
  --color-success: oklch(62% 0.17 145);  /* green */
  --color-warning: oklch(75% 0.18 65);   /* amber */
  --color-error:   oklch(58% 0.22 25);   /* red */
  --color-info:    oklch(60% 0.16 240);  /* blue */
}
```

## CSS Color System

Separate raw palette values from semantic usage tokens:

```css
/* ─── Palette (raw values — rarely used directly in components) ─── */
:root {
  --sage-50:  oklch(97% 0.02 155);
  --sage-100: oklch(93% 0.04 155);
  --sage-200: oklch(86% 0.07 155);
  --sage-500: oklch(55% 0.10 155);
  --sage-700: oklch(38% 0.08 155);
  --sage-900: oklch(22% 0.05 155);

  --stone-50:  oklch(97% 0.01 80);
  --stone-200: oklch(88% 0.02 80);
  --stone-500: oklch(60% 0.03 80);
  --stone-700: oklch(42% 0.02 80);
  --stone-900: oklch(20% 0.01 80);

  --rust-400: oklch(68% 0.16 40);
  --rust-600: oklch(50% 0.18 40);
}

/* ─── Semantic tokens (use these in components) ─── */
:root {
  --color-bg:           var(--stone-50);
  --color-bg-subtle:    var(--stone-200);
  --color-surface:      oklch(100% 0 0);    /* pure white card */
  --color-border:       var(--stone-200);
  --color-border-focus: var(--sage-500);

  --color-text:         var(--stone-900);
  --color-text-muted:   var(--stone-700);
  --color-text-subtle:  var(--stone-500);
  --color-text-inverse: oklch(100% 0 0);

  --color-primary:      var(--sage-500);
  --color-primary-dark: var(--sage-700);
  --color-primary-bg:   var(--sage-50);

  --color-accent:       var(--rust-600);
  --color-accent-hover: var(--rust-400);
}
```

**Rule:** Components reference semantic tokens (`--color-primary`), never raw palette values (`--sage-500`). This makes dark mode and rebranding trivial.

## Dark Mode

```css
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg:           var(--stone-900);
    --color-bg-subtle:    oklch(18% 0.01 80);
    --color-surface:      oklch(22% 0.01 80);
    --color-border:       oklch(28% 0.01 80);
    --color-text:         var(--stone-50);
    --color-text-muted:   var(--stone-200);
    --color-text-subtle:  var(--stone-500);

    /* Primary typically needs a lighter variant in dark mode for contrast */
    --color-primary:      var(--sage-200);
    --color-primary-dark: var(--sage-100);
    --color-primary-bg:   var(--sage-900);
  }
}
```

Dark mode isn't just inverting colors. Surfaces should get lighter as they get higher (like Material Design's elevation system). Never use pure black `#000` — use a very dark neutral with a hint of the brand hue.

## Contrast Checking

WCAG 2.1 AA requirements (legal standard):
- Normal text (< 18pt / < 14pt bold): **4.5:1**
- Large text (≥ 18pt / ≥ 14pt bold): **3:1**
- UI components and graphical objects: **3:1**

WCAG 3 / APCA (emerging standard) uses a different, more perceptually accurate algorithm. Start checking with APCA contrast if your tooling supports it.

```css
/* Quick contrast check: does this text color work on this background? */
/* Use: https://webaim.org/resources/contrastchecker/ */
/* Or Chrome DevTools → Inspect element → color picker shows contrast ratio */

/* Common failure patterns: */
--color-text-muted on --color-bg-subtle  /* light gray on light gray */
--color-primary on white                  /* mid-saturation colors often fail 4.5:1 */
placeholder text in inputs               /* typically too light; needs explicit check */
```

**Tip:** Build your palette with contrast baked in. In an OKLCH scale, L=45–55 on white background typically passes AA for large text; L=35–45 passes for normal text. Check early, not after the fact.

## Palette for a Danish Professional Service Site

A starting point for a psychology/healthcare practice:

```css
:root {
  /* Warm sage green primary — calm, growth, health */
  --sage-50:  oklch(97% 0.015 150);
  --sage-100: oklch(94% 0.030 150);
  --sage-500: oklch(54% 0.095 150);   /* primary — passes 4.5:1 on white at this L */
  --sage-700: oklch(38% 0.075 150);   /* dark variant */
  --sage-900: oklch(22% 0.045 150);

  /* Warm stone neutrals — approachable, not clinical */
  --stone-50:  oklch(97.5% 0.008 75);
  --stone-100: oklch(95%   0.012 75);
  --stone-200: oklch(91%   0.015 75);
  --stone-500: oklch(60%   0.020 75);
  --stone-700: oklch(42%   0.015 75);
  --stone-900: oklch(20%   0.010 75);

  /* Terracotta accent — warmth, human connection */
  --clay-400: oklch(66% 0.130 42);
  --clay-600: oklch(50% 0.145 42);

  /* Semantic tokens */
  --color-bg:        var(--stone-50);
  --color-text:      var(--stone-900);
  --color-text-muted: var(--stone-700);
  --color-primary:   var(--sage-500);
  --color-accent:    var(--clay-600);
}
```

## Tools

| Tool | Use |
|---|---|
| **oklch.com** | Interactive OKLCH picker + palette builder |
| **coolors.co** | Fast palette generation and exploration |
| **Palette generator** (palette.app) | Generate from an image/photo |
| **Realtime Colors** (realtimecolors.com) | Preview palette on a live UI |
| **Colorable** (jxnblk.com/colorable) | Batch contrast checker for a whole palette |
| **Huemint** (huemint.com) | ML-powered palette generation with UI preview |
| **Pigment by ShapeFactory** | Intuitive complementary palette tool |
