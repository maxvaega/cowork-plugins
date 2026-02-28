---
description: List all configured Amazon stores/brands
allowed-tools: Read
---

Read `STORES.md` from the workspace folder and display a clean summary of all configured brands.

## Steps

1. Read `STORES.md` from the workspace folder.
   - If the file doesn't exist or is empty, tell the user: "Nessuno store configurato. Usa `/add-store` per aggiungere il primo brand."

2. Parse each brand entry and display a formatted table:

```
Brand             | Paesi         | Categoria       | Stores configurati
------------------+---------------+-----------------+-------------------
[Brand Name]      | IT, DE, FR    | Beauty          | 3
[Brand Name 2]    | IT            | Electronics     | 1
```

3. After the table, show the total: "Totale: N brand configurati, M store URL attivi."

4. Add a quick-start hint: "Per eseguire il check di un brand: `/daily-check [nome brand]`"
