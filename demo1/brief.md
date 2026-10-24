# Demo 1 brief: Skinhale theme customization (v1.0)

> **Practice project on a Shopify development store. Fictional brand. Not client work.**

| Field | Value |
|---|---|
| Brief ID / task | D1-01 |
| Version | **v1.2, 2026-10-23 (WIB)**: CR-02 Outline style gets a background fill (decision D-7, ISS-D1-01). v1.1 (2026-10-23): CR-04 replaced (decision D-6, DEC-D1-05). v1.0 was frozen 2026-10-20 with decisions D-1 to D-5 (§8). |
| Client (fictional) | **Skinhale**, a small direct-to-consumer skincare brand (wordmark: "skinHALE") |
| Store | `conny-dtc-lab` (development store), shown to shoppers as "skinHALE" |
| Theme | Horizon 4.3.0. Work theme **D1 Work** #192847315128. Reference **BASELINE Horizon v4.3.0 (DO NOT EDIT)** #192610402488 |
| Code | Theme repo `srsyrr/cds-demo-theme`, branch `demo1`; commits `D1 CR-0x: …`; tag `d1-v1.0` |
| Test data | S-08 (`notes/s08-store-data-plan.md`); metafields from D1-07 |

## 1. Background (client voice)
"We sell a small range of gentle skincare. The store runs on Horizon and works, but it looks like the default theme. We want it to look like our brand. We also want three small storefront changes: labels for new and limited products, a clear ingredients and how-to-use list on the product page, and shorter collection descriptions so shoppers see products first, with a way to read the full text. We don't want a redesign, new apps, or anything that touches checkout."

## 2. Pages, viewports and browsers used in every criterion
| Code | Page | URL (D1 Work preview, `?preview_theme_id=192847315128`) |
|---|---|---|
| HOME | Home | `/` |
| COL | Collection "Shop all" (page 1 and 2) | `/collections/shop-all` |
| PDP-F | Product with full metafields | `/products/gentle-gel-cleanser` |
| PDP-P | Product with partial metafields | `/products/hydrating-toner` |
| PDP-E | Product with no metafields | `/products/eye-cream` |
| PDP-V | Product with 12 variants | `/products/daily-hand-cream` |
| CART | Cart page | `/cart` |

**Viewports:** 390, 768, 1440 (core); 360, 1280 (spot checks). **Browser:** Chrome (full). Edge and Firefox spot checks happen in D1-12.
**Evidence:** every criterion maps to a named screenshot or a QA row (`04_qa-evidence-standards.md`).

## 3. Change requests

### CR-01 Brand settings (theme settings only, no code)
**Request:** apply Skinhale's colours, heading font and button style using the theme editor only.

| Token | Current (BASELINE) | New |
|---|---|---|
| Background | `#FFFFFF` | `#FAF7F2` (warm off-white) |
| Text (foreground) | `#000000` | `#2B2622` (deep brown) |
| Accent (buttons; CR-04 toggle link) | Primary button = black | `#A4532F` (terracotta) |
| Muted text (available for secondary text) | — | `#6B625B` |
| Heading font | Inter Bold (`inter_n7`) | **Playfair Display** (serif, decision D-3). Fallback if it is not in the Shopify font library: another serif from the library, recorded in the decision log. Verified in the editor in D1-03. |
| Body font | Inter (`inter_n4`) | **unchanged** (Inter) |
| Primary button | black fill, radius 14 | terracotta fill, white text, **pill** shape (radius 40 or the max the setting allows) |

Contrast, computed with the WCAG 2.x formula: text on background 14.0:1; white on terracotta 5.4:1; terracotta on background 5.1:1; muted on background 5.6:1. All ≥ 4.5:1.

