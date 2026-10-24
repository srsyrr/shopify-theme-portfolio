# Theme customization from a written brief — Horizon (practice project)

> **Practice project.** Fictional skincare brand "Skinhale", built on a Shopify development store.
> Not client work, no real customers or sales. Base theme: Horizon 4.3.0 (free Shopify theme). Dates: 19–24 October 2026.
> **How this was made:** I directed this project: I chose the brand and scope, approved every decision in the brief and decision log, and checked the results. The code, QA runs and write-ups were produced with an AI coding agent (Claude Code). I am now studying the code line by line and rebuilding one change myself.

## Objective & constraints
- Goal: apply 4 change requests (CRs) from a written brief to an existing theme without breaking related storefront functions.
- Constraints: existing theme only (no rebuild), no apps, no JavaScript features, no cart or checkout changes. About 9 logged hours for tasks D1-04 to D1-13 (build and QA with the AI agent, board column `Actual h`). That is not the time the same work would take me alone. Earlier setup and brief tasks were not timed.

## Starting point & brief
- Baseline: unmodified Horizon 4.3.0, kept as a separate theme that was never edited (`BASELINE Horizon v4.3.0 (DO NOT EDIT)`). All work went to a copy, "D1 Work"; nothing was published.
- Brief: [`brief.md`](brief.md) (v1.2): CR-01 … CR-04, each with 6–8 acceptance criteria tied to a page and a viewport.

## What was implemented
| CR | Request | Approach | Verified by |
|---|---|---|---|
| CR-01 | Brand colours, heading font, pill buttons | Theme settings only, no code (10 settings) | theme settings only; colours checked in the browser (computed styles) |
| CR-02 | Label on product cards from a `badge:<text>` tag | Edited the existing card badge code (the card does not accept custom blocks); 2 theme settings | [QA: CR-02](qa-checklist.md#cr-02-product-card-badge-from-tags-d1-05-d1-06) — 7 criteria + 11 edge cases |
| CR-03 | Key ingredients / Skin type / How to use on the product page | New theme block reading 3 metafields; renders nothing when all are empty | [QA: CR-03](qa-checklist.md#cr-03-product-details-from-metafields-d1-08-d1-09) — full / partial / empty / long value |
| CR-04 | Long collection description → first 30 words + "Show more / Show less" | New block wrapping the theme's own text renderer, native `<details>`, 4 theme settings | [QA: CR-04](qa-checklist.md#cr-04-collection-description-show-more--show-less-d1-10-d1-11-brief-v11) — long / short / empty, mouse + keyboard |

Before / after: [`images/`](images/) (`D1_compare_*`) (collection and product page at 390 and 1440 px).

**Code excerpt (CR-02, `blocks/_product-card-gallery.liquid`):**
```liquid
{%- for tag in product.tags -%}
  {%- assign tag_prefix = tag | slice: 0, 6 | downcase -%}
  {%- assign tag_text = tag | slice: 6, tag.size | strip -%}
  {%- if tag_prefix == 'badge:' and tag_text != blank -%}
    <div class="product-badges__badge product-badges__badge--rectangle product-badges__badge--tag product-badges__badge--{{ settings.tag_badge_style }}">
      {{- tag_text | escape -}}
    </div>
    {%- assign has_tag_badges = true -%}
    {%- assign tag_badge_count = tag_badge_count | plus: 1 -%}
    {%- if tag_badge_count == 2 -%}{%- break -%}{%- endif -%}
  {%- endif -%}
{%- endfor -%}
```
It checks the first 6 characters of each tag instead of using `contains`, because a small test showed `contains 'badge:'` also matches a tag like `notbadge:Trap` ([`liquid-study.md`](liquid-study.md)). The label reuses Horizon's own badge classes, so the merchant's existing badge settings (position, radius, font) apply to it too.

## Key technical decisions
- **Settings before code (CR-01):** all brand colours went into the theme palette, so one change re-colours the whole theme; adding white to the palette lifted the button hover text from 4.22:1 to 4.51:1 contrast (DEC-D1-02/03).
- **Edit the existing card code (CR-02):** a proof of concept showed the product card does not accept custom blocks, so the labels sit in Horizon's existing badge area, after Sale / Sold out (DEC-D1-06).
- **Empty means empty (CR-03):** the whole block is wrapped in one condition, so a product with no data has no heading and no empty box in the HTML (DEC-D1-07).
- **Don't touch the shared text block (CR-04):** the description is rendered by Horizon's generic text block, used for most text on the site. It is wrapped in a new block for the collection page instead of being edited, so no other text can change (DEC-D1-08).
- **No JavaScript (CR-04):** the toggle is a native `<details>` element; the full description stays in the page HTML.

Decision log: [`decision-log.md`](decision-log.md).

## QA process & evidence
- Viewports: 390 / 768 / 1440, plus 360 / 1280 spot checks. Browsers: Chromium (headless), Microsoft Edge 156, Firefox 158 (screenshots only). Real phone and Safari: not tested.
- 7 pages × 5 widths: 35 of 35 with no horizontal scroll and no broken images.
- Functional checks: navigation, footer links, search, sort and filter, card → product, variants, add to cart, sold out, cart edit, 404, console, Theme Check (0 errors, 6 upstream warnings, same as baseline).
- Every acceptance criterion has a PASS row with a screenshot or a recorded DOM check; owner-only checks are listed as such. Full list: [`qa-checklist.md`](qa-checklist.md).

## Issues encountered & resolutions
- **Outline badge unreadable on photos (ISS-D1-01):** the brief asked for a transparent fill; on a red product photo the terracotta text almost disappeared. Cause: the label sits on the image, so contrast depends on each photo. Fix (owner-approved brief change v1.2): fill with the page background colour, 5.10:1 on any photo.
- **110 Theme Check errors after adding settings:** new editor labels existed only in English, so `MatchingTranslations` failed. Fix: the same keys in all 23 schema locale files.
- **Store data for tests:** edge-case tags were added to 3 products for one test and removed afterwards; collection descriptions were changed for CR-04. Both are logged with before/after values.

Issue log: [`issue-log.md`](issue-log.md).

## Not tested / limitations
- Safari / iOS not tested (no device). Real phone not tested in this pass. Firefox checked from screenshots only (toggle click not tested there).
- Checkout, contact and newsletter forms were not tested (not changed in this demo).
- The theme editor checks (settings toggles, label change) were tested in a local preview; the same checks inside the Shopify editor are still open (my own check).
- Translations: new editor labels are English in every language. Performance and SEO were not assessed.
- When opened, "Show less" sits above the full text (native `<details>` order, kept so focus order matches visual order).
- CR-04 on wide screens: the preview line is wider than the full text, and "Show more" is centred on the whole section rather than under the text column (the preview and link sit outside the text block's max-width setting). Not visible at 360–390 px. Found after QA, not fixed.
- Content: the demo product descriptions already list ingredients and usage, so on one product the same facts appear twice. Product copy was out of scope.

## What this project covers
Safe theme workflow (untouched baseline, work copy, Git) · reading existing theme code before changing it · Liquid with tags and metafields · merchant-facing theme settings · responsive and functional QA · documentation.

Walkthrough (≤5 min): _link coming_ · Code: [`code/`](code/) (only the files added for this project)
