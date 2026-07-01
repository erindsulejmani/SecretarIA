# SecretarIA — Stato Robustezza e Funzionalità (Upgrade v2)

> Riepilogo delle funzionalità implementate nell'ambito dell'upgrade v2.
> Stato al: 2026-06-30

---

## Legenda

| Simbolo | Significato |
|---------|------------|
| FATTO E VERIFICATO | Implementato; logica verificata dalla revisione del codice; flow testato almeno una volta in esecuzione reale |
| IMPLEMENTATO, NON TESTATO | Codice scritto e validato sintatticamente; non ancora testato con esecuzione reale in n8n |
| DICHIARATA LIMITAZIONE | Funzionalità non implementata in questo PoC; limitazione esplicitamente documentata con piano di mitigazione |
| FUORI SCOPE | Esplicitamente escluso da questo PoC; non da implementare senza decisione separata |

---

## A. KB Dinamica Multi-Formato

Workflow: `secretaria_kb_update_workflow.json`

| Funzionalità | Stato | Note |
|-------------|-------|------|
| Lettura file TXT da Google Drive | FATTO E VERIFICATO | Nodo `05a · Estrai Testo TXT` — estrae via Buffer.from base64 |
| Estrazione testo da PDF | IMPLEMENTATO, NON TESTATO | Nodo `05b · Estrai Testo da PDF` (n8n `extractFromFile`, operation: pdf) + `05b · Normalizza Estratto PDF` |
| Estrazione testo da DOCX | IMPLEMENTATO, NON TESTATO | Nodo `05c · Estrai Testo da DOCX` (n8n `extractFromFile`, operation: docx) + normalizza. Richiede n8n >= 1.50 circa |
| Skip esplicito file non supportati (con log) | IMPLEMENTATO, NON TESTATO | Nodo `05d · Skip File Non Supportato` — logga fileName + mimeType, non crasha |
| Smistamento per mimeType | IMPLEMENTATO, NON TESTATO | Nodo `04 · Smista per Tipo File` (Switch con fallback) |
| Merge risultati multi-branch | IMPLEMENTATO, NON TESTATO | Nodo `06 · Unisci Risultati` (Merge, 4 input, mode: chooseBranchesMerge) |
| Report file saltati (count + motivo) | IMPLEMENTATO, NON TESTATO | Nodo `07 · Assembla Testo KB` — `skippedFiles[]` con reason per ogni file saltato |
| OCR su immagini (jpg/png) | DICHIARATA LIMITAZIONE | Fuori scope per questo PoC. Richiederebbe Google Vision API o Tesseract. File immagine → branch "non supportato" con log. |
| PDF scansionati (solo immagine) | DICHIARATA LIMITAZIONE | Nessun testo estraibile → loggato come skippato con motivo esplicito, non genera contenuto vuoto silenzioso in KB_CACHE |

---

## B. Supporto Messaggi Vocali

Workflow: `SecretarIA___Triage_Bot_Gemini.json`

| Funzionalità | Stato | Note |
|-------------|-------|------|
| Intercettazione voice message Telegram | IMPLEMENTATO, NON TESTATO | Nodo `01b · È un Messaggio Vocale?` (IF su `message.voice`) |
| Download file audio da Telegram | IMPLEMENTATO, NON TESTATO | Nodo `01c · Scarica Audio Telegram` (Telegram node, resource: file) |
| Trascrizione via Gemini API (audio nativo) | IMPLEMENTATO, NON TESTATO | Nodo `01e · Trascrivi con Gemini` (httpRequest a `generativelanguage.googleapis.com`, mime_type + base64) |
| Gestione audio incomprensibile / troppo corto | IMPLEMENTATO, NON TESTATO | Nodo `01g · Audio Comprensibile?` (IF) → `01h · Chiedi di Ripetere` (messaggio al paziente, nessun ticket) |
| Errore trascrizione Gemini (timeout / API down) | IMPLEMENTATO, NON TESTATO | `onError: continueErrorOutput` su `01e` → branch errore → `17 · Gestione Errori` → `18 · Invia Errore al Paziente` |
| Log `inputType: AUDIO` per audit | IMPLEMENTATO, NON TESTATO | Nodo `12 · Log su Google Sheets` — prefisso `[VOCALE]` nel campo Dettaglio |
| Tolleranza errori di trascrizione nel prompt | IMPLEMENTATO, NON TESTATO | System prompt: "se il testo sembra contenere errori di trascrizione plausibili, interpretalo con buon senso" |

