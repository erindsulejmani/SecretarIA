---
tags: [aibs, storia-ai]
---
# Addestramento di ChatGPT

L'addestramento di ChatGPT (basato su [[GPT]]) avviene attraverso tre fasi principali:

1. Pre-training (non supervisionato): il modello viene addestrato su grandi corpus di testo, imparando a predire la parola successiva (capacità che deriva dall'architettura [[Transformer]]).
2. Fine-tuning supervisionato (SFT): il modello viene addestrato con esempi forniti da umani, su input-output specifici.
3. [[RLHF]] (Reinforcement Learning from Human Feedback): vengono date valutazioni umane alle risposte, e il modello apprende a preferire le risposte più utili e sicure.

Un punto importante evidenziato nelle slide: ChatGPT non apprende in tempo reale dagli utenti, ma può essere aggiornato offline con nuovi cicli di training. Questo distingue l'addestramento (fase iniziale in cui il modello impara dai dati, offline e in modo massivo) dall'apprendimento in senso più ampio (si veda [[Addestramento vs Apprendimento]]).

Collegamenti: [[GPT]], [[RLHF]], [[Addestramento vs Apprendimento]], [[Transformer]]
