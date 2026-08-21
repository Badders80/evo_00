# Asset Lock — Live Roster vs Research

**Status:** LOCKED 2026-08-21  
**Governs:** `evo_02` knowledge registry, Mission Control, storefront, SQL seed  
**Does not govern:** `racing-research/` (market intel only; never seed live trainers/owners)

---

## Naming

| Surface | Locked form |
| :--- | :--- |
| Public / MC / registry | **Evolution Stables** |
| Formal legal packs only (PDS/SA) | Evolution Stables Limited |
| Stephen Gray entity | **Stephen Gray Racing** — never “Stables”, never “Grey Stables” |
| Stephen Gray yard | **Copper Belt Lodge**, 160 Green Road, RD6, Palmerston North 4476 |
| Contact | https://stephengrayracing.com/contact/ |

Official spelling is **Gray** (matches `stephengrayracing.com`). Copper Belt Lodge is the **address**, not the trading name.

---

## Live trainers (only these)

1. **Barbara Kennedy Racing** — Byerley Park (Nellie)
2. **Wexford Stables** — Lance O'Sullivan & Andrew Scott, Matamata (Prudentia, Hotta, Manolo)
3. **Stephen Gray Racing** — Copper Belt Lodge, Palmerston North (Mulan, First Gear)

**Out of live registry (research only):** Te Akau, Sam Bergerson, Allan Sharrock, Donna Logan, Stephen Marsh.

---

## Live owners (lessors)

| Horse | Owner | Contact |
| :--- | :--- | :--- |
| Nellie | **B.A.X Bloodstock** | Kylie Bax |
| Prudentia | **B.A.X Bloodstock** | Kylie Bax |
| Hottathanafantasy (Coco) | **B.A.X Bloodstock** | Kylie Bax |
| I Stole A Manolo | **B.A.X Bloodstock** | Kylie Bax |
| Mulan | **Stephen Gray Racing** | Stephen Gray |
| First Gear | **Stephen Gray Racing** | Stephen Gray |

Evolution Stables is the **syndicate manager**, not an owner row.

---

## Live horses

| Slug | Legal name | Sire × Dam | Chip | Status | Website |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `nellie` | Lady Ketchikan (NZ) | Almanzor (FR) × Night Danza (AUS) | 985125000137408 | listed | yes |
| `tml-x-yearn` | Turn Me Loose x Yearn 2023 | Turn Me Loose (NZ) × Yearn (NZ) | 985125000128426 | coming_soon | yes |
| `prudentia` | Prudentia (NZ) | Proisir (AUS) × Little Bit Irish (NZ) | 985125000126462 | fully_subscribed | yes |
| `hottathanafantasy` | Hottathanafantasy (NZ) | Contributer (IRE) × Whiffle (USA) | 985125000139165 | fully_subscribed | yes |
| `i-stole-a-manolo` | I Stole A Manolo (NZ) | Satono Aladdin (JPN) × Canuhandleajandal (NZ) | 985125000139219 | coming_soon | yes |
| `first-gear` | First Gear (NZ) | Derryn (AUS) × A'Guin Ace (NZ) | 985125000126713 | completed | **yes — track record, no checkout** |

Checkout is open only when `listingStatus === 'listed'`.

---

## Research leftovers (do not seed)

- Te Akau / Mark Walker / Sam Bergerson
- Allan Sharrock
- Sword of State, Profondo (no live campaign)
- Fabricated pedigrees: Preferment × Prudence, Hot 'N' Off The Press × Fantasy Island, Profiteer × Hot Fuss, Noverre × Stolen Rose, Tavistock × Gear Up
- “Alex Bax” as founder/owner (founder is **Alex Baddeley**; B.A.X contact is **Kylie Bax**)
