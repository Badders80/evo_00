# Migration Bridge: 02. Data Mapping & Column-Level Schema Transformation

**Status:** RECONCILED & AUDITED SPECIFICATION  
**Target Database:** Supabase PostgreSQL (`Evolution-3.0`)  
**Purpose:** Defines exact column-by-column schemas, data types, constraints, reservation mechanisms, and legacy boundary rules from `evo_01` to `evo_02`.

---

## 1. Table: `inventory` (Horse Campaign & DSL Catalogue)

| Column Name | Target Data Type | Constraints / Default | Semantics & Transformation Rule |
| :--- | :--- | :--- | :--- |
| `id` | `uuid` | `PRIMARY KEY DEFAULT gen_random_uuid()` | Generated fresh UUID |
| `slug` | `text` | `UNIQUE NOT NULL` | Exact match (e.g. `nellie`, `tml-x-yearn`) |
| `legal_name` | `text` | `NOT NULL` | Formal NZTR name (`Lady Ketchikan`, `Turn Me Loose x Yearn 2023`) |
| `barn_name` | `text` | `NOT NULL` | Familiar barn name (`Nellie`, `Mulan`) |
| `sire` | `text` | `NOT NULL` | Thoroughbred lineage sire |
| `dam` | `text` | `NOT NULL` | Thoroughbred lineage dam |
| `trainer_name` | `text` | `NOT NULL` | Registered trainer (`Kylie Bax`, `Stephen Marsh`) |
| `trainer_location` | `text` | `NOT NULL` | Training facility (`Cambridge, NZ`) |
| `cost_monthly_nzd` | `numeric(10,2)` | `NOT NULL` | **Whole-horse monthly wholesale cost** (e.g. `$7000.00` for Nellie) |
| `list_price_nzd` | `numeric(10,2)` | `NOT NULL` | **Whole-horse retail list:** `CEIL(cost_monthly_nzd * 1.05 * 1.03)` (e.g. `$7571.00`) |
| `monthly_keep_unit_nzd`| `numeric(10,2)`| `NOT NULL` | **Per-1% stake rate ($M):** `CEIL(list_price_nzd * 0.01)` (e.g. `$76.00/mo`) |
| `join_float_unit_nzd` | `numeric(10,2)` | `NOT NULL` | **Per-1% initial join float ($5×M):** `5 * monthly_keep_unit_nzd` (e.g. `$380.00`) |
| `total_shares` | `integer` | `NOT NULL DEFAULT 100` | Always 100 (100 units of 1%) |
| `shares_available` | `integer` | `NOT NULL DEFAULT 100` | Remaining unallocated & unreserved units |
| `reserved_shares` | `integer` | `NOT NULL DEFAULT 0` | Active checkout reservations in progress |
| `career_starts` | `integer` | `NOT NULL DEFAULT 0` | Official career race starts |
| `career_wins` | `integer` | `NOT NULL DEFAULT 0` | Official career wins |
| `gross_stakes_nzd` | `numeric(12,2)` | `NOT NULL DEFAULT 0` | Official lifetime gross prize earnings |
| `loveracing_url` | `text` | `NULLABLE` | Official NZTR LoveRacing profile URL |
| `status` | `campaign_status` | `NOT NULL DEFAULT 'draft'` | Enum: `draft`, `coming_soon`, `coming_soon_details`, `listed`, `fully_subscribed`, `completed` |
| `close_style` | `close_style` | `NOT NULL DEFAULT 'fourteen_day'` | Enum: `fourteen_day` (default Case B), `three_x_remaining` (Case B1 per DSL) |
| `payment_style` | `text` | `NOT NULL DEFAULT 'subscription_float'`| Always `subscription_float` ($5×M join float) |
| `hero_image_url` | `text` | `NOT NULL` | Cloudflare R2 URL (`cdn.evolutionstables.nz/horses/...`) |
| `pedigree_image_url`| `text` | `NULLABLE` | Cloudflare R2 URL |
| `pds_hash` | `text` | `NOT NULL` | SHA-256 hash of immutable PDS PDF document |
| `sa_hash` | `text` | `NOT NULL` | SHA-256 hash of immutable Syndicate Agreement PDF document |
| `pds_url` | `text` | `NOT NULL` | Supabase storage URL |
| `sa_url` | `text` | `NOT NULL` | Supabase storage URL |
| `created_at` | `timestamptz` | `NOT NULL DEFAULT now()` | Timestamp |
| `updated_at` | `timestamptz` | `NOT NULL DEFAULT now()` | Auto-updated on row change |

---

## 2. Table: `checkout_reservations` (Cap Table Concurrency & Deadlock Prevention)

