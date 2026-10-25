# SecretarIA — Briefing Stato PoC (per nuova sessione Claude)

> Documento di handover. Descrive lo stato attuale del progetto SecretarIA al 2026-10-25.
> Autore: generato da Claude Code nella sessione di upgrade v2.

---

## 1. Cos'è SecretarIA

Bot Telegram per la gestione del primo contatto paziente di uno studio oftalmologico svizzero (Ticino). Il sistema:

- Riceve messaggi testuali e vocali via Telegram
- Usa Google Gemini AI (via n8n AI Agent) per fare triage clinico e raccogliere dati
- Classifica ogni richiesta con urgenza ROSSO / GIALLO / VERDE
- Scrive un ticket strutturato su Google Sheets
- Notifica la segreteria tramite webhook → dashboard web
- NON prenota in autonomia, NON fa diagnosi (human-in-the-loop by design)

**Stack:** Telegram Bot API → n8n (self-hosted) → Google Gemini 2.5 Flash → Google Sheets → dashboard HTML statica con auth token

---

## 2. Struttura del Repository

```
SecretarIA/
├── SecretarIA___Triage_Bot_Gemini.json      # Workflow principale n8n
├── SecretarIA_API_Ticket.json               # Endpoint API dashboard /tickets
├── secretaria_kb_update_workflow.json       # Workflow aggiornamento Knowledge Base
├── SecretarIA_-_Triage.html                 # Dashboard web segreteria
├── secretarIA_system_prompt.md              # Documentazione prompt di produzione
└── docs/
    ├── briefing-stato-poc.md                # Questo file
    ├── robustness-status.md                 # Stato per-funzionalità (FATTO / NON TESTATO)
    ├── test-cases.md                        # 15 casi di test manuali TC-01..TC-15
    └── LPD-data-protection.md              # Dichiarazione conformità LPD svizzera
```

**Repository GitHub:** `erindsulejmani/SecretarIA`
**Branch attivo:** `claude/secretaria-robustness-upgrade-8jbvm7`
**Commit:** singolo squash commit `f42f887 feat: SecretarIA robustness upgrade v2 (sanitized)`

---

## 3. Cosa è Stato Implementato nell'Upgrade v2

### A. KB Dinamica Multi-Formato (`secretaria_kb_update_workflow.json`)

| Funzionalità | Stato |
|-------------|-------|
| Lettura TXT da Google Drive | FATTO E VERIFICATO |
| Estrazione testo da PDF | IMPLEMENTATO, NON TESTATO |
| Estrazione testo da DOCX | IMPLEMENTATO, NON TESTATO |
| Skip file non supportati con log | IMPLEMENTATO, NON TESTATO |
| Smistamento per mimeType (Switch + fallback) | IMPLEMENTATO, NON TESTATO |
| Merge risultati multi-branch (4 input) | IMPLEMENTATO, NON TESTATO |
| Report file saltati (count + motivo) | IMPLEMENTATO, NON TESTATO |

### B. Supporto Messaggi Vocali (`SecretarIA___Triage_Bot_Gemini.json`)

| Funzionalità | Stato |
|-------------|-------|
| Intercettazione voice message Telegram | IMPLEMENTATO, NON TESTATO |
| Download + trascrizione via Gemini API (audio nativo) | IMPLEMENTATO, NON TESTATO |
| Gestione audio incomprensibile / troppo corto | IMPLEMENTATO, NON TESTATO |
| Errore trascrizione → fallback al paziente | IMPLEMENTATO, NON TESTATO |
| Log `inputType: AUDIO` su Sheets | IMPLEMENTATO, NON TESTATO |

### C. Robustezza Architetturale

| Funzionalità | Stato |
|-------------|-------|
| Human-in-the-loop (nessuna azione autonoma) | FATTO E VERIFICATO |
| Anti-prompt-injection (livello prompt) | IMPLEMENTATO, NON TESTATO |
| Whitelist URGENZA lato codice (ROSSO/GIALLO/VERDE) | IMPLEMENTATO, NON TESTATO |
| Strip ticket falsi dal testo paziente (regex globale) | IMPLEMENTATO, NON TESTATO |
| Cap 500 char su ogni campo ticket | IMPLEMENTATO, NON TESTATO |
| `validationFlag` nel log se URGENZA modificata | IMPLEMENTATO, NON TESTATO |
| `onError: continueErrorOutput` su AI Agent | IMPLEMENTATO, NON TESTATO |
| Messaggio fallback al paziente per errore Gemini | IMPLEMENTATO, NON TESTATO |
| `onError` su scrittura Sheets + webhook dashboard | IMPLEMENTATO, NON TESTATO |
| **Auth endpoint /tickets (token + fail-closed)** | **FATTO E VERIFICATO (logica)** |
| 401 senza dati per token errato/mancante | FATTO E VERIFICATO (logica) |

---

## 4. Regole di Sicurezza — Non Negoziabili

**1. `DASHBOARD_SECRET_TOKEN`** non compare mai in nessun file del repository.
Vive SOLO in: n8n Settings > Variables (in produzione) e nel file HTML locale dell'utente.

**2. `N8N_API_TOKEN`** nella dashboard HTML è il placeholder `SOSTITUISCI_CON_TOKEN_SICURO` su GitHub.
L'utente deve sostituirlo localmente prima di usare la dashboard.

