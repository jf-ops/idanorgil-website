---
name: seo
description: Optimize HTML pages for search engines and social sharing. Use when the user wants to improve discoverability, add meta tags, implement structured data, set up Open Graph/Twitter cards, fix semantic HTML issues, or audit a page for SEO. Trigger on requests like "improve SEO", "add meta tags", "make it shareable", "structured data", "JSON-LD", "sitemap", or "search ranking".
---

# SEO Optimization

This skill guides on-page SEO for static and server-rendered HTML sites. Cover all layers: HTML meta, semantic structure, structured data, social sharing, and crawlability.

## Audit Checklist

Before making changes, audit the page for:

1. **Title tag** — unique, 50–60 chars, front-loads the primary keyword
2. **Meta description** — unique, 140–160 chars, includes a CTA or value prop
3. **Canonical URL** — `<link rel="canonical" href="…">` on every page
4. **Heading hierarchy** — one `<h1>` per page, logical `h2`→`h3` nesting
5. **Image alt text** — descriptive, not empty, not stuffed with keywords
6. **Open Graph tags** — `og:title`, `og:description`, `og:image`, `og:url`, `og:type`
7. **Twitter Card tags** — `twitter:card`, `twitter:title`, `twitter:description`, `twitter:image`
8. **Structured data** — JSON-LD `Person`, `WebSite`, `BreadcrumbList`, or `LocalBusiness` as appropriate
9. **Robots meta** — absent means "index, follow"; add only to exclude pages
10. **Hreflang** — if multi-language site
11. **Sitemap.xml** — references all indexable URLs; referenced in `robots.txt`
12. **robots.txt** — disallows only what should not be indexed

## Essential Head Tags

```html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- Primary -->
  <title>Page Title – Site Name</title>
  <meta name="description" content="140–160 char description with value prop.">
  <link rel="canonical" href="https://example.com/page/">

  <!-- Open Graph (Facebook, LinkedIn, iMessage previews) -->
  <meta property="og:type" content="website">
  <meta property="og:url" content="https://example.com/page/">
  <meta property="og:title" content="Page Title – Site Name">
  <meta property="og:description" content="140–160 char description.">
  <meta property="og:image" content="https://example.com/og-image.jpg">
  <meta property="og:image:width" content="1200">
  <meta property="og:image:height" content="630">
  <meta property="og:locale" content="en_US">
  <meta property="og:site_name" content="Site Name">

  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:site" content="@handle">
  <meta name="twitter:title" content="Page Title – Site Name">
  <meta name="twitter:description" content="140–160 char description.">
  <meta name="twitter:image" content="https://example.com/og-image.jpg">
</head>
```

**OG image requirements:** 1200×630px minimum, under 1 MB, JPG or PNG. No text within 50px of edges (gets cropped on some platforms).

## Structured Data (JSON-LD)

Always inject as `<script type="application/ld+json">` in `<head>`. Prefer JSON-LD over Microdata or RDFa — it's the cleanest and Google-preferred format.

### Person schema (personal/portfolio site)

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "Full Name",
  "url": "https://example.com",
  "image": "https://example.com/photo.jpg",
  "jobTitle": "Job Title",
  "worksFor": {
    "@type": "Organization",
    "name": "Organization Name"
  },
  "sameAs": [
    "https://linkedin.com/in/handle",
    "https://github.com/handle",
    "https://twitter.com/handle"
  ]
}
</script>
```

### WebSite schema (enables Google sitelinks search box)

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "url": "https://example.com",
  "name": "Site Name",
  "description": "Brief site description."
}
</script>
```

### BreadcrumbList schema

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    {"@type": "ListItem", "position": 1, "name": "Home", "item": "https://example.com"},
    {"@type": "ListItem", "position": 2, "name": "About", "item": "https://example.com/about/"}
  ]
}
</script>
```

## Semantic HTML Rules

- One `<h1>` per page — the primary topic, matches or closely mirrors the `<title>`
- `<main>` wraps the primary content; `<nav>`, `<header>`, `<footer>`, `<aside>` for landmarks
- `<article>` for self-contained content; `<section>` for thematic grouping with a heading
- `<a>` link text must describe the destination ("Read more about X", not "click here")
- `<img>` always has `alt`; decorative images use `alt=""`
- `<time datetime="2024-01-15">` for dates

## URL Best Practices

- Lowercase, hyphen-separated: `/about-me/` not `/AboutMe/` or `/about_me/`
- Descriptive, no query strings for public content: `/services/` not `/page?id=3`
- Trailing slash consistency — pick one and 301 the other
- Keep URLs short (under 75 chars ideally)

## Page Speed & Core Web Vitals

SEO is directly tied to Core Web Vitals (LCP, CLS, FID/INP). Key wins:

- **LCP** (Largest Contentful Paint < 2.5s): preload hero image with `<link rel="preload" as="image">`, use `loading="eager"` on hero, `loading="lazy"` on below-fold images
- **CLS** (Cumulative Layout Shift < 0.1): always set explicit `width` and `height` on `<img>` and `<video>`
- **FID/INP** (Interaction to Next Paint < 200ms): defer non-critical JS, avoid render-blocking scripts

## robots.txt Template

```
User-agent: *
Allow: /

Sitemap: https://example.com/sitemap.xml
```

## sitemap.xml Template

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://example.com/</loc>
    <lastmod>2024-01-15</lastmod>
    <changefreq>monthly</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://example.com/about/</loc>
    <lastmod>2024-01-15</lastmod>
    <changefreq>yearly</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

## Validation Tools

- **Google Rich Results Test**: test structured data
- **Open Graph Debugger** (Meta): validate OG tags
- **Twitter Card Validator**: validate Twitter cards
- **PageSpeed Insights**: Core Web Vitals audit
- **Google Search Console**: indexing status and errors
