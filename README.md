# Eureka Partners: retailer site (concept)

- **Live version:** https://eureka-partners-concept.netlify.app
- **This repo:** the latest version. The live site only changes when it is redeployed (see **Deploy**).

## What it is
- One-page scroll story for retailers: "Optimising your shelf space, so every centimetre earns you more rands."
- Everything is 3D and generated in code with three.js. There are no image or 3D model files.
- Black room, orange Eureka shelf, live data overlays in every scene.
- A light / dark toggle at the top right of both pages (see **Light mode** below).

## Files
- `index.html`: the overview scroll story (HTML, CSS and JavaScript in one file).
- `shelf-optimizer.html`: the **Shelf Optimizer™** sales demo, linked from the top navigation.

## Run it locally
- Serve the folder with any web server, for example `python3 -m http.server 8000`, then open http://localhost:8000.
- Needs an internet connection: three.js and the fonts load from CDNs.

## Scenes (in scroll order)
1. **Hero:** a pallet of 192 Eureka jars with a scan outline.
2. **Stocked to sell:** all 192 jars fly onto the shelf, the shelf counter fills, and the empty pallet fades out.
3. **We know what moves:** sales bars rise above the shelf, sparks flow from best sellers, and lids glow by sales speed.
4. **Fast movers, front and centre:** a planogram grid appears, and best sellers move to the eye-level band.
5. **More product. Same shelf.:** round jars turn square, freed space is outlined, and 32 extra jars drop in.
6. **The right product, every time:** three best sellers sell down, a reorder pulse fires, and they refill before running out.
7. **+41% rands per m²:** gold coin stacks rise beside the shelf.

## Placeholders to replace before launch
- **All figures are illustrative:**
  - the +41% (`pct` counter)
  - the 192 units
  - "top 24 sellers"
  - 160 → 135 mm per facing
  - +32 units of space
  - per-product sales speeds (random, seeded, `NP`/`vel`)
- **Links:** the "Become a stockist" button in the closing orange section points to `#`. The header button scrolls to that section.
- **Logo:** the EUREKA wordmark is drawn in the Lilita One font, the closest match to the logo. Swap in the official logo.

## Where things live in `index.html`
- **Headlines:** the `<section class="step">` blocks.
- **Camera path:** `camPos` / `camTgt` (one key per scene).
- **Scene timing:** `fl`, `pf`, `h`, `r`, `m`, `ex`, `cn` inside `frame()`.
- **Shelf logic:**
  - `SPRI`: eye-level priority of the shelves
  - `RP`: the products that run the restock loop
- **Lighting:** the `spot(...)` calls. Brand orange is `#f16e1f`.

## Dependencies (all from CDNs)
- **three.js:** 0.160.0 via cdn.jsdelivr.net, plus these add-ons: RoomEnvironment, EffectComposer, UnrealBloomPass, OutputPass, Reflector and CSS2DRenderer.
- **Google Fonts:** Space Grotesk, Inter, JetBrains Mono and Lilita One, plus Source Serif 4 for light-mode headings.

## Light mode
- **Toggle:** the sun / moon button at the top right of the header on both pages. Dark is the default.
- **Remembered:** the choice is saved in the browser (`localStorage`, key `eureka-theme`), so it carries between the two pages and across visits.
- **Shareable:** add `?theme=light` or `?theme=dark` to a link to open the site in that mode, e.g. `shelf-optimizer.html?theme=light#opportunity`.
- **Look:** styled like a financial presentation: white page, near-black text, hairline borders instead of glows, serif headings (Source Serif 4), Inter with tabular figures for every number, and statement-style tables with a rule under the headers and a double rule under totals.
- **3D:** the black showroom becomes a bright white studio. Lights, floor, reflections and bloom ease across in about half a second, and the orange data overlays switch from additive glow (which vanishes on white) to solid colour.
- **Where it lives:**
  - CSS: the block headed `light theme` at the end of each page's `<style>`. Every rule is scoped to `html[data-theme=light]`, so dark mode is untouched.
  - 3D: the `THEME` object (dark and light values for lights, floor, background, bloom) and `applyTheme()` in each page's script.
  - Charts (Shelf Optimizer): `PAL()` holds the chart colours for each theme.
  - Light-mode orange text is `#bf5009` (a darker brand orange) for legible contrast on white. Brand `#f16e1f` is kept for buttons, bars, pins and fills.

