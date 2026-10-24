# Demo 1 QA checklist

> Public copy. Evidence file names refer to the private project folder; a selection of the screenshots is in [`images/`](images/).

Practice project, fictional brand (Skinhale), development store `conny-dtc-lab`. Not client work.

Standard: `00_plan/04_qa-evidence-standards.md`. Status values: `PASS` · `FAIL (ISS-…)` · `NOT TESTED (reason)` · `N/A`.
Environment unless stated otherwise: theme `demo1` served by `shopify theme dev` (CLI 4.9.4) on Windows 11, captured with headless Chromium (agent-browser), DPR 1. Evidence folder: `evidence/qa/`. Dates in WIB.

## CR-02 Product-card badge from tags (D1-05, D1-06)

Theme code: `demo1` `db80d11` + fix `c85a324`.

### Acceptance criteria
| ID | Check | Viewport | Result | Evidence |
|---|---|---|---|---|
| CR-02-AC1 | Daily Hand Cream shows exactly one label "New"; Eye Cream shows no tag label and no badge markup | 390, 1440 | PASS (DOM: Eye Cream has no `.product-badges`) | `D1_CR02_collection-p2-full_{390,1440}w_…_20261023.png`, `D1_CR02_collection-p1-full_{390,1440}w_…_20261023.png` |
| CR-02-AC2 | Body Wash shows "Limited edition run" then "New"; no overlap with the image edge or each other | 360, 1440 | PASS (measured: inside card, no overlap) | `D1_CR02_collection-p1-full_{360,1440}w_…_20261023.png` |
| CR-02-AC3 | "Limited edition run" wraps inside the card at 360; no horizontal scroll | 360 | PASS. It fits on one line at 360 (126 px of 174 px), so wrapping was proven with a longer test label (row E-04) | `D1_CR02_collection-p1-full_360w_…_20261023.png` |
| CR-02-AC4 | "Show tag badges" off removes every tag label; Sale and Sold out still show | 1440 | PASS in preview (setting set in `settings_data.json` under `theme dev`). Editor re-check on D1 Work: see L2-17 row | `D1_CR02_collection-p{1,2}-tag-badges-off_1440w_…_20261023.png` |
| CR-02-AC5 | Filled ↔ Outline changes every tag label without a code change | 1440 | PASS in preview (same method). Editor re-check: L2-17 | `D1_CR02_collection-p1-full_1440w_…` (Filled), `D1_CR02_collection-p1-style-outline-fixed_1440w_…_20261023.png` (Outline) |
| CR-02-AC6 | Clicking a card with labels opens the correct product | 390 | PASS: click on the "New" label → `/products/daily-hand-cream`; on Body Wash label → `/products/body-wash` | DOM/URL check, 2026-10-23 |
| CR-02-AC7 | Theme Check: no new errors vs baseline | — | PASS: 0 errors / 6 warnings (= baseline) after `c85a324` | command output, 2026-10-23 |

### Edge cases (D1-06, 2026-10-24)
Temporary test tags were added on 2026-10-24 to 3 products and removed again after the test (store-data change, logged here). Restore verified via `/products/<handle>.js`: all three back to `tags = []`.

| ID | Case | Test data (temporary) | Expected | Result 360 / 390 / 1440 | Evidence |
|---|---|---|---|---|---|
| E-01 | 0 badge tags | Eye Cream (none) | No label, no markup | PASS / PASS / PASS | `D1_CR02_qa-edge-tags-collection-p1-full_{360,390,1440}w_…_20261024.png` |
| E-02 | 1 badge tag | Daily Hand Cream `badge:New` | "New" | PASS / PASS / PASS | `…qa-edge-tags-collection-p2-full_…_20261024.png` |
| E-03 | 2 badge tags | Body Wash | "Limited edition run", "New" | PASS / PASS / PASS | p1 files |
| E-04 | Long text wraps | Cleansing Balm `Badge:Hand-poured in small batches for sensitive skin` | Wraps inside the card, never cut | PASS: 3 lines at 360, 2 at 390 and 1440; inside the card | p1 files |
| E-05 | Upper-case prefix | same tag (`Badge:`) | Accepted | PASS | p1 files |
| E-06 | Trap tag | Cleansing Balm `notbadge:Trap` | Ignored | PASS (not shown) | p1 files |
| E-07 | Empty label | Cleansing Balm `badge:` | Ignored, no empty pill | PASS | p1 files |
| E-08 | More than 2 badge tags | Cleansing Balm has 4 valid-looking tags (`Badge:Hand-poured…`, `badge:Refill`, `badge:Vegan`, plus the empty one) | First 2 in Shopify's tag order | PASS: "Hand-poured…", "Refill"; "Vegan" not shown | p1 files |
| E-09 | Sold out + tag | Barrier Cream Moisturizer `badge:Limited edition run` | "Sold out" first, then the tag label | PASS / PASS / PASS | p2 files |
| E-10 | Sale + tag | Hydrating Toner `badge:New` | "Sale" first, then "New" | PASS / PASS / PASS | p2 files |
| E-11 | No horizontal scroll | all of the above | Page width = viewport | PASS / PASS / PASS | measured `scrollWidth` |

