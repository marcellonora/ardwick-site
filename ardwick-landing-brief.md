# Ardwick Partners — Landing Page Brief

Paste this whole file into Claude Code as the project brief.

---

## 0. Fill these in before you start

| Placeholder | Value |
|---|---|
| `TAGLINE` | *(e.g. "Private capital, patiently deployed.")* |
| `CONTACT_EMAIL` | e.g. `contact@ardwickpartners.com` |
| `DOMAIN` | e.g. `ardwickpartners.com` |
| `LEGAL_ENTITY` | Full registered company name |
| `COMPANY_NUMBER` | Companies House / registry number |
| `REGISTERED_ADDRESS` | Full address |

Anywhere the brief says `TAGLINE` etc., substitute the real value.

---

## 1. Objective

Build a single-page static site for Ardwick Partners: a holding page that looks
like it belongs to a serious investment firm. Nothing but the wordmark, a
tagline, a contact link, and a privacy policy. No forms, no analytics, no
tracking, no cookie banner, no JS frameworks.

Reference for tone and motion: hedosophia.com — centred serif wordmark on a
dark, textured field, thin vertical hairline beneath it, small quiet footer
links. We want that restraint, but a **simpler background**: flat dark charcoal
with a very subtle texture, no large diagonal colour blocks.

---

## 2. Tech constraints

- Plain **HTML + CSS**. One small vanilla JS file only if needed for the
  intro animation timing — prefer pure CSS if possible.
- No build step, no npm, no Tailwind, no React. The output must be
  deployable by dragging a folder onto a host.
- Total page weight target: **under 150 KB** including fonts.
- Must work with JS disabled (content visible, just without the entrance
  animation).

### File structure

```
/
├── index.html
├── privacy.html
├── styles.css
├── robots.txt
├── site.webmanifest
└── /assets
    ├── wordmark.svg          (or wordmark.png @2x)
    ├── favicon.svg
    ├── favicon-32.png
    ├── favicon-192.png
    ├── favicon-512.png
    └── apple-touch-icon.png  (180×180)
```

---

## 3. Assets

Two source images are provided:

1. **`AP.png`** — square "AP" mark on charcoal. **This is the favicon / tab
   icon and app icon.** Generate the full icon set from it (32, 192, 512,
   180 apple-touch). Keep the charcoal background baked in — do not make it
   transparent, it should read as a solid tile in the browser tab.
2. **`AP-2.png`** — the "ARDWICK PARTNERS" wordmark with the large ghosted
   "AP" monogram behind it. **This is the page centrepiece.**

Ideally, redraw the wordmark as clean text rather than shipping the PNG:
set "ARDWICK PARTNERS" in **Cyrillic Bodoni Regular** (see Typeface section
below for licensing/fallback) with **small caps styling** (large initial A
and P, remaining letters at small-cap height), letter-spaced. Render the
ghosted "AP" monogram behind it as a separate absolutely-positioned element
at ~35% opacity so it scales cleanly and can be animated independently.
Fall back to the PNG only if the type match isn't close.

**Typeface:** the logo uses **Cyrillic Bodoni, Regular weight** (a
high-contrast Didone). Use this exact typeface if a webfont license is
available:

- If you have a webfont license: place the `.woff2` file(s) in
  `/assets/fonts/` and `@font-face` them directly — this is the preferred
  path, since it'll match the logo exactly. Confirm the license covers
  web/webfont embedding, not just desktop use, before shipping it live.
- If no webfont license is available yet: fall back to a free lookalike with
  similarly high stroke contrast and a similar Didone structure — **Playfair
  Display** or **Cormorant** are the closest free approximations. Treat this
  as a placeholder to swap out once the real font is licensed.

Either way, self-host the font files (do **not** hotlink Google Fonts — no
third-party requests, keeps the privacy policy honest). Subset to Latin
(and Cyrillic only if the family requires it to render correctly).

---

## 4. Visual design

**Palette**
```
--ink:        #262626   /* page background — matches the logo tile */
--ink-deep:   #1e1e1e   /* vignette / texture darker tone */
--bone:       #d8d8d6   /* wordmark, primary text */
--muted:      #8a8a88   /* footer links, tagline */
--hairline:   rgba(216,216,214,0.28)
```

