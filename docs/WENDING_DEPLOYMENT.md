# WendingAI Legend Talk Deployment

Production domain: `https://talk.wending.ai/`

This document records the WendingAI deployment baseline for Legend Talk. It is intentionally short and operational.

## 1. Vercel project

Recommended Vercel settings:

| Setting | Value |
|---|---|
| Framework Preset | Vite |
| Build Command | `npm run build` |
| Output Directory | `dist` |
| Install Command | `npm ci` |
| Production Branch | `main` after review, or `feat/wending-talk-productize` for preview |

The repository includes `vercel.json`, so Vercel should pick these values automatically.

## 2. DNS

Create this DNS record:

| Type | Name | Target |
|---|---|---|
| CNAME | `talk` | Vercel-assigned target |

After Vercel verifies the domain, the public URL should be:

```text
https://talk.wending.ai/
```

## 3. SEO domain baseline

The following files are configured for `talk.wending.ai`:

- `index.html`
  - canonical URL
  - Open Graph URL/image
  - Twitter image
  - hreflang links
  - JSON-LD Organization / WebSite / WebApplication
- `public/robots.txt`
- `public/sitemap.xml`
- `public/site.webmanifest`

The previous `s.newzone.top` analytics script has been removed. Add WendingAI-owned analytics later only if needed.

## 4. API key handling

Current behavior:

- API keys are stored locally in the browser through Zustand persist.
- Storage uses localStorage first and IndexedDB fallback.
- Keys are not sent to a WendingAI backend by this static frontend.

Production recommendation:

- For public users, prefer a WendingAI backend proxy with server-side key management and quota control.
- If keeping BYOK mode, show a clear UI note: “API Key is stored only in this browser.”
- Do not add third-party analytics that can inspect local storage or user prompts.

## 5. Local end-to-end mock

A no-key OpenAI-compatible SSE mock exists for local generation-path testing:

```bash
npm run mock:llm
```

Then configure the app:

- Provider: `Custom (OpenAI Compatible)`
- Base URL: `http://127.0.0.1:8787`
- API key: empty or any placeholder

Use this to test streaming UI, stop, retry, and scrolling without spending model tokens.

## 6. Quality gates

Before merging to `main`:

```bash
npm ci
npm run build
npm run test
```

GitHub Actions now runs the same build and test gates on pushes to `main`, `feat/**`, and pull requests into `main`.

## 7. Post-deploy checklist

- Open `https://talk.wending.ai/`.
- Confirm no request to `s.newzone.top` exists in browser DevTools Network.
- Open `https://talk.wending.ai/robots.txt`.
- Open `https://talk.wending.ai/sitemap.xml`.
- Test hash routes:
  - `/#/chat`
  - `/#/zh/chat`
  - `/#/en/chat`
- Test one local BYOK provider.
- Test one custom OpenAI-compatible provider.
- Test share URL creation and opening in a fresh browser profile.
