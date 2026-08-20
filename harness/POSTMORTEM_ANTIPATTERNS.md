# `evo_01` Post-Mortem & Anti-Pattern Catalog

**Date:** 2026-08-16  
**Purpose:** Comprehensive failure-mode audit of the `evo_01` surface to protect `evo_02` from architectural debt, agentic loops, and operational friction.

---

## 1. Executive Summary & The "70–80% Agentic Trap"

During `evo_01` development, AI agents frequently reported tasks as complete based on passing unit tests or static generation scripts. However, runtime manual walks by the founder uncovered broken UI handlers, empty table cells, stale browser caches, and missing routes.

The founder became the **human integration tester**, forced to micro-debug wiring rather than operating at a strategic macro level.

```
Agent sees green unit tests ──► Claims "Done" ──► Founder clicks UI / runs workflow
                                                         │
                                               Fails at runtime (Dead button, 0-0 data)
                                                         │
                                                         ▼
                                          Founder forced into micro-triage
```

### The 4 Core Root Causes:
1. **Tests vs. Operator Disconnect:** Unit tests validated isolated Python functions or React component renders in memory, but never simulated full browser interaction or verified dynamic DOM bindings.
2. **Context & Pointer Fragmentation:** 15 different instruction/pointer files caused multi-model drift across Claude, Gemini, Grok, and Hermes.
3. **Knowledge $\rightarrow$ UI Data Silos:** Data existed in markdown/JSON files (`race-record.json`, `profile.md`) but lacked enforced schema pipelines into Mission Control and the website.
4. **Nested Repo & Cross-Path Coupling:** A nested git repository (`02_website/`) and dynamic `sys.path` hacks led to relative import failures and deployment breakages on Vercel.

---

## 2. Catalog of Observed `evo_01` Failure Classes