**Observation (not a defect):** at 360 px the 3-line "Hand-poured…" label covers about the top third of the card image. The brief asks for wrapping, never cutting, so this is the intended trade-off. A merchant guide line ("keep badge text short, 1–3 words") goes into the case study.

**Tag order note:** Shopify returned `badge:Refill` before `badge:Vegan` (alphabetical in this data). The brief only requires "Shopify's tag order"; the exact rule Shopify uses is not proven.

## CR-03 Product details from metafields (D1-08, D1-09)

Theme code: `demo1` `a55be45` (new block `blocks/product-details-list.liquid`, placed after the description in `templates/product.json`). Pushed to D1 Work, 25/25 files hash-verified.

### Acceptance criteria
| ID | Check | Viewport | Result | Evidence |
|---|---|---|---|---|
| CR-03-AC1 | Heading + 3 rows with the correct values on PDP-F (Gentle Gel Cleanser) | 390, 1440 | PASS (DOM: Key ingredients 4 lines, Skin type, How to use 3 lines) | `D1_CR03_pdp-f-full_{390,1440}w_…_20261024.png` |
| CR-03-AC2 | Only "Key ingredients" on PDP-P (Hydrating Toner); other labels absent | 390 | PASS | `D1_CR03_pdp-p-full_390w_…`, `D1_CR03_pdp-p-block_390w_…_20261024.png` |
| CR-03-AC3 | Nothing from the block in the HTML on PDP-E (Eye Cream) | 1440 | PASS: no `product-details-list` class anywhere in `outerHTML` | `D1_CR03_pdp-e-full_1440w_…_20261024.png` + DOM check |
| CR-03-AC4 | Changing a label in the editor changes the page text; metafield unchanged | 1440 (editor) | NOT TESTED yet: editor step for the owner (see "Owner editor checks" in D1-12) | — |
| CR-03-AC5 | ≥300-character value wraps, no horizontal scroll (Niacinamide Serum, 435 chars) | 360 | PASS: `scrollWidth` 360 = viewport | `D1_CR03_pdp-long-value-full_360w_…`, `D1_CR03_pdp-long-value-block_360w_…_20261024.png` |
| CR-03-AC6 | PDP-V (Daily Hand Cream): variant change updates price and image; Add to cart adds that variant | 390 | PASS: Unscented/30 ml → Fig/75 ml: URL `?variant=54452923826360`, price $28.00, cart = "Fig / 75 ml / Single" ×1. Image stays the same because no variant has its own image (`variantImages: 0`), same as BASELINE | DOM/cart check 2026-10-24 |
| CR-03-AC7 | Theme Check: no new errors | — | PASS: 0 errors / 6 warnings | command output 2026-10-24 |

### Extra checks (D1-09)
| ID | Check | Result |
|---|---|---|
| E-12 | PDP-F at 768 and 1280: no horizontal scroll | PASS (`D1_CR03_pdp-f-full_{768,1280}w_…_20261024.png`) |
| E-13 | Multi-line metafield keeps its line breaks; merchant text is escaped | PASS (rows show one ingredient per line; values pass through `escape` before `newline_to_br`) |
| E-14 | Mobile layout: label above value; ≥750 px: label and value in two columns | PASS (390 vs 1440 screenshots) |

**Observation (content, out of scope):** the S-08 product descriptions already contain "Key ingredients" and "How to use" sections, so on Gentle Gel Cleanser the same facts appear twice (description + block). Product copy is excluded by brief §4. Note for the case study: on a real store the description would be shortened once the block is in use.

## CR-04 Collection description "Show more / Show less" (D1-10, D1-11), brief v1.1

Theme code: `demo1` `86ba6e5` (new block `blocks/collection-description.liquid`; `templates/collection.json` description block switched to it; Theme settings → Collection description). Pushed to D1 Work, 26/26 files hash-verified.

**Test data (store-data change, 2026-10-24, via Admin API `collectionUpdate`):**
| Collection | Before (S-08) | After (CR-04 test data) |
|---|---|---|
| Shop all | "Every Skinhale product in one place." (6 words) | 2 paragraphs, 119 words (starts with the old sentence) |
| Face care | "Cleansers, toners, serums and moisturizers for a simple daily routine." (11 words) | unchanged (≤ 20 words, the "short" case) |
| Gift sets | "Ready-to-give sets." | empty (the "empty" case) |

So the D1-15 "after" collection screenshots differ from D1-02 "before" in the description text as well; this is test data for CR-04, not a design change.

