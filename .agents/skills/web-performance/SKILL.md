---
name: web-performance
description: Audit and improve web performance for HTML/CSS/JS sites. Use when the user wants faster load times, better Lighthouse scores, Core Web Vitals improvements, image optimization, or a performance review. Trigger on requests like "make it faster", "improve performance", "Lighthouse score", "Core Web Vitals", "LCP", "CLS", "page speed", "optimize images", or "reduce load time".
---

# Web Performance

This skill covers practical performance optimization for static HTML/CSS/JS websites. Target: Lighthouse score ≥ 90 on all categories, Core Web Vitals in the green.

## Core Web Vitals Targets

| Metric | Good | Needs Improvement | Poor |
|---|---|---|---|
| **LCP** (Largest Contentful Paint) | ≤ 2.5s | 2.5–4s | > 4s |
| **CLS** (Cumulative Layout Shift) | ≤ 0.1 | 0.1–0.25 | > 0.25 |
| **INP** (Interaction to Next Paint) | ≤ 200ms | 200–500ms | > 500ms |

Run **PageSpeed Insights** (pagespeed.web.dev) for field data from real users. Run **Lighthouse** in Chrome DevTools for lab data.

## LCP — Largest Contentful Paint

LCP is almost always the hero image or the largest heading. Fix it first.

**1. Preload the LCP resource**

```html
<!-- In <head>, before any other resource hints -->
<link rel="preload" as="image" href="/hero.webp" fetchpriority="high">

<!-- If the LCP is text, preload the font -->
<link rel="preload" as="font" type="font/woff2" href="/fonts/heading.woff2" crossorigin>
```

**2. Set `fetchpriority="high"` and `loading="eager"` on the hero image**

```html
<img
  src="/hero.webp"
  alt="…"
  width="1200"
  height="600"
  fetchpriority="high"
  loading="eager"
>
```

**3. Use modern image formats**

WebP is 25–35% smaller than JPEG at equal quality. AVIF is 50% smaller but has less browser support. Serve WebP with JPEG/PNG fallback:

```html
<picture>
  <source type="image/avif" srcset="/hero.avif">
  <source type="image/webp" srcset="/hero.webp">
  <img src="/hero.jpg" alt="…" width="1200" height="600">
</picture>
```

**4. Remove render-blocking resources**

```html
<!-- Defer non-critical CSS -->
<link rel="preload" href="/styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="/styles.css"></noscript>

<!-- Defer non-critical JavaScript -->
<script src="/analytics.js" defer></script>
<script src="/chat.js" async></script>
```

Critical CSS (above-the-fold styles) should be inlined in `<style>` in `<head>`.

## CLS — Cumulative Layout Shift

CLS is caused by elements that shift after initial paint. The most common cause is images and iframes without explicit dimensions.

**Always set `width` and `height` on images**

```html
<!-- This reserves space before the image loads, preventing layout shift -->
<img src="/photo.jpg" alt="…" width="800" height="600">
```

CSS will override the `width` and `height` attributes for responsive behavior — but the browser uses the ratio to reserve space:

```css
img {
  max-width: 100%;
  height: auto; /* maintains aspect ratio */
}
```

**Reserve space for web fonts**

Font swap causes text reflow. Minimize with `font-display: optional` for non-critical text, or use `size-adjust` to match fallback metrics:

```css
@font-face {
  font-family: 'Heading';
  src: url('/fonts/heading.woff2') format('woff2');
  font-display: swap;
  /* Optional: tune fallback metrics to reduce shift */
  size-adjust: 105%;
  ascent-override: 90%;
}
```

**Avoid injecting content above existing content** — ads, banners, cookie notices should be positioned so they don't push content down.

## Images

Image optimization is the single highest-ROI performance task on most sites.

**Resize to display dimensions**

Never serve a 3000px image in a 600px container. Resize to the maximum display size × device pixel ratio (usually 2×):
- Container is 600px wide → serve 1200px image

**Compress**

Target file sizes:
- Hero image: under 200 KB
- Regular content image: under 100 KB
- Thumbnail: under 30 KB

Tools: **Squoosh** (squoosh.app) for manual optimization, **ImageOptim** (Mac) for batch.

**Lazy load below-fold images**

