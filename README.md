# `evo_00` — Evolution Stables Control Plane & Operator Desk

**Status:** Active Control Plane  
**Mission:** Governs architecture, persistent knowledge, high-trust verification gates, and operator decision logs for the Evolution Stables ecosystem.

---

## 1. The 4-Island Ecosystem Topology

```
┌────────────────────────────────────────────────────────────────────────┐
│  evo_00 — OPERATOR & AGENTIC CONTROL PLANE (Workstation Hub)           │
│   • Persistent Knowledge Graph (`gbrain`), ADRs, Operator Manual       │
│   • High-Trust Verification Gates & Desk Laws                          │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Governs & Verifies
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│  evo_02 — PRODUCTION PLATFORM MONOREPO (Pure, Zero-Bloat App)          │
│   ├── /packages/ (legal_engine, brand_dna, db_models, storage)         │
│   └── /apps/     (web: Next.js 15, mission_control: Operator Portal)   │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Emits Media & Event Data
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│  evo_03 — STUDIO & CREATIVE ENGINE (Heavy Media Sandbox)               │
│   • Remotion 9:16 Video Renderers, TikTok/Reels, Cloudflare R2 Uploads │
└────────────────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────────────────┐
│  evo_01 — HISTORICAL ARCHIVE (Read-Only)                               │
│   • Preserved legacy reference — untouched.                            │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Directory Structure

```
/home/evo/new/evo_00/
├── README.md                              ← Master Index & System Topology
├── CONTINUE.md                            ← Single Next-Action Desk File (SSOT)
├── AGENTS.md                              ← High-Trust Desk Laws & Execution Guardrails
│
├── control_center/                        ← PERMANENT (Ecosystem Governance)
│   ├── ECOSYSTEM_TOPOLOGY.md              ← 4-Island Architecture & System Boundaries
│   └── DECISION_LOG.md                    ← Architectural Decision Records (ADRs)
│
├── doc/                                   ← PERMANENT (Humanized Operator Manual - docs.x.ai Style)
│   ├── OPERATIONS_MANUAL.md               ← 75/25 gross stakes, carry-forward, break clauses & trainer welfare
│   └── DESIGN_SYSTEM_AND_TOKENS.md        ← Scraped docs.x.ai dark tokens & layout primitives
│
├── specs/                                 ← PERMANENT (Technical Blueprints)
│   └── INFRASTRUCTURE_SPEC.md             ← Next.js 15, Supabase, Cloudflare R2, Stripe & Resend
│
├── harness/                               ← PERMANENT (High-Trust Quality Gates)
│   ├── AGENTIC_HARNESS_SPEC.md            ← "Done Means Walked", Playwright checks & gate rules
│   └── POSTMORTEM_ANTIPATTERNS.md         ← Banned patterns (Franken-auth, dual-write drift)
│
└── migration_bridge/                      ← TEMPORARY (Retires 100% upon OTM)
    ├── README.md                          ← Bridge Lifecycle & Retirement Contract
    ├── 01_MIGRATION_MASTER_PLAN.md        ← The Complete 9-Section Greenfield Roadmap
    ├── 02_DATA_MAPPING.md                 ← DB schema transformations & ingestion rules
    ├── 03_ASSET_TRANSFERS.md              ← Cloudflare R2 photo/video staging checklist
    ├── 04_LEGAL_DIFF_AUDIT.md             ← DSL Manual compliance (14-day default + per-DSL 3x buyout)
    ├── 05_CUTOVER_RUNBOOK.md              ← DNS, live keys & launch runbook
    └── scripts/                           ← Ingestion & transformation scripts
```
