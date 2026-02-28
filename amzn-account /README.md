# amzn-account — Amazon Brand Management Plugin

Plugin per la gestione di account Amazon: monitora la salute dei brand, verifica gli ASIN e produce report professionali da inviare ai seller.

---

## Overview

Questo plugin trasforma Cowork in un assistente Amazon che opera come farebbe un account manager: entra sullo store, controlla ogni prodotto e prepara un report strutturato pronto per il cliente.

Il flusso principale è il **Brand Daily Check**, un'analisi completa in 3 fasi:
1. **Store Explorer** — raccoglie tutti gli ASIN dallo store del brand su Amazon
2. **ASIN Auditor** — visita ogni pagina prodotto e verifica disponibilità, prezzo, Buy Box, recensioni e contenuto
3. **Report Compiler** — produce un documento Word professionale in italiano con stato generale, problemi prioritizzati e lista azioni

---

## Setup

### 1. Configura i tuoi brand

Prima di eseguire qualsiasi check, aggiungi i tuoi brand al file `STORES.md` nella cartella di lavoro:

```
/add-store
```

In alternativa, modifica `STORES.md` direttamente (vedi il template incluso nel plugin).

### 2. Requisiti

- **Claude in Chrome**: deve essere attivo e connesso a un browser Chrome. Il plugin usa il browser per navigare su Amazon.
- **Cartella di lavoro Cowork selezionata**: i file `STORES.md` e i report vengono salvati nella cartella selezionata.

---

## Commands

| Comando | Descrizione |
|---------|-------------|
| `/daily-check [brand]` | Esegue il check completo per un brand (3 fasi + report Word) |
| `/check-asin [ASIN] [paese]` | Controlla un singolo ASIN in dettaglio |
| `/add-store [brand]` | Aggiunge un nuovo brand/store a STORES.md in modo guidato |
| `/list-stores` | Mostra tutti i brand configurati |

### Esempi

```
/daily-check Kiko Milano
/daily-check BrandXYZ
/check-asin B08XYZ1234 IT
/check-asin B08XYZ1234 DE
/add-store
/list-stores
```

---

## Skill

### brand-daily-check

Skill attivata automaticamente durante le operazioni di check. Contiene:
- Criteri di classificazione OK / ATTENZIONE / CRITICO per ogni metrica
- Spiegazione dei concetti Amazon (Buy Box, ASIN suppression, A+ Content, ecc.)
- Struttura e tono del report finale
- Lista completa dei marketplace supportati

---

## Agents

I tre agenti sono orchestrati automaticamente dal comando `/daily-check`:

| Agente | Fase | Colore | Ruolo |
|--------|------|--------|-------|
| store-explorer | 1 | 🔵 Blu | Visita lo store e raccoglie gli ASIN |
| asin-auditor | 2 | 🟡 Giallo | Controlla ogni pagina prodotto |
| report-compiler | 3 | 🟢 Verde | Produce il report finale in italiano |

---

## STORES.md — Formato

Il file `STORES.md` deve trovarsi nella cartella di lavoro selezionata in Cowork. Ogni brand ha questo formato:

```markdown
## [Nome Brand]

- **Store Name**: [nome visualizzato]
- **Countries**: IT, DE, FR
- **Category**: [categoria]
- **Store URL (IT)**: https://www.amazon.it/stores/...
- **Store URL (DE)**: https://www.amazon.de/stores/...
- **Store URL (FR)**: —
- **Store URL (ES)**: —
- **Store URL (UK)**: —
- **Brand Identity**: [descrizione del brand]
- **Key Products**: [prodotti principali]
- **Notes**: [note operative]
```

---

## Output

Il `/daily-check` produce un file Word (.docx) salvato nella cartella di lavoro:

```
[brand-name]-daily-check-[YYYY-MM-DD].docx
```

Il report include:
- Stato generale (OK / ATTENZIONE / CRITICO)
- Tabella riepilogativa di tutti gli ASIN
- Problemi prioritizzati (alta, media, monitoraggio)
- Lista azioni concrete per il seller
- Note sulle recensioni
- Dettaglio per ogni ASIN con problemi

---

## Marketplace Supportati

IT, DE, FR, ES, UK, NL, SE, PL, BE

---

*Plugin versione 0.1.0 — Amazon Account Management*
