---
tags: [aibs, fondamenta-ml]
---
# Processo di un Assistente IA Conversazionale

Il deck descrive i passi che un assistente AI conversazionale intraprende per fornire un output di qualità, contestuale al prompt ricevuto:

1. **Ricezione e pre elaborazione del prompt**: il testo del prompt viene raccolto dall'interfaccia e inoltrato al modello.
2. **Elaborazione mediante architettura del modello**: avviene la tokenizzazione (il testo viene suddiviso in unità più piccole) e l'encoding (le unità vengono convertite in numeri).
3. **Controlli di sicurezza ed allineamento**: recupero della cronologia, analisi dell'input, inferenza e filtraggio tematico.
4. **Adattamento al contesto e formattazione**: generazione dell'output e revisione per adattarlo al tono e allo stile del prompt.

Questo processo rende esplicito ciò che accade "dietro le quinte" ogni volta che un utente interagisce con un chatbot come ChatGPT o Claude: non è una risposta istantanea e magica, ma il risultato di una pipeline strutturata che parte dal testo grezzo e arriva a un output formattato e allineato alle richieste.

Il passaggio di tokenizzazione richiamato al punto 2 è coerente con il meccanismo descritto in [[Come Nasce una Risposta IA]], dove il modello genera la risposta token per token in base a probabilità e contesto.

Collegamenti: [[Generative AI]], [[Come Nasce una Risposta IA]], [[Prompt Engineering]]
