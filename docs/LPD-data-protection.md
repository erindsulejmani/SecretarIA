# SecretarIA — Protezione Dati (LPD Svizzera)

> Documento di dichiarazione e mitigazione per la legge federale svizzera sulla protezione dei dati (LPD / nLPD, in vigore dal 1° settembre 2023).
> Questo documento è parte del PoC SecretarIA e riflette lo stato tecnico attuale del sistema.

---

## 1. Responsabile del Trattamento

**Studio Medico Esempio**, Locarno (TI), Svizzera.

Il sistema SecretarIA è uno strumento di supporto interno; il responsabile del trattamento rimane lo studio medico.

---

## 2. Dati Personali Trattati

| Dato | Fonte | Dove viene memorizzato | Necessario per |
|------|-------|------------------------|----------------|
| Nome e cognome | Paziente (Telegram) | Google Sheets (tab TICKET, LOG) | Identificare il paziente per il ricontatto |
| Data di nascita | Paziente (Telegram) | Google Sheets (tab TICKET, LOG) | Protocolo triage (verificare maggiore età / plausibilità) |
| Numero di telefono | Paziente (Telegram) | Google Sheets (tab TICKET, LOG) | Ricontatto da parte della segreteria |
| Motivo del contatto / sintomi | Paziente (Telegram) | Google Sheets (tab TICKET, LOG) | Triage clinico e instradamento alla segreteria |
| Chat ID Telegram | Automatico | Google Sheets (tab TICKET, LOG) | Continuità della sessione di conversazione |
| Trascrizione vocale | Microfono paziente → Gemini API | Non memorizzata separatamente; il testo trascritto è trattato come testo normale | Accessibilità (input voce) |

**Non vengono raccolti:** dati biometrici, immagini mediche, documenti, dati di pagamento.

---

## 3. Finalità del Trattamento

- Rispondere alle richieste di primo contatto dei pazienti (triage, informazioni, prenotazioni).
- Trasmettere le richieste alla segreteria dello studio per il ricontatto.
- Audit interno delle interazioni (tab LOG).

---

## 4. Base Giuridica

Il trattamento si basa sul **consenso implicito** del paziente che contatta volontariamente il bot, unitamente alla **necessità contrattuale** (presa in carico della richiesta medica di primo livello).

Il sistema prompt ricorda al paziente l'informativa privacy (LPD) come indicato nella Knowledge Base dello studio — l'esatta formulazione è gestita dalla segreteria e inserita nella KB.

---

## 5. Terze Parti e Trasferimenti Internazionali

| Servizio | Fornitore | Paese | Dati trasmessi | Misura di mitigazione |
|----------|-----------|-------|----------------|----------------------|
| Telegram Bot API | Telegram FZ-LLC | Emirati Arabi / server UE | Messaggi di testo, file audio | Telegram ToS; dati in transito TLS; il paziente usa Telegram consapevolmente |
| Google Gemini API | Google LLC | USA (con server EU opzionali) | Testo del messaggio / trascrizione, KB | **Dichiarata limitazione** — vedi §6 |
| Google Sheets | Google LLC | USA (con server EU opzionali) | Tutti i dati del ticket | **Dichiarata limitazione** — vedi §6 |
| n8n (self-hosted) | Infrastruttura dello studio | Server VPS Erind / CH | Tutti i dati in transito | Self-hosted, sotto controllo diretto; raccomandato hosting CH/UE |

---

## 6. Limitazioni Dichiarate e Piano di Mitigazione

### 6.1 Trasferimento dati a Google (USA) — Limitazione principale

**Stato attuale (PoC):** I dati dei pazienti (nome, telefono, sintomi) vengono trasmessi alle API Google (Gemini, Sheets) che operano primariamente in USA senza clausole contrattuali standard (SCC) esplicitamente configurate per questo PoC.

**Rischio LPD:** Trasferimento internazionale a paese senza adeguato livello di protezione (art. 16-17 nLPD) senza garanzie sufficienti.

