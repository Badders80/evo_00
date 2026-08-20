# `evo_02` Infrastructure, Cloud Architecture & Agentic Harness Specification

**Status:** LOCKED SSOT  
**Date:** 2026-08-17  
**Purpose:** Defines the cloud production infrastructure (Supabase, Cloudflare R2, Stripe, Resend) and external productivity/verification harness (`gbrain`, `gstack`, `GSD-2`, `openfang`) for `evo_02`.

---

## 1. Cloud Production Infrastructure Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          CLIENTS & INVESTORS                                │
│          Web Storefront / MyStables Portal (Next.js 15 in apps/web)         │
└──────────────────┬──────────────────────┬────────────────────┬──────────────┘
                   │                      │                    │
        Auth & PostgreSQL RLS        Media Delivery        Payments
                   │                 (Zero Egress)             │
                   ▼                      ▼                    ▼
┌──────────────────────────────┐ ┌──────────────────┐ ┌───────────────────────┐
│     SUPABASE (Evolution-3.0) │ │  CLOUDFLARE R2   │ │     STRIPE ENGINE     │
│                              │ │                  │ │                       │
│ • Pure Supabase Auth (JWT)   │ │ • Media CDN Hub  │ │ • $5xM Join Checkout  │
│ • Postgres Relational DB     │ │ • Conformation   │ │ • $M Monthly Keep Sub │
│ • Strict RLS per-user        │ │ • Trackwork MP4s │ │ • Customer Portal     │
│ • Vault: Signed Contract PDFs│ │ • Voice Notes    │ │ • Idempotent Webhooks │
└──────────────────────────────┘ └──────────────────┘ └───────────────────────┘
                   │
                   ▼
┌──────────────────────────────┐
│       RESEND COMMS HUB       │
│ • Dark Transactional Emails  │
│ • PDS/SA Dispatch            │
│ • Distribution Notices       │
└──────────────────────────────┘
```

### 1.1 Supabase Backend & Auth (`Evolution-3.0` / `coqtijrftaklcwgbnqef`)
1. **Pure Supabase Auth:**
   - Single source of auth truth for investor onboarding, session resolution, and role-based access.
   - 100% purged of Firebase Auth SDK and custom claims Frankenstein-architecture (ADR-002).
   - Magic link, passwordless email login, and standard secure session tokens.
2. **PostgreSQL Relational Schema & Row-Level Security (RLS):**
   - Core tables: `inventory`, `holdings`, `leads`, `events`, `race_results`, `communications`.
   - Strict RLS policies: Users can only read their own holdings, KYC status, and signed contract documents.
3. **Private Contract Vault (Supabase Storage):**
   - Stores immutable SHA-256 hashed PDF copies of signed Product Disclosure Statements (PDS) and Syndicate Agreements (SA).
   - Access controlled strictly via Supabase RLS and signed download URLs.

### 1.2 Cloudflare R2 Media Hub (`cdn.evolutionstables.nz`)
1. **Zero-Egress High-Performance CDN:**
   - Dedicated bucket for all heavy public and campaign media (horse conformation photography, trainer workout clips, pedigree diagrams, silks, trainer audio updates).
   - $0 bandwidth egress fees regardless of investor traffic or video streaming volume.
2. **Access & SDK Wrapper (`@evo/storage`):**
   - Uses `@aws-sdk/client-s3` wrapper configured with Cloudflare R2 credentials.
   - Pre-signed upload URLs for Mission Control admin asset intake.
   - Fast edge caching via Cloudflare CDN.

### 1.3 Stripe Payment & Billing Engine
1. **Checkout Pipeline ($5×M Initial Join):**
   - Generates Stripe Checkout sessions for the initial subscription float join ($5\times M$ = 3 months security deposit + 2 months prepaid keep).
   - Metadata tags: `horse_slug`, `stake_percentage`, `user_uuid`, `contract_hash`.
2. **Customer Billing Portal ($M Monthly Keep):**
   - Leverages native Stripe Customer Billing Portal for monthly keep maintenance ($M/month), payment method updates, invoice history, and self-service cancellation requests.
3. **Idempotent Webhook Handler:**
   - Webhook endpoint (`/api/webhooks/stripe`) validates signature using `STRIPE_WEBHOOK_SECRET`.
   - Records every `stripe_event_id` in Supabase `events` table with unique constraint.
   - Guarantees zero duplicate share allocations or double-billing on Stripe event replays.
4. **Safety Kill-Switch:**
   - `PURCHASES_ENABLED` environment variable. If not explicitly `"true"`, all checkout creation routes reject with HTTP 403.

### 1.4 Resend Transactional Communications
1. **Institutional Dark Email Boilers:**
   - Transactional emails styled using `@evo/brand_dna` dark tokens (`#0a0a0a` background, `#d4a964` gold accents, `#f8fafc` text).
