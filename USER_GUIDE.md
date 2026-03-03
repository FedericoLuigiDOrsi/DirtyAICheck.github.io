# Guida Utente — DirtyTag AI Photo Check

> Guida per Sabrina — come usare la webapp di quality check delle foto AI

---

## Cos'è questa webapp?

Questa webapp ti mostra le foto generate dall'intelligenza artificiale (Gemini 2.5 Flash)
e ti chiede di decidere se approvarle o chiedere di rigenerarle, prima che vengano
pubblicate su Vinted.

**Il tuo compito:** guardare ogni prodotto, confrontare la foto originale (RAW) con
quella generata dall'AI, e fare la tua scelta.

---

## Come accedere

1. Apri il link della webapp (Vercel URL)
2. Nel campo **"Airtable API Key"**, incolla il token fornito da Federico
3. Clicca **"Accedi"**

Il token viene salvato automaticamente — non devi reinserirlo ogni volta.

> Se appare "Token non valido", contatta Federico per un token aggiornato.

---

## Schermata principale

```
┌────────────────────────────────────────────────────────────┐
│  DirtyTag AI Photo Check          Reviewing: 12 of 47     │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  SKU: MF-2411    Brand: MONCLER    Mode: MANI              │
│  Rigenerazioni: 0    Versione: v1                          │
│                                                             │
│  ┌──────────────┐    ┌──────────────┐                      │
│  │              │    │              │                      │
│  │  FOTO ORIG.  │    │   FOTO AI    │                      │
│  │   (RAW)      │    │  (Gemini)    │                      │
│  │              │    │              │                      │
│  └──────────────┘    └──────────────┘                      │
│         FRONT                FRONT                         │
│                                                             │
│  ┌──────────────┐    ┌──────────────┐                      │
│  │              │    │              │                      │
│  │  FOTO ORIG.  │    │   FOTO AI    │                      │
│  │   (RAW)      │    │  (Gemini)    │                      │
│  │              │    │              │                      │
│  └──────────────┘    └──────────────┘                      │
│         BACK                 BACK                          │
│                                                             │
│  Versioni: [v1]                                            │
│                                                             │
│  [Rigenera Foto ▼]  [Rigenera Testi]         [Approva ✅]  │
│                                                             │
│  [◀ Precedente]                          [Successivo ▶]   │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

**Legenda:**
- **FOTO ORIG. (RAW):** la foto che abbiamo scattato noi (originale)
- **FOTO AI (Gemini):** la foto rielaborata dall'intelligenza artificiale
- **Versioni [v1], [v2]...:** se il prodotto è stato rigenerato più volte, puoi vedere le versioni precedenti
- **Rigenerazioni: 0:** quante volte è stato rigenerato questo prodotto
- **Mode: MANI:** la modalità AI usata (MANI = su manichino)

---

## Le 4 azioni disponibili

### ✅ Approva

Usa quando la foto AI è **buona** e pronta per essere pubblicata su Vinted.

**Cosa succede:** il prodotto passa direttamente alla fase di export — W5 lo includerà nel prossimo CSV da caricare su Vinted.

**Shortcut:** premi `A` sulla tastiera.

---

### 🔄 Rigenera Foto

Usa quando la foto AI ha problemi visivi (colori sbagliati, distorsioni, artefatti).

Cliccando il bottone appare un menu:
- **Front Only** — rigenera solo la foto frontale
- **Back Only** — rigenera solo il retro
- **Both** — rigenera entrambe

**Cosa succede:** il sistema AI rigenera le foto entro i prossimi 5 minuti. Il prodotto tornerà in coda per il tuo controllo con una nuova versione (v2, v3...).

**Shortcut:** premi `R` sulla tastiera.

> Limite raccomandato: max 3 rigenerazioni per prodotto. Se dopo 3 tentativi la foto non va bene, segnala a Federico.

---

### ✎ Rigenera Testi

Usa quando i **testi del prodotto** (titolo, descrizione) sono sbagliati o mancanti.

> Attenzione: questa azione fa rielaborare il prodotto da zero, incluse le foto AI.
> Usare solo se i testi sono davvero da correggere.

**Cosa succede:** il sistema rigenera i testi e le foto AI da capo. Il prodotto tornerà in coda per il tuo controllo.

**Shortcut:** premi `T` sulla tastiera.

---

### ⏸ Defer (Rimanda)

Usa quando non sei sicura e vuoi rivedere il prodotto più tardi.

**Cosa succede:** il prodotto viene saltato e mostrato alla fine della coda corrente. Non tocca nulla in Airtable.

**Shortcut:** premi `D` sulla tastiera.

---

## Cosa guardare nelle foto AI

### Approva ✅ se:

| Cosa guardare | Criterio |
|---------------|----------|
| **Colore** | Il colore del capo è lo stesso dell'originale |
| **Forma** | Il capo ha la forma corretta, non è distorto |
| **Dettagli** | Logo, cuciture, stampe sono visibili |
| **Completezza** | Il capo è interamente visibile, non tagliato |
| **Background** | Sfondo pulito, nessun elemento strano |
| **Manichino** | Proporzioni naturali, nessuna distorsione |

### Rigenera 🔄 se:

| Problema | Esempio |
|----------|---------|
| Colore sbagliato | Giubbotto rosso → AI lo mostra blu |
| Logo sparito | Marchio non più visibile |
| Distorsioni | Manichino con braccia storte |
| Capo tagliato | La parte bassa del vestito non si vede |
| Sfondo sporco | Ombre strane, oggetti in background |
| Artefatti AI | Parti inventate (es. bottoni che non ci sono) |

---

## Scorciatoie da tastiera (Cheatsheet)

```
A → Approva
R → Rigenera foto (apre menu)
T → Rigenera testi
D → Defer (rimanda)
→ → Prodotto successivo
← → Prodotto precedente
F → Mostra/nascondi foto Front
B → Mostra/nascondi foto Back
1-9 → Seleziona versione (v1, v2...)
Esc → Chiudi menu/popup
```

---

## Indicatori di stato in alto

```
Reviewing: 12 of 47
```

- **12:** prodotto che stai guardando ora
- **47:** totale prodotti in coda per revisione

---

## Cosa succede dopo che approvi

1. Il prodotto passa a stato `LISTING_READY`
2. Rimane in attesa del prossimo export Vinted
3. Federico esegue W5 per generare il file CSV da caricare su Vinted

---

## Problemi comuni

### Non si carica nessun prodotto

Significa che non ci sono foto AI da revisionare in questo momento. Il sistema processa nuove foto ogni 5 minuti — riprova tra poco.

### Le foto non si vedono (quadrato grigio)

Le foto sono su Google Drive. Se vedi un quadrato grigio:
1. Prova ad aggiornare la pagina
2. Se persiste, le foto potrebbero non essere ancora state caricate da W3

### Il bottone "Approva" è disabilitato (grigio)

Il bottone si attiva solo quando:
- Almeno una foto AI è caricata
- I testi del prodotto (titolo, descrizione) sono presenti

Se è disabilitato, il prodotto manca di qualcosa — usa "Rigenera Testi" per farlo rielaborare.

### La webapp non risponde dopo un'azione

Aggiorna la pagina — il token è ancora salvato e non devi reinserirlo.

---

## Contatti

Per problemi tecnici: **Federico** (Federico Luigi D'Orsi)

---

*Versione guida: 1.0 | DirtyTag 3.5 | Marzo 2026*