---

## C. Robustezza Architetturale

Workflow: `SecretarIA___Triage_Bot_Gemini.json` + `SecretarIA_API_Ticket.json`

### C.1 Human-in-the-Loop

| Funzionalità | Stato | Note |
|-------------|-------|------|
| Nessuna azione autonoma oltre log/notifica | FATTO E VERIFICATO | Il bot raccoglie dati e notifica la segreteria; ogni azione clinica è demandata a personale umano. Documentato nel system prompt e nel nodo `AI Agent` (notes) |
| Nessuna prenotazione automatica | FATTO E VERIFICATO | Il system prompt esplicita: "NON prenoti appuntamenti in autonomia" |

### C.2 Prompt Injection Defense

| Funzionalità | Stato | Note |
|-------------|-------|------|
| Istruzioni anti-manipolazione nel system prompt | IMPLEMENTATO, NON TESTATO | Sezione `# SICUREZZA — ISTRUZIONI ANTI-MANIPOLAZIONE` con regole non negoziabili. Test: TC-05, TC-06 nel test set |
| Whitelist URGENZA lato codice (indipendente dall'LLM) | IMPLEMENTATO, NON TESTATO | Nodo `10 · Analizza Risposta`: `ALLOWED_URGENZA = ['ROSSO','GIALLO','VERDE']`; valori non in lista → downgrade a GIALLO + `validationFlag` |
| Strip globale blocchi ticket falsi nel testo paziente | IMPLEMENTATO, NON TESTATO | `10 · Analizza Risposta`: regex globale rimuove tutti i blocchi `<<<TICKET_START>>>...<<<TICKET_END>>>` dalla risposta paziente |
| Cap lunghezza campi ticket (500 char) | IMPLEMENTATO, NON TESTATO | `10 · Analizza Risposta`: troncamento con `...` su ogni campo estratto |
| `validationFlag` nel log se URGENZA modificata | IMPLEMENTATO, NON TESTATO | `12 · Log su Google Sheets`: prefisso `⚠️ VALIDAZIONE:` nel Dettaglio se flag presente |

### C.3 Resilienza API Gemini (down / rate limit)

| Funzionalità | Stato | Note |
|-------------|-------|------|
| `onError: continueErrorOutput` su nodo AI Agent | IMPLEMENTATO, NON TESTATO | Output errore → `17 · Gestione Errori` |
| Messaggio fallback al paziente in caso di errore Gemini | IMPLEMENTATO, NON TESTATO | Nodo `18 · Invia Errore al Paziente` — messaggio fisso rassicurante, invita a richiamare la segreteria |
| `onError` su scrittura ticket su Sheets | IMPLEMENTATO, NON TESTATO | Nodo `14 · Scrivi Ticket su Sheets` con `onError: continueErrorOutput` → `17` |
| `onError` su notifica dashboard (webhook) | IMPLEMENTATO, NON TESTATO | Nodo `16 · Webhook → Dashboard` con `onError: continueErrorOutput` → `17` |
| Gestione chatId mancante in Gestione Errori | IMPLEMENTATO, NON TESTATO | Nodo `17 · Gestione Errori`: try/catch con lookup a cascata da nodi upstream |

### C.4 Autenticazione Dashboard `/tickets`

| Funzionalità | Stato | Note |
|-------------|-------|------|
| Token auth endpoint GET /tickets | FATTO E VERIFICATO (logica) / NON TESTATO in produzione | `SecretarIA_API_Ticket.json`: nodo `02 · Verifica Token Autenticazione` legge `DASHBOARD_SECRET_TOKEN` da env n8n |
| Fail-closed se variabile env non configurata | FATTO E VERIFICATO (logica) | `throw new Error(...)` se `expected === ''`; nessuna richiesta passa senza variabile configurata |
| Risposta 401 senza dati per token errato/mancante | FATTO E VERIFICATO (logica) | Nodo `07 · Rispondi 401 Non Autorizzato`: `{error:'Non autorizzato', tickets:[], count:0}` |
| Header `X-SecretarIA-Token` nella dashboard HTML | FATTO E VERIFICATO (logica) | `SecretarIA_-_Triage.html`: costante `N8N_API_TOKEN` + header in `fetchNuoviTicket()` |
| Test con token errato in produzione | NON TESTATO | Da eseguire dopo deploy: `curl -i https://your-n8n-instance.example.com/webhook/tickets` deve restituire 401 |

### C.5 Protezione Dati (LPD Svizzera)

| Funzionalità | Stato | Note |
|-------------|-------|------|
| Documentazione limitazioni e piano mitigazione | FATTO E VERIFICATO | `docs/LPD-data-protection.md` |
| Trasferimento dati a Google USA | DICHIARATA LIMITAZIONE | DPA Google da stipulare prima di produzione su pazienti reali. Dettagli in LPD-data-protection.md §6.1 |
| Retention automatica dati | DICHIARATA LIMITAZIONE | Da implementare: workflow schedulato di pulizia. Dettagli in LPD-data-protection.md §6.2 |
| Informativa privacy nel system prompt | IMPLEMENTATO, NON TESTATO | Il bot ricorda l'informativa LPD come da KB — formulazione gestita dalla segreteria nella KB |

### C.6 Accuratezza Triage

| Funzionalità | Stato | Note |
|-------------|-------|------|
| Set di test (15 casi simulati) | FATTO E VERIFICATO (set creato) / NON TESTATO con bot reale | `docs/test-cases.md` — include 3 casi ROSSO, 1 GIALLO, 8+ VERDE, 2 injection, 1 minore, 1 lingua tedesca |
| Esecuzione manuale test set | NON TESTATO | Da eseguire con il bot attivo prima della pubblicazione |

---

## Fuori Scope (Esplicitamente Escluso da Questo PoC)

| Funzionalità | Motivazione |
|-------------|-------------|
| Scaling multi-clinica / multi-tenant | Richiederebbe una separazione delle KB, dei prompt e delle credenziali per clinica. Ogni clinica è un'installazione separata con il proprio n8n, Sheet e bot Telegram. Fuori scope per questo PoC mono-cliente. |
| OCR su immagini (jpg/png, PDF scansionati) | Richiederebbe Google Vision API o servizio OCR dedicato. Costo e complessità non giustificati per questo PoC. File saltati con log esplicito. |
| Prenotazioni automatiche su calendario | Human-in-the-loop è un requisito esplicito. Il bot NON prenota; la segreteria gestisce il calendario. |
| Diagnosi o consigli medici | Fuori scope per design (responsabilità medica). Il bot è strumento di primo contatto, non sostituto medico. |
| Cancellazione dati su richiesta paziente (GDPR/LPD workflow) | Da implementare post-PoC (comando `/cancella_dati`). Dichiarata limitazione in LPD-data-protection.md §8. |
| Integrazione con sistema di gestione appuntamenti (Doctolib, ecc.) | Post-PoC. Richiederebbe API dedicata. |

---

## Checklist Pre-Pubblicazione GitHub

- [ ] Anonimizzare/rimuovere dati reali pazienti dai file (PASSO SEPARATO — non fatto in questo upgrade)
- [ ] Configurare `DASHBOARD_SECRET_TOKEN` in n8n production e aggiornare `N8N_API_TOKEN` nella dashboard HTML
- [ ] Testare endpoint `/tickets` con e senza token
- [ ] Eseguire manualmente test set TC-01..TC-15
- [ ] Stipulare DPA con Google (o decidere alternativa self-hosted) prima di produzione con pazienti reali
- [ ] Sostituire `INSERISCI_ID_CARTELLA_DRIVE` in `secretaria_kb_update_workflow.json` con ID reale
- [ ] Verificare che la riga 2 di KB_CACHE esista prima del primo run del workflow KB

---

*Documento generato nell'ambito del progetto SecretarIA — Upgrade v2. Ultima revisione: 2026-06-30.*
