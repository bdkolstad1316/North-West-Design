---
target: "#catalog band, Style # field, footer catalog link in public/index.html"
total_score: 26
max_score: 40
na_heuristics: 
p0_count: 0
p1_count: 3
target_identity: "file:/Users/briankolstad/Developer/nwd/public/index.html"
target_fingerprint: "sha256:0ac337be820ef41f1f1bb21cda63c7e32575e2c548a94c24c11aa3d3f32b08b2"
target_path: /Users/briankolstad/Developer/nwd/public/index.html
timestamp: 2026-10-29T19-08-19Z
slug: public-index-html
---
Method: dual-agent (A: design review sub-agent · B: detector/browser sub-agent)
Target: #catalog band, #qf-style field, footer "Browse the catalog" link in public/index.html (commit bb5b291 "SanMar")

## Design Health Score

| # | Heuristic | Score | Key Issue |
|---|-----------|-------|-----------|
| 1 | Visibility of System Status | 3 | ↗ and "opens in a new tab" present; nothing echoes the style number after submit |
| 2 | Match System / Real World | 2 | "Style #", "PC61", "powered by Company Casuals": three pieces of trade/vendor language in one feature |
| 3 | User Control and Freedom | 3 | New tab preserves the site; field optional; no traps |
| 4 | Consistency and Standards | 2 | Same label "Browse the catalog" does two different things; eyebrow is the only imperative eyebrow; note breaks the mono-label scale |
| 5 | Error Prevention | 2 | Nothing warns that the external site has a login box, SanMar's phone number, and a Logo Tool that sends nothing back |
| 6 | Recognition Rather Than Recall | 2 | The whole flow is recall across tabs; the form hint says "from our catalog" but never links to it |
| 7 | Flexibility and Efficiency | 3 | Accepts number or link; no deep link to the field |
| 8 | Aesthetic and Minimalist Design | 3 | Clean band; the disclaimer renders at 18px and out-shouts the CTAs |
| 9 | Error Recovery | 3 | Generic form fallbacks cover it |
| 10 | Help and Documentation | 3 | The 3 steps are the help; step 3 "send it with your quote" is vague |
| **Total** | | **26/40** | **Acceptable** |

## Design Specificity Verdict
Authored skin on an interchangeable skeleton. Headline construction, ink box with red offset shadow, eyebrow dot+rule are all on-system. The structure is the stock "3 numbered steps in a dark card", no garment imagery, and "Catalog powered by Company Casuals" is the line on every SanMar dealer site. Field: category-standard, acceptable. Footer link: generic and mislabelled.

Deterministic scan: 14 findings file-wide, 3 inside scope. (1) low-contrast 4.0:1 on `.catalog h2 .red` (#d62828 on #e6e6e6) is a false positive at 70px (large text needs 3:1). (2) placeholder "e.g. PC61, or paste a link" 3.4:1 advisory, pre-existing placeholder CSS but this placeholder carries the instruction. (3) 10.5px "Optional · from our catalog" below the 11px floor (pre-existing `.optional` style). Browser measurement caught what the detector missed: `.catalog-note` computes to 18px, not 12px, because `.catalog p` (0,1,1) beats `.catalog-note` (0,1,0). No in-page overlay: the detector ships compiled into the binary, no injectable detect.js.

## Priority Issues
- [P1] Band's "Get a Quote" strands the visitor at the section head; the style field is sixth in the form. Mobile: form card is ~1,170px below #contact, field another ~630px. Fix: id="quote" on .contact-card, point band CTA and step 3 at it, move Style field under Name/Email.
- [P1] Form hint "from our catalog" references a catalog it never links to; no nav entry while Bill Pay has one. Fix: link the hint to companycasuals.com (tracker picks it up); add Catalog to nav.
- [P1] Footer "Browse the catalog" is the same label as the external CTA but scrolls to the band; 17px hit height. Fix: make it the external link with ↗, or relabel "About the catalog"; pad footer links to 24px+.
- [P2] `.catalog-note` renders at 18px (specificity loss, line 705 vs 707); hard-coded #555. Fix: `.catalog p.catalog-note` selector; tokenize color.
- [P2] Note doesn't say what Company Casuals is or that there are no prices/ordering there; generic SanMar root shows SanMar's own 800 number. Fix: rewrite note; ask whether NWD has a dealer storefront URL.

## Persona Red Flags
Jordan: recognises none of "Style #", "PC61", "Company Casuals"; lands on a blue site with a password-reset form and (800) 426-6399 and may call SanMar. Casey: switches tabs, taps Get a Quote, lands on "Stop by, or just call" and calls, or loses the number scrolling; hint wraps with "our catalog" orphaned at 390px. Riley: 201-char URL accepted, shows only tail; tracking params forwarded; empty catalog_style sent as a blank line on every submission; product URLs are /p/1634_Turquoise so "a pasted link works too" over-promises.

## Minor Observations
H2 wraps "WE DO / THE REST." at 1280. Eyebrow "Pick your garment" is an imperative where others are labels, and repeats "pick". .about and .catalog are visual twins (same bg, rule, ink box). ↗ is read as "north east arrow"; no "opens in new tab" on the link. Focus ring on .btn computes to white/ink at 0 offset, not the global red ring. Adjacent, out of scope: #qf-qty number input is unstyled.

## Questions to Consider
1. Does NWD have its own Company Casuals dealer storefront URL?
2. If the pitch is "just call", should the band end in "Found one? Call us with the style number"?
3. Does the shop need style only, or style + colour + sizes? Is one field collecting a fifth of the answer?
