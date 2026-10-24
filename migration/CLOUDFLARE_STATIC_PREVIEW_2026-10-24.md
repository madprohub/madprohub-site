# MADPROHUB Cloudflare Public Content Staging — 2026-10-24

Status: **DEPLOYED TO CLOUDFLARE WORKERS; PUBLIC HTTP REACHABILITY NOT INDEPENDENTLY VERIFIED**

- Cloudflare Worker: `madprohub-static-copy-preview`.
- Account Workers subdomain: `madpro-career.workers.dev`.
- Expected URL: https://madprohub-static-copy-preview.madpro-career.workers.dev/
- API upload: HTTP 200, success.
- Worker `workers.dev` routing enabled (HTTP 200).
- No production DNS modifications. Apex and www still CNAME to `proxy-v2.appdeploy.ai`.

## Source copied
Public text files from AppDeploy snapshot `1792836978750` / v100:
- `public/about.html`
- `public/contact.html`
- `public/privacy.html`
- `public/guide.css`
- `public/guides/index.html`
- `public/guides/marketplace-safety.html`
- `public/guides/listing-checklist.html`
- `public/guides/roblox-item-safety.html`
- `public/icon.svg`

Also created a clearly identified static homepage and staging robots policy.

## Limitations
This is a **public static preview**, not a complete copy of the working marketplace application. Source AppDeploy React frontend and SDK-backed backend are not yet ported. Live listings, login, messaging, private uploads, admin endpoints, wallets, and payment flows are unavailable. Writes to this Worker and `/api/*` endpoints are intentionally disabled. No source customer records or private files are stored on this Worker.

## Next steps
1. Confirm HTTP usability of staging Worker URL with browser (the verification crawler could not access it).
2. Expand copied public static assets as needed.
3. Independently port React/Vite frontend and Cloudflare Workers backend without relying on AppDeploy SDK.
4. Design and authorize source production export and auth identity mapping.
5. Reconcile D1 import and run full end-to-end tests.
6. Seek explicit owner approval before any domain cutover.

Do not use this read-only staging preview as a substitute for a fully migrated MADPROHUB site.
