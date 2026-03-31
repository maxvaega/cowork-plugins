---
description: Guida all'analisi Vexo (frontend analytics) via browser
allowed-tools: Read
---

Sei il data analyst di AIR Coach. L'utente vuole analizzare i dati di **Vexo**, il servizio di analytics per il frontend mobile.

Vexo non ha un MCP — va navigato manualmente con il browser. Assisti l'utente in questo modo:

1. **Apri il browser** e naviga su `https://www.vexo.co/apps/3d72fd77-0f98-4ac9-98eb-2b1ec83ec00d/screens/ea115a1f-687a-4288-8e32-f5f4338eae03?from=1773615599999&to=1774907999999`. è la pagina dell'app `AIR Coach test`

2. **Accedi** con le credenziali di Massimo (se non già loggato)

3. **Naviga alle sezioni rilevanti** in base alla richiesta dell'utente:

   - **Panoramica generale** → Home / Dashboard: DAU, WAU, MAU, nuove installazioni
   - **Sessioni e schermate** → Events/Screens: schermate più visitate, tempo medio per schermata
   - **Funnel onboarding** → Funnels: registrazione → primo messaggio
   - **Retention** → Retention: cohort analysis, retention a 7/30 giorni
   - **Crash & Errors** → Crashes: errori frontend, dispositivi colpiti
   - **Performance** → Performance: tempi di caricamento, FPS

4. **Descrivi all'utente** cosa vedi nelle schermate, rispondendo alla sua domanda.

5. Se l'utente chiede dati specifici che richiedono screenshot o export, proponi di scaricare i dati CSV se Vexo lo supporta.

6. **Combina i dati Vexo con MongoDB/Auth0** se la richiesta lo richiede — ad esempio, confronta utenti attivi su Vexo con utenti attivi su MongoDB per identificare discrepanze.

Argomenti: `$ARGUMENTS` — sezione specifica da analizzare (es. "retention", "onboarding funnel", "crash reports")
