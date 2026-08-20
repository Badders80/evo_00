# Migration Bridge: 04. Legal Pack Diff & DSL Manual Audit

**Status:** RECONCILED & AUDITED SPECIFICATION  
**Governing SSOT:** `evo_00/doc/DSL_MANUAL.md`, `evo_00/doc/OPERATIONS_SOP.md`, and `evo_01/migration/EVOLUTION_OPERATIONS_MANUAL.md`  
**Purpose:** Exhaustive pre-launch legal audit guaranteeing 100% compliance with New Zealand Thoroughbred Racing (NZTR) Syndicate Regulations, the DSL Manual, and total purge of legacy Tokinvest artifacts.

---

## 1. Product Disclosure Statement (PDS) Section-by-Section Audit

| Section | Mandatory Invariant | Verification Checklist |
| :--- | :--- | :--- |
| **§1. Title & Structure** | Syndicate Name matches official NZTR format (`<Horse Name> Racing Syndicate`). | - [ ] Zero tokenization, security token, or cryptocurrency language.<br>- [ ] Discloses registered Syndicate Manager (Evolution Stables Ltd). |
| **§2. Asset Specifics** | Formal pedigree, foaling year, breeder, microchip, and gender accurately stated. | - [ ] Lady Ketchikan (Nellie): Almanzor (FR) × Night Danza (AUS), foaled 2023-10-20, chip 985125000137408 (LoveRacing 454763). Official name overrides the Almanzor × Night Danza placeholder.<br>- [ ] Turn Me Loose x Yearn (2023 Bay Filly). |
| **§3. Commercial Model** | Pricing formula: $\text{list}_{\text{horse}} = \text{cost} \times 1.05 \times 1.03$ (100% GST-inclusive). $M = \lceil \text{list}_{\text{horse}} \times \text{stake}\% \rceil$. | - [ ] 5% Evolution margin explicitly disclosed (§11).<br>- [ ] 3% payment processing buffer explicitly disclosed (§11).<br>- [ ] Nellie: $7,000/mo wholesale cost $\rightarrow$ $7,571/mo list $\rightarrow$ **$76/mo** for 1% stake ($M$).<br>- [ ] TML x Yearn: $6,000/mo wholesale cost $\rightarrow$ $6,489/mo list $\rightarrow$ **$65/mo** for 1% stake ($M$). |
| **§4. Float & Billing** | Initial join float = $5\times M$ (3 mo deposit + 2 mo prepaid keep). Ongoing = $M$/mo. | - [ ] Nellie 1% join float: **$380.00** ($5\times \$76$).<br>- [ ] TML x Yearn 1% join float: **$325.00** ($5\times \$65$).<br>- [ ] Explicit disclosure: unused float refunded pro-rata upon lease termination.<br>- [ ] Default payment style = `subscription_float`. |
| **§5. Gross Stakes Split** | Gross Stakes: $75\%$ Investor Pool / $25\%$ Owner Retention. | - [ ] Calculated strictly on **Officially Published Gross NZTR Stakes**.<br>- [ ] 25% owner retention absorbs all NZTR source deductions (~15–18%), trainer/jockey percentages, nominations, and acceptances.<br>- [ ] Expressly shields investors from any subsequent capital calls. |
| **§6. Exit & Close Style** | Default: `fourteen_day` notice (Case B). Configurable: `three_x_remaining` buyout (Case B1). | - [ ] Verified per-DSL field `close_style`.<br>- [ ] Investor entitled to immediate pro-rata unused float refund upon close. |

---

## 2. Syndicate Agreement (SA) Mandatory Clauses Audit

| Clause | Legal Mandate | Verification Status |
| :--- | :--- | :--- |
| **SA Clause 6: Equine Welfare** | Trainer & Racing Manager hold 100% sole discretion on racing, spelling, veterinary treatment, and retirement. | - [ ] Investor has zero authority to force horse onto track or overrule trainer. |
| **SA Clause 8: Default & Forfeiture**| Unpaid monthly keep beyond 14 days triggers automatic notice; 30-day default draws on float reserve. | - [ ] Clean float drawdown procedure codified without unexpected investor penalties. |
| **SA Clause 11: Management Fee** | 5% syndicate management margin is baked into monthly rate. | - [ ] No separate hidden management invoices or off-platform accounting fees. |
| **SA Clause 12: NZTR COP 22.1** | Syndicate Manager removal mechanism adheres strictly to NZTR Code of Practice Rule 22.1. | - [ ] Requires 75% majority vote of members or NZTR Board intervention for cause. |
| **SA Clause 13: Dispute & Law** | Governed exclusively under New Zealand law and NZTR Racing Rules. | - [ ] Complete removal of Dubai / DIFC / foreign arbitration clauses. |

---

## 3. Regulatory & Anti-Money Laundering (AML/CFT) Gate

- [ ] **NZTR Declaration:** Every subscriber executes standard NZTR Member Declaration (no disqualifications, over 18 years of age, NZ resident or verified international investor).
- [ ] **Stripe Identity KYC:** Automated ID & biometric check before first holding allocation.
- [ ] **Zero Tokinvest Purge:** Search sweep across all code, markdown, and generated PDFs returns 0 occurrences of `tokinvest`, `vara`, or `dubai`.
- [ ] **Contract Immutability:** Generated PDS/SA PDFs are cryptographically SHA-256 hashed and stored in Supabase Vault before listing goes live.
