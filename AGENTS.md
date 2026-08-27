# `evo_00` — Agent Laws & Operator Desk

You are pointed at the **Operator & Governance Control Plane** for Evolution Stables.

## Governing Laws

1. **The Desk:**
   - [`CONTINUE.md`](CONTINUE.md) is the single next-action file.
   - All permanent rules and tokens live strictly in `doc/` and `specs/`.
2. **The High-Trust Gate:**
   - Zero debt rolls into `evo_02`.
   - All rules, formulas, and token values must be sourced exclusively from `doc/`.
3. **Founder Oversight:**
   - Counsel sends, live money deployment (`PURCHASES_ENABLED`), and irreversible roster decisions require explicit founder approval.
4. **Done Means Walked:**
   - Verification requires real runtime execution (headless browser walkthroughs or live API calls). Unit tests alone are insufficient.
5. **Build Loop (locked):**
   - Multi-file builds, sprints, and any work that needs plan → execute → audit use [`harness/skills/build-loop/SKILL.md`](harness/skills/build-loop/SKILL.md) (v1.1.0).
   - No execution before founder `APPROVED:` on `review-synthesis.md`. No auto-stash. No guest checkout / `PURCHASES_ENABLED` / Tokinvest copy on live surfaces inside the loop.
   - One-line fixes may skip the loop; audits alone use `kimi-code-audit`.
6. **Git discipline:**
   - Git operations must use `git -C <path>` or verify cwd after cd — never chain onto a fallback directory.

## gbrain knowledge hub — feed on milestones
Evolution Stables has a canonical knowledge hub in gbrain (MCP at localhost:3456, token in your MCP config).
Use the `gbrain-hub` skill. On a LOCKED fact, a DECISION, or a completed MIGRATION MILESTONE, write/update a
fact card, add typed links to the source docs, add a timeline entry, then verify with a query.
- Source docs in evo_00 stay the SSOT; fact cards just lock the canonical values for recall.
- KYC RULE: NEVER read/feed/commit `migration_bridge/Migrated Existing HLTs/` (investor ID docs). It is gitignored.
- Write through MCP (`tools/call`), not the `gbrain` CLI (PGLite single-writer; serve holds the lock).

- FEED-BACK: whenever you query gbrain and a canonical fact is new or changed, write it back (put_page/add_link/add_timeline_entry) in the same session.
