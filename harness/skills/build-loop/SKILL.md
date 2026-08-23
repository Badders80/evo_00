---
name: build-loop
description: Run builds via plan, review, approve, chunk, execute, audit.
version: 1.1.0
author: Evo, Hermes Agent
license: MIT
platforms: [linux, macos]
metadata:
  hermes:
    tags: [pipeline, planning, execution, audit, multi-model, orchestration]
    related_skills: [kimi-code-audit, opencode, writing-plans, hermes-agent]
---

## When to Use

- A build task spans plan → execution → verification and needs founder sign-off
- Multi-file features or sprints where an independent audit is required before "done"
- Resuming interrupted builds — the loop picks up from disk artifacts

Do NOT use for: one-line fixes (just fix them), pure research/questions, or tasks with
no verifiable artifact. For audits alone, use `kimi-code-audit` directly.

# Build Loop

One skill, five stages, two hard approval gates. The host agent (whatever model loads
this skill) is the ORCHESTRATOR — it never refers to itself as a specific product in
artifacts or reports. Engines are roles resolved through the roster table below.

## Engine Roster

**Single source of truth for stage routing. When a model changes, edit THIS TABLE ONLY.
Stages reference ROLES, never model names.**

| Role | Primary | Fallback | Cost |
|---|---|---|---|
| ORCHESTRATOR | host agent (runs this skill) | — | session |
| PLAN_REVIEWER_A | `opencode run --model opencode/nemotron-3-ultra-free` | any different-lab free model | $0 |
| PLAN_REVIEWER_B | `ollama run deepseek-v4-pro:cloud` | `kimi-k2.7-code:cloud` | paid |
| EXECUTOR | `opencode run` (nemotron-3-ultra-free) | `deepseek-v4-flash:cloud` | $0 / paid |
| GATE | discovered per-repo (see Gate Autodiscovery) | raw language tooling | $0 deterministic |
| AUDITOR | `/kimi-code-audit` procedure with kimi-k2.7-code, diff-only | full-file audit on flagged files only | paid |

**Fallback ladder is mechanical:** primary engine fails or times out twice → swap to
fallback row → note the swap in the current artifact header. Never retry a dead engine
more than twice.

**Token routing principle:** free models build and iterate; paid tokens are spent only
on judgment (plan critique, final audit); deterministic gates absorb volume at zero cost.

## Stage Flow

Each stage reads the previous stage's artifact and writes its own. State lives on disk,
not in chat memory. This makes the loop resumable across sessions and days.

```
<project>/build-loop/
├── plan.md              ← Stage 1
├── review-synthesis.md  ← Stage 2 (+ approval gate record)
├── chunks.md            ← Stage 3 (chunk status ticked during Stage 4)
└── audit-report.md      ← Stage 5
```

Default artifact home is `<project>/build-loop/`. Gitignore optional — founder's call.

### Resume rule (run FIRST on every invocation)

1. Check `<project>/build-loop/` for existing artifacts.
2. Resume at the first missing or incomplete artifact.
3. If `review-synthesis.md` exists with `APPROVED:` recorded → skip to Stage 3.
4. If `review-synthesis.md` exists **without** `APPROVED:` → stay at GATE 1. Do not write or touch `chunks.md`.
5. If resuming Stage 4: scan `chunks.md` for the first unticked `[ ]` chunk, inspect `git log` / `git status` for partial commits, resume from that chunk delta (not the whole chunk again).
6. If no artifacts exist → start at Stage 1.

### Stage 1 — PLAN (ORCHESTRATOR)

Draft the implementation plan from the founder's brief into `build-loop/plan.md`:

- Goal (one sentence), architecture approach, tech constraints
- Exact file paths to create/modify
- Per-chunk definition-of-done and verification commands
- Out of scope list (prevents executor scope creep)
- **Evolution / locked-surface constraints** (when working on Evolution Stables or `evo_*`):
  - Source only `evo_00/doc/` and `evo_00/specs/` — never research folders
  - No `PURCHASES_ENABLED` flips
  - No guest checkout paths
  - No Tokinvest copy on live storefront / campaign surfaces
  - Canonical names and roster from `evo_00/doc/ASSET_LOCK.md` only

Keep plans tight. A plan that needs its own plan is too big — split the task.

### Stage 2 — REVIEW (PLAN_REVIEWER_A + PLAN_REVIEWER_B)

Dispatch BOTH reviews independently — neither sees the other's output:

```bash
# Reviewer A (free): give file path, not chat history
cd <project> && opencode run 'Read build-loop/plan.md. Critique as senior engineer:
wrong assumptions about the codebase, missing files, broken dependencies,
scope gaps, verification steps that would not actually verify. Be specific:
file paths and line-level issues. Output a numbered findings list.' \
  --model opencode/nemotron-3-ultra-free

# Reviewer B (paid): pipe the plan text via prompt file
ollama run deepseek-v4-pro:cloud < build-loop/plan-review-prompt.txt
```

Synthesize into `build-loop/review-synthesis.md`:

