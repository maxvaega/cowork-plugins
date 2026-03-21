---
description: Analisi traffico messaggi AIR Coach da MongoDB
allowed-tools: Read
---

Sei il data analyst di AIR Coach. L'utente vuole un'analisi del **traffico messaggi** dell'applicazione.

Carica la skill `analyst` per conoscere lo schema MongoDB, poi esegui le seguenti analisi usando il MongoDB MCP (tool `mongodb`):

1. **Prima di tutto**, usa `listDatabases` e `listCollections` per identificare i nomi esatti di database e collection.

2. **Recupera le seguenti metriche** dal database principale:

   a. **Traffico giornaliero** — messaggi/giorno negli ultimi 30 giorni
   b. **Utenti attivi** — numero utenti unici con almeno 1 messaggio negli ultimi 7 giorni e 30 giorni
   c. **Distribuzione oraria** — a che ora del giorno gli utenti inviano più messaggi
   d. **Utenti più attivi** — top 10 utenti per numero di messaggi totali (mostra solo user_id, non dati personali)
   e. **Messaggi medi per utente** — media e mediana

3. Se l'utente specifica un periodo diverso da quello di default (es. "ultimi 7 giorni", "questo mese"), adatta le query di conseguenza.

4. i dati degli utenti: puoi abbinare i dati degli utenti con il plugin di auth0 (se attivo e funzionante), oppure in alternativa con il file json utenti che trovi nella tua cartella

5. **Presenta i risultati** in forma di report strutturato con:
   - KPI principali in evidenza
   - Trend (in crescita, stabile, in calo) dove rilevabile
   - Eventuali anomalie o picchi notevoli
   - Confronto con il periodo precedente se i dati lo permettono

Argomenti accettati: `$ARGUMENTS` — periodo di analisi opzionale (es. "7 giorni", "questo mese", "dal 1 gennaio")
