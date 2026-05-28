# Code Review: nwtees.com (`index.html`)

Reviewed 2026-05-28. Covers semantic HTML, accessibility, JS hygiene, security, and SEO hygiene — i.e., the things the mobile and agent readiness audits don't catch. Cross-references those reports where there's overlap.

## Verdict

The site is well-built. Honeypot spam mitigation, focus return after lightbox close, proper ARIA on icon buttons, IntersectionObserver-based fade-ups, dynamic form subject lines — this is competent work. There are two real bugs (one SEO, one a11y) worth fixing before victory-lapping, and a small pile of nice-to-haves.

## Critical

### 1. Canonical URL points at the old Railway preview, not nwtees.com
**Where:** `index.html` line 9.
```html
<link rel="canonical" href="https://north-west-design-production.up.railway.app/" />
```
**Fix:**
```html
<link rel="canonical" href="https://nwtees.com/" />
```
**Why it matters:** `rel="canonical"` is the page telling search engines "this is the official URL." Right now it's pointing at the Railway preview domain. Google will index nwtees.com but credit the SEO authority to the Railway URL, fragmenting visibility and undermining the whole point of buying nwtees.com.

### 2. Open Graph and Twitter Card URLs also point at the Railway preview
**Where:** `index.html` lines 21, 24, 33.
```html
<meta property="og:url" content="https://north-west-design-production.up.railway.app/" />
<meta property="og:image" content="https://north-west-design-production.up.railway.app/images/og-image.png" />
<meta name="twitter:image" content="https://north-west-design-production.up.railway.app/images/og-image.png" />
```
**Fix:** Replace all three Railway preview URLs with `https://nwtees.com/...`.
**Why it matters:** When someone shares nwtees.com in iMessage, Slack, Facebook, or LinkedIn, the link unfurler fetches `og:image` and `og:url`. Right now those go through the Railway preview URL — works today, but if Railway ever renames or expires the preview domain, every existing social share breaks retroactively. Anchor it to the production domain.

## Important

### 3. Lightbox doesn't trap focus
**Where:** `index.html` lines 1377–1383 (markup) and 1555–1593 (JS).
**Current behavior:** Open the lightbox, press Tab. Focus moves to the close button, then escapes to whatever's after the lightbox in the DOM — the underlying page content behind the modal. A keyboard user can navigate "into" the dimmed page they can't see.
**Fix:** Trap focus inside the lightbox while it's open. Smallest workable patch:
```js
lightbox.addEventListener('keydown', (e) => {
  if (e.key !== 'Tab') return;
  const focusables = [lbPrev, lbNext, lbClose];
  const first = focusables[0];
  const last = focusables[focusables.length - 1];
  if (e.shiftKey && document.activeElement === first) {
    e.preventDefault(); last.focus();
  } else if (!e.shiftKey && document.activeElement === last) {
    e.preventDefault(); first.focus();
  }
});
```
**Why it matters:** WCAG 2.4.3 (Focus Order). Mostly affects keyboard users and screen-reader users navigating with Tab. Not a showstopper for a brochure site but real if a screen reader user ever opens the gallery.

### 4. Form inputs at 15px trigger iOS auto-zoom
**Cross-ref:** Already in `MOBILE-READINESS.md` punch list #1. One-line CSS fix (line 868, `15px` → `16px`).

### 5. 21 of 22 images lack width/height attributes
**Cross-ref:** Already in `MOBILE-READINESS.md` punch list #2. Causes Cumulative Layout Shift; also a Core Web Vitals ranking signal.

## Nice to have

### 6. No skip-to-main link
**What:** Standard accessibility pattern — first focusable element on the page is a hidden link that jumps the keyboard user past the nav to the main content.
**Fix:** Insert right after the opening `<body>`:
```html
<a href="#top" class="skip-link">Skip to main content</a>
```
Plus a CSS rule that hides it until focused. Single-page site so the marginal value is small, but it's three lines of code and screen readers expect it.

### 7. Focus styles only present on form inputs and the carousel track
**Where:** Lines 517–518, 880–884.
**What's missing:** Nav links, buttons (`btn-primary`, `btn-outline`), gallery cards, footer links, social icons — none have an explicit `:focus-visible` style. They fall back to whichever default ring the browser draws, which is usually fine but inconsistent across browsers and clashes with the brutalist red/black palette.
**Fix:** One global rule:
```css
a:focus-visible,
button:focus-visible {
  outline: 3px solid var(--red);
  outline-offset: 3px;
}
```
Apply selectively if any element needs different treatment. Tightens the design and improves keyboard nav.

### 8. KD70 credit link opens in the same tab
**Where:** `index.html` line 1398.
```html
<div>Made with care by <a href="https://www.kd70.com">KD70</a></div>
```
**Fix (optional, designer's call):** Add `target="_blank" rel="noopener"` if you'd rather not yank the customer away. Currently same-tab — that's a defensible design choice (cleaner, less "look at me"), but for a credit link most sites use a new tab.

## What's solid

- Honeypot field on the quote form (tabindex=-1, aria-hidden, autocomplete=off — textbook).
- Client-side 5MB file-size validation with a graceful fallback that tells the user to email instead.
- Dynamic email subject line that includes the customer's name — small touch, makes inbox triage easier for Danielle.
- Lightbox keyboard support (Escape, ArrowLeft, ArrowRight), body scroll lock, focus return to the triggering gallery card.
- Proper ARIA on icon-only buttons (`aria-label` on links wrapping SVGs, `aria-hidden="true" focusable="false"` on the SVGs themselves).
- `aria-live="polite"` on form status so screen readers announce send progress.
- IntersectionObserver for fade-ups — performant, unobserves after fire.
- `novalidate` on the form + `checkValidity()` in JS — sensible UX (consistent validation messages, async-first submission).
- Carousel arrow disabled states with a small tolerance (`<= 4`) — handles sub-pixel rounding gracefully.
- Per-hostname `target="_blank" rel="noopener"` on every external link except the KD70 credit (item 8 above).

## Verification

After applying the canonical and OG fixes, paste the URL into:
- [opengraph.xyz](https://www.opengraph.xyz/) — confirms social previews now point at nwtees.com
- [Google's Rich Results Test](https://search.google.com/test/rich-results) — confirms canonical is parsed correctly

For the lightbox focus trap, open the gallery on the live site, click an image, then press Tab repeatedly. Focus should cycle through prev → next → close → prev, never escaping to the page behind.
