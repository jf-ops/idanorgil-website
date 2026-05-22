---
name: gdpr
description: Implement GDPR compliance for Danish and EU websites. Use when the user needs cookie consent, a privacy policy, GDPR-compliant contact forms, data retention rules, or a compliance audit. Trigger on "GDPR", "cookie consent", "privacy policy", "databeskyttelse", "cookiebekendtgørelse", "personoplysninger", "legal basis", "data subject rights", or "compliance".
---

# GDPR Compliance for Danish Websites

This skill covers practical GDPR implementation for Danish websites — including the Danish Cookie Order (Cookiebekendtgørelsen), which adds requirements on top of the EU baseline. Covers cookie consent, privacy policy, contact forms, and data subject rights.

## Danish-specific context

Denmark follows GDPR (EU 2016/679) plus the **Cookiebekendtgørelsen** (BEK nr 1148 af 09/10/2014) enforced by Erhvervsstyrelsen. Key Danish requirement: all cookies that are not strictly necessary require **prior, informed, and freely given consent** — even analytics cookies. Violations are reported to Datatilsynet.

## Cookie Consent

### What requires consent

| Category | Requires consent | Examples |
|---|---|---|
| Strictly necessary | No | Session cookies, CSRF tokens, load balancer |
| Preferences/functional | Yes | Language setting, remembered form data |
| Analytics/statistics | Yes | Google Analytics, Matomo (unless configured for exemption) |
| Marketing | Yes | Meta Pixel, Google Ads, retargeting |

Analytics cookies are **not** automatically exempt in Denmark. Erhvervsstyrelsen allows exemption only for first-party analytics configured to anonymize IPs, disable cross-site tracking, and never share data with third parties (Matomo with correct settings can qualify; GA4 typically does not without a consent mode).

### Consent requirements (GDPR Art. 7 + Cookiebekendtgørelsen)

- **Prior**: shown before cookies are set — not after page load
- **Informed**: clear description of each cookie category and purpose
- **Freely given**: rejecting must be as easy as accepting; no pre-ticked boxes; no cookie wall blocking access to content
- **Granular**: user must be able to accept/reject each category separately
- **Withdrawable**: user can change consent at any time; link in footer
- **Documented**: store a consent record (timestamp, version, choices)

### Minimal cookie banner implementation

```html
<!-- Add before closing </body> -->
<div id="cookie-banner" role="dialog" aria-labelledby="cookie-title" aria-modal="true" hidden>
  <h2 id="cookie-title">Vi bruger cookies</h2>
  <p>
    Vi bruger nødvendige cookies til at få hjemmesiden til at fungere.
    Med dit samtykke bruger vi også analysecookies for at forstå, hvordan
    siden bruges. Du kan til enhver tid ændre dit samtykke.
    <a href="/privatlivspolitik.html">Læs vores privatlivspolitik</a>.
  </p>
  <div class="cookie-actions">
    <button id="cookie-reject" type="button">Afvis ikke-nødvendige</button>
    <button id="cookie-settings" type="button">Indstillinger</button>
    <button id="cookie-accept" type="button">Accepter alle</button>
  </div>
</div>
```

```js
(function () {
  const CONSENT_KEY = 'cookie_consent';
  const CONSENT_VERSION = '1';

  function getConsent() {
    try { return JSON.parse(localStorage.getItem(CONSENT_KEY)); } catch { return null; }
  }

  function saveConsent(choices) {
    localStorage.setItem(CONSENT_KEY, JSON.stringify({
      version: CONSENT_VERSION,
      timestamp: new Date().toISOString(),
      analytics: choices.analytics,
      marketing: choices.marketing,
    }));
  }

  function applyConsent(choices) {
    if (choices.analytics) {
      // Load analytics script here
    }
    if (choices.marketing) {
      // Load marketing pixels here
    }
  }

  const consent = getConsent();
  if (!consent || consent.version !== CONSENT_VERSION) {
    document.getElementById('cookie-banner').hidden = false;
  } else {
    applyConsent(consent);
  }

  document.getElementById('cookie-accept').addEventListener('click', function () {
    const choices = { analytics: true, marketing: true };
    saveConsent(choices);
    applyConsent(choices);
    document.getElementById('cookie-banner').hidden = true;
  });

  document.getElementById('cookie-reject').addEventListener('click', function () {
    const choices = { analytics: false, marketing: false };
    saveConsent(choices);
    document.getElementById('cookie-banner').hidden = true;
  });
})();
```

**Important**: Load analytics/marketing scripts only after consent is given — never on page load unconditionally.

### Cookie banner accessibility

- Use `role="dialog"` and `aria-modal="true"`
- Trap focus inside the banner while it's visible
- Reject button must be at least as prominent as Accept
- Minimum touch target 44×44px on all buttons

## Privacy Policy (Privatlivspolitik)

Required under GDPR Art. 13/14 (information obligation). Must be written in plain language, in the language of the site's audience.

### Required sections

