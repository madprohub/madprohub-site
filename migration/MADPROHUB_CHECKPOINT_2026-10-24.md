# MADPROHUB Cloudflare Migration — Continuation Checkpoint
Date: 2026-10-24 (Asia/Manila)
Status: **MIGRATION BLOCKED — SOURCE DATA EXPORT / AUTH TRANSITION / FULL APPLICATION PORT**
Scope: Read-only audit. No live DNS or payment changes.

## Verified source
- AppDeploy app: `madprohub-gaming-marketplace-h2ggxp`
- Live AppDeploy URL: https://madprohub-gaming-marketplace-h2ggxp.v2.appdeploy.ai/
- Latest ready AppDeploy snapshot: `1792836978750` / v100 (2026-10-24)
- React 19 + Vite 6 frontend; backend uses `@appdeploy/sdk` for router, database and authentication.
- Existing source has many separate `backend/*.ts` and `src/*.tsx` modules; a static HTML copy cannot replace these.
- AppDeploy QA reported missing `guides/manifest.json` and favicon resource requests. Revalidate after port.

## Cloudflare inventory (read-only verified)
- Active zone: `madprohub.online`
- Zone ID: `3dc824b3aea6e8df7fc32437e4d18c19`
- Account ID: `5b72617ddd10ce55e9f589b70040c098`
- Apex and www DNS CNAME: `proxy-v2.appdeploy.ai` (currently NOT cut over).
- MX records use `mx1.spacemail.com`, `mx2.spacemail.com`; preserve email DNS.
- Existing Pages project: `madprohubsite.pages.dev`, most recent deployment 2026-10-04; old relative to AppDeploy v100.
- Existing staging API Worker: `madprohub-migration-staging-api`, bound to staging D1 as `DB`; `nodejs_compat`, observability enabled.
- Staging D1: `madprohub-migration-staging`, ID `04b71ac0-cccf-4326-ba5c-cfd14e302a36`.
- Other D1 databases also exist; do not confuse them with the migration staging database.
- Separate Worker `madpro-connect` / database and media bucket are unrelated until verified.

## Staging database facts
- 24 application tables, excluding internal `_cf_KV` table.
- `PRAGMA foreign_key_check` returned zero violations.
- `migration_sources`: 16 mapping rows; all `export_status=not_started`, no counts recorded and `imported_count=0`.
- `schema_migrations`: 4 rows.
- `users`, `listings`, `messages`, `orders`, `wallets`, `ledger_entries`, and other data tables: 0 rows at inspection time.
- This is a prepared schema and NOT a populated migration.

## GitHub inventory
- Repo: https://github.com/madprohub/madprohub-site
- Existing `main` contains `madprosite/index.html`, `robots.txt`, `sitemap.xml`.
- The HTML is branded **GameVault** and is not the full MADPROHUB React + AppDeploy backend source.
- Do NOT deploy existing `main` to replace current MADPROHUB.

## Critical blockers
1. No verified secure complete export of AppDeploy production database; source uses a key-value table model and numerous logical stores.
2. Auth relies on AppDeploy login tokens; design equivalent Cloudflare-compatible authentication/identity mapping, or a safe reauthentication/migration path.
3. No validated, complete port of AppDeploy SDK backend and React frontend into Cloudflare.
4. GitHub's static HTML snapshot is unrelated to current app version.
5. Need source-vs-destination record counts, ID linkage, checkpointed batches, sensitive-data review, full regression tests, and rollback.

## Verified inventory gap
The existing 16-row migration mapping is incomplete for the current backend: source code also references `marketplace_admin_audit`, `moderation_cases`, `member_xp`, `member_xp_receipts`, `premium_grants_audit`, `ad_review_audit`, `ad_inquiry_admin_inbox`, and per-user dynamically named tables. Enumerate ALL logical stores from full source and actual source metadata before export. Treat secrets, KYC documents, vault data, MFA state and auth tokens separately; never publish PII in GitHub.

## Next safe steps
1. Inventory all AppDeploy database table references (including dynamically generated names) and classify by sensitivity and retention.
2. Implement an authenticated, tightly authorized, paginated source export in AppDeploy ONLY after reviewing full auth and database SDK contracts, data handling and backup strategy. Avoid an unprotected bulk-export route. If unavailable, stop and document.
3. Establish export manifest: per-table counts, stable IDs, checksums, resume cursors, transform version, known excluded ephemeral categories.
4. Set up destination import with idempotent upserts and conflict logging, test on non-sensitive samples and then authorized staging data.
5. Port backend endpoints using D1 bindings; design authentication transition without copying passwords or bearer tokens.
6. Bring frontend to Cloudflare staging; verify auth, listings, messages, admin, policies, access control, payments-disabled safety.
7. Backup and reconcile data; incremental sync, freeze writes at cutover, test, seek explicit approval for DNS switch.
8. Preserve AppDeploy site and DNS as rollback until migration is verified.

## Safety
- NO real-money payment activation; keep sandbox-only restrictions.
- NO source or destination production deletion/overwrite.
- NO apex/www DNS modifications without explicit approval after full readiness.
- NO claims of full migration based on schema existence.
