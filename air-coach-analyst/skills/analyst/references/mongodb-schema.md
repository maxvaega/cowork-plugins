# MongoDB Schema - AIR Coach

## Come scoprire nomi esatti di DB e Collection

I nomi esatti di database e collection sono configurati tramite variabili d'ambiente nel backend.
Prima di eseguire query, usa il MongoDB MCP per scoprirli:

```
# Via MCP
listDatabases()
listCollections(database: "nome_db")
```

## Database Principale (env: DATABASE_NAME)

### Collection Messaggi: `conversations.prod`

Ogni documento rappresenta un singolo scambio utente/AI.

```json
{
  "_id": "ObjectId",
  "userId": "google-oauth2|104612087445133776110",
  "human": "Testo del messaggio utente",
  "system": "Testo della risposta AI",
  "llm": "gemini-3-flash",
  "timestamp": "2025-02-25 11:45:36",
  "feedback_user": "positive" | "negative" | null
}
```

**ATTENZIONE — tipo `timestamp`:**
- È una **stringa** in formato `"YYYY-MM-DD HH:MM:SS"`, **NON** un ISODate MongoDB
- Non usare `new Date(...)` o `$dateToString` nelle query — darà 0 risultati
- Usare confronto tra stringhe: `{ $gte: "2026-02-19" }` funziona correttamente perché il formato è ordinabile alfabeticamente

**Note sui campi:**
- `userId` è camelCase (non `userid`)
- `human` = messaggio utente, `system` = risposta AI (entrambi presenti nel documento)
- `llm` = nome del modello usato per la risposta
- `feedback_user` è null di default, impostato dall'utente nell'app

### Query di Esempio

**Traffico giornaliero (ultimi 30 giorni):**
```javascript
// Calcola la data di 30 giorni fa come stringa "YYYY-MM-DD"
db["conversations.prod"].aggregate([
  { $match: { timestamp: { $gte: "2026-02-19" } } },  // sostituisci con data calcolata
  { $group: { _id: { $substr: ["$timestamp", 0, 10] }, count: { $sum: 1 } } },
  { $sort: { _id: 1 } }
])
```

**Utenti attivi (ultimi 7 giorni):**
```javascript
db["conversations.prod"].aggregate([
  { $match: { timestamp: { $gte: "2026-03-14" } } },  // sostituisci con data calcolata
  { $group: { _id: "$userId" } },
  { $count: "active_users" }
])
```

**Messaggi per utente (ranking):**
```javascript
db["conversations.prod"].aggregate([
  { $group: { _id: "$userId", message_count: { $sum: 1 }, last_active: { $max: "$timestamp" } } },
  { $sort: { message_count: -1 } },
  { $limit: 20 }
])
```

**Feedback negativi recenti:**
```javascript
db["conversations.prod"].find(
  { feedback_user: "negative" },
  { userId: 1, human: 1, system: 1, timestamp: 1 }
).sort({ timestamp: -1 }).limit(50)
```

**Tasso di feedback:**
```javascript
db["conversations.prod"].aggregate([
  { $group: {
    _id: "$feedback_user",
    count: { $sum: 1 }
  }}
])
```

---

## Database Token_metrics

### Collection Token Metrics (env: COLLECTION_NAME, stesso nome del DB principale)

Log di ogni request LLM con dati di utilizzo token.

```json
{
  "_id": "ObjectId",
  "user_id": "google-oauth2|104612087445133776110",
  "model": "models/gemini-3-flash-preview",
  "input_tokens": 1250,
  "output_tokens": 380,
  "total_tokens": 1630,
  "cached_tokens": 800,
  "request_duration_ms": 1243.5,
  "timestamp": "2026-01-19T14:26:04.123Z",
  "metadata": {
    "thread_id": "google-oauth2|104612087445133776110:v1",
    "message_id": "google-oauth2|104612087445133776110_2026-01-19T14:26:03.779"
  }
}
```

### Query Token Metrics

**Costo LLM totale (ultimi 7 giorni):**
```javascript
// Calcola con: input * 0.075/1M + output * 0.30/1M + cached * 0.01875/1M
// Nota: verifica il tipo di timestamp con un documento campione prima di filtrare
db.COLLECTION.aggregate([
  { $match: { timestamp: { $gte: "2026-03-14" } } },  // sostituisci con data calcolata
  { $group: {
    _id: null,
    total_input: { $sum: "$input_tokens" },
    total_output: { $sum: "$output_tokens" },
    total_cached: { $sum: "$cached_tokens" },
    total_requests: { $sum: 1 },
    avg_duration_ms: { $avg: "$request_duration_ms" }
  }}
])
```

**Efficienza cache (% token cached):**
```javascript
db.COLLECTION.aggregate([
  { $match: { timestamp: { $gte: "2026-03-14" } } },  // sostituisci con data calcolata
  { $group: {
    _id: null,
    total_input: { $sum: "$input_tokens" },
    total_cached: { $sum: "$cached_tokens" }
  }},
  { $project: {
    cache_hit_rate: { $divide: ["$total_cached", "$total_input"] }
  }}
])
```

**Latenza percentile:**
```javascript
db.COLLECTION.aggregate([
  { $match: { timestamp: { $gte: "2026-03-20" } } },  // sostituisci con data calcolata
  { $sort: { request_duration_ms: 1 } },
  { $group: { _id: null, durations: { $push: "$request_duration_ms" }, count: { $sum: 1 } }}
  // poi calcola p50, p95, p99 dall'array
])
```

### Collection rate_limit_events

```json
{
  "_id": "ObjectId",
  "user_id": "google-oauth2|...",
  "timestamp": "2026-01-19T...",
  "error_details": { ... }
}
```

**Rate limit events recenti:**
```javascript
db.rate_limit_events.find({
  timestamp: { $gte: "2026-03-20" }  // sostituisci con data calcolata; verifica tipo timestamp
}).sort({ timestamp: -1 })
```