2. **Key Notification Events:**
   - Investor Welcome & KYC Approval.
   - DSL Subscription Confirmation & Immutable PDS/SA PDF Delivery.
   - Monthly Keep Payment Receipts & Stripe Portal Links.
   - Quarterly Gross Stakes Distribution & Carry-Forward Reports.

---

## 2. External Operator Suite & Agent Harness (ADR-005, ADR-006)

```
┌─────────────────────────────────────────────────────────────────────────┐
│ EXTERNAL OPERATOR SUITE (Outside evo_02 — Global CLI / MCP / Daemons)  │
│                                                                         │
│  • gbrain: Global MCP-backed persistent memory and knowledge graph     │
│  • gstack: Headless Chromium/Playwright QA browser daemon               │
│  • GSD-2 (gsd-pi): Spec-driven worktree task runner                     │
│  • OpenFang (Downstream): 24/7 autonomous background worker OS         │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │ Interacts via CLI, Git, & MCP
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ evo_00 (Permanent Control Plane)                                        │
│  • /control_center/ (ADRs, Topology, Decision Log)                      │
│  • CONTINUE.md (Single Active Desk & Task Ledger)                       │
│  • /doc/ (DSL Manual, Operations Manual, Design Tokens)                 │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │ Governs
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ evo_02 REPOSITORY (Pure Zero-Bloat Application Monorepo)                │
│                                                                         │
│  • /packages/ (legal_engine, brand_dna, db_models, storage)             │
│  • /apps/ (web, mission_control)                                        │
│  • Justfile (`just check`, `just test`, `just dev`, `just repomap`)     │
└─────────────────────────────────────────────────────────────────────────┘
```

### 2.1 Tool Roles & Execution Layer

1. **`garrytan/gbrain` (Unified Persistent Memory):**
   - Prevents cross-session amnesia.
   - Intercepts new tickets to query past attempts, preventing duplicate or discarded work.
2. **`garrytan/gstack` (Headless QA Pattern & "Done Means Walked"):**
   - Enforces the verification gate: no task is marked complete without a headless browser walk (`scripts/operator_walk.py` / Playwright) validating DOM bindings and live runtime flows.
3. **`gsd-build/gsd-2` (Spec-Driven Worktree Execution):**
   - Executes isolated tasks in Git worktrees against clean specifications before merging to main.
4. **`RightNow-AI/openfang` (Downstream 24/7 Worker OS):**
   - Scheduled background workers for automated scraping (NZTR results, pedigree data) and alert broadcasting.
   - Runs exclusively in `evo_00` or `evo_03`. Never embedded in `evo_02`.

---

## 3. Productivity & Anti-Avoidance Engine

```
┌──────────────────────────────────────────────────────────────────────────────┐
│ 1. THE UNFINISHED TASK LEDGER (evo_00/CONTINUE.md)                            │
│    • Max 2 active WIP items. No new initiatives opened until active items   │
│      are marked DONE (Walked) or BLOCKED (with explicit reason).             │
│    • Wrap ritual mandates: "What is done + exact next single action."        │
├──────────────────────────────────────────────────────────────────────────────┤
│ 2. PRIOR ATTEMPT INTERCEPTOR (gbrain MCP)                                    │
│    • Before an agent starts any new ticket, it queries gbrain for past        │
│      discussions, half-baked branches, and existing implementations.         │
├──────────────────────────────────────────────────────────────────────────────┤
│ 3. THE "GRILL-ME / ACCOUNTABILITY COACH" PROTOCOL                            │
│    • Agent challenge mode: Audits active blockers vs revenue-generating goals│
│    • Forces the question: "This core task is 80% done. Why are we working on │
│      secondary chrome? What is the single blocker to shipping this today?"   │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## 4. The Daily Operator Loop

1. **Prioritize & Challenge:** Founder reviews `evo_00/CONTINUE.md`. Run accountability check on stale/half-finished tickets.
2. **Execute in Isolation:** Agent runs in a worktree, pulls context and prior attempts from `gbrain`, and executes.
3. **Automate Verification:** `just check` executes static type checks, legal golden snapshots, and headless browser walks before merge.

