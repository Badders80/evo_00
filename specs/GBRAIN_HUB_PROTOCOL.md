# gbrain Canonical Knowledge Hub — Feeding Protocol

**Status:** ACTIVE (seeded 2026-08-20)
**Owner:** Operator Desk (`evo_00`) via any agent with a gbrain MCP token.

## What this is
The `evolution-stables/hub` page in gbrain (`localhost:3456`) is the canonical facts +
project knowledge layer. It is a **recall layer, not a source of truth**: every fact card
points back to its locked source doc (the SSOT) via `derives_from` links.

## Layout
- `evolution-stables/hub` — index / entry point.
- `fact/*` — canonical fact cards (pricing, billing-float, exits, distributions,
  welfare-and-law, design-tokens, voice-and-tone, infrastructure).
- `entity/*` — ecosystem islands (evolution-stables, evo_00/01/02/03).
- Source docs (`doc/*`, `specs/*`, `migration_bridge/*`) stay the SSOT and are already in gbrain.

## Feed triggers (agents MUST do this)
1. A canonical fact is locked or changes → update the matching `fact/*` card + add timeline.
2. A decision is appended to `control_center/DECISION_LOG.md` → reflect it.
3. A migration milestone completes → update links + `add_timeline_entry` on the hub.
4. Topology/entity changes → update `entity/*` + re-link.

## How (MCP, not CLI — the CLI is blocked while `gbrain serve` holds the PGLite lock)
See the `gbrain-hub` skill (`/home/evo/.agents/skills/gbrain-hub/SKILL.md`) for the exact
op names, param shapes, the YAML-colon gotcha, and verification steps. In short:
`put_page` (markdown+frontmatter), `add_link` (`from/to/type`), `add_tag`, `add_timeline_entry`
(`summary`).

## Verification gate
Every feed must end with a real `query` that returns the changed fact card, and `get_health`
holding `brain_score >= ~75`. No "done" without that evidence.

## Auto-sync from git (wired 2026-08-20)

The control plane is now a git repo with a registered gbrain source. This makes the repo the feed:
milestone commits to `evo_00` docs are the canonical fact changes.


- Source: `evo00` → `/home/evo/new/evo_00` (git-source, federated into default search).

- After a milestone commit, refresh the brain (CLI needs the DB, so a brief stop is required):

  `systemctl --user stop gbrain-mcp.service`

  `gbrain sync --source evo00 && gbrain embed --stale`

  `systemctl --user start gbrain-mcp.service`

- Do NOT commit `migration_bridge/Migrated Existing HLTs/` — it holds real investor KYC/ID

  documents; it is gitignored and must stay out of git and the brain.
