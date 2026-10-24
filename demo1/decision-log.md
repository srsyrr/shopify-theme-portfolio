# Demo 1 decision log

> Public copy. Evidence file names refer to the private project folder; a selection of the screenshots is in [`images/`](images/).

Format: `04_qa-evidence-standards.md` §E9. Dates in WIB. Newest entries at the bottom.

---

## DEC-D1-01 · 2026-10-21 · Process (D1-02 / D1-03)
**Decision:** Start D1-03 (CR-01 brand settings) while the owner captures the D1-02 "before" screenshots, instead of waiting for D1-02 to finish. Owner's decision, 2026-10-21.

**Options considered**
| Option | Pros | Cons |
|---|---|---|
| (a) Sequential, as the board says (D1-03 depends on D1-02) | Zero risk to the "before" state | Claude is idle for about 1–1.5 h; D1 slips |
| **(b) Parallel, with guard rules (chosen)** | Saves about 1 h; Claude works in the cloud while the owner captures locally | A store-level change during D1-02 would spoil the "before" set. The guard rules below block this. |

**Why (b) is safe.**
- The "before" screenshots come from **BASELINE** #192610402488 (`?preview_theme_id=192610402488`).
- CR-01 changes only the theme settings of **D1 Work** #192847315128.
- Shopify stores theme settings per theme, in each theme's own `config/settings_data.json`, so a D1 Work change cannot change how BASELINE renders.
- Store data (products, tags, prices, cart, menus, store name) is shared by all themes. That is the real risk, so it is frozen.

**Guard rules (valid until the owner says the 12 "before" screenshots are done)**
1. Change only the D1 Work theme. Never BASELINE, never the live theme `test-data`.
2. **No store-data changes:** products, tags, prices, inventory, cart, menus, store name, pages, policies, metafields, files.
3. CR-01 uploads only `config/settings_data.json` to D1 Work.
4. Check before and after the upload that the BASELINE settings file is unchanged (checksum).

**Consequence / limit**
- The board dependency D1-03 → D1-02 is relaxed **for starting work only**. D1-03 is marked DONE only after D1-02 is DONE, because the CR-01 "after" screenshots are compared with the "before" set.
- Side benefit: D1 Work now looks clearly different (warm background, terracotta buttons). If a "before" screenshot is taken from D1 Work by mistake, it is easy to spot.

**Evidence (rule 4, Verified via Admin API read-back, 2026-10-21):**

| Theme | `config/settings_data.json` checksum before upload | After upload (13:29 WIB) |
|---|---|---|
| BASELINE #192610402488 | `891395e1415b25152680806b7551ae25` (updated 2026-10-14) | `891395e1415b25152680806b7551ae25` (updated 2026-10-14), **unchanged** |
| D1 Work #192847315128 | `891395e1415b25152680806b7551ae25` (same as BASELINE) | `fa53f5f378c2355d816acca3ee960393`, matches theme commit `8344ccd` |
| Live `test-data` | — | theme `updatedAt` still 2026-10-14, unchanged |

No store-data write was made in this session.

---

## DEC-D1-02 · 2026-10-21 · CR-01
**Decision:** Put all brand colours in the theme's colour palette. The primary button settings then point to palette entries instead of fixed hex values.

**Options considered**
| Option | Pros | Cons |
|---|---|---|
| (a) Type hex values into each button setting | Simple | The merchant has to retype the colour in many places; CR-04 has no shared accent token |
| **(b) Palette entries + references (chosen)** | Horizon's own pattern (most settings already point to `color_palette.*`); one place to change a brand colour; the merchant can pick these colours in every colour picker; CR-04 can reuse accent and muted | Palette keys `color3`–`color5` are not self-explaining (documented in `settings-changes.md`) |

**Reason:** Horizon 4.3.0 has no colour schemes. It uses one `color_palette` setting. In BASELINE, 22 global settings plus 51 settings inside `templates/*.json` and `sections/*.json` point to `color_palette.background` or `.foreground` (Verified by counting the references, 2026-10-21). Changing two palette values therefore re-colours the whole theme. New keys use the same `colorN` naming as the existing `color1` / `color2` entries. That this naming matches what the editor creates is an Inference.

**Consequence / limit:** `color5` = white was added only for the button text and the hover contrast (DEC-D1-03).

---

## DEC-D1-03 · 2026-10-21 · CR-01
**Decision:** Add white `#FFFFFF` to the palette, so that the primary button's **hover** text is white and not the off-white page background.

**Why:** Horizon computes the hover state by itself (`snippets/color-palette.liquid`, `snippets/util-palette-hover-shift.liquid`). On hover it lightens the terracotta by 5 % to `#B85D35`, and it sets the hover text to the **lightest palette colour**. Without white in the palette, that colour is `#FAF7F2`. Computed contrast:

| Hover text on `#B85D35` | Ratio | ≥ 4.5:1? |
|---|---|---|
| `#FAF7F2` (palette without white) | 4.22:1 | **No** |
| `#FFFFFF` (palette with white, chosen) | 4.51:1 | Yes, by a narrow margin |

