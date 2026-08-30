# The DSL — Master Commercial & Syndicate Specification

**Status:** APPROVED & LOCKED SSOT  
**Document Class:** Master Product Specification & Legal Constitution  
**Date Locked:** 2026-08-17  
**Applies To:** All Digitally Syndicated Leases (DSLs), PDS/SA Generators, Marketplace Listings, and Quarterly Distributions in `evo_02`.

---

## 1. Commercial Pricing Engine

$$\\text{list} = \\text{cost} \\times 1.05 \\times 1.03 \\quad (\\text{round UP to whole NZD})$$
$$\\text{M} = \\text{list} \\times \\text{stake}\% \\quad (\\text{round UP to whole NZD})$$

### 1.1 Fee Disclosures & Margins
* **Evolution Operating Margin:** **5.0%** margin on base lease/keep costs.
* **Payment Processing Buffer:** **3.0%** buffer absorbing Stripe credit card processing and gateway transaction fees.
* **GST Treatment:** All quoted retail prices, initial join amounts ($5\\times M$), and recurring monthly rates ($M$) are **100% GST-inclusive** (15% GST remitted to the New Zealand Inland Revenue Department).

---

## 2. Syndicate Billing Models

Every DSL declares a `payment_style`:

| Payment Style | Mechanism | When Applied |
| :--- | :--- | :--- |
| `subscription_float` | **$5\\times M$ join float** (3 months deposit reserve + 2 months prepaid keep), then **$M$ monthly** to maintain a constant 5-month float buffer. | Default for live standard DSLs (e.g. Nellie, Mulan). |
| `upfront` | **Single lump-sum payment** for the full lease term. No recurring keep, no float billing, no Stripe subscription. | Historical Tokinvest listings (Prudentia, Hottathanafantasy, completed First Gear) and new fully-paid listings (Manolo). |

### 2.1 Subscription Float Exit & Unused Fund Refunds
Upon formal termination or maturity of a `subscription_float` syndicate lease, all unused prepaid keep and security deposit reserve funds are **refunded pro-rata** to the investor’s verified payment method within 14 business days.

### 2.2 Upfront Model
* No monthly subscription.
* No $5\\times M$ float deposit.
* Prize money distributions still follow the standard 75/25 investor/owner split pro-rata to stake held.
* Exit mechanics are governed by the DSL `close_style` but no recurring billing is paused or refunded.

---

## 3. Syndicate Exit & Close Styles (`close_style`)

Every horse listing (DSL) explicitly declares its governing exit mechanism in its prospectus and contract pack:

| Close Style Parameter | Name | Mechanism | When Applied |
| :--- | :--- | :--- | :--- |
| `fourteen_day` | **Standard 14-Day Notice (Case B)** | 14 calendar days written notice when the underlying head lease concludes or horse is retired. No penalty buyout. | **Default for all new standard DSLs.** |
| `three_x_remaining` | **3× Buyout Liquidating Exit (Case B1)** | 3× remaining lease value buyout paid to syndicate holders where the head lease provides liquidation proceeds. | **Configurable per DSL** when negotiated in head lease. |

---

## 4. Prize Money Governance & Quarterly Distributions

$$\\text{Investor Distribution Pool} = \\text{Officially Published Gross Stakes} \\times 75\\% \\times \\text{Stake}\\%$$

```
┌────────────────────────────────────────────────────────────────────────┐
│                   OFFICIAL NZTR GROSS STAKES (100%)                    │
├───────────────────────────────────┬────────────────────────────────────┤
│  75.0% INVESTOR SYNDICATE POOL    │  25.0% OWNER EXPENSE BUFFER        │
│  • Distributed pro-rata to holders│  • Retained by owner               │
│  • Direct credit to verified bank │  • Absorbs jockey & trainer fees   │
│  • Zero capital call exposure     │  • Absorbs nomination & race fees  │
└───────────────────────────────────┴────────────────────────────────────┘
```