**Background** — keep it simple. Layer, in order:
1. Flat `--ink` fill.
2. A very subtle diagonal pinstripe via CSS `repeating-linear-gradient`
   at 45°, ~3px period, opacity around 0.025. Barely perceptible — it should
   register as texture, not pattern.
3. A soft radial vignette darkening the corners toward `--ink-deep`.

No large colour blocks, no images, no canvas.

**Layout** — full viewport height, everything centred on the vertical axis:

```
            [ ghosted AP monogram, behind ]
              ARDWICK PARTNERS            ← wordmark, ~clamp(2rem, 7vw, 5.5rem)
                    TAGLINE               ← small caps, letter-spaced, --muted
                      │                   ← thin vertical hairline, ~120px tall
                   Contact                ← mailto link
        Privacy Policy    © 2026 Ardwick Partners   ← footer, fixed to bottom
```

Generous whitespace. The wordmark sits slightly above true centre (around
44% from top) so the block feels optically balanced.

**Mobile:** wordmark wraps to two lines ("ARDWICK" / "PARTNERS") below 640px,
hairline shortens to ~70px, footer stacks with the copyright underneath.

---

## 5. Animation

Restrained and slow — the whole sequence lands in about 2.4s. Everything eases
with `cubic-bezier(0.16, 1, 0.3, 1)`.

**Entrance (on load, staggered):**

| Element | Delay | Effect |
|---|---|---|
| Wordmark | 0.2s | Fade 0→1 over 1.4s, plus letter-spacing easing from `0.22em` → `0.08em` |
| Ghosted monogram | 0.4s | Fade 0→0.35 over 1.8s, scale `1.04` → `1` |
| Tagline | 1.0s | Fade in, translateY `8px` → `0` |
| Hairline | 1.3s | `scaleY(0)` → `scaleY(1)`, `transform-origin: top`, 0.9s |
| Contact link | 1.7s | Fade in |
| Footer | 1.9s | Fade in |

**Ambient (continuous, after entrance):** the ghosted monogram breathes very
slowly — opacity oscillating between 0.30 and 0.38 over ~12s, with a
sub-pixel drift. Should be almost subliminal.

**Hover states:**
- Contact link: an underline rule that wipes in from left to right over 0.4s
  (`transform: scaleX()` on a pseudo-element), colour `--muted` → `--bone`.
- Footer links: same treatment, smaller.

**Accessibility:** wrap all of the above in a
`@media (prefers-reduced-motion: reduce)` guard that disables transforms and
the ambient loop, leaving a plain instant render.

---

## 6. Contact link

A `mailto:` with the subject and body pre-filled — **no form**.

```html
<a class="contact"
   href="mailto:CONTACT_EMAIL?subject=Enquiry%20—%20Ardwick%20Partners&body=Hello%20Ardwick%20Partners%2C%0A%0A">
   Contact
</a>
```

Notes:
- URL-encode everything; `%0A` for line breaks in the body.
- Use `—` encoded as `%E2%80%94` if the em dash causes issues in any client.
- Keep the body to a short greeting and two blank lines so the sender's
  cursor lands in a sensible place.

---

## 7. Privacy policy page

`privacy.html` — same background, same type, but left-aligned prose in a
centred column of ~62ch, generous line-height (1.75), `--muted` body text with
`--bone` headings. A small "← Ardwick Partners" link back to the index at the
top.

Content should cover, honestly and briefly:

1. **Who we are** — `LEGAL_ENTITY`, `COMPANY_NUMBER`, `REGISTERED_ADDRESS`,
   and `CONTACT_EMAIL` as the contact point for data matters.
2. **What this website collects** — nothing. State plainly that the site is
   static, sets no cookies, runs no analytics, and makes no third-party
   requests. This is the whole point of self-hosting the font.
3. **Server logs** — note that the hosting provider may record IP addresses
   and user-agent strings for security and operational purposes, and name the
   provider once it's chosen.
4. **Email correspondence** — if you email us, we process what you send in
   order to respond; lawful basis is legitimate interests; retained for as
   long as the correspondence relationship requires.
5. **Your rights under UK GDPR** — access, rectification, erasure, restriction,
   objection, portability; and the right to complain to the ICO
   (ico.org.uk), with the ICO helpline noted.
6. **Changes** — dated "Last updated" line at the foot.

Write it in plain English, no legalese padding. Keep it to roughly 500 words.

> Have a solicitor read this before it goes live — it's a starting draft, not
> legal advice, and the lawful-basis wording should be checked against how
> Ardwick actually handles enquiries.

