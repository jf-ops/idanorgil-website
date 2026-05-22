---
name: svg-illustration
description: Create, optimize, and use SVG graphics — icons, spot illustrations, decorative shapes, and backgrounds. Use when the user wants to draw SVG, create custom icons, add illustrated elements to a page, optimize an SVG file, or animate SVG. Trigger on "SVG", "icon", "illustration", "vector", "draw", "blob shape", "decorative", "background shape", "SVG animation", or "inline SVG".
---

# SVG Illustration

This skill covers creating SVG from scratch in code, building icon systems, drawing decorative elements, optimizing SVG files, and animating SVG for the web.

## SVG Fundamentals

SVG is an XML-based vector format. Every SVG is a coordinate system — by default, 1 unit = 1px, but the `viewBox` decouples the internal coordinate system from the rendered size.

```html
<svg
  xmlns="http://www.w3.org/2000/svg"
  viewBox="0 0 24 24"        <!-- internal coordinate space: 24×24 units -->
  width="48"                  <!-- rendered width: 48px -->
  height="48"                 <!-- rendered height: 48px -->
  fill="none"
  aria-hidden="true"          <!-- decorative: hide from screen readers -->
>
  <path d="M12 2L2 22h20L12 2z" stroke="currentColor" stroke-width="2"/>
</svg>
```

**Key attributes:**
- `viewBox="minX minY width height"` — defines the coordinate system; always set this
- `fill` — the fill color; `fill="none"` for stroke-only icons
- `stroke` — stroke color; `stroke="currentColor"` inherits CSS `color`
- `currentColor` — inherits from the element's CSS `color` property — use this instead of hardcoded colors for icons that need to adapt to theme

## SVG Shapes

```svg
<!-- Rectangle -->
<rect x="10" y="10" width="80" height="60" rx="8"/>

<!-- Circle -->
<circle cx="50" cy="50" r="40"/>

<!-- Ellipse -->
<ellipse cx="50" cy="50" rx="40" ry="25"/>

<!-- Line -->
<line x1="10" y1="10" x2="90" y2="90" stroke="currentColor" stroke-width="2"/>

<!-- Polyline (open path through points) -->
<polyline points="10,10 50,50 90,10" fill="none" stroke="currentColor"/>

<!-- Polygon (closed path through points) -->
<polygon points="50,10 90,90 10,90"/>

<!-- Path (most powerful — draws anything) -->
<path d="M10 10 L90 10 L90 90 L10 90 Z"/>
```

## The Path Element

`<path d="…">` is the core of SVG. The `d` attribute is a series of commands:

| Command | Meaning | Example |
|---|---|---|
| `M x y` | Move to (start) | `M 10 20` |
| `L x y` | Line to | `L 50 80` |
| `H x` | Horizontal line | `H 90` |
| `V y` | Vertical line | `V 50` |
| `C cx1 cy1 cx2 cy2 x y` | Cubic bezier | `C 20 0, 80 0, 50 100` |
| `Q cx cy x y` | Quadratic bezier | `Q 50 0, 100 100` |
| `A rx ry rot large sweep x y` | Arc | `A 30 30 0 0 1 80 50` |
| `Z` | Close path | `Z` |

Lowercase commands are relative to the current position; uppercase are absolute.

## Icon Patterns

### Single-color icon (inherits text color)

```html
<svg viewBox="0 0 24 24" width="20" height="20" fill="none" aria-hidden="true">
  <path
    d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"
    stroke="currentColor"
    stroke-width="2"
    stroke-linecap="round"
    stroke-linejoin="round"
  />
</svg>
```

### Two-tone icon

```html
<svg viewBox="0 0 24 24" width="24" height="24" aria-hidden="true">
  <!-- Background fill -->
  <rect x="2" y="2" width="20" height="20" rx="4" fill="var(--color-primary-bg)"/>
  <!-- Icon stroke -->
  <path d="M8 12h8M12 8v8" stroke="var(--color-primary)" stroke-width="2" stroke-linecap="round"/>
</svg>
```

