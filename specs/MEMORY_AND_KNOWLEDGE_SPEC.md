# `evo_00` Specification: Lean Memory & Knowledge Architecture

**Status:** LOCKED SPECIFICATION  
**Date:** 2026-08-17  
**Context:** Migration to `evo_02` — Establishing agent persistent memory, on-demand code mapping, and human operator documentation without tooling bloat.

---

## 1. Executive Summary: The 2-Piece Lean Core

Rather than running multiple competing memory servers and graph daemons, `evo_02` adopts a strictly unified, zero-bloat architecture:

```
┌──────────────────────────────────────────────────────────────────────────────┐
│ 1. THE DESK & OBSIDIAN VAULT (In-Repo: /evo_00/ + CONTINUE.md)               │
│    • Human-authored Markdown ADRs, sprint queues, and locked facts           │
│    • Pure Markdown with [[Wikilinks]], zero database/binary lock-in          │
└──────────────────────────────────────┬───────────────────────────────────────┘
                                       │ Indexed & Queried via MCP
                                       ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│ 2. OPERATOR MEMORY (External Host MCP Server: gbrain)                        │
│    • Single unified persistent memory graph for all agents across sessions   │
│    • Tracks business rules, meeting notes, founder policies, past decisions  │
│    • Detects duplicate/prior unfinished work across past sessions            │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Tool Evaluation & Roles

### 1. `garrytan/gbrain` (Unified Persistent Memory & Prior Work Detector)
- **Role:** Eliminates cross-session amnesia. 
- **Unfinished Work Detection:** When a new task or topic is started, `gbrain` checks if a similar task was attempted previously, surfacing past context, half-finished PRs, or known blockers so work is not duplicated or lost.
- **Integration:** Global MCP server on the host machine (`http://localhost:3456/` or stdio).

### 2. `garrytan/gstack` (Headless QA Pattern)
- **Role:** Eliminates the "70–80% trap" by running automated browser checks before tasks are marked complete.
- **Integration:** Implemented via Playwright in `scripts/operator_walk.py`.

### 3. `gsd-build/gsd-2` (Spec-Driven Execution in Git Worktrees)
- **Role:** Autonomous task execution in isolated git worktrees without cluttering the main branch.

---

## 3. On-Demand Enhancements (No Background Daemons)

| Enhancement | Tooling | How it is Invoked | Purpose |
| :--- | :--- | :--- | :--- |
| **Code Structure Slicing** | `repomix` / Tree-sitter | `just repomap` CLI recipe | Dumps AST signatures into agent context in ~500 tokens when navigating multi-file packages. |
| **Isolated Execution** | `gsd-2` / `git worktree` | CLI command | Runs long-running or autonomous agent tasks in isolated git worktrees. |
| **Verification Gate** | `just check` + Playwright | Single terminal command | Automated headless operator walk + snapshot tests before marking tasks done. |

---

## 4. Tooling Anti-Patterns (Explicitly Dropped)

- ❌ **No Multiple Memory MCPs:** Dropped competing servers (`Graphiti`, `Mem0`). `gbrain` serves as the single unified memory layer.
- ❌ **No Background AST Daemons:** No continuous language server background daemons; AST maps are generated on-demand via simple CLI recipes.
- ❌ **No Database Files in Monorepo:** SQLite/vector DB files are never committed to `evo_02`.
