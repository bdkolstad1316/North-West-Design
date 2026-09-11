# North West Design (nwtees.com)

## What this is
A single-page marketing site for North West Design, a custom apparel and
promotional production shop in Clarkston, WA (screen printing, embroidery,
DTF, signage, stickers, engraving). Static HTML — no build step, no framework,
no e-commerce. Everything is priced by quote, so the site's job is to get
visitors to the quote form or the phone.

Nearly the entire site is one file: `index.html` (~57 KB, CSS and JS inline).

## Client
- Name: North West Design — Clarkston, WA
- Shop: 1142 16th Ave, Ste A, Clarkston, WA 99403
- Phone: (509) 758-3043, Mon–Fri 8a–5p
- Email: nwdesign2001@gmail.com
- Deliverables (reports, brand files, source photos) live in: TODO — confirm
  the Cowork/iCloud path. They must NOT go in this repo; see the note under
  "Repo root is the website" below.

## Run locally
```
npm ci          # matches package-lock.json exactly — do not use npm install
npm start       # http://localhost:8080  (override with PORT=8123 npm start)
```
`npm start` runs `serve` bound to `0.0.0.0:$PORT`. Response headers for
`llms.txt`, `robots.txt`, and `sitemap.xml` come from `serve.json`.

## Deploy
- GitHub: `bdkolstad1316/North-West-Design`, private
- Railway deploys automatically on push to `main`
- Live URL: https://nwtees.com
- Secrets go in Railway's Variables tab. Never commit `.env`.

## Repo root is the website
Railway serves every tracked file at the repo root, so anything committed here
is publicly reachable by URL. Audits, PDFs, internal renders, and client
deliverables do not belong in this repo at any path — `.gitignore` already
blocks the known offenders. Known exception still outstanding: `Claude outputs/`
is tracked and therefore live at `nwtees.com/Claude outputs/`.

## External services
- **Web3Forms** — the quote form posts to `api.web3forms.com`. The
  `access_key` in `index.html` is a public submit key by design, not a secret.
- **Plausible** — analytics, loaded from `plausible.io`. Custom events fire
  through the `track()` helper; `Quote Form Submitted` fires on success.
- **iPOSpays** — the "pay an invoice" link. Hosted, no integration.
- **Google Fonts** — Anton, Space Grotesk, JetBrains Mono.

## Brand
- Red `#d62828` (deep `#a81d1d`, light `#ee5050`)
- Ink `#1a1a1a` on paper `#f5f5f5`
- Anton for display, Space Grotesk for body, JetBrains Mono for labels

## Rules for Claude
- Pull before editing. Brian may edit on github.com.
- Don't push to `main` without asking — a push is a deploy to the client's
  live site.
- Keep it dependency-free. The only prod dependency is `serve`.
- Redesign work goes on a branch, never a `v2/` folder.
- Update `sitemap.xml` `lastmod` and `llms.txt` when page content changes
  meaningfully.