1. **Data controller identity** — full name, address, CVR number, email, phone
2. **Contact for data questions** — same as above if no DPO; or DPO contact if one exists
3. **What data is collected** — list each category: name, email, IP address, etc.
4. **Purpose and legal basis** for each category:
   - Contact form → legitimate interest (Art. 6(1)(f)) or consent (Art. 6(1)(a))
   - Analytics → consent (Art. 6(1)(a))
   - Booking/contract → contract performance (Art. 6(1)(b))
   - Health data (psykolog) → explicit consent (Art. 9(2)(a)) + professional secrecy
5. **Retention periods** — how long each category is kept
6. **Third parties** — who receives the data (hosting provider, email service, analytics)
7. **Transfers outside EU/EEA** — if any (e.g., US-based analytics without EU data residency)
8. **Data subject rights**:
   - Right to access (Art. 15)
   - Right to rectification (Art. 16)
   - Right to erasure (Art. 17)
   - Right to restriction (Art. 18)
   - Right to data portability (Art. 20)
   - Right to object (Art. 21)
   - Right to withdraw consent at any time
9. **Right to complain** to Datatilsynet (datatilsynet.dk)
10. **Last updated** date

### Template opener (Danish)

```html
<h1>Privatlivspolitik</h1>
<p>Sidst opdateret: [dato]</p>

<h2>Den dataansvarlige</h2>
<p>
  [Fulde navn / virksomhedsnavn]<br>
  [Adresse]<br>
  CVR: [nummer]<br>
  E-mail: [email]<br>
  Telefon: [telefon]
</p>

<h2>Hvilke personoplysninger indsamler vi?</h2>
<p>Vi indsamler følgende oplysninger, når du bruger vores hjemmeside eller kontakter os:</p>
<ul>
  <li><strong>Kontaktformular:</strong> navn, e-mailadresse og de oplysninger, du skriver i beskeden</li>
  <li><strong>Tekniske oplysninger:</strong> IP-adresse, browsertype og besøgstidspunkt (via serverlogfiler)</li>
</ul>
```

### Health data note (for psychology/healthcare sites)

Health data is **special category data** under GDPR Art. 9. Processing requires explicit consent (Art. 9(2)(a)) unless the professional secrecy exemption in Art. 9(2)(h) applies (treatment context). For a psychologist's website:
- The public website itself typically does not process health data
- Session notes, diagnoses, and referral letters processed in the practice context require a separate data processing policy
- If using a third-party booking or EHR system, a data processing agreement (DPA) is required under Art. 28

## Contact Forms

GDPR-compliant contact form requirements:

```html
<form id="contact-form" method="post" action="/send">
  <div>
    <label for="name">Navn *</label>
    <input type="text" id="name" name="name" required autocomplete="name">
  </div>
  <div>
    <label for="email">E-mail *</label>
    <input type="email" id="email" name="email" required autocomplete="email">
  </div>
  <div>
    <label for="message">Besked *</label>
    <textarea id="message" name="message" required></textarea>
  </div>

  <!-- Consent checkbox — required if you store or use data beyond answering the query -->
  <div>
    <input type="checkbox" id="privacy-consent" name="privacy-consent" required>
    <label for="privacy-consent">
      Jeg accepterer, at mine oplysninger behandles for at besvare min henvendelse.
      <a href="/privatlivspolitik.html" target="_blank">Læs privatlivspolitik</a>. *
    </label>
  </div>

  <button type="submit">Send besked</button>
</form>
```

**Data minimisation (Art. 5(1)(c))**: only collect fields you actually need. Phone number is optional if you respond by email. Do not add "how did you hear about us" fields unless genuinely necessary.

**Retention**: delete contact form submissions after responding. Document your retention period in the privacy policy.

## Cookies Register (Cookieoversigt)

Danish law requires a list of all cookies on your site. Publish it in the privacy policy or on a dedicated `/cookies` page:

| Navn | Udbyder | Formål | Type | Udløb |
|---|---|---|---|---|
| `cookie_consent` | Eget website | Gemmer brugerens cookievalg | Nødvendig | 1 år |
| `_ga` | Google Analytics | Skelner besøgende | Statistik | 2 år |
| `_gid` | Google Analytics | Skelner besøgende | Statistik | 24 timer |

Update this table whenever you add or remove cookies.

## Compliance Checklist

- [ ] Cookie banner shown before non-essential cookies are set
- [ ] Rejecting cookies is as easy as accepting
- [ ] Consent is stored with timestamp and version
- [ ] Analytics/marketing scripts load only after consent
- [ ] Privacy policy published and linked in footer
- [ ] Privacy policy covers all 10 required sections
- [ ] Cookie register published
- [ ] Contact form has privacy consent checkbox (if data stored)
- [ ] Data processing agreements in place with all processors (hosting, email, analytics)
- [ ] Health data handled under explicit consent or professional secrecy (for healthcare sites)
- [ ] Footer has link to privacy policy and cookie settings

## Useful Danish resources

- **Datatilsynet** (datatilsynet.dk) — the Danish supervisory authority; has free guidance and templates
- **Erhvervsstyrelsen** — enforces the Cookie Order; has published guidance on cookie exemptions
- **Datatilsynet's standard contractual clauses** — for international data transfers
