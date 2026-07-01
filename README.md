# SecretarIA
AI Medical Studio Triage
# SecretarIA

Assistente virtuale AI per il triage e la gestione del primo contatto pazienti in uno studio medico, via Telegram.

> **Stato: Proof of Concept.** Questo progetto è stato sviluppato come caso di studio pratico per la certificazione APF AI Business Specialist e come esercizio di progettazione di un sistema AI-agentico end-to-end. Non è un prodotto medico certificato e non sostituisce alcun sistema di gestione clinica esistente.

## Cos'è

SecretarIA gestisce il primo contatto tra paziente e studio medico su Telegram: risponde a domande generali, raccoglie i dati di una richiesta (prenotazione, informazioni, sintomo), classifica l'urgenza clinica secondo linee guida configurabili, e genera un ticket per la segreteria — senza mai eseguire autonomamente un'azione clinica (nessuna prenotazione automatica, nessuna diagnosi, nessuna decisione medica).

## Architettura

```
Paziente (Telegram)
      │
      ▼
n8n — Triage Bot (Google Gemini)
      │
      ├─► Trascrizione audio (se messaggio vocale)
      ├─► Knowledge Base dinamica (Google Sheets, alimentata da Drive: TXT/PDF/DOCX)
      ├─► Classificazione urgenza (ROSSO / GIALLO / VERDE)
      │
      ▼
Google Sheets (log + ticket)
      │
      ▼
n8n — API Ticket (endpoint autenticato)
      │
      ▼
Dashboard web (triage board per la segreteria)
```

Tre workflow n8n indipendenti:
- **Triage Bot Gemini** — gestisce la conversazione, il triage, la generazione ticket
- **API Ticket (Dashboard)** — espone i ticket alla dashboard via endpoint autenticato
- **KB Update** — aggiorna la Knowledge Base leggendo i documenti da Google Drive

## Funzionalità

- Conversazione multilingua (italiano, inglese, tedesco, francese)
- Triage clinico a 3 livelli con linee guida configurabili via Knowledge Base
- **Messaggi vocali**: trascrizione automatica via Gemini, con richiesta di ripetizione esplicita se l'audio non è comprensibile
- **Knowledge Base multi-formato**: lettura automatica di TXT, PDF e DOCX da una cartella Google Drive, con skip esplicito (loggato) dei formati non supportati
- Dashboard web in stile kanban per la segreteria, con drag & drop tra stati (da gestire / in attesa / gestito)
- Log completo di ogni interazione (testo o audio) su Google Sheets

## Sicurezza e robustezza

Alcune scelte progettuali pensate per reggere a uno scrutinio tecnico serio, non solo a un demo felice:

- **Autenticazione reale sull'endpoint dashboard**: verifica del token lato server (fail-closed — senza token configurato, l'endpoint rifiuta tutte le richieste), non solo dichiarata
- **Resistenza a prompt injection**: il system prompt include istruzioni anti-manipolazione esplicite, più una validazione indipendente lato codice sull'urgenza dichiarata dal modello (whitelist ROSSO/GIALLO/VERDE; valori anomali vengono declassati cautelativamente a GIALLO per revisione umana, mai a VERDE)
- **Human-in-the-loop**: nessuna azione clinica è mai eseguita in autonomia dal sistema — solo raccolta dati e classificazione, sempre soggette a revisione della segreteria
- **Gestione errori centralizzata**: qualunque fallimento (API AI non raggiungibile, timeout, errore di scrittura) confluisce in un messaggio di fallback esplicito al paziente — mai una richiesta lasciata senza risposta
- **Resilienza**: se la notifica push alla dashboard fallisce, il ticket è comunque già stato scritto sul foglio di log — nessuna perdita di dati paziente

## Limiti noti (dichiarati, non nascosti)

Coerentemente con lo stato di PoC, questi limiti sono espliciti e non risolti in questa versione:

- OCR su PDF scansionati (solo immagine, senza testo estraibile) non incluso — file skippato con log esplicito
- OCR su immagini (JPG/PNG) non incluso — fuori scope per questo PoC
- Dati paziente conservati in chiaro su Google Sheets: adeguato per un PoC interno, non per un ambiente di produzione multi-tenant o soggetto a normative sanitarie stringenti
- Nessuna suite di test automatizzati/regressione sul comportamento del modello di triage
- Nessuna gestione multi-studio/multi-tenant: configurazione pensata per un singolo studio medico

## Stack tecnico

Telegram Bot API · n8n (self-hosted, Docker) · Google Gemini (`gemini-2.5-flash`) · Google Sheets · Google Drive API · HTML/Tailwind/Chart.js/SortableJS (dashboard) · Cloudflare Tunnel (esposizione endpoint)

## Setup

I workflow sono esportati come JSON con placeholder al posto di ogni identificatore reale (ID Google Sheet, credenziali, dominio). Per eseguire il progetto:

1. Importa i 3 file `.json` in una propria istanza n8n
2. Crea un Google Sheet con i tab `TICKET`, `LOG`, `KB_CACHE` e collega le proprie credenziali OAuth (Google Sheets, Google Drive, Telegram, Gemini)
3. Configura la variabile d'ambiente `DASHBOARD_SECRET_TOKEN` in n8n (Settings → Variables) con un valore casuale sicuro
4. Aggiorna `N8N_GET_WEBHOOK_URL` e `N8N_API_TOKEN` nel file HTML della dashboard con i propri valori
5. Dettagli passo-passo sono nei campi `_SETUP` di ciascun file JSON

## Contesto

Sviluppato come progetto finale per la certificazione APF AI Business Specialist (DigitalStrategies Academy Lugano), nel contesto di un ruolo di coordinamento operativo e trasformazione digitale in ambito sanitario.

## Licenza

Codice condiviso a scopo dimostrativo/portfolio. Nessuna garanzia di idoneità a un uso clinico reale.
