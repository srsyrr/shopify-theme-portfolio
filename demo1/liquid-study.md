# Liquid study for CR-02 (D1-04)

2026-10-20, cloud session. Code: `sections/poc-liquid-d1.liquid` on theme branch `poc`. Run with `shopify theme dev`, then open `http://127.0.0.1:9292/?section_id=poc-liquid-d1`. All outputs below were observed in that response, not predicted.

Concepts used: `product.tags`, `for`, `if`, `contains`, `split`, `remove_first`, `slice`, `downcase`, `strip`, `assign`, `append`, `join`, `first`, `size`, `blank`.

## Experiment 1: which test finds a `badge:` tag?
**Input:** `badge:New`, `sale`, `Badge:Upper case`, `notbadge:Trap`, `badge:` (empty), `badge:Limited edition run of only fifty pieces`.

| Tag | `contains 'badge:'` | starts with `badge:` (`slice: 0, 6 == 'badge:'`) | `remove_first: 'badge:'` |
|---|---|---|---|
| `badge:New` | true | true | `New` |
| `sale` | false | false | `sale` |
| `Badge:Upper case` | **false** | false | `Badge:Upper case` |
| `notbadge:Trap` | **true (false positive)** | false | **`notTrap`** (wrong) |
| `badge:` | true | true | `` (empty) |
| `badge:Limited edition run…` | true | true | `Limited edition run of only fifty pieces` |

**Learned:**
- `contains` matches anywhere in the string, so `notbadge:Trap` would wrongly become a badge. Check the **prefix** with `slice: 0, 6` instead.
- `remove_first` also removes the first match anywhere, so it gives `notTrap`. Take the text with `slice: 6, tag.size` after the prefix check instead.
- Comparisons are case-sensitive: `Badge:` does not match `badge:`.

## Experiment 2: collect clean badge texts
**Logic:** prefix `slice: 0, 6 | downcase == 'badge:'`; text `slice: 6, tag.size | strip`; skip if `blank`; build a string with `append` and `|`, then `split: '|'`.

**Output:**
- badges: `["New", "Upper case", "Limited edition run of only fifty pieces"]`, count `3`
- first only: `"New"`; all joined: `"New / Upper case / Limited edition run of only fifty pieces"`

**Learned:**
- `downcase` on the prefix makes `Badge:` work too, and the empty `badge:` is dropped by the `blank` check.
- Building a string and splitting it breaks if a badge text contains the separator `|`. For CR-02 it is simpler to **output each badge inside the `for` loop** (use `break` after the first one if the brief says "first only"), so no list needs to be built.

## Experiment 3: same logic on real store data
**Input:** the first 10 products in `collections.all`.

**Output (summary):** no product has a `badge:` tag yet (tags are `Accessory`, `Sport`, `Winter`, `Premium`, `Snow`, `Snowboard`), so every badge list is `[]`. Theme state that matters for CR-02:
- `Gift Card` and `The 3p Fulfilled Snowboard`: `available = false`, so they already show the theme's **Sold out** badge.
- `The Compare at Price Snowboard`: on sale, so it shows the theme's **Sale** badge.

**Learned:**
- The logic runs on real `product.tags` without errors, and returns an empty list when there are no badge tags (no stray markup).
- Re-run this experiment after S-08 adds `badge:*` tags (§E6: `badge:New`, `badge:Limited edition run`, two badge tags on one product).

## Experiment 3 re-run after S-08 (2026-10-22)
Same code, limit raised to 50 (theme `poc` branch). 13 active Skinhale products returned (archived test products are not in `collections.all`). Observed:
- `Body Wash`: tags `["badge:Limited edition run","badge:New"]` → badges `["Limited edition run","New"]`
- `Daily Hand Cream`: `["badge:New"]` → `["New"]`
- `Facial Oil`: `["badge:Limited edition run"]` → `["Limited edition run"]`
- all 10 others: no tags → `[]`. `Barrier Cream Moisturizer` available = false (Sold out); `Hydrating Toner` on sale = true.

This matches the brief's CR-02 test data and the label order expected in CR-02-AC2 ("Limited edition run", then "New"). Whether Shopify orders tags alphabetically or by another rule is still **not proven** (both rules give this order here); AC2 only depends on the observed order.

## Decisions to make in the D1-01 brief
1. **Case:** accept `Badge:` / `BADGE:` (case-insensitive prefix, Experiment 2) or only lowercase `badge:`? Suggest case-insensitive; merchants type tags by hand.
2. **Several badge tags:** show the first only, or all? Shopify returns tags in its own order (alphabetical in this data) [Inference: not tested with badge tags yet], so "first" may not be the one the merchant expects.
3. **Overlap with Sold out / Sale:** the existing code (`blocks/_product-card-gallery.liquid`, lines 7–28) shows one theme badge in `.product-badges`. Decide whether tag badges sit in the same container next to it or replace it.
4. **Long text:** "Limited edition run of only fifty pieces" is 40 characters; it needs wrapping or truncation rules at 360px (test in D1-06).