### 4.1 Ground-Truth Stake Anchoring
* All distribution calculations are calculated strictly from **officially published NZTR / LoveRacing gross stakes earnings** (100% purse).
* **The Fixed-Cost Shield:** New Zealand Thoroughbred Racing automatically deducts ~15% to 18% at source for jockeys and trainers. The 25% owner retention absorbs these deductions plus nomination and race-day incidentals. **Investors receive their clean 75% share of official gross stakes won without ever being asked for additional capital contributions.**

### 4.2 Quarterly Distribution Cadence & The Carry-Forward Cut-Off Rule
* **Cadence:** Distributions and financial statements are issued quarterly (Q1, Q2, Q3, Q4).
* **The Carry-Forward Cut-Off Rule:** Stakes won in the final calendar month of a quarter (e.g. late June) whose cash settlement has not yet cleared into Evolution Stables bank accounts from NZTR are carried forward to the following quarter’s distribution statement.

### 4.3 The 2-Month Paid-Up Qualification Rule
* **Anti-Speculation Rule:** An investor must have been an active, paid-up syndicate member for **at least two (2) full consecutive calendar months** prior to the race date to qualify for prize money returns from that race.
* **Protection:** This prevents speculative buyers from purchasing a stake immediately prior to a major feature or Group race to capture returns without contributing to the horse’s preparation.

---

## 5. Equine Welfare & Regulatory Compliance

### 5.1 Absolute Trainer Welfare Supremacy (SA Clause 6)
* The licensed Trainer and Racing Manager hold **sole, absolute, and unchallengeable discretion** regarding all training regimes, race nominations, trackwork, spelling, and veterinary care.
* Zero interference: Neither Evolution Stables nor any syndicate investor holds the power to override veterinary or welfare decisions.

### 5.2 Regulatory Governance (SA Clauses 12 & 13)
* **Authorised Syndicator:** Evolution Stables operates as an Authorised Syndicator under New Zealand Thoroughbred Racing (NZTR) Rules of Racing and Syndication Code of Practice.
* **Manager Removal & Mediation:** Syndicate disputes and manager removal powers are governed under NZTR Code of Practice Rule 22.1.
* **AML/CFT:** All investor onboarding and customer due diligence adhere to New Zealand AML/CFT standards.

### 5.3 Tokinvest & Crypto Purge
* Tokinvest, crypto tokens, digital asset exchanges, and Dubai VARA regulations are **completely purged with zero trace** across all new templates, databases, and investor communications.

---

## 6. Campaign Sizing & Listed Pool Mechanics

### 6.1 Listed Pool Identity
A DSL only models the **listed stake pool**. The lead owner retains the remainder implicitly and it is not stored or displayed in the build.

$$\\text{total\\_shares} = \\text{allocated} + \\text{reserved} + \\text{available}$$

### 6.2 Step-Unit Math
`total_shares` is the number of purchasable **step units**, not percentage points:

$$\\text{total\\_shares} = \\frac{\\text{listed\\_stake\\_pct}}{\\text{stake\\_step\\_pct}}$$

Examples:
* 5% listed stake, 0.5% increment → `total_shares = 10` units.
* 5% listed stake, 1.0% increment → `total_shares = 5` units.
* 10% listed stake, 0.25% increment → `total_shares = 40` units.
* 10% listed stake, 1.0% increment → `total_shares = 10` units.

Each unit represents exactly one minimum purchasable step. The reservation RPC enforces that `units` is a positive integer-equivalent number of step units.

### 6.3 Campaign Status Semantics
| Status | Meaning |
| :--- | :--- |
| `draft` | Internal preparation. |
| `coming_soon` | Public teaser, no checkout (Manolo). |
| `coming_soon_details` | Full details visible, reservations allowed for an opening launch. |
| `listed` | Live and open for checkout. |
| `fully_subscribed` | All listed units allocated (Prudentia, Hottathanafantasy). |
| `completed` | Historical track-record entry; checkout blocked (First Gear). |
