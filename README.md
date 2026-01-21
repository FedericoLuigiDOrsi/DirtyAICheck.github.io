<p align="center">
  <img src="https://img.shields.io/badge/🤖-AI%20Photo%20Check-8E75B2?style=for-the-badge" alt="AI Photo Check"/>
</p>

<h1 align="center">DirtyAICheck</h1>

<p align="center">
  <strong>AI Photo Quality Review Webapp con Funzione Regenerate</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-2.0-blue?style=flat-square" alt="Version"/>
  <img src="https://img.shields.io/badge/status-production-success?style=flat-square" alt="Status"/>
  <img src="https://img.shields.io/badge/stack-HTML%20%7C%20JS%20%7C%20Airtable%20API-orange?style=flat-square" alt="Stack"/>
</p>

---

## 📋 Overview

**DirtyAICheck** è la webapp per il controllo qualità delle foto generate dall'AI (Gemini 2.0 Flash). Permette di visualizzare, confrontare e approvare/rigenerare le foto AI prima della pubblicazione sui marketplace.

È il "quality gate" finale prima che un prodotto diventi LISTING_READY.

---

## 🎯 Funzionalità

### Core Features

| Feature | Descrizione |
|---------|-------------|
| **🖼️ Side-by-Side View** | Confronto RAW originale vs AI generata |
| **📂 Version History** | Visualizzazione tutte le versioni (v1, v2, v3...) |
| **✅ Approve** | Approva foto e avanza prodotto a LISTING_READY |
| **🔄 Regenerate** | Richiedi nuova generazione AI (Front/Back/Both) |
| **🔍 Zoom Modal** | Ingrandimento per verifica dettagli |
| **📊 Progress Counter** | "Reviewing X of Y" con auto-advance |
| **⌨️ Keyboard Shortcuts** | Review veloce senza mouse |

### Keyboard Shortcuts

| Tasto | Azione |
|-------|--------|
| `A` | Approve current |
| `R` | Reject/Regenerate |
| `←` | Previous SKU |
| `→` | Next SKU |
| `Esc` | Chiude modali |

---

## 🔄 Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    AI QUALITY CHECK WORKFLOW                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  W3 AI_PROCESSOR                                                │
│       ↓                                                          │
│  AI_Quality_Check = PENDING                                     │
│       ↓                                                          │
│  ┌─────────────────────────────────┐                            │
│  │   🤖 DIRTY AI CHECK WEBAPP      │  ← Tu sei qui              │
│  │                                  │                            │
│  │   • RAW vs AI side-by-side      │                            │
│  │   • Version comparison          │                            │
│  │   • Approve / Regenerate        │                            │
│  └─────────────────────────────────┘                            │
│       ↓                           ↓                              │
│  ✅ APPROVE                   🔄 REGENERATE                      │
│       ↓                           ↓                              │
│  W3.5 APPROVE_HANDLER        W3 (new version)                   │
│       ↓                           ↓                              │
│  LISTING_READY               Back to PENDING                    │
│       ↓                                                          │
│  W4 TEXT_PROCESSOR                                              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Componente | Tecnologia |
|------------|------------|
| **Frontend** | HTML5, CSS3, Vanilla JavaScript |
| **Database** | Airtable API |
| **Storage** | Google Drive (foto RAW e AI) |
| **Auth** | Airtable Personal Access Token |
| **Hosting** | GitHub Pages |

---

## 📊 Database Schema

### Tabella Principale

**Base:** DirtyTag 3.0 (`apptD8GSxN3vhhivI`)

| Tabella | Table ID | Ruolo |
|---------|----------|-------|
| `INVENTARIO` | `tblddAcLcQAyk050u` | Record prodotti |

### Campi Utilizzati

| Campo | Tipo | Descrizione |
|-------|------|-------------|
| `SKU` | Text | Identificatore prodotto |
| `AI_Quality_Check` | Single Select | PENDING, APPROVED, REJECTED |
| `AI_Front_Image_Link` | URL | Link foto AI front |
| `AI_Back_Image_Link` | URL | Link foto AI back |
| `RAW_Front_URL` | Formula | Link foto RAW front |
| `RAW_Back_URL` | Formula | Link foto RAW back |
| `AI_Regenerate_Trigger` | Checkbox | Trigger per rigenerazione |
| `AI_Regen_Scope` | Single Select | Front, Back, Both |
| `AI_Approved_Version` | Number | Versione approvata |
| `AI_Regeneration_Count` | Number | Contatore rigenerazioni |
| `AI_Approved_Processed` | Checkbox | Flag post-approvazione |

### AI Quality Check Stati

| Stato | Significato | Azione Sistema |
|-------|-------------|----------------|
| `PENDING` | In attesa di review | Mostra in webapp |
| `APPROVED` | Foto approvate | W3.5 processa |
| `REJECTED` | Richiesta rigenerazione | W3 rigenera |

---

## ✅ Criteri di Approvazione

### Foto AI Accettabile ✅

| Criterio | Check |
|----------|-------|
| **Fedeltà colore** | Colori simili all'originale |
| **Dettagli preservati** | Logo, etichette, cuciture visibili |
| **Composizione** | Prodotto centrato e completo |
| **Background** | Pulito, coerente con template |
| **Manichino** | Proporzionato, non distorto |
| **No artefatti** | Nessuna distorsione AI evidente |

### Foto AI da Rigenerare 🔄

| Problema | Descrizione |
|----------|-------------|
| **Colore alterato** | Colori diversi dall'originale |
| **Dettagli persi** | Logo/brand non visibile |
| **Distorsione** | Forme alterate, proporzioni sbagliate |
| **Artefatti AI** | Elementi aggiunti o mancanti |
| **Cut-off** | Parti del capo tagliate |
| **Background sporco** | Elementi indesiderati |