| # | Failure Class | What Happened in `evo_01` | Root Cause | `evo_02` Architectural Invariant |
|---|---|---|---|---|
| **1** | **Module Scope Incompatibility** | Wizard Next button completely dead on click in Mission Control. | `app.js` loaded as `type="module"`, but HTML used inline `onclick="handleNext()"`. Functions were not assigned to `window`. | **Pure Modern Framework:** Next.js 15 React handlers or compiled TypeScript modules with explicit event listener bindings. |
| **2** | **Stale Browser Cache** | UI updates deployed but browsers served stale JavaScript. | Static assets lacked automated build hashing/mtime cache-busting. | **Automated Content Hashing:** Next.js asset hashing or server-enforced `?v=mtime` query param injection. |
| **3** | **Pytest DB Destruction** | Running `pytest` wiped the live local operator SQLite database (`ssot_local.db`). | Test runner did not mock `ADMIN_DB_PATH` or use an isolated SQLite in-memory/tempfile fixture. | **Isolated Test Fixtures:** Test suites strictly reject connection to production/dev DB paths. Hard assertion in test runner. |
| **4** | **Process Suicide on Restart** | Script running `pkill -f admin_server.py` killed its own caller/wrapper script. | Unscoped process matching string matched both the daemon and the monitoring wrapper. | **PID-tracked Process Control:** Dedicated PID files or container/systemd service management. |
| **5** | **Pointer & Boot File Sprawl** | Up to 15 different `continue.md` and pointer files drifted out of sync across agents. | Sub-projects created local memory files, causing agents to read outdated instructions. | **Single Desk Invariant:** Exactly ONE root `CONTINUE.md` and ONE `AGENTS.md`. Hard CI check fails if island files are created. |
| **6** | **Product Facts in Pointers** | Agents wrote live business logic (`PURCHASES_ENABLED`, horse names, fee %) into static rule files. | Lack of clear boundary between desk instructions and project state. | **Programmatic Desk Linter:** Automated linter (`just check`) enforcing byte limits and forbidding product state in rule files. |
| **7** | **"Data Exists, UI Empty"** | Prudentia had 10 starts / 2 wins in `race-record.json`, but UI showed `—–0–0`. | UI components defaulted to empty strings instead of throwing validation warnings when data resolution failed. | **Typed Schema Contracts:** Zod/Pydantic contracts with fail-visible alerts for missing required fields on listed horses. |
| **8** | **Filename & Naming Drift** | Official name was updated to *Lady Ketchikan*, but generated contracts were still titled `(unnamed)`. | File generator relied on manual string overrides rather than canonical horse model properties. | **Immutable Document Pipeline:** Legal engine strictly reads from validated entity records in `@evo/db_models`. |
| **9** | **Dual Template Drift** | DOCX generator (Python) and markdown generator (TypeScript) drifted in clause language. | Two independent implementations of the legal agreement text existed simultaneously. | **Single Legal Compiler:** Single canonical compiler (`@evo/legal_engine`) generating both DOCX and Markdown with 100% snapshot parity. |
| **10** | **Nested Repo Boundary Failures** | `02_website/` had its own `.git` repo inside `evo_01`, breaking Vercel builds and relative imports. | Git sub-repository structure prevented shared code imports (`_shared/`) at deploy time. | **Clean Monorepo Topology:** Single root repository with workspace packages (`packages/brand_dna`, `packages/db_models`). |
| **11** | **Dynamic `sys.path` Hacks** | Python scripts injected `sys.path.append('../../api')` at runtime to resolve models. | Lack of standard package packaging (`pyproject.toml` / workspace install). | **Standardized Packaging:** Workspace packages installed in editable/wheel mode (`pip install -e` or `uv`). |
| **12** | **Repository Context Bloat** | Repo swelled to ~8GB with video renderers, openreel editor, and `.venv` directories. | Heavy operator satellite tools lived inside the core web codebase, choking agent context windows. | **Quarantined Studio Hub:** Video and data scrapers isolated in `/studio/`, completely decoupled from `/apps/web/`. |
| **13** | **Franken-Auth Split** | Firebase Auth handled client login while Supabase handled database and holdings. | Migration from GCP was stopped halfway, leaving a brittle dual-auth configuration. | **Pure Supabase Auth:** Single unified Auth & Database layer on Supabase with Row Level Security (RLS). |
| **14** | **Scope Leak During Unfinished Work** | Agents started research on v2 (T-REX / ANTA tokenization) while v1 UI bugs were still open. | No physical separation between current active sprint and long-term backlog. | **Active Sprint Isolation:** `ACTIVE_SPRINT.md` holds current work only; future ideas must be parked in `control_center/roadmap/PARKED_IDEAS.md`. |

---

## 3. Programmatic Guardrails Checklist for `evo_02`

To guarantee that these mistakes cannot re-occur, `evo_02` must implement the following automated test gates in `just check`:

### Gate 1: Desk Integrity (`scripts/desk_check.py`)
- [ ] Only root `CONTINUE.md` and `control_center/sprints/ACTIVE_SPRINT.md` may contain active tasks.
- [ ] No product facts allowed in static pointer files.
- [ ] Pointer files must not exceed byte caps (max 900 bytes).

### Gate 2: Test Runner Safety
- [ ] Unit tests must execute against temporary SQLite/mock fixtures.
- [ ] Production and staging database connection strings are blocked in test configuration.

### Gate 3: Legal Golden Snapshots
- [ ] Legal engine automatically compiles PDS and SA documents for Nellie (Lady Ketchikan) and TML (Mulan).
- [ ] Golden-file comparison asserts 100% diff-free parity against approved DSL Manual markdown/DOCX snapshots.

### Gate 4: Headless Operator Walk (`scripts/operator_walk.py`)
- [ ] Automated headless cURL / Playwright checks verify:
  - Marketplace routes return HTTP 200 with non-empty pricing and non-empty race records.
  - PDS/SA preview routes render valid markdown.
  - Checkout session creation fails with HTTP 403 when `PURCHASES_ENABLED !== "true"`.
  - Stripe webhook handles duplicate event replay idempotently (HTTP 200 without duplicate row insertion).

### Gate 5: Monorepo Package Cleanliness
- [ ] Zero relative imports escaping project boundaries (`../../`).
- [ ] All cross-project code shared via `@evo/*` workspace packages.
- [ ] TypeScript strict mode enabled with zero compile warnings.
