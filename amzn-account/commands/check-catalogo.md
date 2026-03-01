---
description: Analisi catalogo Amazon da CLR (Catalogue Listing Report) con arricchimento web e action plan
allowed-tools: Task, Read, Write, Bash
argument-hint: [brand-name]
---

Esegui un'analisi completa del catalogo Amazon per il brand "$ARGUMENTS" utilizzando il Catalogue Listing Report (CLR) come base dati, arricchita con dati live dalle pagine prodotto.

## Setup

1. Leggi `STORES.md` dalla workspace folder per trovare il brand corrispondente a "$ARGUMENTS".
   - Se non viene fornito un argomento, elenca tutti i brand disponibili da STORES.md e chiedi all'utente quale analizzare.
   - Se il brand non è trovato in STORES.md, informa l'utente e fermati.

2. Estrai da STORES.md: nome brand, marketplace principale (es. `amazon.it`), categoria, prodotti chiave, note.

3. **Individua il file CLR** nella workspace folder. I formati accettati sono `.csv`, `.txt`, `.tsv`. Cerca file con nomi che contengono: "CLR", "catalogue", "catalog", "listing-report", oppure il nome del brand. Il CLR scaricato da Seller Central si chiama tipicamente `inventory-report` o `flat-file-open-listings`.

4. Se il file CLR **non è presente**:
   - Chiedi all'utente se preferisce:
     - **A) Caricare il file**: l'utente salva il CLR nella workspace folder e rilancia il comando
     - **B) Download automatico**: Claude naviga su Seller Central e scarica il report
   - Se l'utente sceglie B, vai al paragrafo "Download automatico da Seller Central" sotto.
   - Aspetta la scelta dell'utente prima di proseguire.

5. Crea la directory di lavoro temporanea: `/tmp/amzn-clr/`

6. Copia il file CLR trovato in `/tmp/amzn-clr/clr-input.csv` (o con l'estensione corretta).

7. Annota la data corrente in formato ISO (YYYY-MM-DD) per il naming dei file di output.

---

### Download automatico da Seller Central

Se l'utente sceglie il download automatico:

1. Naviga su `https://sellercentral.amazon.it/reportcentral/FLAT_FILE_OPEN_LISTINGS_DATA/1` (o il marketplace corretto).
2. Se non sei già autenticato, informa l'utente che dovrà completare il login e attendere.
3. Richiedi la generazione del report e attendi il download.
4. Salva il file scaricato nella workspace folder come `clr-[brand-lowercase]-[YYYY-MM-DD].csv`.
5. Copia il file in `/tmp/amzn-clr/clr-input.csv` e prosegui con la Fase 1.

---

## Fase 1 — CLR Enricher

Usa il tool Task per avviare un sottoagente con il ruolo **clr-enricher**.

Fornisci all'agente:
- Nome del brand
- Percorso file CLR input: `/tmp/amzn-clr/clr-input.csv`
- Percorso file output: `/tmp/amzn-clr/clr-enriched.md`
- Marketplace principale del brand (es. `amazon.it`)

Attendi il completamento della Fase 1 e verifica che `/tmp/amzn-clr/clr-enriched.md` esista e non sia vuoto prima di procedere.

---

## Fase 2 — CLR Report Builder

Usa il tool Task per avviare un sottoagente con il ruolo **clr-report-builder**.

Fornisci all'agente:
- Nome del brand
- Percorso file input (dati arricchiti): `/tmp/amzn-clr/clr-enriched.md`
- Percorso file output: `/tmp/amzn-clr/clr-report-raw.md`

Attendi il completamento della Fase 2 e verifica che `/tmp/amzn-clr/clr-report-raw.md` esista prima di procedere.

---

## Fase 3 — CLR Analyst

Usa il tool Task per avviare un sottoagente con il ruolo **clr-analyst**.

Fornisci all'agente:
- Nome del brand
- Informazioni brand (incolla l'entry completa di STORES.md per questo brand)
- Percorso file input: `/tmp/amzn-clr/clr-report-raw.md`
- Percorso file output: `/tmp/amzn-clr/clr-final-report.md`

Attendi il completamento della Fase 3 prima di procedere.

---

## Finalizzazione

1. Leggi `/tmp/amzn-clr/clr-final-report.md`.

2. Usa lo skill `docx` per creare un documento Word (.docx) dal contenuto del report. Salvalo nella workspace folder con il nome:
   `[brand-name-lowercase]-catalogo-[YYYY-MM-DD].docx`

3. Presenta il documento all'utente con un link `computer://`.

4. Mostra un breve riepilogo in chat:
   - **Stato generale catalogo**: 🟢 OK / 🟡 ATTENZIONE / 🔴 CRITICO
   - **ASIN analizzati**: N totale
   - **Problemi critici**: N (o "Nessuno")
   - **Top 3 anomalie**: elenca le tre anomalie più urgenti emerse (o "Catalogo in salute")
   - **Prossimo passo**: prima azione dell'action plan (Priorità ALTA)