---

## 🔄 Regeneration Scope

Quando rigeneri, puoi scegliere cosa rigenerare:

| Scope | Descrizione | Use Case |
|-------|-------------|----------|
| `Front` | Solo foto frontale | Front OK, back problematico |
| `Back` | Solo foto retro | Back OK, front problematico |
| `Both` | Entrambe le foto | Problemi su entrambe |

### Limite Rigenerazioni

- **Raccomandato:** Max 3 rigenerazioni per prodotto
- Dopo 3 tentativi, valuta:
  - Qualità foto RAW originali
  - Necessità nuovo shooting
  - Cambio modalità AI

---

## 📁 Struttura Versioni AI

```
RAW/{SKU}/
├── {SKU}_FRONT.jpg           ← RAW originale
├── {SKU}_BACK.jpg            ← RAW originale
└── AI_GENERATIONS/
    ├── v1/                   ← Prima generazione
    │   ├── AI_FRONT.png
    │   └── AI_BACK.png
    ├── v2/                   ← Dopo rigenerazione
    │   ├── AI_FRONT.png
    │   └── AI_BACK.png
    └── DISCARDED/            ← Versioni scartate
        └── v1_AI_FRONT.png
```

---

## 🚀 Setup & Deploy

### Prerequisiti

- Account GitHub
- Airtable Personal Access Token con scope:
  - `data.records:read`
  - `data.records:write`
  - `schema.bases:read`

### Deploy su GitHub Pages

```bash
# Clone repository
git clone https://github.com/FedericoLuigiDOrsi/DirtyAICheck.github.io.git

# Il file index.html è già configurato
# GitHub Pages serve automaticamente da branch main
```

### Configurazione Token

1. Apri la webapp
2. Inserisci il token Airtable nel campo dedicato
3. Il token viene salvato in `localStorage` del browser

---

## 📱 Interfaccia

### Layout Principale

```
┌─────────────────────────────────────────────────────────────────┐
│  🤖 DirtyTag AI Photo Check                                     │
│                                                                  │
│  Reviewing: 12 of 47                                [🔧 Debug]  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  SKU: MF-2411          Brand: MONCLER          Mode: MANI       │
│  Regenerations: 0      Version: v1                               │
│                                                                  │
│  ┌─────────────────────┐    ┌─────────────────────┐             │
│  │                     │    │                     │             │
│  │    RAW FRONT        │    │    AI FRONT         │             │
│  │                     │    │                     │             │
│  │    [Original]       │    │    [Generated]      │             │
│  │                     │    │                     │             │
│  └─────────────────────┘    └─────────────────────┘             │
│                                                                  │
│  ┌─────────────────────┐    ┌─────────────────────┐             │
│  │                     │    │                     │             │
│  │    RAW BACK         │    │    AI BACK          │             │
│  │                     │    │                     │             │
│  │    [Original]       │    │    [Generated]      │             │
│  │                     │    │                     │             │
│  └─────────────────────┘    └─────────────────────┘             │
│                                                                  │
│  Version History: [v1]                                           │
│                                                                  │
│  [🔄 Regenerate ▼]                            [✅ Approve]      │
│   └─ Front Only                                                  │
│   └─ Back Only                                                   │
│   └─ Both                                                        │
│                                                                  │
│  [◀ Previous]                                    [Next ▶]       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## ⚡ Performance Tips

### Review Veloce

1. Usa keyboard shortcuts (`A` approve, `R` reject)
2. Focus su differenze evidenti prima
3. Batch review: fai tutti i facili prima, poi i dubbi

### Tempo Medio per Review

| Metodo | Tempo/Prodotto |
|--------|----------------|
| Manual Airtable | 3-5 min |
| Con Webapp | ~1 min |
| Con Shortcuts | ~30 sec |

---

## 🔧 Troubleshooting

### Problemi Comuni

| Problema | Causa | Soluzione |
|----------|-------|-----------|
| Foto non caricano | Token scaduto | Reinserisci token |
| Link AI vuoti | W3 non completato | Controlla log W3 |
| Approve non funziona | W3.5 non attivo | Verifica workflow |
| Regen non parte | A5 automation off | Attiva in Airtable |

### Debug Mode

Clicca il pulsante 🔧 Debug per visualizzare:
- Log operazioni
- Chiamate API
- Errori dettagliati

---

## 📚 Documentazione Correlata

| Documento | Contenuto |
|-----------|-----------|
| [GUIDE_AI_CHECK_WEBAPP.md](https://github.com/FedericoLuigiDOrsi/dirtytag-system/blob/main/GUIDE_AI_CHECK_WEBAPP.md) | Guida utente completa |
| [WORKFLOWS.md](https://github.com/FedericoLuigiDOrsi/dirtytag-system/blob/main/WORKFLOWS.md) | Workflow W3, W3.5 |
| [AI_PROMPTS.md](https://github.com/FedericoLuigiDOrsi/dirtytag-system/blob/main/AI_PROMPTS.md) | Configurazione AI |

---

## 🔗 Links

| Risorsa | URL |
|---------|-----|
| **Webapp Live** | https://federicoluigidorsi.github.io/DirtyAICheck.github.io/ |
| **Sistema Principale** | https://github.com/FedericoLuigiDOrsi/dirtytag-system |
| **AI Support Chat** | https://notebooklm.google.com/notebook/7b62519e-9fbf-4d40-bf47-2f43c0fd0b28 |

---

## 📄 License

Proprietario — Tutti i diritti riservati.

---

## 👤 Author

**Federico Luigi D'Orsi** — [@FedericoLuigiDOrsi](https://github.com/FedericoLuigiDOrsi)
