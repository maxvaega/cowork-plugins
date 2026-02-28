---
description: Add a new brand/store to STORES.md
allowed-tools: Read, Write, Edit
argument-hint: [brand-name]
---

Add a new Amazon store/brand entry to `STORES.md` in the workspace folder.

## Steps

1. Check if `STORES.md` exists in the workspace folder.
   - If not, create it with this header:
     ```
     # Amazon Stores — Brand Directory

     Questo file contiene le informazioni su tutti i brand gestiti.
     Ogni entry include URL dello store, paesi attivi, identità del brand e note operative.

     ---
     ```

2. Use "$ARGUMENTS" as the brand name if provided. Otherwise, ask the user: "Come si chiama il brand / store da aggiungere?"

3. Check if an entry for this brand already exists in STORES.md. If so, inform the user and ask if they want to update it or cancel.

4. Collect the following information from the user, one question at a time:

   a. **Store name** (nome del brand per visualizzazione, es. "Kiko Milano")
   b. **Product category** (categoria principale, es. "Beauty", "Electronics", "Home & Kitchen", "Sports")
   c. **Active countries**: ask which countries the brand is active on Amazon (IT, DE, FR, ES, UK, US, NL, SE, PL, BE, etc.)
   d. **Store URLs**: for each active country, ask for the Amazon Store URL.
      Format: `https://www.amazon.[tld]/stores/[brand]/page/[id]`
      If the user doesn't have it, suggest: "Vai su Amazon → cerca il brand → clicca sul nome del brand sotto il titolo → copia l'URL della pagina Store."
      Mark inactive countries as "—"
   e. **Brand identity**: ask for a description of the brand positioning, tone of voice, and key differentiators (2-4 sentences).
   f. **Key products**: main product lines or flagship products (free text).
   g. **Notes**: any special notes or operational considerations (optional, can be left empty).

5. Format the entry and append it to STORES.md:

```markdown
## [Brand Name]

- **Store Name**: [display name]
- **Countries**: [comma-separated, e.g. IT, DE, FR]
- **Category**: [category]
- **Store URL (IT)**: [url or "—"]
- **Store URL (DE)**: [url or "—"]
- **Store URL (FR)**: [url or "—"]
- **Store URL (ES)**: [url or "—"]
- **Store URL (UK)**: [url or "—"]
- **Brand Identity**: [description]
- **Key Products**: [products]
- **Notes**: [notes or "—"]

---
```

6. Show the completed entry to the user and confirm: "Store aggiunto correttamente. Puoi ora lanciare `/daily-check [brand-name]` per eseguire il primo controllo."