Method: WCAG 2.x formula in Python. The script's versions of `color_lighten` and `color_brightness` reproduce the examples in Shopify's Liquid docs exactly (`#EA5AB9` → `#fbe2f3`, brightness 143.89). The hover colour is an Inference from reading the theme code. It still needs a DevTools check on the live page (owner, D1-03 verification).

**Options considered:**
- (a) accept 4.22:1 and disclose it;
- (b) darken the brand accent (changes brief v1.0, needs an owner decision);
- **(c) add white to the palette (chosen):** settings only, no brief change.

**Consequence / limit:**
- The hover pass is narrow (4.51:1).
- "Lightest palette colour" is also used for the hover text of the selected variant button, the cart bubble and the transparent header. There it is now `#FFFFFF` instead of `#FAF7F2`, which keeps the contrast the same or slightly higher (selected-variant hover: 8.36:1 instead of 7.82:1).
- The brief's acceptance criterion CR-01-AC4 covers the default state only. The hover result is recorded here as extra information, not as a new criterion.

---

## DEC-D1-04 · 2026-10-21 · CR-01
**Decision:**
- Heading font **Playfair Display Bold** (`playfair_display_n7`).
- Primary button corner radius **100 px**.

**Reason**
- **Font:** the brief names the family, not the weight. Rule used: change only what the brief names and keep the BASELINE weight (Inter **Bold** `inter_n7` → Playfair Display **Bold**). Playfair Display is in the Shopify font library with weights n4–n9 (Verified: shopify.dev "Fonts" page, available-fonts table, checked 2026-10-21). So the brief's fallback (another serif) is not needed.
- **Radius:** the brief says "radius 40 or the max the setting allows". The setting allows 0–100 px (Verified in `config/settings_schema.json`). 100 is also Horizon's own default. CSS limits the rounding to half the button height, so any radius at or above half the height gives a full pill. 100 keeps the pill shape on taller buttons too, where 40 would not.

**Options considered:**
- Font: `playfair_display_n4` (regular) looks lighter. It is a taste choice outside the brief. The owner can switch it in the editor in under a minute; the change goes in this log if made.
- Radius: 40 also gives a pill on normal-height buttons.

**Consequence / limit:**
- The secondary button and variant buttons keep radius 14 (the brief changes the primary button only). Mixed corner styles may be visible next to each other. Logged as an Observation for D1-12, not changed now.

---

## DEC-D1-05 · 2026-10-23 · CR-04 (brief v1.1)
**Decision:** Replace CR-04 "Save X%" price label with a collection description "Show more / Show less" toggle. Owner approved 2026-10-22 ("setuju") after the A-01b detail pass. Brief decision D-6; supersedes D-1.

**Options considered**
| Option | Pros | Cons |
|---|---|---|
| (a) Keep "Save X%" (v1.0) | Already specified; proves editing a shared snippet | No matching request among 40 D1 posts read in full |
| **(b) Collection description toggle (chosen)** | Same as a real hired Upwork request ($25); adds the collection page (9 of 40 D1 posts); small and safe; no JS needed (`<details>`) | Needs collection descriptions in store data; Horizon's description markup not read yet |
| (c) PDP promo box editable per product | Also a real request ($50) | Overlaps CR-03 (metafields on the PDP) |

**Evidence:** `40_acquisition/a01b-protocol.md` §8 "Stage 1b detail pass"; `40_acquisition/a01b-r2-details.csv` (job 2111785168044246191).

**Consequence / limit**
- CR-01 accent and muted palette entries stay; CR-04 now uses only the accent (link colour).
- D1-10 starts by reading Horizon's collection-description render; if it cannot be shortened without JS, record it here before changing the rules.
- Store-data change in D1-10: descriptions for "Shop all" and "Face care" (logged with before/after values).

---

## DEC-D1-06 · 2026-10-23 · CR-02
**Decision:** How the tag badges are built inside Horizon (theme commit `db80d11`, branch `demo1`).

