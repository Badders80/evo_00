# Architecture Decision Records (ADRs) — Evolution Stables

**Status:** ACTIVE SSOT  
**Governing Workspace:** `evo_00` (Control Plane)

---

## ADR-001: Greenfield `evo_02` Monorepo Over In-Place Refactoring
* **Status:** APPROVED
* **Date:** 2026-08-16
* **Context:** `evo_01` accumulated significant architectural debt across development eras (Remotion video scripts, scraping bloat, dual-write Google Sheets scaffolding, local SQLite databases, and Franken-auth).
* **Decision:** Establish `evo_02` as a clean, greenfield Next.js 15 monorepo. `evo_01` is preserved untouched as a read-only historical archive.
* **Consequences:** Zero regression risk to live `evo_01` assets; clean module boundaries from Day 1.

---

## ADR-002: Pure Supabase Auth & Row-Level Security (Purge Firebase)
* **Status:** APPROVED
* **Date:** 2026-08-16
* **Context:** `evo_01` suffered from "Franken-auth" where Firebase handled login/KYC claims while Supabase managed database tables and holdings, creating async state synchronization lag and session dropouts.
* **Decision:** Consolidate 100% of authentication, session management, and KYC claims into native Supabase Auth + strict PostgreSQL Row Level Security (RLS) policies. Completely purge Firebase Auth SDK.
* **Consequences:** Single source of auth truth, instantaneous session resolution, zero cross-service auth drift.

---

## ADR-003: Hybrid Storage Architecture (Cloudflare R2 + Supabase Vault)
* **Status:** APPROVED
* **Date:** 2026-08-16
* **Context:** High-resolution horse conformation photos, trainer workout videos, and social media reels risk massive bandwidth egress bills if served through transactional databases.
* **Decision:** Split storage into two strict tiers:
  1. **Supabase Storage:** Private regulatory contract vault for SHA-256 hashed PDS & SA PDFs and KYC verification snapshots (protected by RLS).
  2. **Cloudflare R2 (`cdn.evolutionstables.nz`):** Heavy media hub for all horse photos, workout MP4s, trainer voice notes, and finished 9:16 reels ($0 egress fees, sub-30ms global edge delivery).
* **Consequences:** Infinite media scalability with zero egress costs; total contract security.

---

## ADR-005: Live Asset Lock vs Market Research
* **Status:** APPROVED & LOCKED
* **Date:** 2026-08-21
* **Context:** A morning market-research pass and an Antigravity MC restyle leaked Te Akau, Allan Sharrock, fabricated pedigrees, and “Alex Bax” / “Stephen Gray Stables” into the live registry.
* **Decision:** `doc/ASSET_LOCK.md` is the human SSOT; `@evo/db_models` `asset-lock.ts` is the code SSOT. Live trainers are Barbara Kennedy, Wexford, and **Stephen Gray Racing** at Copper Belt Lodge, 160 Green Road, RD6, Palmerston North 4476. Owners: B.A.X Bloodstock (Nellie, Prudentia, Hotta, Manolo); Stephen Gray Racing (Mulan, First Gear). Public name is **Evolution Stables**; “Limited” only on formal legal packs. First Gear stays **visible** on the website as completed track record with checkout blocked.
* **Consequences:** Research folders must not seed trainers, owners, or pedigrees. Any “Stables” form of Stephen Gray / Grey is banned.

---

## ADR-004: DSL Commercial & Syndicate Constitution (The DSL Manual)
* **Status:** APPROVED & LOCKED
* **Date:** 2026-08-17
* **Context:** Syndicate pricing formulas, billing models, and exit terms were previously fragmented across 13 markdown files and hardcoded generator functions.
* **Decision:** Lock the single canonical commercial rules into `doc/DSL_MANUAL.md`:
  1. **Pricing:** $\\text{list} = \\text{cost} \\times 1.05 \\times 1.03$ (100% GST-inclusive).
  2. **Billing:** $5\\times M$ float join (3 months deposit + 2 months prepaid) + monthly $M$ keep (unused float refunded pro-rata upon exit).
  3. **Exits:** `fourteen_day` notice default (Case B) + per-DSL `three_x_remaining` buyout where head lease provides exit proceeds (Case B1).
  4. **Distributions:** Calculated from **officially published gross stakes** $\\times 75\\% \\times \\text{stake}\%$ (owner 25% absorbs all racing fees; late-month stakes carry forward; 2-month paid-up anti-speculation rule).
  5. **Welfare & Law:** Trainer absolute welfare supremacy (SA Clause 6); NZTR COP 22.1 governance; zero Tokinvest trace.
* **Consequences:** All legal compilers (`pack_lib`), web storefronts, and database constraints must implement these exact formulas.

---

## ADR-005: High-Trust Governance & "Done Means Walked" Verification Gate
* **Status:** APPROVED
* **Date:** 2026-08-17
* **Context:** In `evo_01`, agents frequently marked tasks "complete" based solely on passing unit tests while runtime event handlers, DOM bindings, and live checkout flows remained broken.
* **Decision:** Enforce the "Done Means Walked" harness law. No task, PR, or module may be marked done without an automated headless browser walkthrough (`just check` / Playwright) and contract hash validation.
* **Consequences:** Eliminates the "70-80% agentic trap"; guarantees production execution reliability.

---

## ADR-006: Single Desk Law (Ecosystem Governance in `evo_00`)
* **Status:** APPROVED
* **Date:** 2026-08-17
* **Context:** Having markdown control centers, roadmaps, or continue files inside both `evo_00` and `evo_02` creates dual-brain drift where agents follow conflicting task states.
* **Decision:** `evo_00` is the **sole desk and governance workspace** for all ADRs, planning, and task tracking (`AGENTS.md` + `CONTINUE.md`). `evo_02` is strictly a pure application monorepo (`/packages/` and `/apps/`) containing zero competing markdown control desks.
* **Consequences:** Unified agent state, zero desk duplication, single point of truth.
