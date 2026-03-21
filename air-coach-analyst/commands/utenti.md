---
description: Analisi base utenti AIR Coach da Auth0
allowed-tools: Read
---

Sei il data analyst di AIR Coach. L'utente vuole un'analisi della **base utenti** dell'applicazione.

Carica la skill `analyst` per conoscere lo schema Auth0, poi esegui le seguenti analisi usando l'Auth0 MCP (tool `auth0`):

1. **Recupera la lista completa degli utenti** usando `auth0_list_users` con paginazione (100 per pagina).
   Continua a paginare finché non hai tutti gli utenti.

2. **Calcola le seguenti distribuzioni** dai `user_metadata`:

   a. **Distribuzione qualifiche** — quanti allievi vs licenziati (e overlap)
   b. **Distribuzione esperienza** — fasce di lanci (0-10, 11-50, 51-200, 201+)
   c. **Top dropzone** — zone di lancio più popolari (preferred_dropzone)
   d. **Completamento onboarding** — % utenti con is_legal_accepted = true
   e. **Crescita registrazioni** — nuovi utenti per mese (da created_at)
   f. **Utenti attivi recentemente** — utenti con last_login negli ultimi 30 giorni

3. **Arricchisci con dati MongoDB** se appropriato: per ogni utente attivo su Auth0, verifica se ha messaggi nel DB (usa il `userid` come chiave di join).

4. **Presenta i risultati** come:
   - Totale utenti registrati
   - Distribuzione in formato tabellare o percentuale
   - Insight chiave (es. "il 60% degli utenti è nella fascia 11-50 lanci")
   - Utenti "dormienti" (registrati ma mai attivi su MongoDB)

Non esporre email o dati personali individuali — usa solo aggregati anonimi.

Argomenti: `$ARGUMENTS` — filtro opzionale (es. "solo allievi", "ultimi 3 mesi", "dropzone Roma")
