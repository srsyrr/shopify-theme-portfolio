# Demo 1 issue log

> Public copy. Evidence file names refer to the private project folder; a selection of the screenshots is in [`images/`](images/).

Format: `04_qa-evidence-standards.md` §E9. Dates in WIB. Newest entries at the bottom.

---

## ISS-D1-01 · 2026-10-23 · CR-02 · RESOLVED (2026-10-23)
**Problem:** with "Tag badge style" = *Outline*, the label text is hard or impossible to read on some product photos. Body Wash (red/orange photo) and Facial Oil (grey photo) at 1440 px: terracotta text and line on a transparent fill sit directly on the image.
**Evidence:** `evidence/qa/D1_CR02_collection-p1-style-outline_1440w_chromium-headless_20261023.png` (compare `…collection-p1-full_1440w…` for Filled).
**Cause:** the brief (CR-02) asks for "accent border, transparent fill". The badges sit on the image, so the contrast depends on each photo and cannot be guaranteed (AGENTS.md: keep text contrast for merchant-selectable choices).
**Options:**
- (a) Keep it as the brief says; disclose "Outline is only readable on light photos" as a limitation.
- (b) Outline = page background fill (`#FAF7F2`) + terracotta line and text. Terracotta on `#FAF7F2` = 5.1:1 (brief CR-01 table) on every photo. One CSS line; needs brief v1.2 (decision D-7).
**Recommendation:** (b).
**Resolution:** owner chose (b) on 2026-10-23 ("Sesuai rekomendasi"); brief v1.2, decision D-7. Theme commit `c85a324` (`snippets/product-badges-styles.liquid`: Outline background `var(--color-background)`), pushed to D1 Work and hash-verified. Re-tested 1440 + 360: text `#A4532F` on `#FAF7F2` = 5.10:1 on every tag label; readable on the red Body Wash and grey Facial Oil photos (`evidence/qa/D1_CR02_collection-p1-style-outline-fixed_{1440,360}w_chromium-headless_20261023.png`). Theme Check 0 errors / 6 warnings (= baseline).
**Time spent:** ~20 min.
