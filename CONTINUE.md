# CONTINUE — evo_00 Control Plane

**Status:** Active Control Plane  
**Active Milestone:** Milestone 2 — Web Storefront & Marketplace Clean-Room Rebuild (`apps/web`)

---

## 1. Locked & Approved Canonicals

- [x] **`doc/DSL_MANUAL.md` LOCKED (2026-08-17):**
  - Pricing SSOT: $\text{list} = \text{cost} \times 1.05 \times 1.03$ (5% margin + 3% processing).
  - Billing: $5\times M$ float join + monthly $M$ keep (unused funds refunded pro-rata).
  - Exits: `fourteen_day` default (Case B) + per-DSL `three_x_remaining` buyout (Case B1).
  - Distributions: Officially published gross stakes $\times 75\% \times \text{stake}\%$ (owner 25% absorbs fees, 2-month qualification, late-month carry-forward).
  - Welfare & Law: SA Clause 6 trainer primacy, NZTR COP 22.1, zero Tokinvest.
- [x] **`doc/DESIGN_SYSTEM_AND_TOKENS.md` LOCKED (2026-08-17):**
  - Dark elevation hierarchy scraped from `docs.x.ai` (`--bg-base` through `--bg-l4`, `--accent-gold: #d4a964` OG Hex SSOT).
  - Monospace tabular figures for financial tables (`ui-monospace`).
  - Resend dark email transactional boilerplates.
  - Single layout shell across Operator Docs & Mission Control.
- [x] **`doc/VOICE_AND_TONE_MANUAL.md` LOCKED (2026-08-17):**
  - Private Banker Standard ("Grit & Elegance", professional but not stuffy).
  - Silent Gavel laws (lead with thoroughbred, zero justification, no negation openers).
  - Digital-Syndication nomenclature + NZTR / FMA Equine Exemption positioning.
  - Master vocabulary whitelist and banned-terms dictionary with strict CI copy validation.
  - Locked taglines (*"Ownership, evolved."*, *"Ownership, reimagined."*, *"Own the Experience."*, and the *Grounded in tradition* triad).
- [x] **`specs/INFRASTRUCTURE_SPEC.md` LOCKED (2026-08-17):**
  - Cloudflare R2 (`cdn.evolutionstables.nz`) $0 egress media vault.
  - Supabase `Evolution-3.0`: Pure Supabase Auth, PostgreSQL with RLS, contract storage vault.
  - Stripe Engine: $5×M float join checkout, Customer Billing Portal ($M/mo), idempotent webhooks.
- [x] **`migration_bridge/01_MIGRATION_MASTER_PLAN.md` (ADR-004 Locked):**
  - Clean-room storefront rebuild over legacy route copying to avoid build debt.

---

## 2. Milestone 1 Status (COMPLETE & VERIFIED)

- [x] **Monorepo Scaffolding:** `pnpm-workspace.yaml`, `package.json`, `turbo.json`, `tsconfig.json`, `Justfile`.
- [x] **`@evo/brand_dna` Package:** Scraped `docs.x.ai` dark elevation hierarchy, `#d4a964` gold tokens, shadcn/ui CSS custom properties, and Tailwind preset (`just check` passed).
- [x] **`@evo/storage` Package:** Cloudflare R2 zero-egress client, presigned private vault download/upload helpers, public CDN media resolvers (`https://cdn.evolutionstables.nz`), and isomorphic zero-dependency SHA-256 digest engine (passing NIST test vectors).
- [x] **`@evo/legal_engine` Package:** DSL compiler (Term Sheet, PDS, and SA generator with SHA-256 digests and compliance validation), canonical settlement math (75/25 prize pool, Case B pro-rata + unused float, Case D 4→3 delinquency rule, Case E 4-month burn, NZ GST 3/23 breakdown).
- [x] **`@evo/db_models` Package:** Supabase client factories, PostgreSQL schemas (`00001_initial_schema.sql` and `00002_cap_table_and_reservations.sql`), strict RLS policies, 15-minute checkout TTL table `checkout_reservations`, atomic row-locking concurrency RPCs (`reserve_campaign_shares`, `release_expired_reservations`), and full TypeScript database models (`types/database.types.ts`).
- [x] **`apps/mission_control` Cockpit Desk:** 3-column Operator Console, Smart Intake parser with NZTR stud book scraper/lookup, KYC audit inspector, 4-way cap table reconciler, and interactive Settlement Desk calculator (clean Next.js 15 production build). *(Note: Demo data plane currently backed by typed immutable fixtures until wired to live Supabase client).*

---

## 3. Milestone 2: Web Storefront & Marketplace Architecture

### A. The Clean-Room Take vs. Leave Protocol

```
┌─────────────────────────────────────────────────────────────┬─────────────────────────────────────────────────────────────┐
│ ✅ TAKE FROM LEGACY (Asset & Aesthetic Transfer)           │ 🚫 LEAVE BEHIND (Burned Legacy Build Debt)                  │
├─────────────────────────────────────────────────────────────┼─────────────────────────────────────────────────────────────┤
│ • Dark luxury color palette & typography hierarchy          │ • Legacy Firebase Admin SDK & token sync                    │
│ • High-resolution conformation stills & video clips         │ • Google Sheets sync scripts & Tokinvest crypto models     │
│ • Bloodline pedigree stories & trainer yard bios            │ • Legacy checkout routes & unverified webhook handlers      │
│ • Core layout proportions & institutional brand tone        │ • Hardcoded pricing numbers ($76/$380 is Nellie only)       │
└─────────────────────────────────────────────────────────────┴─────────────────────────────────────────────────────────────┘
```