| Column Name | Target Data Type | Constraints / Default | Purpose |
| :--- | :--- | :--- | :--- |
| `id` | `uuid` | `PRIMARY KEY DEFAULT gen_random_uuid()` | Unique reservation record |
| `horse_id` | `uuid` | `NOT NULL REFERENCES inventory(id)` | Horse being reserved |
| `user_id` | `uuid` | `NOT NULL REFERENCES profiles(id)` | Reserving investor |
| `shares_reserved` | `integer` | `NOT NULL CHECK (shares_reserved > 0)` | Number of 1% shares reserved |
| `expires_at` | `timestamptz` | `NOT NULL` | Reservation TTL (15 minutes from session start) |
| `status` | `text` | `NOT NULL DEFAULT 'active'` | `active`, `converted`, `expired`, `released` |
| `created_at` | `timestamptz` | `NOT NULL DEFAULT now()` | Reservation creation timestamp |

* **Stored Procedures (RPC):**
  * `reserve_campaign_shares(p_horse_id, p_user_id, p_shares, p_ttl_minutes)`: Atomically decrements `shares_available` and increments `reserved_shares`.
  * `release_expired_reservations()`: Scheduled cron worker releasing reservations older than TTL back to `shares_available`.

---

## 3. Table: `profiles` (Investors & Users)

| Column Name | Target Data Type | Constraints / Default | Transformation Rule |
| :--- | :--- | :--- | :--- |
| `id` | `uuid` | `PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE` | 1:1 mapped to Supabase Auth UUID (Firebase purged) |
| `email` | `text` | `UNIQUE NOT NULL` | Lowercased, trimmed email |
| `full_name` | `text` | `NULLABLE` | Investor legal name |
| `phone` | `text` | `NULLABLE` | Contact phone number |
| `kyc_status` | `kyc_status` | `NOT NULL DEFAULT 'unverified'` | Enum: `unverified`, `pending`, `verified`, `rejected` |
| `kyc_verified_at` | `timestamptz` | `NULLABLE` | Timestamp of identity approval |
| `stripe_customer_id` | `text` | `UNIQUE NULLABLE` | Stripe customer object ID (`cus_...`) |
| `stripe_verification_session_id` | `text` | `NULLABLE` | Stripe Identity verification session (`vs_...`) |
| `kyc_audit_digest` | `text` | `NULLABLE` | SHA-256 hash of AML/KYC approval packet |
| `nztr_license_number` | `text` | `NULLABLE` | Optional NZTR owner/trainer registration ID |
| `created_at` | `timestamptz` | `NOT NULL DEFAULT now()` | Account creation date |
| `updated_at` | `timestamptz` | `NOT NULL DEFAULT now()` | Auto-updated on row change |

---

## 4. Table: `holdings` (DSL Active Subscriptions)

| Column Name | Target Data Type | Constraints / Default | Semantics & Rule |
| :--- | :--- | :--- | :--- |
| `id` | `uuid` | `PRIMARY KEY DEFAULT gen_random_uuid()` | Unique holding record |
| `user_id` | `uuid` | `NOT NULL REFERENCES profiles(id)` | Foreign key to investor |
| `horse_id` | `uuid` | `NOT NULL REFERENCES inventory(id)` | Foreign key to horse |
| `stake_percentage` | `numeric(5,2)` | `NOT NULL CHECK (stake_percentage > 0)` | Purchased stake % (e.g. `1.00` = 1%) |
| `float_months_held` | `numeric(4,2)` | `NOT NULL DEFAULT 5.0` | Active float reserve (starts at 5.0 months) |
| `float_balance_nzd` | `numeric(10,2)` | `NOT NULL` | Total float held ($5×M join deposit, e.g. `$380.00`) |
| `monthly_keep_rate_nzd`| `numeric(10,2)`| `NOT NULL` | $M monthly subscription rate (e.g. `$76.00`) |
| `stripe_subscription_id`| `text` | `UNIQUE NULLABLE` | Stripe subscription ID (`sub_...`) |
| `status` | `holding_status` | `NOT NULL DEFAULT 'active'` | Enum: `active`, `paused`, `exiting`, `cancelled`, `settled` |
| `signed_pds_hash` | `text` | `NOT NULL` | SHA-256 hash of immutable PDS agreed at checkout |
| `signed_sa_hash` | `text` | `NOT NULL` | SHA-256 hash of immutable SA agreed at checkout |
| `created_at` | `timestamptz` | `NOT NULL DEFAULT now()` | Checkout completion timestamp |
| `updated_at` | `timestamptz` | `NOT NULL DEFAULT now()` | Auto-updated on row change |