**Acceptance criteria**
| ID | Criterion | Where | Viewport |
|---|---|---|---|
| CR-01-AC1 | Page background is `#FAF7F2` and body text is `#2B2622` (DevTools computed colour) | HOME, COL, PDP-F | 1440 |
| CR-01-AC2 | Headings (page title "Shop all", product title) use the new serif; body text is still Inter | COL, PDP-F | 390 + 1440 |
| CR-01-AC3 | The "Add to cart" button has a terracotta fill, white text and a pill shape | PDP-F | 390 + 1440 |
| CR-01-AC4 | All text/background pairs from the table above measure ≥ 4.5:1 | HOME, PDP-F | 1440 |
| CR-01-AC5 | No theme code file changed: `git diff` touches only `config/settings_data.json` | repo | — |
| CR-01-AC6 | Every changed setting is listed old → new in `docs/settings-changes.md` | docs | — |

### CR-02 Product-card badge from tags (code: edit existing component)
**Request:** products tagged `badge:<text>` show `<text>` as a small label on product cards. The merchant can switch the labels off and choose a style.

**Rules**
- A tag counts if it **starts with** `badge:`, in any letter case (`Badge:New` works). The label text is everything after the colon, trimmed. Empty text (`badge:`) shows nothing. (Prefix check from D1-04, Experiment 1: `contains` gives false positives.)
- **Maximum 2 labels per card**, in Shopify's tag order (decision D-2).
- Labels sit in the **same badge area** as the theme's Sale / Sold out badge (`blocks/_product-card-gallery.liquid`). The theme badge comes first.
- Long text **wraps** inside the card; it is never cut off.
- Settings live under **Theme settings → Badges** (the card does not accept custom blocks, S-07 result): checkbox "Show tag badges" (default on) and select "Tag badge style": *Filled* (accent background) / *Outline* (accent border and text on the page background colour; v1.2, D-7: a transparent fill was unreadable on dark or colourful photos).
- Cards only. The product page is out of scope.

**Test data (S-08):** `badge:New` → Daily Hand Cream; `badge:Limited edition run` → Facial Oil; both → Body Wash; no badge tag → all others; sold out → Barrier Cream Moisturizer; sale → Hydrating Toner.

**Acceptance criteria**
| ID | Criterion | Where | Viewport |
|---|---|---|---|
| CR-02-AC1 | Daily Hand Cream shows exactly one label "New"; Eye Cream (no badge tag) shows no tag label and no empty badge markup | COL p1/p2 | 390 + 1440 |
| CR-02-AC2 | Body Wash shows two labels, "Limited edition run" and "New", and neither overlaps the image edge or each other | COL | 360 + 1440 |
| CR-02-AC3 | "Limited edition run" wraps inside the card at 360 px; no horizontal scroll on the page | COL | 360 |
| CR-02-AC4 | Turning "Show tag badges" off removes every tag label; Sale and Sold out badges still show | COL (editor) | 1440 |
| CR-02-AC5 | Switching the style Filled ↔ Outline changes the look of every tag label without a code change | COL (editor) | 1440 |
| CR-02-AC6 | Clicking a card with labels still opens the correct product page | COL → PDP | 390 |
| CR-02-AC7 | Theme Check: no new errors compared with `01_setup/theme-check-baseline.txt` | repo | — |

### CR-03 Product details from metafields (code: new theme block)
**Request:** a "Product details" list on the product page shows Key ingredients, Skin type and How to use when they are filled, and hides what is empty.

**Data (created in D1-07):** product metafield definitions
| Label (default) | Namespace.key | Type |
|---|---|---|
| Key ingredients | `custom.key_ingredients` | Multi-line text |
| Skin type | `custom.skin_type` | Single-line text |
| How to use | `custom.how_to_use` | Multi-line text |

Filled on Gentle Gel Cleanser (all 3), Hydrating Toner (Key ingredients only), Niacinamide Serum (all 3, one long value of ≥ 300 characters). Empty elsewhere.

**Rules**
- New theme block `blocks/product-details-list.liquid` (name may change), added inside the product details column from the editor (`_product-details` accepts `@theme`, S-07 result).
- Block settings: heading (default "Product details"), and the three row labels.
- A row renders only when its metafield has a value. When all three are empty, the block outputs **nothing** (no heading, no empty wrapper).

