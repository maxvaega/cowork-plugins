# Detailed Check Criteria — Brand Daily Check

Detailed evaluation criteria for each data point collected during an ASIN audit.

---

## Availability

**How to determine availability on a product page:**

- "Aggiungi al carrello" button present and active → **Disponibile** ✅
- "Aggiungi al carrello" present but grayed out → check for seller restrictions
- "Attualmente non disponibile" visible → **Out of stock temporaneo** ⚠️ (might restock)
- "Questo articolo non è disponibile" or "Prodotto non disponibile" → **Non disponibile** 🔴
- Page 404 / redirected to homepage / "Spiacenti, questa pagina non esiste" → **ASIN rimosso/sospeso** 🔴
- "Non spedibile nella tua area" → shipping restriction (note the location)

**Variant products**: If a product has size/color/scent variants, check the default variant. If the default is unavailable, note which variants are available.

**Enrollment check**: "Compra Nuovi: N da €XX,XX" section — if present, it means third-party sellers offer this product new. Note the number of sellers and the lowest price.

---

## Price

**What to collect and how to interpret:**

- **Current price**: the prominent price shown in the Buy Box area (e.g., "€24,90")
- **Original/list price**: crossed-out price shown above or next to current price
- **Discount**: percentage or absolute amount (e.g., "-15%" or "Risparmi €3,90")

**Red flags:**
- No price shown at all → almost always means Buy Box is lost or suppressed
- Price much higher than expected → possible third-party seller holding Buy Box at a markup
- Price much lower than expected → possible dumping by unauthorized reseller (damages brand positioning)
- Price varies significantly between marketplaces → may indicate a market grey import issue

**Note**: Amazon prices can change hourly due to dynamic pricing. Always timestamp the data.

---

## Buy Box

**Most critical metric — highest priority in all reports.**

**How to find the Buy Box holder:**
1. Look below the product title and price for the "Venduto da" (Sold by) text
2. It appears in the buy box area: "Venduto e spedito da [Seller Name]" or "Venduto da [Seller Name], spedito da Amazon"
3. If the seller is Amazon itself: shows "Amazon" or the brand's own seller account name

**Classification:**
- Seller = brand name or brand's official Amazon seller account → ✅ OK
- Seller = "Amazon" (Amazon itself selling wholesale) → ✅ OK (brand has a vendor relationship)
- Seller = any other third-party name → ❌ BUY BOX LOST — 🔴 CRITICO

**What to record for third-party sellers:**
- Exact seller name (e.g., "FastShop EU", "Best_Deals_IT")
- Their price vs. brand's known price
- Whether they offer Prime

**Why this matters:**
The Buy Box determines 80%+ of all direct sales on Amazon. A lost Buy Box means:
1. The brand is losing revenue to an unauthorized seller
2. The unauthorized seller may be selling grey market, expired, or counterfeit goods
3. The brand has no control over the customer experience (packaging, inserts, returns handling)
4. Amazon's algorithm may lower the brand's organic ranking over time

---

## Reviews & Ratings

**What to collect:**

- **Overall rating**: The star average (e.g., 4.3) shown prominently below the product title
- **Total ratings count**: The number in parentheses (e.g., "1.247 recensioni")
- **Recent reviews**: Sort by "Più recenti" (Most recent) — collect the last 2-3 reviews:
  - Date (DD/MM/YYYY or "X giorni fa")
  - Star rating (1–5)
  - Review title (headline)
  - First 1-2 sentences of the review body

**Status classification:**
- Rating ≥ 4.0 → 🟢 OK
- Rating 3.5–3.9 → 🟡 ATTENZIONE
- Rating < 3.5 → 🔴 CRITICO
- Spike of 3+ recent 1-star reviews → 🟡 ATTENZIONE regardless of overall rating

**Red flags to specifically note:**
- 1-star review mentioning "contraffatto" (counterfeit) or "falso" (fake) → report immediately
- 1-star review mentioning "prodotto sbagliato" (wrong product) → possible ASIN merging issue
- Multiple reviews mentioning the same defect → possible quality issue requiring brand action
- Unusually high review velocity (many reviews in a short period) → investigate source

**Note on review language**: Amazon.it shows primarily Italian reviews. Amazon.de shows German reviews. The rating aggregates all marketplace-wide reviews, but visible reviews are filtered by language/country.

---

## Product Content

**What to check and how:**

**Title** (first element in breadcrumb and H1):
- Is it correct and recognizable as this brand's product?
- Does it contain relevant keywords without being keyword-stuffed?
- Is it the same title the brand intended? (Title hijacking can occur on high-traffic ASINs)

**Images**:
- Count visible images in the main image gallery (thumbnails on the left or bottom)
- Standard expectation: 5–7 images for a well-optimized listing
- Minimum acceptable: 3 images
- Main image must be on white background (Amazon requirement)
- Red flag: only 1 image, wrong product images, competitor product visible

**A+ Content** (Enhanced Brand Content):
- Scroll below the bullet points
- A+ content appears as branded sections with rich images, comparison tables, or brand story modules
- It is ONLY available to brands registered with Amazon Brand Registry
- If absent on a key product: note as content opportunity (🟡)
- If a brand's listings should have A+ and it's missing: flag it

**Bullet points** (not always visible in audits, but note if clearly wrong or missing):
- Should be 3–5 bullet points highlighting key features
- If bullet points are in a different language than the marketplace: flag as content issue

---

## Anomalies

**Always flag immediately:**

- Page 404 or "Questa pagina non esiste" → ASIN removed or URL broken
- Redirect to a completely different product → ASIN merged (critical — can destroy reviews and content)
- "Al momento non disponibile" with no timeline → listing issue
- Counterfeit or quality complaint banner (rare but critical)
- "Non acquistabile" due to regulatory issues
- Amazon A-to-Z Guarantee claim banner
- "Acquistalo su" button redirecting to external store → Amazon is pushing away from this listing
- ASIN showing under a completely wrong brand name → possible catalog attack

**Less critical but worth noting:**
- Sponsored ads for competitor products placed immediately below the Buy Box (normal, but worth noting)
- "Frequentemente acquistato insieme" (frequently bought together) showing competitor products
- Missing breadcrumb trail (suggests catalog categorization issues)
