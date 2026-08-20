# Migration Bridge: 03. Asset & Media Transfer Inventory

**Status:** RECONCILED & AUDITED SPECIFICATION  
**Target Storage:** Cloudflare R2 (`cdn.evolutionstables.nz`)  
**Egress Cost:** $0.00 / GB  
**Purpose:** Maps all verified legacy static assets, documents, and high-res media from `evo_01` to their immutable Cloudflare R2 object keys.

---

## 1. Horse Asset Inventory

### 1.1 Horse: Lady Ketchikan (`slug: nellie` / Barn Name: Nellie)
| Asset Description | Source Legacy Path (`evo_01`) | Target Cloudflare R2 Key | MIME Type | Target Dimension / Format |
| :--- | :--- | :--- | :--- | :--- |
| **Hero Conformation (Banner)** | `_assets/horses/almanzor-x-night-danza/images/Nellie cover image.png` | `horses/lady-ketchikan/hero/conformation.webp` | `image/webp` | 2400x1600 (Hero banner) |
| **Hero Transparent BG** | `02_website/public/images/content/horses/nellie/Nellie-BG.png` | `horses/lady-ketchikan/hero/cutout.webp` | `image/webp` | Cutout PNG/WebP |
| **Side Conformation (Gallery 1)** | `_assets/horses/almanzor-x-night-danza/images/side_view.jpg` | `horses/lady-ketchikan/gallery/side_view.webp` | `image/webp` | 1920x1280 |
| **Front Conformation (Gallery 2)**| `_assets/horses/almanzor-x-night-danza/images/front_view.png` | `horses/lady-ketchikan/gallery/front_view.webp` | `image/webp` | 1920x1280 |
| **Yearling Trackwork / Gallop** | `pipelines/studio/06_content-pipeline/public/almanzor-x-night-danza/video.mp4` | `horses/lady-ketchikan/updates/cambridge_gallop.mp4` | `video/mp4` | 1080x1920 (H.264) |
| **Frozen PDS Contract PDF** | `02_website/public/documents/almanzor-x-night-danza/pds.pdf` | `horses/lady-ketchikan/contracts/pds_frozen.pdf` | `application/pdf` | Legal snapshot |
| **Frozen SA Contract PDF** | `02_website/public/documents/almanzor-x-night-danza/sa.pdf` | `horses/lady-ketchikan/contracts/sa_frozen.pdf` | `application/pdf` | Legal snapshot |

### 1.2 Horse: Turn Me Loose x Yearn 2023 (`slug: tml-x-yearn` / Barn Name: Mulan)
| Asset Description | Source Legacy Path (`evo_01`) | Target Cloudflare R2 Key | MIME Type | Target Dimension / Format |
| :--- | :--- | :--- | :--- | :--- |
| **Hero Conformation** | `_assets/horses/turn-me-loose-x-yearn/images/turn-me-loose-x-yearn.png` | `horses/tml-x-yearn/hero/conformation.webp` | `image/webp` | 2400x1600 (Hero banner) |
| **Hero Transparent BG** | `02_website/public/images/content/horses/tml-x-yearn/TMLxYearn-BG.png` | `horses/tml-x-yearn/hero/cutout.webp` | `image/webp` | Cutout PNG/WebP |
| **Video Reel (Vimeo Raw)** | `_assets/horses/turn-me-loose-x-yearn/videos/2026-05-28_turn-me-loose-x-yearn_vimeo-1196163233.mp4` | `horses/tml-x-yearn/updates/intro_reel.mp4` | `video/mp4` | 1080x1920 (H.264) |
| **Frozen PDS Contract DOCX** | `02_website/public/documents/tml-x-yearn/pds.docx` | `horses/tml-x-yearn/contracts/pds_frozen.docx` | `application/vnd.openxmlformats-officedocument.wordprocessingml.document` | Legal master |
| **Frozen SA Contract DOCX** | `02_website/public/documents/tml-x-yearn/sa.docx` | `horses/tml-x-yearn/contracts/sa_frozen.docx` | `application/vnd.openxmlformats-officedocument.wordprocessingml.document` | Legal master |

---

## 2. Brand, Silks & Core Design Assets

| Asset Description | Source Legacy Path (`evo_01`) | Target Cloudflare R2 Key | MIME Type | Format / Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **Monogram Gold Mark** | `_shared/brand_original/logos/marks/monogram-gold.svg` | `brand/logos/monogram-gold.svg` | `image/svg+xml` | Vector Crest Mark |
| **Wordmark Gold Primary**| `_shared/brand_original/logos/primary/wordmark-gold.svg` | `brand/logos/wordmark-gold.svg` | `image/svg+xml` | Vector Header Logo |
| **Lockup Horizontal Gold**| `_shared/brand_original/logos/lockups/lockup-horizontal-gold.svg` | `brand/logos/lockup-horizontal-gold.svg` | `image/svg+xml` | Full Horizontal Lockup |
| **Geist Sans VF Font** | `_shared/brand_original/fonts/GeistSans-VF.woff2` | `brand/fonts/GeistSans-VF.woff2` | `font/woff2` | Web Font Asset |
| **Geist Mono VF Font** | `_shared/brand_original/fonts/GeistMono-VF.woff2` | `brand/fonts/GeistMono-VF.woff2` | `font/woff2` | Web Font Asset |
| **NZTR Syndicator Seal** | `02_website/public/images/content/documents/authorised-seal.svg` | `brand/badges/nztr-authorised-syndicator.svg` | `image/svg+xml` | Compliance Footer Badge |

---

## 3. Transfer & Verification Protocol
1. **Conversion & Optimization:** Convert source PNGs/JPGs to modern WebP with 85% quality factor.
2. **Checksum Verification:** Compute SHA-256 hash before and after R2 upload and store in Supabase storage manifest.
3. **HTTP 200 Fast Head Check:** Verify global edge accessibility via `curl -I https://cdn.evolutionstables.nz/<key>`.
