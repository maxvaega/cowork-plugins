---
description: Check a single Amazon ASIN in detail
allowed-tools: Read, Write, mcp__Claude_in_Chrome__tabs_context_mcp, mcp__Claude_in_Chrome__tabs_create_mcp, mcp__Claude_in_Chrome__navigate, mcp__Claude_in_Chrome__read_page, mcp__Claude_in_Chrome__get_page_text, mcp__Claude_in_Chrome__computer, mcp__Claude_in_Chrome__find, mcp__Claude_in_Chrome__javascript_tool
argument-hint: [ASIN] [country-code]
---

Run a detailed health check on a single Amazon ASIN using the browser.

Use the `brand-daily-check` skill for evaluation criteria.

## Parse Arguments

Parse "$ARGUMENTS":
- First token: ASIN code (10-character alphanumeric, e.g. B08XYZ1234)
- Second token (optional): country code — IT, DE, FR, ES, UK (default: IT)

If no ASIN is provided, ask the user: "Quale ASIN vuoi controllare? (e su quale marketplace — IT, DE, FR, ES, UK?)"

## Build Product URL

Construct the URL based on country:
- IT → `https://www.amazon.it/dp/[ASIN]`
- DE → `https://www.amazon.de/dp/[ASIN]`
- FR → `https://www.amazon.fr/dp/[ASIN]`
- ES → `https://www.amazon.es/dp/[ASIN]`
- UK → `https://www.amazon.co.uk/dp/[ASIN]`

## Strategia di raccolta dati

**Seguire sempre questo ordine per massimizzare l'efficienza (obiettivo: 3-4 chiamate totali):**

1. Naviga alla pagina prodotto e attendi il caricamento completo.

2. **`get_page_text`** — prima operazione obbligatoria. Estrae in un colpo solo: titolo, prezzo, venditore, rating, numero recensioni, testo delle recensioni, disponibilità. Copre l'80% dei dati necessari.

3. **`javascript_tool`** — usa solo se `get_page_text` non ha restituito un dato specifico in modo chiaro. Selettori utili:
   - Pulsante carrello: `!!document.querySelector('#add-to-cart-button')`
   - Prezzo Buy Box: `document.querySelector('.a-price .a-offscreen')?.innerText`
   - Venditore: `document.querySelector('#sellerProfileTriggerId, #merchant-info')?.innerText`
   - Rating: `document.querySelector('#acrPopover')?.title`
   - N° recensioni: `document.querySelector('#acrCustomerReviewText')?.innerText`

4. **`computer` (screenshot)** — usare **solo** per confermare visivamente:
   - Numero di immagini nel gallery laterale
   - Presenza di A+ content (sezione grafica sotto i bullet point)
   - Badge "Scelta Amazon" / "Amazon's Choice"
   - Anomalie visive sulla pagina

## Open and Check

1. Get browser context, create a new tab if needed.
2. Navigate to the product URL.
3. Wait for the page to fully load.
4. Seguire la **Strategia di raccolta dati** sopra per raccogliere tutti i dati in modo efficiente.

**Disponibilità**
- Is "Aggiungi al carrello" (Add to Cart) present?
- If not: what is the status message? (Out of stock, discontinued, page error, 404?)

**Prezzo**
- Current Buy Box price
- Original/list price (if crossed out)
- Active discount percentage (if shown)

**Buy Box**
- Is the Buy Box present?
- Seller shown in "Venduto da" / "Sold by":
  - Brand name or Amazon → OK
  - Third-party seller → CRITICO — note the exact seller name

**Recensioni**
- Average star rating (X.X/5)
- Total number of ratings
- Leggi **solo le recensioni degli ultimi 30 giorni** (data ≥ oggi − 30 giorni); ignora completamente le recensioni più vecchie.
- Per ogni recensione nel periodo: data, stelle, titolo, breve sommario.
- Segnala esplicitamente le recensioni con 1 o 2 stelle negli ultimi 30 giorni ⚠️
- Se non ci sono recensioni negli ultimi 30 giorni, indicare "Nessuna recensione nel periodo".

**Contenuto**
- Product title (first 80 chars)
- Number of main images visible
- A+ content section present? (look below the bullet points)
- Any visible anomalies in content?

**Anomalie**
- Page 404 or redirect?
- Any warning banners on the page?
- Product suspended, merged, or redirected?

## Output

Display the result directly in the chat in this format:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ASIN CHECK — [ASIN] ([country])
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Prodotto : [title, max 60 chars]
URL      : [url]

STATO GENERALE: 🟢 OK / 🟡 ATTENZIONE / 🔴 CRITICO

📦 Disponibilità : [Disponibile / Out of stock / Non disponibile]
💶 Prezzo        : €XX.XX [— listino €XX.XX, sconto XX%]
🏆 Buy Box       : ✅ [brand/Amazon] / ❌ Persa — [seller name]
⭐ Rating        : X.X/5 (N recensioni)

📝 Recensioni ultimi 30 giorni:
  • [data] ★★★★☆ — "[titolo]"
  • [data] ★★☆☆☆ — "[titolo]" ⚠️
  (oppure: "Nessuna recensione nel periodo")

🖼️ Immagini      : N visibili
✨ A+ Content    : Presente / Assente

⚠️ Anomalie: [list or "Nessuna"]

💡 Azioni consigliate:
  1. [action if any]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Assign status based on brand-daily-check skill criteria.
