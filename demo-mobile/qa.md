# D-Mobile QA results (2026-10-30)

**Environment:** Windows 11 laptop; headless Chromium via `agent-browser`, 100% scale (DPR 1); local theme preview with `shopify theme dev` (store `conny-dtc-lab`, development theme). Theme branch `dmobile`: before = `a7ab083`, after = `46a7f02`. Pushed afterwards as the unpublished theme "D-Mobile Work" (#193366229176). Nothing published.

**Pages:** homepage `/`, Face care collection `/collections/face-care`, product page `/products/gentle-gel-cleanser`.
**Widths × heights:** 360 × 800, 390 × 844, 414 × 896 (phones), 768 × 1024 (tablet), 1440 × 900 (desktop).

## How each check works
- **Sideways scroll:** page width minus screen width, measured as the shopper sees the page (before any screenshot-only styles). 0 = no sideways scroll.
- **Sticking out:** up to 5 visible elements whose right edge is past the screen edge (Horizon's closed menu drawer is excluded: it is meant to sit off-screen).
- **Tap size:** width × height of the menu button, cart icon and Add to cart button. Target: at least 44 × 44 px (Apple's guideline; WCAG 2.5.5 AAA). Also checks that the icon is inside the screen.
- **First screen:** bottom edge of Add to cart compared with the screen height.
- Tool: `capture-mobile.sh` (screenshots + checks), `summarize-mobile-checks.py` (tables below). Raw results: kept in the private workspace (`50_demo-mobile/evidence/`).


### Before (simulated broken state)

| Page | Width | Sideways scroll | Sticking out | Menu button | Cart icon | Add to cart |
|---|---|---|---|---|---|---|
| home | 360 | 324 px (FAIL) | li.trust-bar__item right=684<br>div.promo-banner__content right=528 | 32×32 FAIL | 24×24 FAIL | — |
| collection | 360 | none (PASS) | — | 32×32 FAIL | 24×24 FAIL | — |
| pdp | 360 | none (PASS) | — | 32×32 FAIL | 24×24 FAIL | 196×32 FAIL, bottom at 804 px (BELOW first screen) |
| home | 390 | 309 px (FAIL) | li.trust-bar__item right=699<br>div.promo-banner__content right=528 | 32×32 FAIL | 24×24 FAIL | — |
| collection | 390 | none (PASS) | — | 32×32 FAIL | 24×24 FAIL | — |
| pdp | 390 | none (PASS) | — | 32×32 FAIL | 24×24 FAIL | 226×32 FAIL, bottom at 844 px (on first screen) |
| home | 414 | 297 px (FAIL) | li.trust-bar__item right=711<br>div.promo-banner__content right=528 | 32×32 FAIL | 24×24 FAIL | — |
| collection | 414 | none (PASS) | — | 32×32 FAIL | 24×24 FAIL | — |
| pdp | 414 | none (PASS) | — | 32×32 FAIL | 24×24 FAIL | 250×32 FAIL, bottom at 876 px (on first screen) |
| home | 768 | 120 px (FAIL) | div.header__column.header__column--right right=849<br>li.trust-bar__item right=888 | n/a | 44×44 OFF-SCREEN FAIL | — |
| collection | 768 | none (PASS) | div.header__column.header__column--right right=849 | n/a | 44×44 OFF-SCREEN FAIL | — |
| pdp | 768 | none (PASS) | div.header__column.header__column--right right=849 | n/a | 44×44 OFF-SCREEN FAIL | 223×52 PASS, bottom at 352 px (on first screen) |
| home | 1440 | none (PASS) | — | n/a | 44×44 PASS | — |
| collection | 1440 | none (PASS) | — | n/a | 44×44 PASS | — |
| pdp | 1440 | none (PASS) | — | n/a | 44×44 PASS | 292×52 PASS, bottom at 368 px (on first screen) |

### After (fixed)

| Page | Width | Sideways scroll | Sticking out | Menu button | Cart icon | Add to cart |
|---|---|---|---|---|---|---|
| home | 360 | none (PASS) | — | 46×46 PASS | 44×44 PASS | — |
| collection | 360 | none (PASS) | — | 46×46 PASS | 44×44 PASS | — |
| pdp | 360 | none (PASS) | — | 46×46 PASS | 44×44 PASS | 196×52 PASS, bottom at 704 px (on first screen) |
| home | 390 | none (PASS) | — | 46×46 PASS | 44×44 PASS | — |
| collection | 390 | none (PASS) | — | 46×46 PASS | 44×44 PASS | — |
| pdp | 390 | none (PASS) | — | 46×46 PASS | 44×44 PASS | 226×52 PASS, bottom at 734 px (on first screen) |
| home | 414 | none (PASS) | — | 46×46 PASS | 44×44 PASS | — |
| collection | 414 | none (PASS) | — | 46×46 PASS | 44×44 PASS | — |
| pdp | 414 | none (PASS) | — | 46×46 PASS | 44×44 PASS | 250×52 PASS, bottom at 758 px (on first screen) |
| home | 768 | none (PASS) | — | n/a | 44×44 PASS | — |
| collection | 768 | none (PASS) | — | n/a | 44×44 PASS | — |
| pdp | 768 | none (PASS) | — | n/a | 44×44 PASS | 223×52 PASS, bottom at 352 px (on first screen) |
| home | 1440 | none (PASS) | — | n/a | 44×44 PASS | — |
| collection | 1440 | none (PASS) | — | n/a | 44×44 PASS | — |
| pdp | 1440 | none (PASS) | — | n/a | 44×44 PASS | 292×52 PASS, bottom at 368 px (on first screen) |


## Desktop unchanged (1440 px)
Pixel comparison of before vs after full-page screenshots (`pixel-diff.py`, `pixel-diff-mask.py`; photo positions measured in the browser).

| Page | Same size | Pixels different (all) | Outside product photos | Result |
|---|---|---|---|---|
| Homepage | yes, 1440 × 3032 | 56,206 (inside 1 of 10 photos) | 0 | PASS |
| Face care collection | yes, 1440 × 1748 | 35,800 (inside 1 of 8 photos) | 0 | PASS |
| Product page | yes, 1440 × 2596 | 0 | 0 | PASS |

The differences inside one photo per page are most likely the browser scaling the same image file slightly differently between runs (Hypothesis; not confirmed). No CSS rule that applies at 1440 px changed: all fixes are inside `max-width: 749px`, `max-width: 989px` or `750–989px` media queries. The only markup change is one extra class on the promo banner (for the new phone setting), which has no style at desktop width.

## Functional checks (after)
| Check | Width | Result | Evidence |
|---|---|---|---|
| Menu opens (6 links visible), closes with the Close button (52 × 52 px) | 360 | PASS | `images/DM_after_menu-open_360w_chromium-headless_20261030.png` |
| Add to cart adds "Gentle Gel Cleanser - 150 ml" × 1 (checked in `/cart.js`; cart badge shows 1) | 360 | PASS | `images/DM_after_add-to-cart_360w_chromium-headless_20261030.png` |
| Promo banner setting "Text on phones: On the photo": text box inside the photo and the screen (box 32–328 px, photo 16–344 px), no sideways scroll | 360 | PASS | `images/DM_after_promo-text-on-photo-setting_360w_chromium-headless_20261030.png` |
| Promo banner default "Below the photo" | 360–414 | PASS | after full-page screenshots |
| Tablet header: cart icon right edge at 768 / 900 / 989 px = 728 / 860 / 949 px | 768–989 | PASS | `images/DM_compare_header_768w_chromium-headless_20261030.png` |
| Shopify Theme Check | — | PASS: 0 errors, 6 warnings = baseline | run 2026-10-30 |

## Not tested
| Area | Reason |
|---|---|
| Real phone, Safari / iOS | No device used in this run (simulated widths in Chromium only) |
| Landscape phone, 1280 px laptop | Out of time-box |
| Cart drawer / cart page layout, checkout | Out of scope (no change made there) |
| Theme editor UI for the new "Text on phones" setting | Setting changed in `templates/index.json` during preview, not clicked in the editor |
| Keyboard focus order | Out of scope |
