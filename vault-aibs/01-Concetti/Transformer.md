---
tags: [aibs, storia-ai]
---
# Transformer

Il Transformer è un'architettura di rete neurale introdotta da Google nel 2017, descritta nel paper di ricerca "Attention is All You Need". È particolarmente efficace nell'elaborazione di sequenze di dati, come il testo, e ha rivoluzionato il NLP (Natural Language Processing).

Le principali caratteristiche del Transformer sono:
- Elaborazione parallela: elabora le sequenze (parole, frasi) in parallelo, e non sequenzialmente come facevano le precedenti architetture RNN o LSTM (risultando quindi più veloce e accurato).
- [[Self-Attention]]: usa il meccanismo di self-attention per "pesare" le parole in base al contesto.
- È una rete neurale profonda (DL = Deep Learning) e si parla anche di LLM (Large Language Model).
- È la base dei modelli moderni: [[GPT]], [[BERT]], T5, Claude, Gemini.

Il Transformer è internamente composto da blocchi [[Encoder vs Decoder|Encoder e/o Decoder]]: l'Encoder è usato in BERT, il Decoder in GPT, mentre la combinazione Encoder-Decoder è usata in modelli come T5 per compiti di traduzione e riscrittura del testo.

Collegamenti: [[Self-Attention]], [[Encoder vs Decoder]], [[BERT]], [[GPT]], [[NLP]], [[Rete Neurale]]