## Deploy
- **Hosting:** static files, no build step. Any static host works.
- **Netlify:** leave the build command empty. The live site was deployed with `netlify deploy --prod --no-build --dir .`.

## Shelf Optimizer™ (`shelf-optimizer.html`)
A three-step sales tool for Eureka reps to use in retailer meetings. The progress bar under the header stays visible. Back, Continue and the step buttons move between steps, and the browser back button works too (`#products`, `#location`, `#opportunity`). Selections are kept when moving back and forth.

1. **Choose products.** Six products, each with a live 3D thumbnail. Tap to add or remove. The live 3D shelf and the 1–24 shelf slider are the same as before. The right panel shows revenue and gross profit before location is applied.
   - Badges are computed from the data, never hand-assigned: **Top Seller** = highest `unitsPerShelf`; **Highest ROI** = highest GMROI (margin ÷ (1 − margin) × stock turns).
2. **Select location.** Interactive South Africa map: an SVG built from Natural Earth country outlines, simplified and embedded in the page, with no map library.
   - Pick Johannesburg, Cape Town, Durban or Bloemfontein, or tap anywhere in South Africa. Lesotho and Eswatini are recognised and refused.
   - Custom taps get an approximate label (e.g. "≈ 170 km E of Springbok, Northern Cape (approx.)") and approximate coordinates. They are never presented as an address.
   - The dot grid on the map shows the simulated location potential everywhere.
3. **Your opportunity.** Dashboard with:
   - the 3D shelf
   - revenue, gross profit, gross profit per shelf and multi-year gross profit
   - products, shelves, location and multiplier
   - a "how the numbers add up" strip
   - a per-product breakdown table (shelf space, units, average price, revenue, margin, gross profit)
   - revenue contribution and revenue vs gross profit charts
   - a Spotlight on the badge products: what adding them would change
   - a 3- or 5-year outlook with adjustable growth
   - a 1–24 shelf scaling chart

### The maths (all simulated)
- **Revenue per shelf (product mix)** = Σ selected products: shelf share × `unitsPerShelf` × `price`. Shelf share = the product's `space` ÷ total `space` of selected products, so each shelf is shared and no product is counted twice.
- **Annual revenue** = revenue per shelf × shelf scaling factor × location multiplier. The shelf scaling factor = shelves ^ `shelfScaling.exponent`; default 1, which is linear.
- **Gross profit** = Σ products: product revenue × product `margin`. Product rows are rounded so they add up exactly to the headline figures.
- **Location multiplier** = based on distance to the nearest hub along `location.curve`:
  - the hub's own value within 25 km (Johannesburg 1.20×, Cape Town 1.15×, Durban 1.10×, Bloemfontein 1.05×)
  - then 1.02× at 100 km, 0.92× at 250 km, 0.82× at 450 km and 0.75× from 650 km
  - straight-line in between
  - "National baseline" (1.00×) until a location is chosen
- **Multi-year**: year *n* = year 1 × (1 + growth)^(n−1). Default growth 3%, adjustable on the page from 0–15%.
- Every figure is labelled as an illustrative estimate. Gross profit is described as before operating costs, so it is not net profit.

### Where to edit
Everything is in `CONFIG` and `PRODUCTS` at the top of the page's script.
- **`PRODUCTS`**:
  - `price`: average selling price
  - `unitsPerShelf`: annual units if a whole shelf held only this product
  - `margin`
  - `stockTurns`
  - `space`: share of shelf width
  - packaging `type`: `jar`, `tub`, `box` or `card`
  - label text
- **`CONFIG.location`**:
  - `hubs`: names, coordinates and multipliers
  - `curve`: distance → multiplier
  - `bands`: market-type labels
  - `towns`: reference towns used to label custom taps
- **`CONFIG.badges`**: what each badge measures and its explanation text.
- **`CONFIG.projection`**: years, default growth and maximum growth.
- **`CONFIG.shelfScaling`**: set the exponent below 1 for diminishing returns.
- **`CONFIG.shelves`**: slider range and shelves per bay.
