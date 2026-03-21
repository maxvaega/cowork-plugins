---
name: analyst
description: >
  Usa questa skill quando l'utente vuole analizzare i dati di AIR Coach:
  "analizza il traffico", "quanti utenti attivi", "quanto stiamo spendendo in token",
  "mostrami i feedback negativi", "chi sono i nostri utenti", "analisi dell'app",
  "metriche AIR Coach", "stato dell'applicazione", "usage report", "report settimanale".
  Contiene lo schema completo di MongoDB e Auth0 dell'app AIR Coach, le metriche chiave
  da monitorare e le istruzioni per navigare Vexo.
version: 0.1.0
---

# AIR Coach Analyst

AIR Coach è un'app mobile italiana (iOS/Android, beta) per paracadutisti sportivi.
Funziona come coach personale AI: risponde a domande su tecniche di volo, sicurezza,
attrezzatura e preparazione agli esami teorici di licenza.

**Stack tecnico:**
- Backend: FastAPI + LangGraph + Gemini 3 Flash (Vertex AI, region: europe-west8)
- Frontend: React Native / Expo
- Database operativo: MongoDB Atlas
- Autenticazione utenti: Auth0
- Analytics frontend: Vexo (solo browser, nessun MCP)
- Deploy: Vercel (backend serverless) + Cloudflare

---

## Fonti Dati Disponibili

### 1. MongoDB (MCP: `mongodb`)

Il MongoDB MCP opera in **modalità read-only** — nessuna modifica ai dati.

Ci sono due database principali:

**Database principale** (nome configurato via env `DATABASE_NAME`, tipicamente `air_coach` o simile):
- Collection principale: **`conversations.prod`** (usa questo nome esatto, non una variabile env)
  - Ogni documento rappresenta uno scambio utente/AI
  - Campi principali:
    - `userId` (camelCase, **non** `userid`) — Auth0 user ID
    - `human` — testo del messaggio utente
    - `system` — testo della risposta AI
    - `llm` — modello usato (es. `"gemini-3-flash"`)
    - `timestamp` — **stringa** formato `"YYYY-MM-DD HH:MM:SS"` (**NON** ISODate; usare confronto tra stringhe nelle query)
    - `feedback_user` — `"positive"` | `"negative"` | `null`

**Database `Token_metrics`**:
- Collection di metriche (env `COLLECTION_NAME`): log token LLM per ogni request
  - Campi: `user_id`, `model`, `input_tokens`, `output_tokens`, `total_tokens`, `cached_tokens`, `request_duration_ms`, `timestamp`, `metadata` (thread_id, message_id)
- Collection `rate_limit_events`: eventi di rate limiting (HTTP 429 da Gemini)
  - Campi: `user_id`, `timestamp`, `error_details`

**Come interrogare MongoDB via MCP:**
Usa il tool `find` o `aggregate` del MongoDB MCP. Per scoprire i nomi esatti di database e collection usa `listDatabases` e `listCollections`. Il MCP accetta query in linguaggio naturale oppure sintassi MongoDB.

Esempi di query utili:
- Messaggi per utente nell'ultima settimana: filtra `timestamp >= "2026-03-14"` (stringa), raggruppa per `userId`
- Token spesi oggi: filtra `Token_metrics` per `timestamp >= "2026-03-21"`, somma `total_tokens`
- Feedback negativi recenti: filtra `feedback_user == "negative"`, ultimi N documenti
- Utenti più attivi: raggruppa per `userId`, conta messaggi, ordina DESC

**IMPORTANTE — `timestamp` è una stringa:** usa confronto tra stringhe (es. `{ $gte: "2026-03-14" }`), **non** `new Date(...)` o operatori temporali nativi MongoDB.

---

### 2. Auth0 (MCP: `auth0`)

Auth0 gestisce l'autenticazione e i profili degli utenti. Usa il tenant `dev-hldygfksiqb6rf3d` (ambiente di sviluppo/produzione).

**User Metadata** (salvati nel campo `user_metadata` di ogni utente Auth0):
```json
{
  "full_name": "Diego",
  "sex": "Maschio",
  "date_of_birth": "1993-05-12",
  "qualifications": ["allievo", "licenziato"],
  "jumps": "11 - 50",
  "preferred_dropzone": "Ravenna",
  "others_dropzone": ["Skydive Pullout - Ravenna"],
  "is_legal_accepted": true
}
```

**Valori possibili per `qualifications`:** `"allievo"` (studente), `"licenziato"` (patentato)
**Valori possibili per `jumps`:** range come `"0 - 10"`, `"11 - 50"`, `"51 - 200"`, `"201+"`

**Come interrogare Auth0 via MCP:**
- `auth0_list_users`: lista utenti con filtri
- `auth0_get_user`: dettaglio singolo utente per ID
- Per analisi aggregate degli user_metadata, recupera la lista utenti e aggrega lato Claude

Metriche utenti interessanti:
- Distribuzione qualifiche (allievi vs licenziati)
- Distribuzione zone di lancio (dropzone popularity)
- Distribuzione fasce di esperienza (numero lanci)
- Tasso di completamento onboarding (`is_legal_accepted`)

---

### 3. Vexo (solo browser)

Vexo è il servizio di analytics per il frontend mobile. Non ha un MCP, quindi va navigato manualmente tramite il browser.

Per accedere: apri il browser e vai su [vexo.co](https://vexo.co) con le credenziali di Massimo.

Metriche disponibili su Vexo:
- Sessioni attive e DAU/WAU/MAU
- Schermate più visitate
- Funnel di onboarding (registrazione → primo messaggio)
- Retention degli utenti
- Crash reports e performance

---

## KPI Principali da Monitorare

| Metrica | Fonte | Frequenza consigliata |
|--------|-------|----------------------|
| Messaggi/giorno per utente | MongoDB | Giornaliera |
| Utenti attivi (≥1 msg) ultimi 7 giorni | MongoDB | Settimanale |
| Costo token LLM (USD) | Token_metrics | Giornaliera |
| % cache hits (cached_tokens / input_tokens) | Token_metrics | Settimanale |
| Latenza media request (ms) | Token_metrics | Giornaliera |
| Feedback negativi (%) | MongoDB | Settimanale |
| Nuovi utenti registrati | Auth0 | Settimanale |
| Distribuzione qualifiche utenti | Auth0 | Mensile |
| Rate limit events | Token_metrics | Giornaliera |

---

## Costi LLM Stimati (Gemini 3 Flash)

Per calcolare i costi da MongoDB `Token_metrics`, usa questi prezzi indicativi:
- Input tokens: ~$0.075 / 1M token
- Output tokens: ~$0.30 / 1M token
- Cached tokens: ~$0.01875 / 1M token (75% di sconto su input)

Consulta [Google AI pricing](https://ai.google.dev/pricing) per i prezzi aggiornati.

---

## Note Operative

- Il `userId` (camelCase) in MongoDB corrisponde all'ID utente Auth0 (formato: `google-oauth2|...` o `auth0|...`)
- Il `HISTORY_LIMIT` (default: 10) limita il contesto storico inviato all'LLM — da tenere in mente nell'analisi dei pattern conversazionali
- se non riesci ad accedere ai dati utente su auth0, usa il file locale `utenti.json` come fallback

Vedi `references/mongodb-schema.md` per schema dettagliato e query di esempio.
Vedi `references/auth0-schema.md` per schema completo utenti Auth0.
