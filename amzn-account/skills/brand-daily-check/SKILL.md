---
name: brand-daily-check
description: >
  This skill should be used when performing an Amazon brand health check, evaluating
  ASIN status, checking product pages on Amazon, or assessing brand performance on
  Amazon marketplaces. Trigger phrases: "daily check", "controlla il brand",
  "verifica gli ASIN", "brand check Amazon", "stato del brand", "check store Amazon",
  "controlla i prodotti su Amazon", "verifica Buy Box", "analisi brand Amazon".
version: 0.1.0
---

# Brand Daily Check — Domain Knowledge

Core knowledge for evaluating Amazon brand health across marketplaces. This skill powers the `/daily-check` and `/check-asin` commands, and guides the store-explorer, asin-auditor, and report-compiler agents.

For detailed evaluation criteria, see `references/check-criteria.md`.
For the full report format specification, see `references/report-structure.md`.

---

## The 3-Phase Workflow

A Brand Daily Check is a systematic audit of a brand's presence on Amazon, always run in this order:

1. **Phase 1 — Store Explorer** (`store-explorer` agent): Visits the brand's Amazon store page(s) and collects all ASIN codes + product URLs for every active marketplace.
2. **Phase 2 — ASIN Auditor** (`asin-auditor` agent): Opens each product page and collects structured health data for every metric.
3. **Phase 3 — Report Compiler** (`report-compiler` agent): Synthesizes all audit data into a professional Italian Word document for the seller.

The phases are sequential — each phase depends on the output file of the previous one.

---

## STORES.md — Brand Configuration File

The plugin uses a `STORES.md` file in the user's workspace folder to store all brand configurations. Always read this file before starting any check.

**File location**: workspace root folder (the folder selected in Cowork)
**Format**: One `##` heading per brand, with bullet fields for all metadata.

Key fields to extract from each entry:
- `Store URL (XX)` — the Amazon Store page URL for each marketplace
- `Countries` — list of active marketplaces
- `Brand Identity` — brand positioning, tone, differentiators (used in report context)
- `Category` — product category
- `Key Products` — flagship products or main lines

If STORES.md doesn't exist, direct the user to run `/add-store` first.

---

## Status Classification

Assign one of three status levels to each ASIN and to the brand overall:

| Status | Label | Meaning |
|--------|-------|---------|
| 🟢 | OK | All metrics within acceptable range, no active issues |
| 🟡 | ATTENZIONE | Issues present requiring monitoring or action this week |
| 🔴 | CRITICO | Urgent issue requiring action today |

**Overall brand status = worst ASIN status found.**

### CRITICO triggers (any one is sufficient):
- Buy Box lost to a third-party seller
- ASIN is completely unavailable or removed (page 404, listing suspended)
- Average rating < 3.5 stars
- Page error or redirect

### ATTENZIONE triggers:
- ASIN temporarily out of stock
- Rating between 3.5 and 3.9
- Recent 1-star or 2-star reviews in the last 30 days
- A+ Content missing on a key ASIN
- Price significantly above or below expected (potential dumping or markup by third party)
- Minor content anomalies (missing images, incomplete title)

### OK:
- Product available and purchasable
- Buy Box held by brand or Amazon
- Rating ≥ 4.0
- No recent negative review spikes
- Content looks complete

---

## Key Amazon Concepts to Know

**Buy Box**: The "Add to Cart" area on a product page. 80%+ of sales go through the Buy Box holder. If a third-party seller holds the Buy Box, the brand is losing those sales to an unauthorized reseller — always the highest-priority issue.

**ASIN** (Amazon Standard Identification Number): A 10-character code that uniquely identifies a product on Amazon. Format: 1 letter + 9 alphanumerics (e.g., B08XYZ1234). The same physical product has different ASINs in different marketplaces.

**A+ Content**: Enhanced brand content below the bullet points — brand story, comparison tables, rich imagery. Requires Amazon Brand Registry. Its absence on key ASINs is a missed conversion opportunity.

**Buy Box rotation**: Amazon sometimes rotates the Buy Box between eligible sellers. A third-party appearing on the Buy Box almost always signals an unauthorized seller undercutting the brand's price.

**ASIN suppression**: Amazon can suppress (hide) an ASIN without notice if it violates policies, has unresolved A-to-Z claims, or fails content quality checks. The page may redirect or show an error.

**Price parity rule**: If a lower price exists elsewhere (Amazon's own marketplace or other channels), Amazon may suppress the Buy Box. This means the product appears on Amazon but can't be purchased.

**Review velocity**: A sudden spike in negative reviews can indicate a coordinated attack, a quality defect, or a counterfeiting problem. Always flag if 3+ negative reviews appear within a short window.

---

## Amazon Marketplace TLDs

| Country | TLD | Marketplace Code |
|---------|-----|-----------------|
| Italy | amazon.it | IT |
| Germany | amazon.de | DE |
| France | amazon.fr | FR |
| Spain | amazon.es | ES |
| United Kingdom | amazon.co.uk | UK |
| Netherlands | amazon.nl | NL |
| Sweden | amazon.se | SE |
| Poland | amazon.pl | PL |
| Belgium | amazon.com.be | BE |

Product URL format: `https://www.amazon.[tld]/dp/[ASIN]`
Store URL format: `https://www.amazon.[tld]/stores/[brand]/page/[page-id]`
