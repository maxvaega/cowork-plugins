---
name: check-catalogo
description: >
  Analisi catalogo Amazon da CLR (Catalogue Listing Report) con arricchimento web e action plan.
  Usa quando: l'utente vuole analizzare il catalogo Amazon, controllare gli ASIN dal CLR,
  verificare disponibilità prodotti, trovare listing inattivi, controllare stock FBA,
  fare un'analisi del catalogo da file, check catalogo Amazon.
  Trigger: "check catalogo", "analizza il CLR", "controlla il catalogo", "analisi listing",
  "verifica ASIN dal report", "analisi disponibilità catalogo".
---

# Check Catalogo — Flusso a 3 Agenti

Questo skill orchestra un'analisi automatizzata del catalogo Amazon in 3 fasi:

## Flusso

```
Utente carica: Report catalogo.xlsm + Contabilità inventario.csv
        ↓
/check-catalogo [brand-name]
        ↓
[Fase 1] clr-enricher (Haiku)
  - Legge CLR (.xlsm foglio "Modello")
  - Legge Inventario (.csv)
  - Crea file di lavoro con tabella per ASIN
        ↓
[Fase 2] clr-report-builder (Haiku)
  - Visita www.amazon.it/dp/[ASIN] per ogni ASIN
  - Raccoglie: pagina OK, acquistabilità, Buy Box, prezzo, rating, recensioni
  - Aggiorna le colonne [Web] nel file di lavoro
        ↓
[Fase 3] clr-analyst (default)
  - Legge il file di lavoro completo
  - Produce il report finale in italiano
  - Highlights, criticità, punti positivi, action plan
        ↓
Report salvato come [YYYY-MM-DD]-[brand]-check-catalogo.md
```

## Dati disponibili dai file

### Dal CLR (Report catalogo.xlsm — foglio "Modello")
- ✅ ASIN, SKU, status listing (Attivo/Inattivo)
- ✅ Titolo prodotto, brand, livello parentela
- ✅ Prezzi (listino, vendita, promozioni con date)
- ✅ Canale fulfillment (FBA/FBM), stock FBM, data rientro stock
- ✅ URL immagini (conta immagini caricate)
- ⚠️ Non include: stock FBA reale, Buy Box, rating, recensioni

### Dal file Inventario (Contabilità inventario.csv)
- ✅ Stock FBA per ASIN per magazzino (aggregato)
- ✅ Disposition: SELLABLE, DISTRIBUTOR_DAMAGED, WAREHOUSE_DAMAGED
- ✅ Vendite del periodo (Customer Shipments)
- ✅ Magazzini attivi per ASIN
- ⚠️ Non include: Buy Box, rating, informazioni di listing

### Dal Web (pagine pubbliche Amazon)
- ✅ Pagina raggiungibile (ASIN non soppresso)
- ✅ Acquistabilità (pulsante "Aggiungi al carrello")
- ✅ Buy Box e venditore attuale
- ✅ Prezzo visualizzato al cliente
- ✅ Rating medio e numero recensioni
- ❌ Testo recensioni, campagne ads, Buy Box Report (richiedono Seller Central)

## File richiesti dall'utente

Prima di lanciare `/check-catalogo`, l'utente deve aver caricato nella cartella:

1. **Catalogue Listing Report** (`.xlsm`): scaricabile da Seller Central → Inventario → Gestisci l'inventario → Scarica il modello di listino
2. **Contabilità Inventario** (`.csv`): scaricabile da Seller Central → Rapporti → Gestione dell'evasione degli ordini → Contabilità inventario Amazon

## Note operative

- I prodotti **Parent** (senza pagina prodotto diretta) vengono inclusi nella tabella ma non verificati via web.
- Il flusso verifica il marketplace **IT** di default; se il brand è attivo su altri marketplace (DE, FR, ES, UK), vengono inclusi se configurati in STORES.md.
- I dati web sono uno **snapshot** al momento dell'esecuzione.
