<p align="center">
  <img src="https://img.shields.io/badge/🤖-AI%20Quality%20Check-8E75B2?style=for-the-badge" alt="AI Quality Check"/>
</p>

<h1 align="center">DirtyTag AI Photo Check</h1>

<p align="center">
  <strong>Webapp QC v2 — Quality Review per foto AI generate da Gemini</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-2.0-blue?style=flat-square" alt="Version"/>
  <img src="https://img.shields.io/badge/status-production-success?style=flat-square" alt="Status"/>
  <img src="https://img.shields.io/badge/stack-HTML%20%7C%20Vanilla%20JS%20%7C%20Airtable%20API-orange?style=flat-square" alt="Stack"/>
  <img src="https://img.shields.io/badge/deploy-Vercel-black?style=flat-square" alt="Deploy"/>
</p>

---

## Overview

**DirtyTag AI Photo Check** è l'interfaccia di quality control (QC) per le foto generate dall'AI nel sistema DirtyTag 3.5.

Sabrina visualizza ogni prodotto con le foto RAW originali affiancate alle foto AI generate da Gemini 2.5 Flash, e decide se approvarle o richiedere rigenerazione prima della pubblicazione su Vinted.

È il **quality gate** tra la generazione AI (W3) e l'export Vinted (W5).

---

## Pipeline DirtyTag 3.5

```
RAW PHOTOS in Drive
        ↓
  [W024] UNIFIED_PROCESSOR
  (ogni 5 min — schedule)
  • Scan cartella Drive
  • Vision analysis Gemini
  • Genera testi (titolo + descrizione)
  • Setta AI_Pending_Trigger = 1
        ↓
  [W3] AI_PROCESSOR
  (ogni 5 min — schedule)
  • Scarica foto RAW da Drive
  • Genera foto AI con Gemini 2.5 Flash
  • Carica su Drive AI_PROCESSED/
  • Setta AI_Quality_Check = PENDING
        ↓
  ★ DIRTY AI CHECK WEBAPP  ←── Sei qui
  • Sabrina confronta RAW vs AI
  • Approva / Rigenera / Rigenera Testi
  • Approvazione → Product_Status = LISTING_READY
        ↓
  [W5] DOTB_EXPORTER
  (manuale — Chat trigger)
  • Genera CSV per Vinted
  • Setta Published_Vinted_Mannequin = 1
```

---

## Funzionalità

| Feature | Descrizione |
|---------|-------------|
| **Side-by-Side View** | Confronto foto RAW originale vs AI generata (front + back) |
| **Version History** | Visualizza e naviga tra tutte le versioni (v1, v2, v3...) |
| **Approve** | Approva foto → prodotto avanza a `LISTING_READY` |
| **Regenerate Foto** | Richiede nuova generazione AI (Front / Back / Both) |
| **Regenerate Testi** | Richiede rigenazione titolo + descrizione (via W024) |
| **Defer** | Rimanda prodotto alla fine della coda (localStorage) |
| **Zoom Modal** | Ingrandimento foto per verifica dettagli |
| **Progress Counter** | "Reviewing X of Y" con statistiche sessione |
| **Keyboard Shortcuts** | Review veloce senza mouse |

---

## Keyboard Shortcuts

| Tasto | Azione |
|-------|--------|
| `A` | Approve |
| `R` | Rigenera foto (apre dropdown) |
| `T` | Rigenera testi |
| `D` | Defer (rimanda) |
| `→` | Prodotto successivo |
| `←` | Prodotto precedente |
| `F` / `B` | Toggle Front / Back |
| `1` - `9` | Seleziona versione v1...v9 |
| `Esc` | Chiude modal / dropdown |

---

## Azioni e Effetti su Airtable

### Approve

```
AI_Quality_Check  → APPROVED
Product_Status    → LISTING_READY
AI_Approved_Version → {n}   (numero versione approvata)
AI_Approved_Processed → 1
```

Dopo l'approvazione, W5 includerà il prodotto nel prossimo export CSV Vinted.

### Rigenera Foto

```
AI_Regenerate_Trigger → 1   (W3 picks this up)
AI_Pending_Trigger    → 1
AI_Regen_Scope        → "Front" | "Back" | "Both"
```

W3 si esegue ogni 5 minuti e processerà il record automaticamente.

### Rigenera Testi

```
Product_Status    → RAW_WAITING   (W024 picks this up)
AI_Quality_Check  → REJECTED
AI_Pending_Trigger → 0
Listing_Title     → ""  (cancellato)
Vinted_Description → "" (cancellato)
```

W024 si esegue ogni 5 minuti e rigenera titolo, descrizione e foto AI da capo.

---

## Criteri di Approvazione

### Approva se ✅

| Criterio | Check |
|----------|-------|
| Colori fedeli all'originale | Il colore del capo è uguale al RAW |
| Dettagli preservati | Logo, etichette, cuciture visibili |
| Composizione corretta | Prodotto centrato e completo |
| Background pulito | Nessun elemento indesiderato |
| Manichino proporzionato | Nessuna distorsione evidente |
| Nessun artefatto AI | Nessun elemento aggiunto o mancante |

### Rigenera se 🔄

