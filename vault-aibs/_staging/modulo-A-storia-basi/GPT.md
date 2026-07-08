---
tags: [aibs, storia-ai]
---
# GPT

GPT è un modello basato sull'architettura [[Transformer]], che utilizza in particolare il blocco [[Encoder vs Decoder|Decoder]]. A differenza di [[BERT]], GPT è autoregressivo e generativo: predice il token successivo guardando quelli già generati, ed è pensato per la generazione testuale libera.

ChatGPT, basato su GPT, viene addestrato attraverso tre fasi principali (si veda [[Addestramento di ChatGPT]]): pre-training, fine-tuning supervisionato (SFT) e [[RLHF]]. ChatGPT non apprende in tempo reale dagli utenti, ma può essere aggiornato offline con nuovi cicli di training.

Ogni versione di GPT ha un limite massimo di token gestibili in una singola richiesta (si veda [[Tokenizzazione]]): GPT 3.5 = 4.096 token, GPT 4 = 32.000 token, GPT-5 = 128.000 token.

Collegamenti: [[Transformer]], [[Encoder vs Decoder]], [[BERT]], [[Addestramento di ChatGPT]], [[Tokenizzazione]]
