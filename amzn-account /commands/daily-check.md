---
description: Full Amazon brand health check for a store
allowed-tools: Task, Read, Write, Bash
argument-hint: [brand-name]
---

Run a complete 3-phase brand health check for the Amazon store matching "$ARGUMENTS".

Use the `brand-daily-check` skill throughout this workflow for evaluation criteria and report structure.

## Setup

1. Read `STORES.md` from the workspace folder to find the store for "$ARGUMENTS".
   - If no argument was given, list all available brands from STORES.md and ask the user which one to check.
   - If the brand is not found in STORES.md, inform the user and stop.
2. Extract from STORES.md: brand name, all store URLs (per country), active countries, brand identity, category, key products, notes.
3. Create the working directory: `/tmp/amzn-check/`
4. Note the current date (ISO format) for file naming.

---

## Phase 1 — Store Explorer

Use the Task tool to spawn a subagent using the **store-explorer** agent role.

Provide the agent with:
- Brand name
- All store URLs from STORES.md (one per active country)
- Output path: `/tmp/amzn-check/asin-list.md`

Wait for Phase 1 to complete and confirm that `/tmp/amzn-check/asin-list.md` was created before proceeding.

---

## Phase 2 — ASIN Auditor

Use the Task tool to spawn a subagent using the **asin-auditor** agent role.

Provide the agent with:
- Brand name
- Input path: `/tmp/amzn-check/asin-list.md`
- Output path: `/tmp/amzn-check/audit-data.md`

Wait for Phase 2 to complete and confirm that `/tmp/amzn-check/audit-data.md` was created before proceeding.

---

## Phase 3 — Report Compiler

Use the Task tool to spawn a subagent using the **report-compiler** agent role.

Provide the agent with:
- Brand name
- Brand info (paste the full STORES.md entry for this brand)
- Input path: `/tmp/amzn-check/audit-data.md`
- Output path: `/tmp/amzn-check/report-draft.md`

Wait for Phase 3 to complete before proceeding.

---

## Finalize

1. Read `/tmp/amzn-check/report-draft.md`
2. Create a Word document (.docx) from the report content and save it to the workspace folder as:
   `[brand-name-lowercase]-daily-check-[YYYY-MM-DD].docx`
3. Present the document to the user with a `computer://` link.
4. Show a brief summary in the chat:
   - Overall brand status: 🟢 OK / 🟡 ATTENZIONE / 🔴 CRITICO
   - Top 3 issues found (or "Nessun problema rilevato" if all OK)
   - Number of ASINs checked
