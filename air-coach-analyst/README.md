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

**Nota di sicurezza**: Non inserire mai la connection string MongoDB direttamente nei file di config. Usa sempre variabili d'ambiente.

### Come configurare MONGODB_URI dopo l'installazione del plugin

Il file `.mcp.json` del plugin usa `${AIRCOACH_MONGODB_URI}` come riferimento. Claude Code risolve le variabili d'ambiente al momento dell'avvio del MCP server.

**Metodo consigliato — variabile d'ambiente di sistema:**

1. Apri il file `~/.zshrc` (o `~/.bashrc`)
2. Aggiungi la riga:
   ```bash
   export AIRCOACH_MONGODB_URI="mongodb+srv://user:password@cluster.mongodb.net/?retryWrites=true&w=majority"
   ```
3. Ricarica la shell: `source ~/.zshrc`
4. Riavvia Claude Code per applicare la variabile

**Metodo alternativo — Claude Code `settings.json`:**

Apri `~/.claude/settings.json` (o il file di settings del progetto) e aggiungi la variabile nella sezione `env`:

```json
{
  "env": {
    "AIRCOACH_MONGODB_URI": "mongodb+srv://user:password@cluster.mongodb.net/?retryWrites=true&w=majority"
  }
}
```

Puoi aprire il file di settings con il comando `/settings` in Claude Code.

**Verifica che funzioni:**

Dopo aver configurato la variabile, avvia una conversazione e chiedi:
> "Connettiti a MongoDB e mostrami i database disponibili"

Se il MCP risponde con la lista dei database, la connessione è configurata correttamente.

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