### B. Hard Information (Fact) vs. Soft Information (Story) Ownership Rules

| Dimension | 🔒 Hard Information (Fact) | ⚡ Soft Information (Story) |
| :--- | :--- | :--- |
| **Domain** | Commercial, Legal, Regulatory Truth | Emotional Resonance, Athletic Potential |
| **Code Ownership** | Sourced **exclusively** from `@evo/legal_engine` and `@evo/db_models`. | Sourced from `@evo/brand_dna`, `@evo/storage/cdn`, and editorial traits. |
| **Pricing Rule** | **Never hardcoded.** Derived dynamically per horse ($76/mo for Nellie, $65/mo for Mulan). | **Never drives pricing or share counts.** |
| **Key Surfaces** | Monospace pricing card, 4-Way Cap Table balancer, Frozen PDS/SA SHA-256 PDF links, KYC status. | Conformation gallery, trainer profile, pedigree bloodlines, **Thoroughbred Attributes**. |

### C. Thoroughbred Attributes Schema ("Pokemon Powers" Internal Metaphor)
* **Rule:** The phrase *"Pokemon Powers"* is strictly an internal engineering nickname. **It must NEVER appear in customer-facing UI or copy.**
* **Customer-Facing Term:** **"Thoroughbred Attributes"** or **"Performance & Pedigree Profile"**.
* **Typed Schema (`ThoroughbredAttributes`):**
  1. `distance_sweet_spot`: string (e.g. *"1200m – 1600m Miler"*)
  2. `turn_of_foot`: string (e.g. *"Explosive Final 400m Acceleration"*)
  3. `conformation_build`: string (e.g. *"Deep Girth, Balanced Overstep"*)
  4. `maturity_timeline`: string (e.g. *"Early Pre-Christmas 2YO Target"*)

### D. Canonical 4-Way Cap Table Formula
On all surfaces, horse share allocations strictly obey the 4-way integer identity:
$$\text{Total (100\%)} = \text{Retained (e.g. 95\%)} + \text{Allocated (e.g. 2\%)} + \text{Reserved (15-min lock, e.g. 1\%)} + \text{Available (e.g. 2\%)}$$
Where $\text{Campaign Shares} = 100 - \text{Retained Shares} = \text{Allocated} + \text{Reserved} + \text{Available}$.

### E. Standardized Route Structure
* Standard Route: **`/horses/[slug]`** (e.g. `/horses/nellie`, `/horses/tml-x-yearn`).

---

## 4. Milestone 2 Sub-Phases & Execution Order (COMPLETE ✅)

1. **Sub-Phase 2A: Storefront Shell (`apps/web`)** ✅
   * Header, Nav, Footer, SEO metadata (`layout.tsx`, `robots.ts`, `sitemap.ts`, `globals.css`).
   * Homepage (`/`): Hero showcase, brand triad, syndicate mechanics.
   * Thoroughbred PDP (`/horses/[slug]`): Two-tier layout (Hero/Traits top, Hard Pricing/Cap Table/Data Room bottom).
2. **Sub-Phase 2B: Supabase SSR Authentication (`apps/web`)** ✅
   * PKCE SSR cookie auth (`/lib/supabase-server.ts`, `/lib/supabase-client.ts`, `/middleware.ts`).
   * `/login` page: Dark institutional luxury UI with Magic Link & password login.
   * `/auth/callback` PKCE exchange handler + `/auth/signout` action.
   * Header dynamic session indicator (Sign In / MyStable).
3. **Sub-Phase 2C: Stripe Concurrency Checkout & MyStable Portal (`apps/web`)** ✅
   * Connected "Join Syndicate" on PDP to `/api/checkout/create-session` (15-min reservation lock).
   * Webhook handler `/api/webhooks/stripe` for `checkout.session.completed` with holding allocation.
   * Legal contract download endpoint `/api/legal/download` powered by `@evo/legal_engine`.
   * `/mystable` investor dashboard with Holdings, Yard Memos, Contract Vault, and Stripe Billing Portal.
4. **Sub-Phase 2D: Static Legal & Information Pages (`apps/web`)** ✅
   * `/terms` (Terms of Service under NZTR & Equine Exemption).
   * `/privacy` (Privacy Policy under NZ Privacy Act 2020).
   * `/faq` (Interactive DSL mechanics and 5×M float guide).
   * `/learn/returns` (75/25 gross stakes prize money worked examples).

---

## 5. Next Milestone: Milestone 3 — Studio (`evo_03`) & Full-Loop Sandbox Launch Gate

---

## 5. Island Locations:
- `evo_00`: `/home/evo/new/evo_00` (Permanent Control Plane & Governance)
- `evo_01`: `/home/evo/new/evo_01` (Read-Only Historical Reference)
- `evo_02`: `/home/evo/new/evo_02` (Target Production App Monorepo)
- `evo_03`: `/home/evo/new/evo_03` (Target Studio & Creative Sandbox)
