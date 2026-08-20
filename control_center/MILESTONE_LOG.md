# Milestone Log
| Date | Milestone | Outcome |
|------|-----------|---------|
| 2026-08-20 | gbrain knowledge hub seeded | 32 pages, brain_score 45 -> 79 |
| 2026-08-20 | gbrain upgraded 0.46.12.3 -> 0.46.23.0 | schema 130->132; all data intact |
| 2026-08-20 | control plane under git + gbrain git-source | auto-sync on commit |


## 2026-08-20 — gbrain hub: "fully running" end-to-end
- **brain_score 70 -> 83** (full graph linking: hub->source/control docs, entities->governing docs, facts->source docs, migration docs->entities; 36 links added)
- **stale_pages 49 -> 0**, **timeline coverage 0 -> 0.8** (13 timeline entries on hub+facts+entities)
- **orphans 33 -> 21** (all genuinely-linkable content now inbound-linked; remaining = empty default-source stubs + duplicate source-doc copies, kept to avoid cross-source link breakage)
- **doctor health 90 -> 95**; `gbrain reindex --markdown` 17/17 aligned (contextual-retrieval ladder)
- **Maintenance automation (G1)**: `gbrain-sync.sh` now runs sync + extract --stale + embed --stale on each new commit (deterministic self-maintenance, ~3s window)
- **Backup (G4)**: nightly `gbrain-backup.timer` 02:30 -> /mnt/s/gbrain-backups/ (verified restorable tar, retains 7)
- **Cleanup (G5)**: 385MB stale WAL-repair backup moved to /mnt/s/gbrain-backups/stale-wal-repair-20260820
- **KYC no-commit rule intact**: migration_bridge/Migrated Existing HLTs/ stays gitignored + never fed to gbrain
