---
tags: [aibs, storia-ai]
---
# Self-Attention

La self-attention è il cuore del [[Transformer]]: permette al modello di pesare le parole in una frase per capire il testo, cioè di stabilire quali parole sono più rilevanti tra loro nel contesto.

Esempio dalle slide: nella frase "Il gatto mangia il topo", la parola "mangia" ha più attenzione su "gatto" (chi mangia) che su "topo" (chi viene mangiato).

Ogni blocco del Transformer riceve come input i vettori generati dalla self-attention, li trasforma attraverso strati di neuroni e produce un vettore aggiornato che rappresenta meglio il significato del token nel contesto. Il processo può essere autoregressivo, cioè predice la parola successiva guardando quella già generata.

In sostanza, l'attenzione alle parole/token, la trasformazione in vettori di significato e la normalizzazione degli stessi permettono di dare stabilità al modello e di migliorare l'apprendimento (training).

Collegamenti: [[Transformer]], [[Encoder vs Decoder]], [[Rete Neurale]]
