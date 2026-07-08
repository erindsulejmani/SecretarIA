---
tags: [aibs, fondamenta-ml]
---
# Come Funziona un Sistema RAG

Un sistema [[RAG (Retrieval-Augmented Generation)]] funziona come un "ponte" tra un testo generato da un LLM e informazioni specifiche ed aggiornate provenienti da fonti esterne, attraverso due fasi:

**Fase 1: recupero (retrieval)**
- Quando riceve una domanda (un "prompt"), il sistema RAG non si affida solo alla sua memoria interna.
- Utilizza algoritmi di ricerca avanzati per recuperare informazioni pertinenti da una base di conoscenza esterna: può essere un database aziendale, documenti specifici (PDF, file HTML), pagine web, articoli scientifici, o qualsiasi altra fonte di dati affidabile.
- Queste informazioni vengono convertite in rappresentazioni numeriche (chiamate "embedding") e archiviate in un database vettoriale, che permette una ricerca rapida basata sulla somiglianza semantica.

**Fase 2: generazione (generation)**
- Una volta recuperate le informazioni più rilevanti, queste vengono fornite all'LLM insieme alla domanda originale.
- L'LLM utilizza queste informazioni "aumentate" (cioè integrate con i dati esterni) per generare una risposta. In questo modo, la risposta non si basa solo sulla conoscenza pre-esistente del modello, ma è "ancorata" a dati specifici e verificabili.

Questo meccanismo permette di aggiornare la conoscenza del sistema semplicemente aggiornando la base di conoscenza esterna, senza dover riaddestrare l'intero LLM (vedi i vantaggi economici in [[Vantaggi del RAG]]).

Collegamenti: [[RAG (Retrieval-Augmented Generation)]], [[Vantaggi del RAG]]
