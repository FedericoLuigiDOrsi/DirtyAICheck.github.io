# Deployment Guide — DirtyTag AI Photo Check

> Istruzioni per deployare la webapp su Vercel (metodo raccomandato) o GitHub Pages.

---

## Opzione A — Vercel (Raccomandato)

### Prerequisiti

- Account GitHub con la repo della webapp
- Account Vercel (gratuito su https://vercel.com)

### Step 1 — Crea la repo GitHub

```bash
# Crea una nuova repo su GitHub (es. "dirtytag-qc-webapp")
# Poi clona localmente e copia i file della webapp:

git clone https://github.com/TUO_USERNAME/dirtytag-qc-webapp.git
cd dirtytag-qc-webapp

# Copia i file dalla cartella webapp-deploy/ del repo principale:
cp /path/to/dirtytag-system/webapp-deploy/* .

# Commit e push:
git add .
git commit -m "feat: initial deploy DirtyTag AI Photo Check v2.0"
git push origin main
```

### Step 2 — Importa in Vercel

1. Vai su https://vercel.com/new
2. Clicca **Import Git Repository**
3. Seleziona la repo `dirtytag-qc-webapp`
4. **Framework Preset:** Other (nessun framework)
5. **Root Directory:** `.` (root)
6. **Build Command:** (lascia vuoto)
7. **Output Directory:** `.` (root)
8. Clicca **Deploy**

### Step 3 — Verifica

1. Vercel genera automaticamente un URL tipo `dirtytag-qc-webapp.vercel.app`
2. Apri l'URL → inserisci il token Airtable → verifica che carichi i prodotti PENDING
3. Se la webapp mostra "No pending products" è normale se non ci sono record in QC

### Aggiornamenti Futuri

Ogni `git push` sulla repo triggera automaticamente il redeploy su Vercel.

```bash
# Per aggiornare index.html:
cp /path/to/dirtytag-system/webapp-deploy/index.html .
git add index.html
git commit -m "fix: update webapp index.html"
git push origin main
# Vercel fa redeploy automaticamente in ~30 secondi
```

---

## Opzione B — Deploy Manuale su Vercel (senza Git)

Se vuoi deployare senza configurare Git:

1. Vai su https://vercel.com/new
2. Clicca **Deploy** nel menu → **Upload**
3. Trascina la cartella `webapp-deploy/` nel drop zone
4. Vercel carica e deploya i file statici
5. Copia l'URL generato

> **Attenzione:** Con deploy manuale non hai auto-redeploy. Dovrai ripetere l'upload ad ogni aggiornamento.

---

## Opzione C — GitHub Pages (Alternativo)

Se preferisci usare GitHub Pages:

1. Crea la repo GitHub con i file della webapp
2. Vai su **Settings** → **Pages**
3. Source: **Deploy from a branch**
4. Branch: `main`, folder: `/ (root)`
5. Salva → GitHub Pages deploya automaticamente

URL: `https://TUO_USERNAME.github.io/NOME_REPO/`

> **Nota:** GitHub Pages può avere un delay di 1-2 minuti per i deploy. Vercel è più veloce.

---

## Configurazione Post-Deploy

### API Key Airtable

La webapp NON usa variabili d'ambiente — l'API key viene inserita dall'operatore a runtime.

**Per Sabrina:**
1. Vai su https://airtable.com/account
2. Sezione **Personal Access Token** → **Create new token**
3. Nome: `DirtyTag QC Webapp`
4. Scopes: `data.records:read`, `data.records:write`, `schema.bases:read`
5. Access: seleziona base `DirtyTag 3.0 (AI)` (`apptD8GSxN3vhhivI`)
6. Copia il token (inizia con `pat...`)
7. Apri la webapp → incolla il token → clicca Accedi

Il token viene salvato automaticamente in `localStorage` del browser. Non serve re-inserirlo ad ogni sessione.

### Dominio Custom (opzionale)

Su Vercel:
1. **Project Settings** → **Domains**
2. Aggiungi dominio (es. `qc.dirtytag.it`)
3. Configura DNS secondo le istruzioni Vercel

---

## Variabili Hardcoded (non modificare)

Le seguenti costanti sono hardcoded nell'`index.html` e non richiedono configurazione:

```javascript
const BASE_ID = 'apptD8GSxN3vhhivI';         // Airtable base production
const INVENTARIO_TABLE = 'tblddAcLcQAyk050u'; // Tabella INVENTARIO
```

Se la base Airtable cambiasse, modificare direttamente questi valori in `index.html`.

---

## Troubleshooting Deploy

| Problema | Soluzione |
|----------|-----------|
| Vercel mostra "404 Not Found" | Verifica che `index.html` sia alla root della repo |
| Vercel non rileva il framework | Seleziona "Other" come preset, nessun build command |
| CORS errors in console | L'API Airtable supporta CORS — verificare che il token sia valido |
| Pagina bianca | Apri DevTools → Console → verificare errori JavaScript |
| Cache vecchia dopo update | Forzare refresh con `Ctrl+Shift+R` (il `vercel.json` ha `no-cache`) |

---

*Documento: DEPLOYMENT.md | DirtyTag 3.5 | Marzo 2026*
