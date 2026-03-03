# CHANGELOG — DirtyTag AI Photo Check

---

## v2.0 — Marzo 2026 (DirtyTag 3.5)

### Aggiornamenti Pipeline

- **Compatibilità DirtyTag 3.5**: aggiornato il flusso di approvazione per il nuovo sistema
  - `approvePhotos()` ora setta `Product_Status = LISTING_READY` direttamente (rimosso W3.5 come intermediario)
  - `regenerateTexts()` usa `Product_Status = RAW_WAITING` per triggerare W024 (rimosso `RAW_Ready_Trigger` deprecato)
  - `triggerRegeneration()` aggiunge `AI_Pending_Trigger = 1` per assicurare che W3 raccolga il record

- **Bottone Approva**: disabilitato se i testi (Listing_Title, Vinted_Description) sono vuoti — previene approvazione di prodotti incompleti

- **Model AI aggiornato**: generazione tramite Gemini 2.5 Flash (`gemini-2.5-flash-image-preview`)

### Bug Fix

| Bug | Descrizione | Fix |
|-----|-------------|-----|
| WA-1 | `triggerRegeneration()` non ri-triggerava W3 | Aggiunto `AI_Pending_Trigger = 1` nel payload |
| WA-2 | `regenerateTexts()` non funzionava correttamente | Cambiato da `RAW_PROCESSED` a `RAW_WAITING` + rimosso trigger deprecato |
| WA-3 | Bottone Approva abilitato con testi vuoti | Aggiunta validazione testi obbligatori |

### Deploy

- Migrato da GitHub Pages a Vercel (deploy primario)
- Aggiunto `vercel.json` con `Cache-Control: no-store` per dati sempre freschi

---

## v2.1 — 15-16 Gennaio 2026

### Critical Fix: FileID Contamination

**Gravità:** CRITICA — 139 SKU (22% del catalogo) avevano foto cross-contaminate

**Root Cause:** Race condition nella webapp Photo QC — se l'operatore cambiava prodotto rapidamente durante la selezione, il sistema salvava il FileID di un prodotto sul record sbagliato.

**Fix:**
- Validazione obbligatoria SKU match prima di qualsiasi salvataggio
- Storage completo dei dati di selezione (non solo record ID) per prevenire race condition
- Warning visivo per selezioni inconsistenti
- Debug mode per troubleshooting

**Scope del cleanup eseguito:**
- Eliminati 139 record contaminati da INVENTARIO
- Reset record corrispondenti in OLD_INVENTORY per re-migrazione
- Tutti i prefissi coinvolti: CG (70), AE (31), TT (17), altri (21)

---

## v2.0 — Gennaio 2026 (DirtyTag 3.0 → 3.5 pre-release)

### Features Principali

- **Side-by-side view**: confronto RAW originale vs AI generata (front + back)
- **Version history**: navigazione tra tutte le versioni generate (v1, v2, v3...)
- **Regenerate scope**: scelta granulare Front / Back / Both
- **Keyboard shortcuts**: review veloce senza mouse (A, R, T, D, ←, →, 1-9)
- **Deferred list**: rimanda prodotti dubbi alla fine della coda (localStorage)
- **Progress counter**: "Reviewing X of Y" con statistiche sessione
- **Zoom modal**: ingrandimento foto per dettagli
- **Auto-advance**: salta automaticamente al prodotto successivo dopo ogni azione

### Stack

- HTML5 + CSS3 + Vanilla JavaScript (single-file SPA, no framework, no build)
- Airtable REST API v0
- Google Drive file URLs per foto

---

*Nota: Le versioni pre-v2.0 si riferiscono alla webapp Photo QC precedente (diverso strumento).*
