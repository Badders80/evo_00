# Migration Bridge: 05. Go-Live Cutover Runbook

**Status:** Temporary Migration Bridge (Retires upon evo_02 live launch)  
**Target State:** Open To Market (OTM) with live Stripe payments enabled.

---

## 1. Pre-Flight Verification Gate
- [ ] Full headless walk of `/marketplace` and `/mystable` via Playwright.
- [ ] End-to-end sandbox Stripe checkout test ($5\times M$ float creation).
- [ ] Automatic PDF generation & SHA-256 hash storage in `holdings`.
- [ ] Transactional welcome email delivery test via Resend.

---

## 2. DNS & Production Cutover
1. **Domain Pointing:** Point `evolutionstables.co.nz` and `evolutionstables.nz` to Vercel production deployment (`apps/web`).
2. **CDN Pointing:** Point `cdn.evolutionstables.nz` to Cloudflare R2 bucket.
3. **Environment Variables:** Inject production Supabase URL, service role keys, Resend API key, PostHog telemetry, and live Stripe keys.
4. **Kill-Switch Release:** Founder explicitly sets `PURCHASES_ENABLED=true`.

---

## 3. Post-Launch Retirement
- [ ] Archive `/home/evo/evo_01` (read-only cold storage).
- [ ] Remove `evo_00/migration_bridge/`.
- [ ] Lock `evo_00` as the permanent live operations desk.
