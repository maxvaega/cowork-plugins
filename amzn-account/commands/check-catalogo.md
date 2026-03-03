---
description: Analisi catalogo Amazon da CLR (Catalogue Listing Report) con arricchimento web e action plan
allowed-tools: Task, Read, Write, Bash, Glob
argument-hint: [brand-name]
---

Esegui l'analisi completa del catalogo Amazon in 3 fasi partendo dal Catalogue Listing Report (CLR) e dal file di contabilità inventario presenti nella cartella di lavoro.

## Setup iniziale

1. Leggi `STORES.md` dalla cartella di lavoro per trovare lo store corrispondente a "$ARGUMENTS".
   - Se non è stato fornito alcun argomento, elenca i brand disponibili in STORES.md e chiedi all'utente quale analizzare.
   - Se il brand non è in STORES.md, procedi lo stesso (i file forniti dall'utente definiranno il brand).

2. Cerca nella cartella di lavoro i file necessari:
   - **File CLR**: cerca file con estensione `.xlsm` o `.xlsx` (es. "Report catalogo.xlsm")
   - **File Inventario**: cerca file `.csv` contenente "Contab" o "inventario" nel nome (es. "Contaibilità inventario.csv")
   - Se trovi più file candidati per ciascun tipo, elencali all'utente e chiedi quale usare.
   - Se un file non è trovato, avvisa l'utente e spiega dove caricarlo.

3. Ricava il nome del brand:
   - Prima da "$ARGUMENTS" se fornito
   - Altrimenti da STORES.md se trovato
   - Altrimenti lo ricaverà l'Agente 1 dai dati del CLR (campo brand)

4. Crea la directory di lavoro temporanea: `/tmp/catalogo-check/`
5. Nota la data corrente in formato ISO (YYYY-MM-DD) per la denominazione del file.
6. Costruisci il nome del file di lavoro intermedio:
   `[YYYY-MM-DD]-[brand-slug]-check-catalogo.md`
   dove brand-slug è il nome brand in minuscolo con trattini (es. "mandorle-meli")

---

## Fase 1 — CLR Enricher (Haiku)

Usa il tool `Task` per lanciare un sub-agente con il ruolo **clr-enricher**.

Fornisci all'agente:
- Percorso del file CLR (percorso assoluto nella cartella di lavoro)
- Percorso del file Inventario (percorso assoluto nella cartella di lavoro)
- Nome del brand (se disponibile, altrimenti indicare "da rilevare dal file")
- Data corrente in formato YYYY-MM-DD
- Percorso di output: `/tmp/catalogo-check/[filename].md`

Attendi il completamento della Fase 1 e verifica che il file di output sia stato creato prima di procedere.

---

## Fase 2 — CLR Report Builder — Arricchimento Web (Haiku)

Usa il tool `Task` per lanciare un sub-agente con il ruolo **clr-report-builder**.

Fornisci all'agente:
- Percorso del file di lavoro creato dalla Fase 1
- Marketplace principale (da STORES.md o default: IT)
- Eventuali marketplace aggiuntivi attivi per il brand

Attendi il completamento della Fase 2 prima di procedere.

---

## Fase 3 — CLR Analyst — Report Finale in Word (modello default)

Usa il tool `Task` per lanciare un sub-agente con il ruolo **clr-analyst**.

Fornisci all'agente:
- Nome del brand
- Percorso del file di lavoro arricchito dalla Fase 2
- Percorso di output del report `.docx`: `/tmp/catalogo-check/[YYYY-MM-DD]-[brand-slug]-check-catalogo.docx`
- Data corrente in formato YYYY-MM-DD

Attendi il completamento della Fase 3 e verifica che il file `.docx` sia stato creato.

---

## Finalizzazione

1. Copia il file `.docx` da `/tmp/catalogo-check/` alla cartella di lavoro dell'utente con il nome:
   `[YYYY-MM-DD]-[brand-slug]-check-catalogo.docx`
2. Presenta il documento all'utente con un link `computer://`.
3. Mostra in chat un riassunto breve:
   - Stato generale: 🟢 OK / 🟡 ATTENZIONE / 🔴 CRITICO
   - Numero ASIN analizzati
   - Top 3 anomalie rilevate (o "Nessuna anomalia rilevata" se tutto OK)
   - Numero ASIN con dati web recuperati
