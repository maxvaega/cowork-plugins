---
description: Analisi traffico messaggi AIR Coach da MongoDB
allowed-tools: Read, mcp__mongodb_mongodb__aggregate, mcp__mongodb_mongodb__find, mcp__auth0_auth0__list-users, mcp__auth0_auth0__get-user
---

Sei il data analyst di AIR Coach. L'utente vuole un'analisi del **traffico messaggi** dell'applicazione.

Carica la skill `analyst` per conoscere lo schema MongoDB completo, poi esegui le analisi usando il MongoDB MCP.

**Collection di riferimento:** `conversations.prod` nel database principale. Usa `listDatabases` + `listCollections` solo se non riesci a connetterti direttamente.

**IMPORTANTE — timestamp:** Il campo `timestamp` è una **stringa** `"YYYY-MM-DD HH:MM:SS"`. Calcola le date di inizio come stringhe nel formato `"YYYY-MM-DD"` (es. oggi = "2026-03-21", 30 giorni fa = "2026-02-19"). Non usare `new Date()` — darà 0 risultati.

**PERIODO DI ANALISI:** Default = ultimi 30 giorni. Se `$ARGUMENTS` specifica un periodo diverso, adatta le date. Calcola sempre anche il **periodo precedente equivalente** (stessa durata immediatamente prima) per abilitare il confronto.

1. **Recupera le seguenti metriche** dal database principale:

   a. **Traffico giornaliero** — messaggi/giorno negli ultimi 30 giorni + DAU medio (media giornaliera del periodo)
   b. **Utenti attivi** — utenti unici con ≥1 messaggio negli ultimi 7 e 30 giorni
   c. **Distribuzione oraria** — top 3 ore di picco (estrai ora con `$substr: ["$timestamp", 11, 2]`)
   d. **Utenti più attivi** — top 10 per numero totale messaggi; per ogni userId usa `auth0_get_user` per ottenere nome e cognome
   e. **Messaggi medi per utente** — media e mediana

2. **Confronto con periodo precedente** — per traffico giornaliero e utenti attivi, calcola gli stessi valori nel periodo precedente equivalente e mostra la variazione (↑/↓/→).

3. **Presenta i risultati** nel seguente formato strutturato:

   - **Riepilogo KPI** — tabella con: metrica | valore attuale | valore periodo precedente | trend (↑/↓/→)
   - **Traffico giornaliero** — tabella data/messaggi per i 14 giorni più recenti
   - **Distribuzione oraria** — top 3 ore di picco con conteggio messaggi
   - **Utenti più attivi** — tabella top 10 con nome, messaggi nel periodo, variazione vs periodo precedente
   - **Anomalie e note** — picchi insoliti, cali improvvisi, pattern rilevanti

REGOLE SEMPRE VALIDE:
- se Auth0 mcp non funziona o non è attivo, leggi il file `utenti.json` come fallback per ottenere i dati utente
- se devi creare dei file, salvali nella cartella `tmp/`

Argomenti accettati: `$ARGUMENTS` — periodo di analisi opzionale (es. "7 giorni", "questo mese", "dal 1 gennaio")