**Acceptance criteria**
| ID | Criterion | Where | Viewport |
|---|---|---|---|
| CR-03-AC1 | Heading plus 3 rows with the correct values show on PDP-F | PDP-F | 390 + 1440 |
| CR-03-AC2 | Only the "Key ingredients" row shows on PDP-P; the other 2 labels are absent | PDP-P | 390 |
| CR-03-AC3 | Nothing from the block is in the HTML on PDP-E (check Elements panel for the block wrapper) | PDP-E | 1440 |
| CR-03-AC4 | Changing a label in the editor changes the text on the page; the metafield value is unchanged | PDP-F (editor) | 1440 |
| CR-03-AC5 | The ≥ 300-character value wraps with no horizontal scroll | Niacinamide Serum | 360 |
| CR-03-AC6 | On PDP-V, changing variant still updates price and image, and "Add to cart" adds the chosen variant | PDP-V → CART | 390 |
| CR-03-AC7 | Theme Check: no new errors compared with baseline | repo | — |

### CR-04 Collection description "Show more / Show less" (code: modify existing component) — v1.1
**Request:** on collection pages, show only the start of a long collection description, followed by "…" and a centred "Show more" link. Clicking it shows the full text and the link changes to "Show less". The full description stays in Shopify and in the page HTML (SEO). Short descriptions show in full.

Origin: a real Upwork post (2026-10-18, Parallax theme, $25, hired) asked for exactly this; see `40_acquisition/a01b-r2-details.csv` job 2111785168044246191. Rebuilt here on Horizon with fictional data.

**Rules**
- Find and read the existing collection-description render first (D1-10); list every template/section that uses it. Change only that render; other collection header content stays as BASELINE.
- **No JavaScript** (§4): the hidden part sits in `<details>`/`<summary>`. The preview is the first N words of the description as plain text (HTML stripped), cut on a word boundary, with "…".
- The full description is in the HTML both collapsed and expanded.
- Theme settings (global, under a new "Collection description" group, or on the existing collection section if Horizon allows): checkbox "Shorten long descriptions" (default on); range "Words shown" 15–60, default 30; text "Show more label" (default "Show more"); text "Show less label" (default "Show less").
- A description with ≤ N words shows in full with no link. An empty description outputs nothing (no wrapper, no link).
- Link colour = accent; it is keyboard-focusable (native `<summary>`).

**Test data (store-data change, logged in D1-10):** "Shop all" gets a description of ≥ 90 words in 2 paragraphs; "Face care" gets ≤ 20 words; "Gift sets" stays empty.

**Acceptance criteria**
| ID | Criterion | Where | Viewport |
|---|---|---|---|
| CR-04-AC1 | "Shop all" shows the first 30 words + "…" and a centred "Show more" link | COL | 390 + 1440 |
| CR-04-AC2 | Clicking "Show more" shows the full 2-paragraph text and the link reads "Show less"; clicking again collapses it | COL | 390 |
| CR-04-AC3 | Tab reaches the link and Enter or Space toggles it; focus outline visible | COL | 1440 |
| CR-04-AC4 | The full description text is in the page source while collapsed | COL (view-source) | — |
| CR-04-AC5 | "Face care" shows its full short description with no link; "Gift sets" has no description markup at all | `/collections/face-care`, `/collections/gift-sets` | 390 |
| CR-04-AC6 | Turning "Shorten long descriptions" off shows the full description as in BASELINE; changing "Words shown" or the labels in the editor changes the output without code | COL (editor) | 1440 |
| CR-04-AC7 | No horizontal scroll and no overlap with the product grid at 360 | COL | 360 |
| CR-04-AC8 | Theme Check: no new errors compared with baseline | repo | — |

## 4. Out of scope (exclusions)
- New page templates; home page redesign; header/footer layout changes (D1 Work footer menu and pagination are test setup from S-08, not deliverables).
- Cart drawer, cart page and checkout changes. Quick add: **checked for regressions only** if enabled.
- Any JavaScript feature; app installs or app embeds (Klaviyo is out of scope and is noted as an untested third-party element).
- Product page badges; collection filters; search results styling. Product-card price styling (the v1.0 CR-04 "Save X%" label, dropped in v1.1).
- Translations beyond English; performance and SEO work; logo image (the wordmark stays as text from the store name "skinHALE", decision D-4; a logo image is a v1.1 idea).
- Product copy, photos and data changes beyond D1-07 metafields.