```html
<!-- Add loading="lazy" to all images below the fold -->
<img src="/photo.jpg" alt="…" width="800" height="600" loading="lazy">
```

**Responsive images with srcset**

```html
<img
  src="/photo-800.webp"
  srcset="/photo-400.webp 400w, /photo-800.webp 800w, /photo-1200.webp 1200w"
  sizes="(max-width: 600px) 100vw, 800px"
  alt="…"
  width="800"
  height="600"
  loading="lazy"
>
```

## CSS

**Inline critical CSS**

Critical CSS = styles needed to render above-the-fold content. Inline in `<head>` to eliminate a render-blocking request. Load the full stylesheet asynchronously.

Rule of thumb: inline the first 10–14KB of CSS, defer the rest.

**Remove unused CSS**

Use Chrome DevTools → Coverage tab to identify unused CSS rules. For a static site with a few pages, manually audit and delete. Target: under 50KB of CSS total (uncompressed).

**Use `contain` and `content-visibility` for long pages**

```css
/* Tell the browser this section is independent — limits style/layout recalc scope */
.section {
  contain: layout style;
}

/* Skip rendering off-screen sections entirely */
.below-fold-section {
  content-visibility: auto;
  contain-intrinsic-size: 0 500px; /* estimate height to prevent CLS */
}
```

## JavaScript

**Defer all non-critical JS**

```html
<!-- defer: execute after HTML parsed, maintains order -->
<script src="/main.js" defer></script>

<!-- async: execute as soon as downloaded, order not guaranteed -->
<script src="/analytics.js" async></script>
```

**Never block the main thread**

Operations over 50ms block input. For long tasks (parsing, complex calculations), use `requestIdleCallback` or a Web Worker.

**Remove unused JavaScript**

Audit with Chrome DevTools → Coverage. Remove polyfills for modern browsers, tree-shake libraries. For a static site targeting modern browsers, you rarely need a JS framework or bundler.

## Fonts

**Self-host fonts** — no DNS lookup, better caching control, no GDPR risk from Google Fonts

```html
<link rel="preload" href="/fonts/heading.woff2" as="font" type="font/woff2" crossorigin>
```

```css
@font-face {
  font-family: 'Heading';
  src: url('/fonts/heading.woff2') format('woff2');
  font-display: swap;
  font-weight: 700;
}
```

**Subset fonts** — remove characters you don't use. A Latin subset is 50–80% smaller than the full character set. Use **glyphhanger** or **Font Squirrel Webfont Generator**.

**Limit font variations** — each weight/style is a separate file. Two fonts × two weights = 4 requests. Use variable fonts if you need multiple weights.

## Caching

For static HTML sites, set these headers on the server or CDN:

```
# Immutable assets (hashed filenames — never change)
Cache-Control: public, max-age=31536000, immutable

# HTML pages (revalidate on each visit)
Cache-Control: no-cache

# Images without hash in filename
Cache-Control: public, max-age=86400
```

Use hashed filenames for CSS/JS/images to enable `immutable` caching: `styles.a1b2c3.css`.

## Network

**Preconnect to critical third-party origins**

```html
<!-- Establish connection early for resources you'll need soon -->
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link rel="dns-prefetch" href="https://www.google-analytics.com">
```

**Compress with Brotli or gzip**

Brotli gives ~15–20% better compression than gzip. Enable on the server or CDN. Text resources (HTML, CSS, JS, SVG) compress 60–80%.

## Performance Budget

Set a budget and don't exceed it:

| Resource | Budget |
|---|---|
| Total page weight | < 1 MB |
| Images | < 600 KB |
| JS (parsed + executed) | < 150 KB |
| CSS | < 50 KB |
| Fonts | < 100 KB |
| LCP | < 2.5s on mobile 4G |

## Quick Wins Checklist

- [ ] All images have explicit `width` + `height` (CLS fix)
- [ ] Hero image has `fetchpriority="high"` and `loading="eager"`
- [ ] All below-fold images have `loading="lazy"`
- [ ] Images converted to WebP
- [ ] Images resized to max display size × 2
- [ ] Non-critical JS uses `defer` or `async`
- [ ] Fonts self-hosted with `font-display: swap`
- [ ] CSS for above-fold content inlined in `<head>`
- [ ] `<html lang="da">` set (also an accessibility requirement)
- [ ] Preload critical font(s)