**Piano di mitigazione (priorità per messa in produzione):**
1. Stipulare un Data Processing Agreement (DPA) con Google (disponibile tramite Google Workspace for Business / Google Cloud).
2. Attivare la residenza dati UE/CH per Google Workspace e Vertex AI (Gemini Enterprise).
3. In alternativa: sostituire Gemini con un modello LLM self-hosted (es. Ollama su VPS CH) per eliminare il trasferimento internazionale.
4. Valutare pseudonimizzazione dei dati prima della trasmissione a Gemini (es. sostituire nome/telefono con ID interno nel prompt, mappare offline).

### 6.2 Retention dei dati — Limitazione secondaria

**Stato attuale:** I dati rimangono nel Google Sheet indefinitamente senza cancellazione automatica.

**Piano di mitigazione:**
1. Implementare una policy di retention (es. 90 giorni per i ticket, 12 mesi per i log di audit).
2. Aggiungere un workflow n8n schedulato che archivia/cancella righe più vecchie del periodo di retention.

### 6.3 Accesso al Google Sheet — Limitazione secondaria

**Stato attuale:** L'accesso al Sheet è limitato all'account Google configurato, ma non è applicato un controllo degli accessi granulare.

**Piano di mitigazione:**
1. Limitare la condivisione del Sheet allo stretto necessario (solo account segreteria + account n8n service account).
2. Abilitare audit log di accesso su Google Workspace.

### 6.4 Endpoint dashboard `/tickets` — RISOLTO

**Stato precedente:** Endpoint pubblico senza autenticazione (dati paziente accessibili a chiunque conoscesse l'URL).

**Stato attuale:** Risolto nel workflow `SecretarIA_API_Ticket.json` — autenticazione tramite token segreto (`DASHBOARD_SECRET_TOKEN` via variabile d'ambiente n8n); fail-closed se variabile non configurata; 401 senza dati per token mancante/errato.

---

## 7. Misure Tecniche e Organizzative Attuali

- **Accesso dashboard:** autenticato via token (vedi §6.4).
- **Trasmissione:** tutte le comunicazioni avvengono su HTTPS/TLS (Telegram, Google API, n8n webhook).
- **Dati in chiaro nel prompt:** il testo del paziente viene incluso nel prompt Gemini in forma non pseudonimizzata — dichiarata limitazione per questo PoC.
- **Audit log:** ogni interazione registrata nel tab LOG con timestamp, chatId, inputType (TESTO/AUDIO), e flag di validazione.
- **Human-in-the-loop:** il bot NON prenota, NON invia risposte mediche definitive, NON agisce oltre la notifica alla segreteria — ogni azione clinica è demandata a personale umano qualificato.

---

## 8. Diritti degli Interessati

I pazienti hanno diritto a:
- Accesso ai propri dati (art. 25 nLPD)
- Rettifica
- Cancellazione ("diritto all'oblio")
- Portabilità

**Processo attuale:** le richieste devono essere indirizzate direttamente alla segreteria dello studio, che ha accesso al Google Sheet e può eseguire le operazioni manualmente.

**Miglioramento consigliato (post-PoC):** implementare un comando Telegram `/cancella_dati` che attivi un workflow n8n di cancellazione delle righe associate al chatId richiedente.

---

## 9. Valutazione del Rischio (Sintesi)

| Rischio | Probabilità | Impatto | Mitigazione attuale | Residuo |
|---------|------------|---------|---------------------|---------|
| Accesso non autorizzato ai ticket | Bassa (auth implementata) | Alto | Token auth + fail-closed | Basso |
| Trasferimento dati a Google USA | Presente | Medio | DPA da stipulare | Medio |
| Retention indefinita | Presente | Medio | Policy da implementare | Medio |
| Prompt injection | Bassa (doppio livello) | Medio | Prompt + whitelist codice | Basso |
| Perdita disponibilità Gemini | Media | Basso | Fallback messaggio al paziente | Basso |

**Conclusione:** Questo PoC è adeguato per la fase di sviluppo e dimostrazione. Prima della messa in produzione su pazienti reali, i rischi classificati "Medio" nel piano di mitigazione devono essere risolti.

---

*Documento generato nell'ambito del progetto SecretarIA — Upgrade v2. Ultima revisione: 2026-06-30.*
