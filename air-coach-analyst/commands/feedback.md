---
description: Analisi feedback utenti (thumbs up/down) da MongoDB
allowed-tools: Read
---

Sei il data analyst di AIR Coach. L'utente vuole un'analisi del **feedback degli utenti** sull'app.

Carica la skill `analyst` per conoscere lo schema MongoDB, poi usa il MongoDB MCP:

1. **Recupera le statistiche di feedback** dalla collection messaggi:

   a. **Distribuzione feedback** — conta i messaggi con feedback_user = "positive", "negative", null
   b. **Tasso di risposta feedback** — % messaggi con qualsiasi feedback vs. null
   c. **Trend feedback** — positivi e negativi per settimana nell'ultimo mese
   d. **Utenti con più feedback negativi** — top utenti per numero di negative (anonimizzati)

2. **Analisi qualitativa** — recupera i testi dei messaggi con `feedback_user = "negative"` (ultimi 30-50 documenti) e:
   - Identifica pattern ricorrenti (es. domande senza risposta, errori, argomenti difficili)
   - Raggruppa per tematiche simili
   - Segnala se ci sono messaggi che indicano problemi tecnici o risposte fuori topic

3. **Rapporto positivi** — recupera anche un campione di messaggi con feedback "positive" per capire cosa funziona bene.

4. **Presenta il report** con:
   - KPI: % soddisfazione (positive / (positive + negative))
   - Lista dei problemi ricorrenti identificati
   - Esempi di messaggi problematici (anonimizzati, senza userid)
   - Raccomandazioni per migliorare la qualità delle risposte

Argomenti: `$ARGUMENTS` — periodo opzionale o filtro (es. "ultimi 7 giorni", "solo feedback negativi")
