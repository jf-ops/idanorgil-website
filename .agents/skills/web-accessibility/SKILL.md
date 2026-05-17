---
name: web-accessibility
description: Audit and fix accessibility issues in HTML/CSS/JS. Use when the user wants WCAG compliance, better keyboard navigation, screen reader support, color contrast fixes, or an accessibility review. Trigger on requests like "accessibility", "a11y", "WCAG", "screen reader", "keyboard navigation", "color contrast", "ARIA", or "make it accessible".
---

# Web Accessibility (a11y)

This skill guides WCAG 2.1 AA compliance for HTML websites. AA is the legal standard in most jurisdictions and the right default target. Start with an audit, then fix in priority order.

## Quick Audit

Run these automated checks first — they catch ~30–40% of issues:
- **axe DevTools** (Chrome extension) — most accurate free tool
- **WAVE** (wave.webaim.org) — good visual overlay
- **Lighthouse** (Chrome DevTools → Lighthouse tab) — integrated, fast

Automated tools miss ~60% of issues. After automation, do a manual keyboard and screen reader test.

## WCAG 2.1 AA Checklist

### Perceivable

**Text alternatives (1.1.1)**
- Every `<img>` has `alt` text — descriptive for informational images, `alt=""` for decorative
- `<svg>` icons used as buttons/links: add `aria-label` or `<title>` inside the SVG
- CSS background images conveying information: add visually-hidden text alternative

```html
<!-- Informational image -->
<img src="portrait.jpg" alt="Ida Nørgil smiling in her office">

<!-- Decorative image -->
<img src="divider.png" alt="">

<!-- SVG icon button -->
<button aria-label="Close menu">
  <svg aria-hidden="true" focusable="false">...</svg>
</button>
```

**Color and contrast (1.4.1, 1.4.3, 1.4.11)**
- Normal text (under 18pt/14pt bold): minimum 4.5:1 contrast ratio
- Large text (18pt+ or 14pt+ bold): minimum 3:1 contrast ratio
- UI components (input borders, button outlines): minimum 3:1 against adjacent colors
- Never use color alone to convey meaning — add a text label, pattern, or icon

Check contrast: **webaim.org/resources/contrastchecker** or the eyedropper in browser DevTools.

**Resize and reflow (1.4.4, 1.4.10)**
- Text must be readable and functional at 200% zoom without horizontal scrolling
- At 320px wide viewport, all content reflows to single column (no horizontal scroll)

**Text spacing (1.4.12)**
The following overrides must not break content:
```css
/* Users may apply these via browser extension — your layout must survive them */
line-height: 1.5;
letter-spacing: 0.12em;
word-spacing: 0.16em;
paragraph spacing: 2em;
```

### Operable

**Keyboard navigation (2.1.1, 2.1.2)**
- All interactive elements reachable by Tab key
- No keyboard traps — Tab always moves focus forward
- Focus never disappears into a void (modal, dropdown, custom widget)

Test: unplug the mouse and navigate the entire page with Tab, Shift+Tab, Enter, Space, and arrow keys.

**Focus visible (2.4.7, 2.4.11 in WCAG 2.2)**
Never remove the focus outline without replacing it with something equally visible:

```css
/* Don't do this */
*:focus {
  outline: none;
}

/* Do this instead — custom focus style */
:focus-visible {
  outline: 3px solid #0066cc;
  outline-offset: 2px;
  border-radius: 2px;
}

/* Use :focus-visible to avoid showing outline on mouse click */
:focus:not(:focus-visible) {
  outline: none;
}
```

**Skip navigation (2.4.1)**
Provide a skip link as the first focusable element on every page:

```html
<a class="skip-link" href="#main-content">Skip to main content</a>

<style>
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  padding: 8px 16px;
  background: #000;
  color: #fff;
  z-index: 9999;
  transition: top 0.1s;
}
.skip-link:focus {
  top: 0;
}
</style>
```

**Page title (2.4.2)**
Each page has a unique, descriptive `<title>`: "About – Ida Nørgil" not just "About".

**Link purpose (2.4.4)**
Link text must describe the destination on its own. Screen readers list all links out of context.

```html
<!-- Bad -->
<a href="/services">Click here</a>
<a href="/contact">Read more</a>

<!-- Good -->
<a href="/services">View my services</a>
<a href="/contact">Contact Ida</a>
```

**Timing (2.2.1)**
If any content auto-updates or disappears (carousels, session timeouts), users must be able to pause, extend, or disable it.

### Understandable

**Language (3.1.1)**
Set the page language on `<html>`:
```html
<html lang="da">  <!-- Danish -->
<html lang="en">  <!-- English -->
```

**Labels (3.3.2)**
Every form input must have a visible `<label>` associated via `for`/`id`:

```html
<!-- Good -->
<label for="email">Email address</label>
<input type="email" id="email" name="email" autocomplete="email">

<!-- Bad — placeholder is not a label -->
<input type="email" placeholder="Email address">
```

**Error identification (3.3.1, 3.3.3)**
Errors must be described in text (not just color), associated with the field, and provide a suggestion for correction.

```html
<label for="email">Email address</label>
<input
  type="email"
  id="email"
  aria-describedby="email-error"
  aria-invalid="true"
>
<p id="email-error" role="alert">
  Enter a valid email address, for example name@example.com.
</p>
```

### Robust

**Valid HTML (4.1.1)**
- No duplicate `id` attributes
- All elements properly nested and closed
- Validate at **validator.w3.org**

**Name, role, value (4.1.2)**
Custom interactive elements must expose their role, state, and name to assistive technology via ARIA when native HTML elements aren't used:

```html
<!-- Prefer native HTML -->
<button type="button">Open menu</button>

<!-- When you must use a div as a button -->
<div
  role="button"
  tabindex="0"
  aria-expanded="false"
  aria-controls="menu"
  onclick="toggleMenu()"
  onkeydown="handleKey(event)"
>
  Open menu
</div>
```

**Rule:** Never use ARIA to fix bad HTML. Use ARIA to enhance good HTML.

## Common Fixes

| Issue | Fix |
|---|---|
| `outline: none` without replacement | Add `:focus-visible` style |
| Images without `alt` | Add descriptive `alt` or `alt=""` for decorative |
| Low contrast text | Darken text or lighten background to meet 4.5:1 |
| Click targets under 24px | Minimum 24×24px touch target (WCAG 2.2), preferably 44×44px |
| Links that say "click here" | Rewrite to describe the destination |
| Missing `<label>` on inputs | Add `<label for="id">` |
| No skip link | Add skip nav as first focusable element |
| No `lang` on `<html>` | Add `lang="da"` or appropriate language code |
| `<div>` used as button | Use `<button>` or add `role="button"` + `tabindex="0"` |
| No page `<title>` | Add unique `<title>` to every page |

## Screen Reader Testing

Basic test with NVDA (Windows, free) or VoiceOver (Mac, built-in):

1. Land on the page — does the page title read out?
2. Tab through — does each interactive element get a clear name?
3. Activate links/buttons — do they do what their name says?
4. Fill out forms — do labels read before the input? Errors after submission?
5. Navigate by headings (H key in NVDA) — does the heading structure make sense out of context?
