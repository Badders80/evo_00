# Ecosystem Topology & Boundaries

**Status:** Active SSOT  
**Island Topologies:**

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