### Accessible icon button

```html
<!-- When the icon IS the button label -->
<button type="button" aria-label="Close">
  <svg viewBox="0 0 24 24" width="20" height="20" fill="none" aria-hidden="true" focusable="false">
    <path d="M18 6L6 18M6 6l12 12" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
  </svg>
</button>

<!-- When the icon accompanies text -->
<button type="button">
  <svg viewBox="0 0 24 24" width="16" height="16" fill="none" aria-hidden="true" focusable="false">
    <!-- icon paths -->
  </svg>
  Send message
</button>
```

## Decorative Elements

### Blob shapes

Organic blob shapes soften professional sites and add warmth. Use bezier curves with asymmetric handles:

```html
<svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">
  <path fill="var(--color-primary-bg)" d="
    M 40,100
    C 40,60 60,20 100,20
    C 140,20 170,50 170,90
    C 170,130 150,170 110,175
    C 70,180 40,140 40,100
    Z
  "/>
</svg>
```

Use the **Blobmaker** (blobmaker.app) or **Blob generator** (blobs.app) to generate random blob paths. Copy the `d` attribute and customize colors/size.

### Abstract background shapes

```html
<!-- Floating circle accent -->
<svg
  class="bg-shape"
  viewBox="0 0 400 400"
  aria-hidden="true"
  style="position:absolute; top:-100px; right:-80px; width:400px; opacity:0.12; pointer-events:none;"
>
  <circle cx="200" cy="200" r="200" fill="var(--color-primary)"/>
</svg>
```

```css
/* CSS-positioned SVG backgrounds */
.hero {
  position: relative;
  overflow: hidden;
}

.hero::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath fill='%23f0f4f0' d='M40,100 C40,60 70,20 110,25 C150,30 170,70 165,110 C160,150 130,175 90,170 C50,165 40,140 40,100Z'/%3E%3C/svg%3E");
  background-repeat: no-repeat;
  background-position: top right;
  background-size: 50%;
  pointer-events: none;
}
```

### Wavy dividers

```html
<!-- Between sections instead of a straight line -->
<svg viewBox="0 0 1440 80" preserveAspectRatio="none" aria-hidden="true"
  style="display:block; width:100%; height:80px;">
  <path
    d="M0,40 C240,80 480,0 720,40 C960,80 1200,0 1440,40 L1440,80 L0,80 Z"
    fill="var(--color-bg-subtle)"
  />
</svg>
```

### Dot grid pattern

```html
<svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <pattern id="dots" x="0" y="0" width="24" height="24" patternUnits="userSpaceOnUse">
      <circle cx="2" cy="2" r="1.5" fill="var(--color-border)"/>
    </pattern>
  </defs>
  <rect width="100%" height="100%" fill="url(#dots)"/>
</svg>
```

## SVG Illustration Style

For a consistent illustrative style across a site:

1. **Consistent stroke weight** — pick one stroke width (e.g., 1.5 or 2) and never deviate
2. **Limited palette** — use 2–3 colors from your brand palette; never more than 5
3. **Consistent corner style** — always rounded (`stroke-linecap="round" stroke-linejoin="round"`) or always sharp
4. **Consistent fill style** — all filled, all outlined, or all duotone — never mix
5. **Consistent proportions** — set a grid (24×24 for icons, 200×200 for spots, 400×300 for hero)

For a professional service site, use **outlined + one fill accent**:

```html
<!-- Spot illustration: person at desk -->
<svg viewBox="0 0 120 120" fill="none" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
  <!-- Accent fill shape (background blob) -->
  <ellipse cx="60" cy="70" rx="45" ry="35" fill="var(--sage-100)"/>
  <!-- Desk -->
  <rect x="15" y="75" width="90" height="6" rx="3" fill="var(--stone-200)"/>
  <!-- Person outline -->
  <circle cx="60" cy="40" r="14" stroke="var(--sage-700)" stroke-width="2"/>
  <path d="M35 75 C35 58 45 50 60 50 C75 50 85 58 85 75"
        stroke="var(--sage-700)" stroke-width="2" stroke-linecap="round"/>
</svg>
```

## SVG Animation

### CSS animation on SVG elements

```css
/* Fade in on load */
.hero-illustration {
  animation: fadeUp 0.6s ease-out both;
}

@keyframes fadeUp {
  from { opacity: 0; transform: translateY(12px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* Continuous float */
.floating-shape {
  animation: float 4s ease-in-out infinite;
}

@keyframes float {
  0%, 100% { transform: translateY(0); }
  50%       { transform: translateY(-12px); }
}
```

### Stroke draw-on animation

```css
.icon path {
  stroke-dasharray: 100;     /* must be ≥ path length */
  stroke-dashoffset: 100;
  animation: draw 0.6s ease-out forwards;
}

@keyframes draw {
  to { stroke-dashoffset: 0; }
}
```

Get the path length with JS: `path.getTotalLength()`. Then set `stroke-dasharray` to that value.

### Morph between two paths (same number of points required)

```html
<path id="blob">
  <animate
    attributeName="d"
    dur="8s"
    repeatCount="indefinite"
    values="
      M40,100 C40,60 70,20 110,25 C150,30 165,70 160,110 C155,150 125,175 85,170 C45,165 40,140 40,100Z;
      M50,95  C45,55 75,15 115,22 C155,29 168,68 162,112 C156,156 120,178 80,172 C40,166 55,135 50,95Z;
      M40,100 C40,60 70,20 110,25 C150,30 165,70 160,110 C155,150 125,175 85,170 C45,165 40,140 40,100Z
    "
  />
</path>
```

## Optimization (SVGO)

Always optimize SVG before shipping. Raw exports from Figma/Illustrator include junk.

```bash
npx svgo input.svg -o output.svg
```

SVGO removes: editor metadata, redundant attributes, unnecessary precision, empty groups, default values. Typical savings: 20–60%.

**Manual cleanup checklist:**
- Remove `id` attributes that aren't referenced
- Remove `data-name` and editor-specific attributes
- Replace hardcoded color values with `currentColor` or CSS variables
- Remove `<title>` tags unless the SVG is standalone (not decorative)
- Ensure `viewBox` is set and `width`/`height` on the `<svg>` element is handled by CSS, not attributes (for responsive sizing)

## Inline vs External SVG

| Method | Use when | How |
|---|---|---|
| **Inline `<svg>`** | Need to target with CSS/JS, animate, or use `currentColor` | Paste SVG code directly in HTML |
| **`<img src="icon.svg">`** | Simple decorative image, no interaction needed | `<img src="/images/hero.svg" alt="">` |
| **CSS `background-image`** | Decorative pattern or background shape | URL-encode and embed, or reference file |
| **SVG sprite** | Many icons used across pages | `<use href="/icons.svg#icon-name">` |

**SVG sprite pattern** for icon systems:

```html
<!-- icons.svg (hidden, placed once at top of <body>) -->
<svg xmlns="http://www.w3.org/2000/svg" hidden>
  <symbol id="icon-arrow" viewBox="0 0 24 24">
    <path d="M5 12h14M12 5l7 7-7 7" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
  </symbol>
  <symbol id="icon-mail" viewBox="0 0 24 24">
    <path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z" stroke="currentColor" stroke-width="2"/>
    <polyline points="22,6 12,13 2,6" stroke="currentColor" stroke-width="2"/>
  </symbol>
</svg>

<!-- Usage anywhere on the page -->
<svg width="20" height="20" aria-hidden="true">
  <use href="#icon-arrow"/>
</svg>
```