| Problema | Esempio |
|----------|---------|
| Colore alterato | Capo rosso diventa rosa |
| Dettagli persi | Logo non visibile |
| Distorsione | Forme alterate, proporzioni sbagliate |
| Cut-off | Parte del capo tagliata fuori |
| Background sporco | Elementi indesiderati visibili |
| Artefatti AI | Aggiunte non esistenti |

---

## Tech Stack

| Componente | Tecnologia |
|------------|------------|
| Frontend | HTML5 + CSS3 + Vanilla JavaScript |
| AI Generation | Gemini 2.5 Flash (`gemini-2.5-flash-image-preview`) |
| Database | Airtable API v0 |
| Storage foto | Google Drive (via file URL) |
| Auth | Airtable Personal Access Token (runtime) |
| Deploy | Vercel (static) |
| Build | Nessuno — single-file SPA |

---

## Airtable Schema

**Base:** `apptD8GSxN3vhhivI` (DirtyTag 3.0 AI — Production)

**Tabella:** `INVENTARIO` (`tblddAcLcQAyk050u`)

### Campi letti

| Campo | Field ID | Tipo |
|-------|----------|------|
| `SKU` | `fldDtobO4CsjM7TpS` | Text |
| `AI_Quality_Check` | `fldO0kpBwy56XYjve` | Single Select |
| `AI_Front_Image_Link` | `fldHNo25zo8Gd5ll6` | URL |
| `AI_Back_Image_Link` | `fldyJlDLgwveM20tj` | URL |
| `rawID_FRONT` | `fldIKYaLbCsbRHqdv` | Text |
| `rawID_BACK` | `fldx6kLefoaWld3yO` | Text |
| `AI_Processing_History` | `fldx2Eritd9D8elO4` | Long Text |
| `AI_Regeneration_Count` | `fldxj1doZ0FSnMLdN` | Number |
| `Brand_TXT` | `fldazdOhQEFyETRBt` | Formula |
| `Category_TXT` | `fld5L4WnkEYQEplC4` | Formula |
| `AI_Mode` | `fldFyQ1E00jDRxGjR` | Single Select |
| `Product_Status` | `fldZFVmurwJRgkDI4` | Single Select |

### Campi scritti

| Campo | Valori | Quando |
|-------|--------|--------|
| `AI_Quality_Check` | `APPROVED` | Approva |
| `Product_Status` | `LISTING_READY` / `RAW_WAITING` | Approva / Rigenera testi |
| `AI_Regenerate_Trigger` | `1` | Rigenera foto |
| `AI_Pending_Trigger` | `1` / `0` | Rigenera foto / Rigenera testi |
| `AI_Regen_Scope` | `Front` / `Back` / `Both` | Rigenera foto |
| `AI_Approved_Version` | `{n}` | Approva |
| `AI_Approved_Processed` | `1` | Approva |
| `Listing_Title` | `""` | Rigenera testi |
| `Vinted_Description` | `""` | Rigenera testi |

---

## Setup

### Prerequisiti

- Account Airtable con accesso alla base DirtyTag production
- Personal Access Token Airtable con scope:
  - `data.records:read`
  - `data.records:write`

### Login

1. Apri la webapp
2. Nel campo **API Key**, incolla il tuo Airtable Personal Access Token
3. Clicca **Accedi**
4. Il token viene salvato in `localStorage` per le sessioni successive

### Struttura Drive — versioni AI

```
RAW/{SKU}/
├── {SKU}_FRONT.jpg           ← Foto RAW originale
├── {SKU}_BACK.jpg            ← Foto RAW originale
└── AI_GENERATIONS/
    ├── v1/
    │   ├── AI_FRONT.png      ← Prima generazione Gemini
    │   └── AI_BACK.png
    ├── v2/                   ← Dopo prima rigenerazione
    │   ├── AI_FRONT.png
    │   └── AI_BACK.png
    └── ...
```

---

## Troubleshooting

| Problema | Causa probabile | Soluzione |
|----------|----------------|-----------|
| Foto AI non caricano | Link Drive scaduto / privato | Verifica che AI_Front_Image_Link sia URL pubblico Drive |
| Nessun prodotto in coda | Nessun record con AI_Quality_Check = PENDING | Controlla che W3 abbia completato l'elaborazione |
| Approva non funziona | Token scaduto | Logout e reinserisci il token |
| Rigenera non parte | W3 non attivo | Verifica che W3_AI_PROCESSOR sia attivo in n8n |
| Rigenera testi non parte | W024 non attivo | Verifica che W024_UNIFIED_PROCESSOR sia attivo in n8n |
| Errore 401 / 403 | Token non valido | Genera nuovo PAT su airtable.com/account |
| Errore 422 | Campo non valido | Controlla la versione della webapp (deve essere v2.0+) |

---

## Deploy

Vedere [DEPLOYMENT.md](./DEPLOYMENT.md) per le istruzioni complete di deploy su Vercel.

---

## Links

| Risorsa | URL |
|---------|-----|
| Sistema Principale (n8n) | VPS Hostinger 72.61.186.44 |
| Airtable Base | https://airtable.com/apptD8GSxN3vhhivI |
| Repo Principale | https://github.com/FedericoLuigiDOrsi/dirtytag-system |

---

## License

Proprietario — Tutti i diritti riservati.

**Author:** Federico Luigi D'Orsi — [@FedericoLuigiDOrsi](https://github.com/FedericoLuigiDOrsi)

*Version: 2.0 | DirtyTag 3.5 | Marzo 2026*
