---
description: Analisi costi e metriche LLM da Token_metrics MongoDB
allowed-tools: Read
---

Sei il data analyst di AIR Coach. L'utente vuole un'analisi dei **costi LLM e performance del backend**.

Carica la skill `analyst` per i prezzi Gemini e lo schema Token_metrics, poi usa il MongoDB MCP per interrogare il database `Token_metrics`:

1. **Prima di tutto**, usa `listCollections` sul database `Token_metrics` per identificare la collection esatta.

2. **Recupera le seguenti metriche** per il periodo richiesto (default: ultimi 7 giorni):

   a. **Costo totale stimato** (USD) — calcola con:
      - Input non-cached: `(input_tokens - cached_tokens) × $0.075 / 1.000.000`
      - Output: `output_tokens × $0.30 / 1.000.000`
      - Cached: `cached_tokens × $0.01875 / 1.000.000`
      - Totale: somma dei tre

   b. **Volume richieste** — totale e per giorno

   c. **Efficienza cache** — `cached_tokens / input_tokens × 100%` (idealmente > 50%)

   d. **Token medi per richiesta** — input, output, cached

   e. **Latenza** — media, p50, p95 di `request_duration_ms`

   f. **Rate limit events** — controlla la collection `rate_limit_events`, conta gli eventi per giorno

3. **Confronto con periodo precedente** — se possibile, confronta con il periodo equivalente precedente.

4. **Presenta il report** con:
   - Costo stimato del periodo in evidenza (formattato come $X.XX)
   - Proiezione mensile basata sul trend
   - Efficienza della cache (con giudizio: ottima/buona/da migliorare)
   - Alert se ci sono rate limit events frequenti
   - Raccomandazioni se le metriche indicano ottimizzazioni possibili

Argomenti: `$ARGUMENTS` — periodo opzionale (es. "oggi", "questo mese", "ultimi 14 giorni")