### Acceptance criteria
| ID | Check | Viewport | Result | Evidence |
|---|---|---|---|---|
| CR-04-AC1 | Shop all shows the first 30 words + "…" and a centred "Show more" link | 390, 1440 | PASS (30 words; trailing full stop removed before "…"; summary `text-align: center`, colour `#A4532F`) | `D1_CR04_col-shop-all-collapsed_{390,1440}w_…_20261024.png` |
| CR-04-AC2 | Click "Show more" → full 2-paragraph text, link reads "Show less"; click again collapses | 390 | PASS (mouse click ×2) | `D1_CR04_col-shop-all-expanded_390w_…_20261024.png` |
| CR-04-AC3 | Tab reaches the link; Enter and Space toggle it; focus outline visible | 1440 | PASS: reached after 13 Tabs from the page start; Enter opens, Space closes; outline `solid` (theme `*:focus-visible`), none on mouse click | DOM check 2026-10-24 |
| CR-04-AC4 | Full description text in the page source while collapsed | — | PASS: closed `<details>` contains both paragraphs (`innerHTML` includes the last sentence) | DOM check 2026-10-24 |
| CR-04-AC5 | Face care: full short text, no link; Gift sets: no description markup | 390 | PASS (Face care renders through the normal text snippet; Gift sets has only the title text block) | `D1_CR04_col-face-care-short_390w_…`, `D1_CR04_col-gift-sets-empty_390w_…_20261024.png` |
| CR-04-AC6 | "Shorten long descriptions" off → full description as in BASELINE; "Words shown" and labels change the output without code | 1440 | PASS in preview (settings set in `settings_data.json` under `theme dev`, then restored): off → 119 words, no toggle; 15 words + "Read more"/"Read less" → 15-word preview, "Read more". Editor re-check: owner editor checks (D1-12) | `D1_CR04_settings-shorten-off_1440w_…`, `D1_CR04_settings-words15-labels_1440w_…_20261024.png` |
| CR-04-AC7 | No horizontal scroll, no overlap with the grid at 360 | 360 | PASS (collapsed and expanded: `scrollWidth` 360) | `D1_CR04_col-shop-all-{collapsed,expanded}_360w_…_20261024.png` |
| CR-04-AC8 | Theme Check: no new errors | — | PASS: 0 errors / 6 warnings | command output 2026-10-24 |

**Observation:** when open, "Show less" sits above the full text (native `<details>` puts the summary first). Moving it below with CSS `order` works in Chrome 145 but makes the visual order differ from the Tab order when a description contains links, so it was not done (DEC-D1-08).

## D1-12 Full Demo 1 QA pass (2026-10-24)

Theme under test: `demo1` `86ba6e5` (CR-01 settings + CR-02/03/04 code), served by `shopify theme dev` (development theme) with live store data; the same files are on D1 Work (hash-verified after each push).

### Level 2 functional checks (§E3)
| ID | Check | Result | Note / evidence |
|---|---|---|---|
| L2-01 | Header nav + menu | PASS | All header links return 200 (`/`, Shop all, Face care, Gift sets, About, Contact, Search) |
| L2-02 | Footer links | PASS | Policies return 200. `/pages/stockists` returns 404 **on purpose**: S-08 test data for the 404 case (§E6) |
| L2-03 | Search | PASS | "cleanser" returns products; "zzqxv" shows "No results found" |
| L2-04 | Sort / filter | PASS | Price low→high starts $12 → $16 → $16 → $18; high→low starts $68 → $42 → $38; "In stock" filter drops the sold-out Barrier Cream |
| L2-05 | Card → PDP | PASS | Title link of Hydrating Toner → `/products/hydrating-toner`; badge click (CR-02-AC6) → correct PDP |
| L2-06 | Variant selection | PASS | PDP-V: URL, price and selected options update (CR-03-AC6) |
| L2-07 | Add to cart | PASS | Hydrating Toner → cart "200 ml ×1", cart bubble shows 1; PDP-V adds "Fig / 75 ml / Single" |
| L2-08 | Sold-out product | PASS | Barrier Cream: Add to cart disabled, labelled "Sold out" |
| L2-09 | Cart edit | PASS | Quantity 3 → total $54.00; quantity 0 → "Your cart is empty" |
| L2-10 | Checkout (test gateway) | NOT TESTED | Out of D1 scope (brief §4: no cart/checkout changes); a test payment is the owner's step and belongs to the D3 QA checklist |
| L2-11 | Contact form | NOT TESTED | Not changed by D1; submitting sends a real notification email. Covered in D3 |
| L2-12 | Newsletter form | NOT TESTED | Not changed by D1; submitting creates a customer. Covered in D3 |
| L2-13 | 404 page | PASS | Unknown URL → "Page not found" + "Continue shopping" |
| L2-14 | Password page | owner check | Needs the storefront password (owner only): `OWNER-CHECKLIST.md` §5 D |
| L2-15 | Console errors | PASS (with note) | Two error types on most pages, both from code D1 did not change: Horizon `assets/predictive-search.js` ("No empty section markup found") and Shopify analytics `fetch` failures through the `theme dev` proxy. Not compared against BASELINE in the same run (NOT TESTED: BASELINE needs its own `theme dev` session) |
| L2-16 | Theme Check | PASS | 0 errors / 6 warnings = `01_setup/theme-check-baseline.txt` |
| L2-17 | Theme editor | preview PASS; editor = owner check | Every new setting changed the output in preview (CR-02 AC4/AC5, CR-04 AC6); the editor run on D1 Work is `OWNER-CHECKLIST.md` §5 A–C |

