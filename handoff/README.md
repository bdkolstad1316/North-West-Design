# nwtees.com — Website Archive

A complete, self-contained copy of the North West Design website (nwtees.com) as deployed in May 2026.

## What's in here

| File | What it is |
|---|---|
| `index.html` | The entire website. Single page; everything you see at nwtees.com lives here. |
| `images/` | Every photo, logo, favicon, and graphic on the site. |
| `robots.txt` | Tells search engines and AI crawlers what they're allowed to do. |
| `sitemap.xml` | Standard map of the site for search engines. |
| `llms.txt` | A plain-language summary of the business for AI tools (ChatGPT, Claude, Perplexity) that look it up. |
| `serve.json` | Configuration for the file server that hosts the site on Railway. |
| `package.json` | Dependencies and start command for the Railway server. |

## How the site is hosted (as of May 2026)

- **Domain registrar:** Cloudflare (transferred from Northwest Media in May 2026)
- **DNS:** Cloudflare
- **Hosting:** Railway (deploys automatically when files in this repo are pushed to GitHub)
- **Form submissions:** Web3Forms (the "Get a Quote" form on the site sends to nwdesign2001@gmail.com via Web3Forms; the access key is embedded in `index.html`)
- **Online payments:** Echelon (HYFIN) — the "Pay your invoice online" link points to `echelon.hyfin.app/7AW5JPK25CXM`

## If you need to redeploy or move the site

1. The contents of this folder are the entire deployable website.
2. Any Node-friendly host (Railway, Render, Fly.io, Netlify, Vercel) can serve it. They'll read `package.json` and run `serve` to host the files.
3. If you're handing this to a developer cold, give them this folder and tell them: "It's a static single-page site served by the `serve` npm package. Point a host at it, set the start command to `npm start`, and add the custom domain in DNS."

## Contact

Built by Brian Kolstad — [kd70.com](https://www.kd70.com) · brian@kd70.com
