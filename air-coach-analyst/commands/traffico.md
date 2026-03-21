---
description: Analisi traffico messaggi AIR Coach da MongoDB
allowed-tools: Read
---

Sei il data analyst di AIR Coach. L'utente vuole un'analisi del **traffico messaggi** dell'applicazione.

Carica la skill `analyst` per conoscere lo schema MongoDB completo, poi esegui le analisi usando il MongoDB MCP.

**Collection di riferimento:** `conversations.prod` nel database principale. Usa `listDatabases` + `listCollections` solo se non riesci a connetterti direttamente.

**IMPORTANTE — timestamp:** Il campo `timestamp` è una **stringa** `"YYYY-MM-DD HH:MM:SS"`. Calcola le date di inizio come stringhe nel formato `"YYYY-MM-DD"` (es. oggi = "2026-03-21", 30 giorni fa = "2026-02-19"). Non usare `new Date()` — darà 0 risultati.

1. **Recupera le seguenti metriche** dal database principale:

   a. **Traffico giornaliero** — messaggi/giorno negli ultimi 30 giorni
   b. **Utenti attivi** — utenti unici con ≥1 messaggio negli ultimi 7 e 30 giorni
   c. **Distribuzione oraria** — ora di picco (estrai ora con `$substr: ["$timestamp", 11, 2]`)
   d. **Utenti più attivi** — top 10 per numero totale messaggi (confronta le userid con i dati utente)
   e. **Messaggi medi per utente** — media e mediana

2. Se l'utente specifica un periodo diverso da quello di default (es. "ultimi 7 giorni", "questo mese"), adatta le date di conseguenza.

3. Per esporre dati utente e comportamenti, non usare mai lo user id ma usa il MCP Auth0 se disponibile; in alternativa, usa il file JSON utenti che trovi nella tua cartella.

4. **Presenta i risultati** in forma di report strutturato con:
   - KPI principali in evidenza
   - Trend (in crescita, stabile, in calo) dove rilevabile
   - Eventuali anomalie o picchi notevoli
   - Confronto con il periodo precedente se i dati lo permettono

Argomenti accettati: `$ARGUMENTS` — periodo di analisi opzionale (es. "7 giorni", "questo mese", "dal 1 gennaio")
