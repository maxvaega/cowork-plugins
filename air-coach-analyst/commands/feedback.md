---
description: Analisi feedback utenti (thumbs up/down) da MongoDB
allowed-tools: Read
---

Sei il data analyst di AIR Coach. L'utente vuole un'analisi del **feedback degli utenti** sull'app.

Carica la skill `analyst` per conoscere lo schema MongoDB, poi usa il MongoDB MCP.

**Collection di riferimento:** `conversations.prod` nel database principale.

**IMPORTANTE — timestamp:** Il campo `timestamp` è una **stringa** `"YYYY-MM-DD HH:MM:SS"`. Per filtrare per periodo usa confronto tra stringhe (es. `{ $gte: "2026-02-21" }`). Non usare `new Date()`.

1. **Recupera le statistiche di feedback** dalla collection `conversations.prod`:

   a. **Distribuzione feedback** — conta i documenti con feedback_user = "positive", "negative", null
   b. **Tasso di risposta feedback** — % documenti con qualsiasi feedback vs. null
   c. **Trend feedback** — positivi e negativi per settimana nell'ultimo mese
   d. **Utenti con più feedback negativi** — top utenti per numero di negative (anonimizzati)

2. **Analisi qualitativa** — recupera il campo `human` (messaggio utente) dei documenti con `feedback_user = "negative"` (ultimi 30-50) e:
   - Identifica pattern ricorrenti (es. domande senza risposta, errori, argomenti difficili)
   - Raggruppa per tematiche simili
   - Segnala se ci sono messaggi che indicano problemi tecnici o risposte fuori topic

3. **Rapporto positivi** — recupera anche un campione di messaggi con feedback "positive" per capire cosa funziona bene.

4. **Presenta il report** con:
   - KPI: % soddisfazione (positive / (positive + negative))
   - Lista dei problemi ricorrenti identificati
   - Esempi di messaggi problematici (anonimizzati, senza userId)
   - Raccomandazioni per migliorare la qualità delle risposte

Argomenti: `$ARGUMENTS` — periodo opzionale o filtro (es. "ultimi 7 giorni", "solo feedback negativi")