---

## 8. Head / metadata

```html
<title>Ardwick Partners</title>
<meta name="description" content="TAGLINE">
<meta name="theme-color" content="#262626">
<link rel="icon" href="/assets/favicon.svg" type="image/svg+xml">
<link rel="icon" href="/assets/favicon-32.png" sizes="32x32">
<link rel="apple-touch-icon" href="/assets/apple-touch-icon.png">
<link rel="manifest" href="/site.webmanifest">
<meta property="og:title" content="Ardwick Partners">
<meta property="og:description" content="TAGLINE">
<meta property="og:image" content="https://DOMAIN/assets/og.png">
<meta property="og:url" content="https://DOMAIN/">
<meta name="twitter:card" content="summary_large_image">
```

Also generate `/assets/og.png` at 1200×630 — the wordmark centred on the
charcoal field, same composition as the page.

`robots.txt`: allow everything, point at nothing else.

---

## 9. Acceptance checklist

- [ ] Renders correctly at 375px, 768px, 1440px, and 2560px wide
- [ ] Wordmark never clips or overflows at any width
- [ ] Square AP mark appears in the browser tab and as the iOS home-screen icon
- [ ] Contact link opens the mail client with subject and body pre-filled
      (test on macOS Mail, Gmail web, and iOS)
- [ ] Privacy page reachable and styled consistently
- [ ] `prefers-reduced-motion` disables all animation
- [ ] Lighthouse: 100 on Accessibility and Best Practices
- [ ] Zero third-party network requests in the Network tab
- [ ] Passes W3C HTML validation

---

# Publishing

You own the domain already, so this is DNS plus a static host. Cloudflare
Pages is the recommendation: free, fast, free SSL, and deploys from a Git push.

## Option A — Cloudflare Pages (recommended)

1. **Push to GitHub.** In the project folder:
   ```bash
   git init
   git add .
   git commit -m "Ardwick Partners landing page"
   gh repo create ardwick-site --private --source=. --push
   ```

2. **Create the Pages project.** In the Cloudflare dashboard →
   Workers & Pages → Create → Pages → Connect to Git → pick `ardwick-site`.
   - Framework preset: **None**
   - Build command: *leave empty*
   - Build output directory: `/`
   - Deploy. You'll get a `ardwick-site.pages.dev` URL in under a minute.

3. **Attach your domain.** Pages project → Custom domains → Set up a custom
   domain → enter `DOMAIN`.
   - If your domain's nameservers are already on Cloudflare, it adds the DNS
     record itself and you're done.
   - If not, either move the nameservers to Cloudflare (best — go to your
     registrar, replace the nameservers with the two Cloudflare gives you), or
     add a `CNAME` at your existing DNS provider pointing to
     `ardwick-site.pages.dev`. For the apex domain you'll need a provider that
     supports CNAME flattening or ALIAS records.
   - Add `www` as a second custom domain and set it to redirect to the apex.

4. **Wait for SSL.** The certificate issues automatically, usually within
   15 minutes. DNS propagation can take up to a few hours.

5. **After go-live:** set Always Use HTTPS on in SSL/TLS → Edge Certificates,
   and check the site over `https://DOMAIN` on a phone as well as desktop.

Every subsequent `git push` redeploys automatically.

## Option B — Netlify

Same shape: connect the repo, leave the build command empty, publish
directory `/`, then Domain settings → Add custom domain → follow the DNS
records it gives you. If you'd rather skip Git entirely, drag the project
folder onto app.netlify.com/drop and add the domain afterwards.

## Option C — GitHub Pages

Free but slightly more fiddly with apex domains. Repo → Settings → Pages →
source `main` / root. Add a `CNAME` file containing `DOMAIN` at the project
root, then at your registrar create four `A` records pointing to
`185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`,
plus a `CNAME` on `www` to `USERNAME.github.io`. Tick "Enforce HTTPS" once
the certificate provisions.

## Email

`mailto:` doesn't need any mail configuration on your side — it just opens the
visitor's client. But `CONTACT_EMAIL` needs to actually receive mail, so make
sure your `MX` records point at your mail provider (Google Workspace, Fastmail,
etc.) before you publish. Test by emailing the address from an outside account.
While you're in DNS, add `SPF`, `DKIM`, and a `DMARC` record — a firm's domain
with no email authentication gets spoofed.