---

## 5. Table: `leads` (Waitlist & Expression of Interest)

| Column Name | Target Data Type | Constraints / Default | Purpose |
| :--- | :--- | :--- | :--- |
| `id` | `uuid` | `PRIMARY KEY DEFAULT gen_random_uuid()` | Unique lead record |
| `email` | `text` | `NOT NULL` | Contact email |
| `full_name` | `text` | `NULLABLE` | Name |
| `horse_slug` | `text` | `NULLABLE REFERENCES inventory(slug)`| Target horse of interest |
| `stake_intent_pct`| `numeric(5,2)` | `NULLABLE` | Desired stake % |
| `source` | `text` | `NOT NULL DEFAULT 'web_waitlist'`| Marketing / referral source |
| `created_at` | `timestamptz` | `NOT NULL DEFAULT now()` | Submission timestamp |

---

## 6. Table: `race_results` (Official Gross Stakes & Distribution Ledger)

| Column Name | Target Data Type | Constraints / Default | Transformation Rule |
| :--- | :--- | :--- | :--- |
| `id` | `uuid` | `PRIMARY KEY DEFAULT gen_random_uuid()` | Unique result record |
| `horse_id` | `uuid` | `NOT NULL REFERENCES inventory(id)` | Foreign key to horse |
| `race_date` | `date` | `NOT NULL` | Race meeting date |
| `track` | `text` | `NOT NULL` | Racecourse (e.g. `Te Rapa`, `Ellerslie`, `Trentham`) |
| `race_name` | `text` | `NOT NULL` | Official race title |
| `placing` | `integer` | `NOT NULL` | Finishing place (1 = Win, 2 = 2nd, etc.) |
| `gross_stakes_nzd` | `numeric(12,2)` | `NOT NULL` | **100% Official NZTR Gross Stakes** |
| `investor_pool_nzd`| `numeric(12,2)` | `NOT NULL` | **Formula:** `gross_stakes_nzd * 0.75` (Owner absorbs 25% fees) |
| `quarter` | `text` | `NOT NULL` | Payout period tag (e.g. `2026-Q3`) |
| `distribution_status`| `distribution_status`| `NOT NULL DEFAULT 'pending'` | Enum: `pending`, `carried_forward`, `distributed` |
| `created_at` | `timestamptz` | `NOT NULL DEFAULT now()` | Logged timestamp |

---

## 7. Table: `events` (Idempotent Webhook & Audit Log)

| Column Name | Target Data Type | Constraints / Default | Transformation Rule |
| :--- | :--- | :--- | :--- |
| `id` | `uuid` | `PRIMARY KEY DEFAULT gen_random_uuid()` | Audit record ID |
| `stripe_event_id` | `text` | `UNIQUE NOT NULL` | Guarantees zero duplicate webhook executions |
| `event_type` | `text` | `NOT NULL` | e.g. `checkout.session.completed`, `invoice.paid` |
| `payload` | `jsonb` | `NOT NULL` | Full raw Stripe event JSON |
| `processed` | `boolean` | `NOT NULL DEFAULT false` | Webhook processing success flag |
| `processed_at` | `timestamptz` | `NULLABLE` | Timestamp of completion |
| `error_message` | `text` | `NULLABLE` | Error diagnostics if processing failed |
| `operator_id` | `uuid` | `NULLABLE` | Operator override ID if manual retry |
| `created_at` | `timestamptz` | `NOT NULL DEFAULT now()` | Event receipt timestamp |

---

## 8. Legacy Entity Transition & Scope Boundary

1. **Clean Slate Greenfield Marketplace:**
   * Active syndicate purchases in `evo_02` only launch for new Digitally Syndicated Leases (DSLs) governed by `DSL_MANUAL.md` (initial campaigns: Lady Ketchikan / Nellie, and Turn Me Loose x Yearn / Mulan).
2. **Legacy Syndicates (First Gear, Prudentia, Hottathanafantasy, I Stole A Manolo):**
   * These 4 legacy syndicates were executed under the old $20 \times 5\%$ one-time leasehold structure with separate Tokinvest-era legal packs.
   * **Boundary Rule:** They are imported into `evo_02` Mission Control as a **Read-Only Historical Registry** for audit and existing investor statement lookups. They are **not** listed on the live public marketplace and require no automated schema conversion to the 5×M subscription float model.
3. **Legacy Owners (39) & Leads (13):**
   * Legacy owners are imported into `profiles` with their historical holding records for account continuity.
   * Legacy leads from `evo_01` are seeded into the new `leads` table for campaign launch marketing.
