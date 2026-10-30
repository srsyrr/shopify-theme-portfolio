# D-Mobile: how the "broken on mobile" state was made

**This "before" state is simulated.** No real store or client was involved. To practise a typical Upwork job ("my Shopify store looks broken on phones"), I started from the Demo 1 theme (Horizon 4.3.0 + Demo 1 changes, theme branch `demo1` `86ba6e5`). I then added code the way a previous developer might have: built and checked on a laptop, never on a phone. Theme branch `dmobile`, commit `a7ab083` ("before").

All five problems come from three added files and one edited line:

| File | What it is |
|---|---|
| `assets/store-custom.css` (new) | A "store custom CSS" file. It is loaded after the theme's own styles via one new line in `layout/theme.liquid`. |
| `sections/trust-bar.liquid` (new) | A custom section with 3 short store promises (shipping, returns, formulas), added to the homepage. |
| `sections/promo-banner.liquid` (new) | A custom section with a product photo and a text box on top of it, added to the homepage. |
| `templates/index.json` | The two new sections placed on the homepage: trust bar after the hero, promo banner at the end. |

## The five problems

| # | Problem a shopper sees on a phone | Page | How it was introduced (cause) |
|---|---|---|---|
| 1 | The page slides sideways. The first promise box is cut off at the left edge. | Home | Trust bar items have a **fixed width** (`flex: 0 0 320px`) in a row that never wraps. 3 × 320 px + gaps = 1,008 px, wider than any phone or a 768 px tablet. `justify-content: center` pushes the overflow out on both sides. |
| 2 | The promo text box runs off the right edge of the screen, over the photo, and the page slides sideways. | Home | Promo banner text box positioned with **fixed pixel values** (`position: absolute; top: 72px; left: 72px; width: 440px`, 44 px heading), designed for a 1,440 px screen. |
| 3 | Header icons (menu, search, account, cart) are tiny, 24 × 24 px or less, and partly clipped. They are hard to tap. | All | A "compact header on phones" rule in `store-custom.css` sets the icons to `width: 24px; height: 24px; padding: 0`. Horizon's own size is about 44 × 44 px. |
| 4 | Add to cart sits at the very bottom edge of the first screen (or below it), and is a thin 32 px button. | Product | A "taller product photos on phones" rule (`aspect-ratio: 3 / 4 !important`) roughly doubles the photo height. A "compact buttons" rule makes the Add to cart button 32 px tall with 12 px text. |
| 5 | A long product title spills over the price on the product card. | Collection (Face care) | A rule meant to line up prices on desktop gives every card title a **fixed height** (`height: 4.5em`, 3 lines at 16 px). On a phone the card is about 160 px wide, so the 88-character title "Overnight Hydrating Sleeping Mask with Hyaluronic Acid and Oat Extract for Dry Skin, 50 ml" needs about 6 lines. |

## What was deliberately kept normal

- **Desktop (990 px and wider) looks fine** in the before state. That is the point of the story: the store owner checked on a laptop. The 1,440 px "before" screenshots are the reference for "desktop unchanged".
- No store data was changed: same products, collections and prices as Demo 1. The long title already existed (Demo 1 edge-case product).
- Nothing was published. Work ran on a development theme via `shopify theme dev`. The BASELINE theme and D1 Work were not touched.
