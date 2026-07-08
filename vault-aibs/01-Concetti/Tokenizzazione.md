---
tags: [aibs, storia-ai]
---
# Tokenizzazione

La tokenizzazione è il processo che trasforma un testo in una sequenza di token numerici, ovvero vettori che costituiscono l'input per la [[Rete Neurale]], dato che il modello lavora con numeri e funzioni matematiche, non con il linguaggio naturale direttamente.

Un token è una unità minima di testo che un modello AI utilizza per comprendere e generare linguaggio. Un token può essere:
- Una parola intera (es. "casa")
- Parte di una parola (es. "inter" + "azione")
- Un simbolo (es. "?" o "!")

Un aspetto fondamentale: il modello AI predice il token successivo, non la parola intera.

In italiano, mediamente, una parola corrisponde a circa 1,2 token. Ogni modello ha un limite massimo di token che può gestire in una singola richiesta: ad esempio GPT 3.5 = 4.096 token, GPT 4 = 32.000 token, GPT-5 = 128.000 token.

Collegamenti: [[Rete Neurale]], [[GPT]], [[Transformer]]