- 🔥 **Both agree** — real problems, non-negotiable fixes
- 🟠 **One flagged** — table: finding / which model / confirmed by other?
- 🔵 **Contested** — models diverge → both options + your recommendation
- **Orchestrator gap analysis** — what BOTH missed (you have context they don't)

Apply agreed fixes to plan.md. Leave contested items open for the founder.

### ⛔ GATE 1 — FOUNDER APPROVAL

Present the synthesis. The founder either:

- **Approves** → record `APPROVED: <date>` at top of review-synthesis.md → proceed to Stage 3
- **Contours** → revise plan.md, re-run Stage 2 (or just contested items)

**No execution of any kind before this gate. Nothing proceeds without explicit approval.**

**Hard rule:** Do not create, write, or edit `chunks.md` until `APPROVED:` is recorded.
GATE 1 sits after Stage 2 only. Stage 3 is post-approval.

### Stage 3 — CHUNK (ORCHESTRATOR)

Split approved plan into `build-loop/chunks.md`:

- Each chunk = one bounded task: exact files, exact commands, DOD, commit message
- Order chunks so each leaves the repo green
- Mark any chunk needing founder decisions as BLOCKED-ON-FOUNDER

### Stage 4 — EXECUTE (EXECUTOR + GATE)

#### Pre-flight (every chunk)

1. **Dirty-tree check:** run `git status --porcelain`.  
   - Empty → proceed.  
   - Non-empty → **halt**. Do not auto-stash. Report the dirty paths and wait for founder (clean commit, explicit discard, or BLOCKED-ON-FOUNDER).  
2. Confirm GATE command via Gate Autodiscovery (below) if not already pinned for this project.

#### Per chunk, in order

1. Dispatch EXECUTOR with that chunk only (one chunk at a time — never batch):

```bash
cd <project> && opencode run '<chunk spec from chunks.md>. Do not touch files
outside this spec. Run <gate command> when done. Commit with: <message>' \
  --model opencode/nemotron-3-ultra-free
```

2. **GATE:** run the gate command yourself. Verify real output — do not trust the
   executor's claim. Green → tick chunk in chunks.md, next chunk. Red → fix loop
   (max 2 retries) → escalate to fallback engine → still red → stop, report.
3. BLOCKED-ON-FOUNDER chunks halt the loop until answered.

**Executor never self-approves.** Green gate ≠ done; only Stage 5 passing = done.

#### Gate Autodiscovery (run once per project, cache in plan or chunks header)

1. Prefer project-documented gate if present and verified to exist.
2. Else try, in order: `just check` → `turbo run lint typecheck test` → `pnpm run lint && pnpm run typecheck && pnpm run test` → language-native (`tsc --noEmit`, `pytest`, `cargo test`, …).
3. Confirm the binary/script exists before dispatch (`which just`, recipe in Justfile, script in package.json). Missing high-level driver → fall back; do not fail the stage on a missing tool name alone.
4. Record the chosen gate command in the current artifact header so resume does not rediscover differently.

### Stage 5 — AUDIT (AUDITOR)

Run the `kimi-code-audit` skill procedure on the full diff:

- Inventory every claim from plan.md + chunks.md
- Diff-scope first (`git diff <start-ref> --stat`), full-file reads only on flagged files
- Real command output for every test/typecheck claim — invented exit codes are FAIL
- Write PASS/FAIL/WARN table with `file:line` evidence to `build-loop/audit-report.md`

**Done means:** audit verdict PASS (or WARNs accepted by founder). Merge/deploy remains
founder-only, always.

## Rules

1. Stages never name models directly — resolve all engines through the roster table.
2. One cycle per session: if input tokens climb turn-over-turn, hand off via artifacts
   and continue fresh.
3. Every subagent/CLI claim gets verified against the filesystem before reporting.
4. Dirty working tree after a chunk = chunk not done.
5. Never apply migrations or push to production/live surfaces inside this loop —
   those are separate founder-gated actions.
6. Never write `chunks.md` before GATE 1 approval is recorded.
7. Never auto-stash a dirty tree; halt and ask.
8. Evolution work: source `evo_00/doc/` only; no purchases flag, no guest checkout,
   no Tokinvest on live surfaces.

## Pitfalls

- **Marathon sessions snowball context.** Each stage should be a small-context call
  reading files, not a growing chat. Observed 21K→220K token creep in relay runs
  without artifact isolation.
- **Nested git repos eat commits.** Check for `.git` subdirectories before dispatching.
- **Executor timeouts leave partial work.** Verify filesystem state and `git status`
  before re-dispatching a failed chunk — send the delta, not the whole chunk again.
- **Free-model quality drift.** If EXECUTOR output fails the gate 3+ times on simple
  chunks, suspect the model, not the spec — swap to fallback early rather than burning
  retries.
- **Reviewer anchoring.** Reviews must be independent (no cross-contamination). Synthesis
  happens only after both return.
- **Harness vs Justfile drift.** A repo may document five gates while `just check` only
  runs three. Discover and pin the real command; do not trust the doc alone.
