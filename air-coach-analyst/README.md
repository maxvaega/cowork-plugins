# AIR Coach Analyst Plugin

Plugin per l'analisi dell'applicazione **AIR Coach** — app mobile italiana per paracadutisti sportivi. per info: www.air-coach.com

L'analista combina dati operativi da MongoDB, profili utente da Auth0 e analytics frontend da Vexo per fornire una visione completa dello stato e dell'utilizzo dell'app.

## Componenti

### Comandi

| Comando | Descrizione |
|---------|-------------|
| `/traffico [periodo]` | Analisi traffico messaggi da MongoDB (DAU, utenti attivi, distribuzione oraria) |
| `/utenti [filtro]` | Analisi base utenti da Auth0 (qualifiche, esperienza, dropzone, crescita) |
| `/costi [periodo]` | Analisi costi e metriche LLM da Token_metrics (spend, cache efficiency, latenza) |
| `/feedback [periodo]` | Analisi feedback utenti thumbs up/down con analisi qualitativa dei negativi |
| `/vexo [sezione]` | Guida all'analisi di Vexo via browser (retention, funnel, crash reports) |

### Skill

- **analyst** — si attiva automaticamente quando parli di AIR Coach. Contiene la conoscenza completa dello schema dati, KPI chiave e come interpretare le metriche.

### MCP Servers

- **mongodb** — Accesso read-only al database MongoDB Atlas di AIR Coach
- **auth0** — Accesso ai profili utente sul tenant Auth0

## Setup

### Variabili d'Ambiente Richieste

Prima di usare il plugin, configura queste variabili d'ambiente:

| Variabile | Descrizione | Dove trovarla |
|-----------|-------------|---------------|
| `AIRCOACH_MONGODB_URI` | Connection string MongoDB Atlas | Dashboard MongoDB Atlas → Connect |
| `AIRCOACH_AUTH0_DOMAIN` | Dominio tenant Auth0 | Dashboard Auth0 → Settings |
| `AIRCOACH_AUTH0_CLIENT_ID` | Client ID per MCP Auth0 | Dashboard Auth0 → Applications → MCP App |

**Nota di sicurezza**: Non inserire mai la connection string MongoDB nella configurazione del plugin. Usa sempre variabili d'ambiente.

### Configurazione MongoDB MCP

Il MongoDB MCP opera in **modalità read-only** per sicurezza — nessuna modifica ai dati di produzione è possibile tramite questo plugin.

### Configurazione Auth0 MCP

Al primo utilizzo, l'Auth0 MCP avvierà un flusso di Device Authorization — apri il link che appare nel terminale per autenticarti. Le credenziali vengono salvate nel keychain di sistema.

## Utilizzo

### Esempi di domande

- "Com'è andato il traffico questa settimana?"
- "Quanti utenti attivi abbiamo?"
- "Quanto stiamo spendendo in token LLM questo mese?"
- "Ci sono molti feedback negativi ultimamente? Cosa chiedono?"
- "Che qualifiche hanno i nostri utenti?"
- "Apri Vexo e dimmi com'è la retention a 30 giorni"

### Come funziona l'analisi cross-source

Il `userid` in MongoDB corrisponde all'`user_id` in Auth0. Per analisi avanzate, l'analista può combinare:
- **Attività su MongoDB** (messaggi, feedback) con **profilo Auth0** (qualifiche, esperienza)
- **Dati quantitativi** (traffico, costi) con **analisi qualitativa** (contenuto feedback negativi)

## Struttura del Progetto AIR Coach

Per completezza, AIR Coach è composto da:

- **`serverless-AIR-coach`** — Backend FastAPI + LangGraph
- **`App_AIR-Coach`** — Frontend React Native/Expo
- **`Knowledge-AIR-Coach`** — Knowledge base di dominio (paracadutismo)
- **Website** — [www.air-coach.com](https://www.air-coach.com)
