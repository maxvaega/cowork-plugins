# Auth0 Schema - AIR Coach

## Configurazione Tenant

- **Dominio**: `dev-hldygfksiqb6rf3d.us.auth0.com` (tenant sviluppo/produzione)
- **API Audience**: `https://dev-hldygfksiqb6rf3d.us.auth0.com/api/v2/`
- **Algoritmo JWT**: RS256

## Struttura Utente Auth0

```json
{
  "user_id": "google-oauth2|104612087445133776110",
  "email": "utente@email.com",
  "name": "Nome Completo",
  "picture": "https://...",
  "created_at": "2025-10-15T10:30:00.000Z",
  "last_login": "2026-01-19T14:25:00.000Z",
  "logins_count": 42,
  "user_metadata": {
    "full_name": "Diego",
    "sex": "Maschio",
    "date_of_birth": "1993-05-12",
    "qualifications": ["allievo", "licenziato"],
    "jumps": "11 - 50",
    "preferred_dropzone": "Ravenna",
    "others_dropzone": ["Skydive Pullout - Ravenna"],
    "is_legal_accepted": true
  }
}
```

## Valori dei Campi user_metadata

### qualifications (array, obbligatorio)
| Valore | Significato |
|--------|-------------|
| `"allievo"` | Studente in formazione (corso AFF o post-AFF) |
| `"licenziato"` | Paracadutista con licenza |

Un utente può avere entrambe le qualifiche (es. durante la transizione).

### jumps (stringa, fasce)
| Valore | Range lanci |
|--------|-------------|
| `"0 - 10"` | Principiante assoluto |
| `"11 - 50"` | Novizio |
| `"51 - 200"` | Intermedio |
| `"201+"` | Esperto |

### sex
`"Maschio"` | `"Femmina"` | non impostato

### is_legal_accepted (boolean, obbligatorio)
True se l'utente ha accettato i termini legali durante il signup. Utenti con `false` non hanno completato l'onboarding.

### preferred_dropzone / others_dropzone
Zone di lancio italiane (es. "Ravenna", "Casale Monferrato", "Fermo"). Utile per capire la distribuzione geografica degli utenti.

## Query Auth0 via MCP

### Lista utenti con filtri
```
auth0_list_users(
  per_page: 100,
  page: 0,
  q: "user_metadata.qualifications:licenziato"
)
```

### Utente singolo
```
auth0_get_user(user_id: "google-oauth2|104612087445133776110")
```

### Analisi della base utenti
Per analisi aggregate dei `user_metadata`, recuperare la lista utenti completa e calcolare distribuzioni lato Claude:
- Distribuzione qualifiche: raggruppa per `qualifications`
- Heatmap dropzone: conta occorrenze di `preferred_dropzone`
- Distribuzione esperienza: raggruppa per `jumps`
- Tasso completamento: conta `is_legal_accepted == true` vs `false`
- Distribuzione per data registrazione: raggruppa per mese di `created_at`

## Collegamento MongoDB ↔ Auth0

Il campo `userid` in MongoDB corrisponde a `user_id` in Auth0. Formati comuni:
- `google-oauth2|{numeric_id}` — login con Google
- `auth0|{alphanumeric}` — login con email/password

Per arricchire un'analisi MongoDB con il profilo utente, usa l'ID di MongoDB come chiave per query Auth0.

## Note Privacy

I dati degli utenti (email, nome, data di nascita) sono dati personali. Nelle analisi aggregate, evitare di esporre dati individuali. Preferire distribuzioni statistiche anonimizzate.
