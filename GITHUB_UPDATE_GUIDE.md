# GITHUB REPOSITORY UPDATE GUIDE
## DirtyTag 3.0 — Gennaio 2026

---

## 📋 SOMMARIO AGGIORNAMENTI

Questo documento elenca tutti i file da aggiornare nella repository `github.com/FedericoLuigiDOrsi/dirtytag-system`.

---

## 🆕 FILE DA AGGIUNGERE

| File | Destinazione | Descrizione |
|------|--------------|-------------|
| `CHANGELOG_2026-01-15_2026-01-16.md` | `/docs/` | Changelog fix critici 15-16 Gennaio |
| `W0.7_MIGRATION_BRIDGE_v9.json` | `/workflows/Migration/` | Versione con anti-duplication |
| `ai-photo-qc-review_v2.1.html` | `/webapp/` | Fix race condition |

---

## 📝 FILE DA AGGIORNARE

### 1. CHANGELOG.md
**Path:** `/CHANGELOG.md`

**Aggiungi alla fine (dopo riga 287):**
```markdown
### 15-16 Gennaio 2026 — Critical Fixes

**FileID Contamination Crisis**
- 139 SKU contaminati (22% catalogo)
- Root cause: webapp race condition
- Fix: Photo QC v2.1 con SKU validation

**W0.7_MIGRATION_BRIDGE v9**
- Anti-duplication controls
- Sequential check PROCESS_QUEUE + INVENTARIO
- Error path trigger reset

**Additional Fixes:**
- Kids mannequin support in W3
- T-SHIRT → TSHIRT normalization
- AI regeneration trigger fix

---

*Ultimo aggiornamento: 16 Gennaio 2026*
```

### 2. WORKFLOWS.md
**Path:** `/WORKFLOWS.md`

**Sezione W0.7 — Sostituisci con:**
```markdown
## W0.7_MIGRATION_BRIDGE

**Versione:** v9  
**Ultimo Update:** 15 Gennaio 2026

### Funzione
Trasferisce prodotti da OLD_INVENTORY a PROCESS_QUEUE con:
- Anti-duplication checks (PROCESS_QUEUE + INVENTARIO)
- FileID preservation
- Error logging dettagliato

### Trigger
- Schedule: Every 10 minutes
- Condition: `Ready_To_Migrate = TRUE`

### Anti-Duplication Flow (v9)
```
Fetch OLD_INVENTORY (Ready_To_Migrate = 1)
    │
    ▼
Check PROCESS_QUEUE (by SKU)
    │
    ├── Found → SKIP (log reason)
    │
    ▼
Check INVENTARIO (by SKU)
    │
    ├── Found → SKIP (log reason)
    │
    ▼
Create PROCESS_QUEUE record
    │
    ▼
Reset Ready_To_Migrate = 0
```

### Output Fields (PROCESS_QUEUE)
| Campo | Source | Note |
|-------|--------|------|
| SKU | OLD_INVENTORY.SKU | |
| Brand_Source | OLD_INVENTORY.Brand | |
| Category_Source | OLD_INVENTORY.Category | |
| Front_FileId | OLD_INVENTORY.Front_FileId | Preserved |
| Back_FileId | OLD_INVENTORY.Back_FileId | Preserved |
| Migration_Status | "PENDING" | Initial status |

### Error Handling
- Duplicates → Skip + log `Migration_Skip_Reason`
- API errors → Log to `Last_Error`
- Always reset trigger to prevent loops
```

### 3. AIRTABLE_SCHEMA.md
**Path:** `/AIRTABLE_SCHEMA.md`

**Aggiungi in OLD_INVENTORY Table:**
```markdown
### OLD_INVENTORY — Migration Fields (Updated Jan 2026)

| Campo | Tipo | Descrizione |
|-------|------|-------------|
| Migration_Skip_Reason | Long Text | Motivo skip se duplicato |
```

### 4. README.md (Project Root)
**Path:** `/README.md`

**Aggiorna sezione "Recent Updates":**
```markdown
## Recent Updates

### Gennaio 2026
- **16 Jan:** Workflow trigger audit, W1-W4 optimization identified
- **15 Jan:** FileID contamination fix (139 SKU), webapp v2.1
- **15 Jan:** W0.7 v9 anti-duplication, kids mannequin support
- **14 Jan:** T-SHIRT normalization, migration pipeline stabilization
- **7 Jan:** W3.5 v4, W6 archive cleanup, notifications
```

---

## 🗂️ WORKFLOW JSON FILES

### Da Aggiornare/Sostituire

| Workflow | File Attuale | Nuova Versione | Note |
|----------|--------------|----------------|------|
| W0.7_MIGRATION_BRIDGE | v8 | v9 | Anti-duplication |
| W3_AI_PROCESSOR | v14 | v14.1 | Kids mannequin |
| W00_MIGRATION_AI_AGENT | v3 | v3.1 | T-SHIRT fix |

---

## 📊 VERIFICA POST-UPDATE

### Checklist
```
[ ] CHANGELOG.md aggiornato
[ ] WORKFLOWS.md aggiornato
[ ] AIRTABLE_SCHEMA.md aggiornato
[ ] README.md aggiornato
[ ] W0.7_MIGRATION_BRIDGE_v9.json uploadato
[ ] ai-photo-qc-review_v2.1.html uploadato
[ ] CHANGELOG_2026-01-15_2026-01-16.md uploadato
[ ] Commit con messaggio descrittivo
```

### Commit Message Suggerito
```
fix: FileID contamination crisis + anti-duplication controls

- Fix webapp Photo QC race condition (v2.1)
- W0.7 v9 with sequential duplicate checking
- Kids mannequin support in W3
- T-SHIRT → TSHIRT normalization
- 139 SKU cleanup completed

Refs: CHANGELOG_2026-01-15_2026-01-16.md
```

---

## ⚠️ BREAKING CHANGES

### Nessuno
I fix sono retrocompatibili. I workflow aggiornati mantengono lo stesso interface.

---

## 📞 SUPPORTO

Per problemi post-update:
1. Verifica log workflow in n8n
2. Controlla `Last_Error` fields in Airtable
3. Riferimento: `CHANGELOG_2026-01-15_2026-01-16.md`

---

**Documento:** GitHub Update Guide  
**Data:** 16 Gennaio 2026  
**Autore:** Claude AI Engineer
