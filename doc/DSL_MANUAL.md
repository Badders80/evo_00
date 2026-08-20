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

## 2. Syndicate Billing & Subscription Float Model

* **The $5\\times M$ Join Float:**
  * At initial checkout, an investor pays **$5\\times M$**.
  * **Breakdown:** 3 months security deposit reserve + 2 months prepaid keep.
* **The Monthly $M$ Keep Subscription:**
  * Commencing on Month 2, the investor pays **$M$ monthly** to maintain the constant 5-month float buffer.
* **Contract Exit & Unused Fund Refunds:**
  * Upon formal termination or maturity of the syndicate lease, all unused prepaid keep and security deposit reserve funds are **refunded pro-rata** to the investor’s verified payment method within 14 business days.

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