## 5. Constraints
- Never edit or publish the BASELINE or live theme. Work only on D1 Work.
- Settings before code: if a change can be done with a setting, it must be (CR-01).
- No new errors in Theme Check; keep LF line endings.
- Explain every code line in your own words before committing (no unexplained AI code).

## 6. Deliverables
`docs/settings-changes.md` (CR-01), commits `D1 CR-02/03/04`, before/after screenshots (D1-02, D1-15), `docs/qa-checklist.md`, `docs/issue-log.md`, `docs/decision-log.md`, case study, ≤ 5 min video.

## 7. Changes from the original plan (`03_demo-execution-plans.md` §4)
| Item | Original plan | This brief | Why |
|---|---|---|---|
| CR-03 fields | Material / Origin / Care | Key ingredients / Skin type / How to use | Skincare brand (S-08); same code pattern, three text metafields |
| CR-04 content | Sale price accent, compare-at struck through, title/price order | Sale price accent, compare-at muted, **"Save X%" label** | Strike-through already exists, and title/price order is an editor setting (block order), so neither proves a code change. The savings label is a real logic change in a shared snippet. |
| CR-02 location | New theme block or edit existing card code | Edit existing card code + global theme settings | S-07: the card does not accept custom theme blocks |
| CR-02 Outline (v1.2) | Accent border, transparent fill | Accent border and text on the page background colour | Transparent fill was unreadable on dark or colourful photos (ISS-D1-01); the fill gives 5.10:1 on any photo. (D-7) |
| CR-04 (v1.1) | "Save X%" price label on cards (v1.0) | Collection description "Show more / Show less" | A-01b detail pass: hired D1 jobs asked for small, safe changes on live themes; this one is a real hired request. The price label had no matching demand. (D-6) |

**Comparison for CR-04 (the main scope change):**
| Option | Code needed | Proves | Risk |
|---|---|---|---|
| A. Original (accent + strike + order) | Colour only; strike and order exist already | Little: mostly settings/CSS | Weak demo; reviewer may call it "settings work" |
| **B. Accent + muted + "Save X%" (recommended)** | CSS + small Liquid calculation in a shared snippet, scoped to cards | Reading shared code, scoping a change, regression-checking the PDP | Slightly more work (~0.5 h) |
| C. Drop CR-04 (v1.1) | None | — | Allowed by the plan only if disclosed |

## 8. Decisions (approved by the owner 2026-10-20)
| ID | Question | Decision |
|---|---|---|
| D-1 | CR-04 scope: A (original), B ("Save X%") or C (v1.1)? | **B**: accent sale price, muted compare-at, "Save X%" label on cards only |
| D-2 | CR-02 with several badge tags | **Max 2** labels per card, in Shopify's tag order |
| D-3 | CR-01 heading font | **Playfair Display** (fallback rule in CR-01) |
| D-4 | Logo | **Text wordmark** "skinHALE" from the store name for v1.0 |
| D-6 | CR-04 after the A-01b research (2026-10-22/23) | **Replace** "Save X%" with the collection description "Show more / Show less" (v1.1). D-1 is superseded. |
| D-7 | CR-02 Outline style readability (ISS-D1-01, 2026-10-23) | **Fill** the Outline label with the page background colour; accent border and text stay (option b, owner: "Sesuai rekomendasi"). |
| D-5 | Base theme | **Horizon** stays (owner, 2026-10-20). Copying or merging real websites was considered and rejected: copyright risk, harder to evidence, scope growth. Real sites may be used later only as style references, never copied. |

## 9. Brief self-check (D1-01 completion criterion)
- [x] Fictional brand stated, practice label at the top
- [x] 4 CRs maximum
- [x] Each CR has ≥ 3 acceptance criteria (CR-01: 6, CR-02: 7, CR-03: 7, CR-04: 8 in v1.1)
- [x] Every criterion names a page (or repo/docs) and a viewport where it applies
- [x] Test data for each criterion exists (S-08) or is scheduled (D1-07 metafields)
- [x] Exclusions listed
- [x] Owner approved decisions D-1 to D-5 (2026-10-20); version set to v1.0
