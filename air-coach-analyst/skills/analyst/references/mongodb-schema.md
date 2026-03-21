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

### Collection Messaggi (env: COLLECTION_NAME)

Ogni documento rappresenta un singolo messaggio scambiato tra utente e AI.

```json
{
  "_id": "ObjectId",
  "userid": "google-oauth2|104612087445133776110",
  "message": "Testo del messaggio utente",
  "timestamp": "2026-01-19T14:26:03.779Z",
  "message_id": "google-oauth2|104612087445133776110_2026-01-19T14:26:03.779",
  "feedback_user": "positive" | "negative" | null
}
```

**Indice**: `timestamp` (decrescente) — usare sempre nelle query per performance.

**Note:**
- `message_id` è `{userid}_{timestamp_ISO}` — identificatore univoco cross-collection
- `feedback_user` è null di default, impostato dall'utente nell'app dopo aver ricevuto la risposta
- Non c'è il testo della risposta AI nel documento (solo il messaggio utente)

### Query di Esempio

**Traffico giornaliero (ultimi 30 giorni):**
```javascript
db.COLLECTION.aggregate([
  { $match: { timestamp: { $gte: new Date(Date.now() - 30*24*60*60*1000) } } },
  { $group: { _id: { $dateToString: { format: "%Y-%m-%d", date: "$timestamp" } }, count: { $sum: 1 } } },
  { $sort: { _id: 1 } }
])
```

**Utenti attivi (ultimi 7 giorni):**
```javascript
db.COLLECTION.aggregate([
  { $match: { timestamp: { $gte: new Date(Date.now() - 7*24*60*60*1000) } } },
  { $group: { _id: "$userid" } },
  { $count: "active_users" }
])
```

**Messaggi per utente (ranking):**
```javascript
db.COLLECTION.aggregate([
  { $group: { _id: "$userid", message_count: { $sum: 1 }, last_active: { $max: "$timestamp" } } },
  { $sort: { message_count: -1 } },
  { $limit: 20 }
])
```

**Feedback negativi recenti:**
```javascript
db.COLLECTION.find(
  { feedback_user: "negative" },
  { userid: 1, message: 1, timestamp: 1 }
).sort({ timestamp: -1 }).limit(50)
```

**Tasso di feedback:**
```javascript
db.COLLECTION.aggregate([
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
db.COLLECTION.aggregate([
  { $match: { timestamp: { $gte: new Date(Date.now() - 7*24*60*60*1000) } } },
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
  { $match: { timestamp: { $gte: new Date(Date.now() - 7*24*60*60*1000) } } },
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
  { $match: { timestamp: { $gte: new Date(Date.now() - 24*60*60*1000) } } },
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
  timestamp: { $gte: new Date(Date.now() - 24*60*60*1000) }
}).sort({ timestamp: -1 })
```
