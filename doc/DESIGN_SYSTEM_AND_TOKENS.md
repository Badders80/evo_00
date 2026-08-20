# `evo_02` Specification: Design Tokens, Layout Primitives & Canonical Knowledge

**Status:** LOCKED SSOT  
**Date:** 2026-08-17  
**Context:** Migration to `evo_02` — Establishing locked design primitives, transactional communication templates, and canonical business knowledge based on the institutional `docs.x.ai` dark architecture.

---

## 1. Core Philosophy: Unified Operator Portal (Docs + Mission Control)

1. **Single Layout & Token Architecture:** Both the internal operator docs (`The Evolution Bible`) and Mission Control (`The Execution Console`) share the exact same UI shell, dark theme, and typography inspired directly by `docs.x.ai`.
2. **Internal-First Knowledge Hub:** The docs portal starts as an **internal reference manual in humanized plain English** for the Founder & Operators to verify policies (e.g., quarterly distribution procedures, break clauses, carry-forward rules) before exposing anything publicly.
3. **Founder Review Gate:** Every canonical document and UI primitive must be reviewed and locked by the founder prior to code execution in `evo_02`. Zero debt or unverified assumptions roll over.

---

## 2. Scraped `docs.x.ai` Design Tokens (`@evo/brand_dna`)

These tokens are extracted directly from `docs.x.ai` and adapted for Evolution Stables:

### 2.1 Dark Elevation Hierarchy
```css
:root {
  /* Canvas & Elevation Levels (Scraped from docs.x.ai) */
  --bg-base: hsl(0 0% 4%);             /* #0a0a0a - Main viewport canvas */
  --bg-l1: hsl(0 0% 7%);               /* #121212 - Sidebar & secondary panels */
  --bg-l2: hsl(0 0% 10%);              /* #1a1a1a - Cards & content modules */
  --bg-l3: hsl(0 0% 13%);              /* #212121 - Inset containers & hovers */
  --bg-l4: hsl(0 0% 15%);              /* #262626 - Active tabs & selected items */
  --bg-codeblock: hsl(0 0% 7%);        /* #121212 - Code & formula blocks */

  /* Borders & Dividers */
  --border-muted: hsl(0 0% 19%);       /* #303030 - Subtle structural borders */
  --border-subtle: hsl(216 4% 22%);    /* #36393e - Card & panel dividers */
  --border-bold: hsl(222 19% 86%);     /* High-contrast focus borders */

  /* Typography & Foreground */
  --foreground: hsl(210 40% 98%);      /* #f8fafc - Primary ultra-crisp text */
  --muted-foreground: hsl(0 0% 60%);   /* #999999 - Subtitles & metadata */
  --fg-muted: hsl(216 4% 51%);         /* Secondary muted text */

  /* Evolution Brand Accent */
  --accent-gold: #d4a964;              /* Evolution Champagne Gold (OG Hex SSOT) */
  --accent-gold-hover: #c39853;        /* Darkened interactive hover state */
  --accent-foreground: hsl(210 0% 98%);

  /* Status Indicators */
  --status-active: #10b981;            /* Emerald green (Racing / Settled) */
  --status-pending: #f59e0b;           /* Amber (Funding / Carry-Forward) */
  --status-closed: #64748b;            /* Slate (Spelling / Closed) */

  /* Radii */
  --radius-sm: 0.125rem;               /* 2px */
  --radius-md: 0.375rem;               /* 6px */
  --radius-lg: 0.5rem;                 /* 8px */
  --radius-xl: 0.75rem;                /* 12px */
  --radius-pill: 9999px;
}
```

### 2.2 Gold Restraint & Usage Discipline (The 3–5% Rule)
* **Visual Weight:** Gold (`#d4a964`) must occupy **strictly 3% to 5%** of any screen viewport. 95%+ of the screen is deep black/charcoal (`#0a0a0a` to `#1a1a1a`) and crisp white/gray typography.
* **Allowed Uses:** Single primary action button (e.g. `[ Join Syndicate ]`), header logo mark, active tab indicator line (2px), or key hero metric callout.
* **Strictly Forbidden:** Never use gold for container/card backgrounds, body paragraphs, headings, full card borders, or secondary buttons.

