---
tags: [aibs, storia-ai]
---
# Encoder vs Decoder

Comprendere ad alto livello cos'è un Encoder e un Decoder permette di capire molto di ciò che avviene in strumenti che usiamo giornalmente. Sono i due blocchi principali che compongono l'architettura [[Transformer]], insieme al meccanismo di [[Self-Attention]].

1. Encoder: ha lo scopo di comprendere il testo (input). È usato nel modello [[BERT]].
2. Decoder: ha lo scopo di generare il testo in uscita (output). È usato nel modello [[GPT]].
3. Encoder-Decoder: ha lo scopo di tradurre/riscrivere il testo. È usato nel modello T5, per compiti di traduzione e riscrittura.

Ogni blocco riceve come input i vettori generati dalla self-attention, li trasforma attraverso strati di neuroni e produce un vettore aggiornato che rappresenta meglio il significato del token nel contesto.

Collegamenti: [[Transformer]], [[Self-Attention]], [[BERT]], [[GPT]]
