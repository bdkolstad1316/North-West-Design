# Agent Readiness Report: nwtees.com

**Score: 1/14 → projected 8/14 after this PR**

Audited 2026-05-28 against https://nwtees.com.

## What was missing

The site had nothing in place for AI agents or AI-aware search. Only the homepage 200 was passing.

| Category | Before | After | Notes |
|---|---|---|---|
| Discoverability | 1/4 | 4/4 | robots.txt, sitemap.xml, Link header all added. |
| Content Accessibility | 0/2 | 1/2 | llms.txt added; markdown content negotiation skipped (static site limitation). |
| Bot Access Control | 0/3 | 2/3 | AI bot rules + Content Signals added; Web Bot Auth skipped (advanced, not applicable). |
| Protocol Discovery | 0/5 | 0/5 | MCP, OAuth, API catalog all N/A — brochure site with no agent-drivable actions. |

## Files added

All four files live at the project root. Railway serves them at the URL paths shown below.

### `robots.txt` → `nwtees.com/robots.txt`
Declares Content Signals (`search=yes, ai-input=yes, ai-train=no`), blocks training crawlers (GPTBot, ClaudeBot, Google-Extended, CCBot, Bytespider, Amazonbot, Applebot-Extended, Meta-ExternalAgent), and explicitly allows live-user agents (ChatGPT-User, Claude-User, Claude-Web, Perplexity-User) plus answer-engine indexes (OAI-SearchBot, PerplexityBot).

**Note for Danielle:** this also overrides Cloudflare's default "block all AI bots" managed rule. If you turned that on at the Cloudflare dashboard during the transfer, leave it off — the robots.txt now handles this with more nuance.

### `sitemap.xml` → `nwtees.com/sitemap.xml`
Single-URL sitemap pointing at the homepage. Single-page site, so that's all there is. Update `<lastmod>` whenever you ship a substantive copy change.

### `llms.txt` → `nwtees.com/llms.txt`
Markdown summary of NWD — services, contact, gallery section. When an AI agent asks "who does custom embroidery in Clarkston," this is what it'll see if it knows to look here. Treats the homepage's content as structured data instead of HTML soup.

### `serve.json` → consumed by the `serve` static server (Railway)
Adds the `Link:` response header so agents can discover the sitemap/llms.txt/robots.txt without guessing, and sets correct `Content-Type` on each. The `serve` npm package reads this file at startup.

## What was intentionally skipped

- **MCP Server Card, Agent Skills index, OAuth metadata, API catalog** — N/A. nwtees.com is a brochure site. There are no agent-drivable actions (no booking, no checkout, no API). Generating stub manifests for these would be amateur-hour signal.
- **Web Bot Auth (HTTP Message Signatures)** — N/A. Bot identity signing is for sites with paid/gated content where authenticity matters. Not relevant here.
- **Markdown content negotiation** — would require server logic (serve `text/markdown` when `Accept: text/markdown` is sent). The `serve` static server can't do this. `llms.txt` covers 95% of the value anyway.

## Deploy steps

1. Commit and push these four files (`robots.txt`, `sitemap.xml`, `llms.txt`, `serve.json`).
2. Railway redeploys.
3. Verify:

   ```bash
   curl -sI https://nwtees.com | grep -i '^link:'
   curl -s https://nwtees.com/robots.txt | head -5
   curl -s https://nwtees.com/llms.txt | head -5
   curl -s https://nwtees.com/sitemap.xml | head -3
   ```

4. Re-run the audit or paste `https://nwtees.com` into [isitagentready.com](https://isitagentready.com) — expect ~8/14, with the remaining 6 being intentional N/A.

## Maintenance

- Update `llms.txt` if services change, contact info changes, or the shop moves. It's the one file that drifts.
- Update `sitemap.xml` `<lastmod>` on substantive copy changes.
- Robots.txt should be stable. Revisit only if a new AI crawler shows up worth blocking or allowing.
