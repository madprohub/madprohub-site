# MADPROHUB — Cloudflare Cutover Gates

| Gate | State 2026-10-24 | Required proof |
|---|---|---|
| AppDeploy latest source identified | VERIFIED | Snapshot v100 `1792836978750` |
| Cloudflare zone active | VERIFIED | API zone status |
| Live DNS routed to Cloudflare app | NOT DONE | Apex/www currently AppDeploy |
| Staging D1 schema exists | VERIFIED | 24 app tables |
| Staging integrity | VERIFIED for current empty data | `PRAGMA foreign_key_check`: zero rows |
| Complete source table inventory | BLOCKED | Source references more than 16 mappings |
| Secure source export | NOT STARTED | Authenticated export manifest and checksums |
| Production user data imported | NOT STARTED | Source+destination count reconciliation |
| Login/identity mapping | BLOCKED | Test existing accounts and recovery |
| Backend feature parity | BLOCKED | API regression suite |
| Frontend feature parity | BLOCKED | Browser QA |
| Wallet/payment safeguards | NOT TESTED | Real-money disabled; admin gate checks |
| Production backups/rollback | NOT VERIFIED | Backup and restore drill |
| DNS cutover approval | NOT REQUESTED | Explicit owner authorization |

## Read-only D1 queries for verification
Run against **madprohub-migration-staging**, never against an ambiguous database.

```sql
SELECT name FROM sqlite_master
 WHERE type = 'table' AND name NOT LIKE 'sqlite_%'
 ORDER BY name;
PRAGMA foreign_key_check;
SELECT source_table, record_kind, export_status, expected_count, imported_count
 FROM migration_sources ORDER BY source_table;
SELECT COUNT(*) AS users_count FROM users;
SELECT COUNT(*) AS listings_count FROM listings;
SELECT COUNT(*) AS messages_count FROM messages;
SELECT COUNT(*) AS orders_count FROM orders;
SELECT COUNT(*) AS wallets_count FROM wallets;
```

## Non-negotiable
Leave `madprohub.online` on current AppDeploy origin, preserve email MX/TXT/CNAME/SRV, and do not promote an outdated static GameVault page as the MADPROHUB production app. All new app code must go to a migration branch and staging environment, not directly to main.