### 2.3 Typography Scale & Fonts
* **Primary / UI Font:** `ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial` (or `Inter` / `Geist`)
* **Financials & Tabular Figures:** `ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace` (Used for stakes %, gross NZTR earnings, and 75/25 split tables)

---

## 3. Unified Shell Primitives (`apps/mission_control` & `/docs`)

The shared operator shell layout inspired directly by `docs.x.ai`:

### 3.1 Top Header & Global `Cmd+K` Bar
* Left: Evolution Stables minimalist crest + workspace indicator (`evo_02`).
* Center: Global Omni-Search (`⌘K` to search Rulebook docs, active horses, or contract clauses).
* Right: Operator mode switcher (`[ 📖 Operator Docs | ⚡ Mission Control Ops ]`).

### 3.2 Left Sidebar (Hierarchical Navigation)
```
┌──────────────────────────────────────┐
│  OPERATOR BIBLE (HUMANIZED DOCS)    │
│  ├─ 01. Syndicate & DSL Model        │
│  ├─ 02. Distribution & Carry-Forward │
│  ├─ 03. Break Clauses & Exits        │
│  ├─ 04. Equine Welfare & NZTR COP    │
│  └─ 05. Listing Requirements         │
│                                      │
│  MISSION CONTROL (OPS CONSOLE)       │
│  ├─ Horse Intake & Roster            │
│  ├─ DSL Pack Generator (PDS/SA)      │
│  ├─ R2 Cloud Media Vault             │
│  └─ Quarterly Distribution Run       │
└──────────────────────────────────────┘
```

### 3.3 Interactive Document & Ops Canvas
* **Doc Mode:** Renders clean, readable Markdown/MDX with callouts, formula blocks, and reference tables.
* **Ops Mode:** Renders actionable intake forms, live Supabase/R2 data, and distribution payout calculators.

### 3.4 Component Primitives (`shadcn/ui` Integration)
To maintain velocity and zero design distraction, all UI elements in `evo_02` are built strictly by composing standard **shadcn/ui** primitives mapped to `@evo/brand_dna`:
* **Search / Command:** `<Command>`, `<Dialog>` (`⌘K` omni-search modal).
* **Ledgers & Data Tables:** `<Table>` with monospace figures for gross stakes and float logs.
* **Containers & Modules:** `<Card>`, `<CardHeader>`, `<CardContent>` with 1px hairline `#303030` borders.
* **Status Badges:** `<Badge>` using `--status-active`, `--status-pending`, `--status-closed`.
* **Action Triggers:** `<Button>` with primary gold variant (`bg-[#d4a964] text-black font-semibold hover:bg-[#c39853]`) or secondary ghost variant (`border border-[#303030] text-white hover:bg-[#212121]`).
* **Rule:** Zero custom CSS inventing or styling bike-shedding. Focus 100% on content, business logic, and user flows.

---

## 4. Transactional Communication Primitives (Resend / Email)

Universal HTML email template container matching the dark institutional look:
* **Header:** Deep black banner with crisp gold Evolution bar.
* **Body:** Clean, responsive typography with callout boxes for leasehold details.
* **Footer:** Registered NZTR Syndicate Manager disclosures + support contact.

---

## 5. Master Inventory: Canonical Knowledge Sets (`evo_00/doc/`)

```
evo_00/doc/
├── DSL_MANUAL.md                   # DSL Commercial Model, 5xM float, 14-day close, 75/25 split
├── OPERATIONS_MANUAL.md            # Mandatory clauses §1-14, NZTR COP 22.1, welfare primacy, payout ops
└── DESIGN_SYSTEM_AND_TOKENS.md     # docs.x.ai tokens, card primitives, HTML email boilers
```
