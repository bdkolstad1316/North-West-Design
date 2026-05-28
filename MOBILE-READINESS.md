# Mobile Readiness Report: nwtees.com

**Overall: D** — Layout and typography are solid; payload weight and form fields drag the grade.

Audited 2026-05-28 against https://nwtees.com. Static analysis only; no browser rendering.

## Grades

| Category | Grade | Summary |
|---|---|---|
| Viewport & Layout | A | Correct viewport meta, six breakpoints, no horizontal scroll. |
| Tap Targets | Unverifiable | Static analysis can't measure rendered sizes; eyeball on a device. |
| Typography | A | Body at 17px, comfortably above the 16px floor. |
| Forms | C | Input font-size is 15px — iOS will auto-zoom on focus. |
| Performance | D | 4.85MB total payload; 21 images lack width/height (CLS risk). |
| Navigation & Interaction | A | No hover-only menus; mobile hamburger works without JS-hover. |

## Punch List

Ordered by impact — fix top-down.

### 1. Forms — input font-size triggers iOS auto-zoom
**Current:** `.contact-form input[type="text"|"email"|"tel"], .contact-form textarea` is set to `font-size: 15px` (index.html line 868).
**Fix:** Change `font-size: 15px` → `font-size: 16px` on that rule.
**Why it matters:** iOS Safari zooms the viewport when a user taps an input under 16px. The page reflows, the layout breaks, the user has to pinch back out. One-line CSS fix; biggest mobile UX win on the site.

### 2. Performance — images missing width/height attributes
**Current:** 21 of 22 `<img>` tags lack explicit `width`/`height`. Hero (`nwd-collage.jpg`), about (`nwd-shop.jpg`), and all 17 gallery images. Only `nwd-mark-sm.png` has dimensions.
**Fix:** Add `width="1200" height="1500"` to the hero, about, and gallery images (they're all 4:5 aspect at 1200×1500). Browser reserves space before image loads → no layout shift.
**Why it matters:** Cumulative Layout Shift is a real mobile annoyance — text jumps as images load, users mis-tap. Also a Core Web Vitals signal Google ranks on.

### 3. Performance — total payload is heavy on cellular
**Current:** 4.85MB total across 22 images. Gallery folder is 4.1MB on its own; some gallery images run 300–370KB at 1200×1500.
**Mitigating factor:** All 17 gallery images use `loading="lazy"`, so they don't hit the wire until the user scrolls. Initial page weight is closer to ~1.2MB, which is B territory. The rubric grades total payload, hence the D.
**Fix:** Two options. (a) Add `srcset` with smaller variants (600w + 1200w) so phones pull the small one. (b) Recompress the heaviest gallery images at q75 instead of q82. Either drops total payload meaningfully without re-shooting anything.
**Why it matters:** Users who tap into the gallery on cellular burn through their data plan. Less of a homepage-load issue than a "scrolling through your work" issue.

### 4. Forms — quantity field uses wrong input type
**Current:** `<input name="quantity" type="text">` in the quote form.
**Fix:** Change to `<input type="number" inputmode="numeric" min="1">`. Mobile keyboards open the number pad instead of QWERTY.
**Why it matters:** Small detail, real UX improvement for the user who's typing "12" on a phone keyboard.

### 5. Forms — add `inputmode` and stronger `autocomplete` hints
**Current:** Name/email/phone/text inputs have generic `autocomplete` but no `inputmode`.
**Fix:** Add `inputmode="text"` + `autocomplete="name"` to name, `inputmode="email"` + `autocomplete="email"` to email (mostly already there), `inputmode="tel"` + `autocomplete="tel"` to phone.
**Why it matters:** Browsers open the right keyboard variant and offer autofill. Reduces friction on the highest-stakes form on the site.

## What's solid

- Viewport meta is correct and doesn't block user zoom (accessibility win).
- Body typography at 17px is comfortably readable and doesn't trigger iOS zoom.
- Mobile hamburger menu uses click, not hover — works on touch with no JS gymnastics.
- Gallery images lazy-load — initial page weight is far lower than total payload suggests.
- Hero, brand mark, and wordmark are correctly set to eager-load (above the fold). The audit script flagged them as "missing lazy" — that's a false positive; don't change them.
- Six well-placed media queries cover the layout from desktop down to small phones.

## Verification

After applying fixes, re-run the audit or test at [pagespeed.web.dev](https://pagespeed.web.dev) — Lighthouse will run a real browser and confirm CLS, LCP, and tap target rendering that static analysis can't.