| Choice | Chosen | Why |
|---|---|---|
| Where the code goes | Edit `blocks/_product-card-gallery.liquid` (same `.product-badges` box as Sale / Sold out); styles in `snippets/product-badges-styles.liquid` | S-07: the card does not accept custom theme blocks. `.product-badges` is only rendered by this block (checked with a search), so the CSS change cannot reach other pages. |
| Tag test | `tag \| slice: 0, 6 \| downcase == 'badge:'`; label `tag \| slice: 6, tag.size \| strip`, then `escape` | D1-04 Experiment 1: `contains` and `remove_first` give wrong results on `notbadge:Trap`. Tags are merchant text, so the label is escaped. |
| Max 2, order | Count the printed labels; `break` at 2; Shopify's tag order | Brief D-2. Output inside the `for` loop (D1-04 Experiment 2), so no list is built. |
| "Accent" colour | `--color-primary-button-background` (fill / line) and `--color-primary-button-text` (text on Filled) | The brief defines the accent as the primary button colour (CR-01 table). No new colour setting, and the merchant's button colour keeps controlling it. White on terracotta was measured 5.4:1 in CR-01. |
| Several badges | Column stack with a small gap, right-aligned when the position is top-right, `max-width` = card minus both insets, `overflow-wrap: anywhere` | AC2/AC3: no overlap, text stays inside the card. |
| Outline line | `box-shadow: inset` instead of `border` | Filled and Outline stay the same size, so switching style does not move anything. |
| No-image cards | `snippets/card-gallery.liquid` gets an optional `has_tag_badges` parameter | Horizon pads the title placeholder only when a badge exists; without this, a tag label would cover the title of a product with no image. Not in the test data (Tinted Lip Balm has no badge tag); code path only. |
| Editor labels | 5 new `t:` keys in `en.default.schema.json`; the 22 other `*.schema.json` files get the same English text | Without them Theme Check reports 110 `MatchingTranslations` errors (fails CR-02-AC7). Real translations are out of scope (brief §4); Shopify shows English for these 5 labels in other admin languages either way. |

**Consequence / limit**
- Theme Check after the change: 0 errors, 6 warnings (= baseline).
- The Outline style had a readability problem over photos (`issue-log.md` ISS-D1-01). Fixed 2026-10-23 in `c85a324`: Outline is filled with the page background colour (brief v1.2, D-7).

---

## DEC-D1-07 · 2026-10-24 · CR-03
**Decision:** How the "Product details" block is built (theme commit `a55be45`).

| Choice | Chosen | Why |
|---|---|---|
| Block type | New theme block `blocks/product-details-list.liquid`, product from `closest.product` | `_product-details` accepts `@theme` blocks (S-07). Same pattern as Horizon's own product blocks (e.g. `sku.liquid`). |
| Placement | Added to `templates/product.json` after the description | The brief says "added from the editor"; writing the same JSON the editor writes makes the result reproducible from Git. The merchant can still move, hide or remove it in the editor. |
| Markup | `<h2>` heading + `<dl>` with one `<dt>`/`<dd>` per row | A label/value list is what `<dl>` is for; screen readers read label and value as a pair. |
| Empty state | `if` around the whole block; no wrapper when all three are blank | CR-03-AC3 asks for nothing in the HTML. Limit: in the editor an empty block has no preview to click; it is still selectable in the sidebar. |
| Values | `value \| escape \| newline_to_br` for multi-line text, `escape` for single-line | Keeps the merchant's line breaks without allowing HTML in metafield text. |
| Styling | Theme tokens only (`--gap-*`, `--padding-sm`, `--opacity-10`, subheading font for labels); heading size from a "Heading preset" select (default h4), copied from Horizon's `disclosures` block | Settings-driven, matches nearby blocks; first version used a fixed `h5` that looked like a row label. |
| Editor labels | 8 new `t:` keys in all 23 schema locales (English in non-English files) | Same reason as DEC-D1-06 (Theme Check `MatchingTranslations`). |

---

## DEC-D1-08 · 2026-10-24 · CR-04 (brief v1.1)
**Decision:** Build CR-04 as a new `collection-description` block that wraps Horizon's existing text renderer, instead of editing the generic `text` block (theme commit `86ba6e5`).

**What Horizon does (read first, as D1-10 asks):** the collection description is not a dedicated component. `templates/collection.json` uses the generic `text` block with the dynamic source `{{ closest.collection.description }}`; `blocks/text.liquid` → `snippets/text.liquid`. Only this one template uses `collection.description` (search of all `.liquid` and `.json` files). Horizon already has small wrapper blocks for this pattern (`product-description.liquid`, `collection-title.liquid`).

| Option | Pros | Cons |
|---|---|---|
| (a) Edit `blocks/text.liquid` / `snippets/text.liquid` | One file | Every heading and paragraph on the site goes through it: high regression risk; the toggle would need a "is this the collection description?" check |
| **(b) New wrapper block, same settings as `text`, swapped in on the collection template (chosen)** | Horizon's own pattern; the generic text block is untouched; saved typography settings carry over (same IDs); short / empty / off cases render exactly like before | One more block file; "modify existing component" is met by reusing and re-rendering the existing text snippet rather than editing it |

**Other choices**
- Settings in **Theme settings → Collection description** (the brief's first option), so one switch controls every collection page.
- Preview = description with HTML stripped (`</p>` gets a trailing space first so paragraphs don't merge), split on spaces, first N words, trailing punctuation removed, then "…".
- Toggle = native `<details>`/`<summary>`: keyboard (Enter/Space), focus and screen-reader state come from the browser; no JavaScript (brief §4).
- The preview `<p>` is hidden with `:has([open])` once the toggle is open, so the start of the text is not shown twice.
- "Show less" stays above the text (native order); see the Observation in the QA checklist.

**Consequence / limit:** the first N words appear twice in the HTML (preview + full text). Acceptable for this demo; noted for the case study.