### Responsive matrix (L1-01, L1-05) — 7 pages × 5 widths
Pages: home, collection (Shop all), PDP-F, PDP-P, PDP-E, PDP-V, cart (2 items). Widths 360, 390, 768, 1280, 1440. Each page scrolled to the end first so lazy images load. 9 cells timed out on the first run (slow network, 30–40 s per page) and were re-run until they loaded.

| Check | Result |
|---|---|
| L1-01 No horizontal scroll (`scrollWidth` = viewport) | **35/35 PASS** |
| L1-05 No broken image (loaded `<img>` with natural width 0) | **35/35 PASS** (one 360 PDP-F cell first showed 1 broken image during a timeout; the re-run showed 0) |
| L1-02 Text readability in changed areas | PASS: badge text wraps (CR-02 E-04), long metafield value wraps (CR-03-AC5), description preview wraps (CR-04-AC7) |
| L1-03 Typography (CR-01 serif headings, Inter body) | PASS (CR-01 verified in D1-03; the new CR-03 heading uses the theme's h4 preset, so it is Playfair Display too) |
| L1-06 Colour & contrast in changed areas | PASS: Filled badge white on `#A4532F` 5.4:1; Outline `#A4532F` on `#FAF7F2` 5.10:1; "Show more" link `#A4532F` on `#FAF7F2` 5.1:1 |
| L1-09 Grids | PASS: no change to grid columns (CR-02 adds overlays only) |
| L1-10 Edge-case content | PASS: long title, sale, sold out, no image (Tinted Lip Balm), 12 variants all render (matrix + CR-02 edge rows) |
| L1-11 Visual states | PASS: keyboard focus outline on "Show more" (CR-04-AC3); sold-out button disabled (L2-08) |
| L1-12 Changed areas match the brief | PASS: see the CR-02, CR-03 and CR-04 acceptance tables above |

### Cross-browser (L3-01)
| Browser | Pages / widths | Result | Evidence |
|---|---|---|---|
| Chromium (headless, agent-browser) | everything above | PASS | rows above |
| Microsoft Edge 156 | collection + PDP-F at 390 and 1440 | PASS: 3 tag labels, toggle present and opens ("Show less"), 3 detail rows, no horizontal scroll | `D1_qa_{collection,pdp-f}-full_{390,1440}w_edge_20261024.png` |
| Firefox 158.0.1 (headless `--screenshot`) | collection + PDP-F at 390 and 1440 | PASS (visual): collapsed description + centred "Show more", tag labels, product details list render as in Chromium. Clicking the toggle in Firefox: NOT TESTED (the headless screenshot mode cannot click) | `D1_qa_{collection,pdp-f}-viewport_{390,1440}w_firefox_20261024.png` |
| Safari / iOS | — | NOT TESTED (no Apple device, R-11) | — |
| Real phone (Samsung Note 9, Chrome) | — | NOT TESTED in this pass; optional owner step | — |

### Keyboard spot-check of changed areas (L3-04, partial)
- CR-04 toggle: reachable with Tab, toggles with Enter and Space, focus outline visible (CR-04-AC3). PASS.
- CR-02 labels and CR-03 list are not interactive (no focus stops added). The card link still works (CR-02-AC6). PASS.

### Regression vs BASELINE (L3-03)
See D1-15: the 12 "after" screenshots are compared with the D1-02 "before" set. Expected differences are CR-01 colours/fonts/buttons, CR-02 labels, CR-03 block, and CR-04 description (with the changed test data).

### Summary
No Blocker and no "Fix before publishing" item found in this pass. Open owner items: editor checks (L2-17, CR-02 AC4/AC5 in the editor, CR-03-AC4, CR-04-AC6 in the editor) and the password page (L2-14), `OWNER-CHECKLIST.md` §5.
Observations (no action): "Buy it now" shows its loading state in headless Firefox screenshots (Shopify accelerated checkout, third-party); policy pages write the brand as "skinHALE" (store name), the brand rule says "Skinhale" in copy.