**3. Anonimizzazione dati paziente:** NON ancora eseguita. I dati reali del cliente
(nome studio, dottoressa, dati demo) sono stati lasciati intenzionalmente.
È uno step separato successivo, esplicito vincolo del cliente.

**4. Sanitizzazione già eseguita (GitHub):** tutti i riferimenti a:
- URL n8n reale → `https://your-n8n-instance.example.com`
- Google Sheet ID reale → `YOUR_GOOGLE_SHEET_ID`
- ID cartella Google Drive reale → `INSERISCI_ID_CARTELLA_DRIVE`
- UUID webhook reali → `YOUR_WEBHOOK_UUID`
- Credential ID n8n reali → `YOUR_CREDENTIAL_ID`
...sono stati sostituiti con placeholder nei 3 workflow JSON e nella dashboard HTML.

---

## 5. Setup Richiesto per Funzionare (Azioni Manuali Utente)

1. **n8n Variables:** creare `DASHBOARD_SECRET_TOKEN` con un token sicuro generato (es. `openssl rand -hex 32`)
2. **Dashboard HTML locale:** sostituire `SOSTITUISCI_CON_TOKEN_SICURO` con lo stesso token
3. **Dashboard HTML locale:** sostituire `https://your-n8n-instance.example.com/webhook/tickets` con l'URL reale del proprio n8n
4. **KB workflow:** sostituire `INSERISCI_ID_CARTELLA_DRIVE` con l'ID reale della cartella Google Drive
5. **Tutti i workflow JSON:** re-linkare le credenziali n8n (Google Sheets, Google Drive, Telegram, Google Gemini) tramite UI n8n dopo l'importazione
6. **Google Sheets:** verificare che la riga 2 di KB_CACHE esista prima del primo run del workflow KB

---

## 6. Cosa Manca Prima della Produzione

### Bloccante (LPD / legale)
- [ ] Stipulare DPA con Google (Gemini + Sheets trattano dati pazienti in USA)
- [ ] Implementare policy di retention (workflow n8n schedulato di pulizia, es. 90 giorni)

### Testing
- [ ] Eseguire manualmente i 15 casi di test TC-01..TC-15 (`docs/test-cases.md`)
- [ ] Testare endpoint `/tickets` con token corretto e token errato
- [ ] Testare il flusso audio (voice message Telegram)
- [ ] Testare l'importazione KB con PDF e DOCX

### Anonimizzazione (step separato concordato)
- [ ] Sostituire nome reale studio, dottoressa e dati demo con placeholder/fittizi nella KB, nel system prompt e nella dashboard

### Opzionale / Post-PoC
- [ ] Proxy server-side per il token dashboard (se la dashboard va pubblica online)
- [ ] Comando Telegram `/cancella_dati` per esercizio diritto all'oblio (LPD art. 25)
- [ ] Integrazione sistema prenotazioni (Doctolib o equivalente)

---

## 7. Architettura del Workflow Principale (Triage Bot)

```
Telegram message in
    │
    ├─ Testo → 02 · Estrai Dati Messaggio
    │
    └─ Audio → 01c Scarica → 01d Prepara → 01e Trascrivi Gemini
                   │                              │
                   │                      onError → 17 Gestione Errori → 18 Invia Errore
                   │
               01f Estrai Trascrizione → 01g Audio OK?
                   TRUE → 02 · Estrai Dati Messaggio
                   FALSE → 01h Chiedi di Ripetere

02 · Estrai Dati → 03 · Merge KB + Dati → 09 · AI Agent (Gemini)
                                                  │
                                          onError → 17 → 18

09 output → 10 · Analizza Risposta (strip ticket, whitelist URGENZA, cap 500)
    │
    ├─ 11 · Invia Risposta Telegram (testo senza ticket)
    ├─ 12 · Log su Google Sheets
    ├─ 14 · Scrivi Ticket su Sheets ──onError──┐
    └─ 16 · Webhook Dashboard ────────onError──┴→ 17 → 18
```

---

## 8. Nodo Critico: `10 · Analizza Risposta`

Logica centrale di sicurezza (Code node, `runOnceForEachItem`):

```javascript
// 1. Strip ticket falsi iniettati nel testo
const ticketRegex = /<<<TICKET_START>>>[\s\S]*?<<<TICKET_END>>>/g;
patientReply = patientReply.replace(ticketRegex, '').trim();

// 2. Estrai blocco ticket reale
const match = fullResponse.match(/<<<TICKET_START>>>([\s\S]*?)<<<TICKET_END>>>/);

// 3. Whitelist URGENZA
const ALLOWED_URGENZA = ['ROSSO', 'GIALLO', 'VERDE'];
if (!ALLOWED_URGENZA.includes(urgenza)) {
  urgenza = 'GIALLO';
  validationFlag = true;
}

// 4. Cap campi a 500 char
function cap(s) { return s.length > 500 ? s.substring(0, 497) + '...' : s; }
```

---

## 9. Branch e Prossimi Passi Git

Il repository ha:
- Branch `claude/secretaria-robustness-upgrade-8jbvm7` (unico branch, 1 commit)
- Nessun branch `main`

Se si vuole creare una PR o rinominare come default branch, va fatto manualmente su GitHub o tramite `gh` CLI / MCP GitHub tools.

---

*Briefing generato il 2026-10-25. Per dettagli implementativi completi vedere `docs/robustness-status.md`.*
