---
tags: [aibs, fondamenta-ml]
---
# Come Nasce una Risposta IA

Il deck spiega come un modello di [[Generative AI]] riesce a creare qualcosa di "nuovo", attraverso quattro elementi:

- **Generazione token per token**: il modello costruisce la risposta producendo singole unità (token) una dopo l'altra.
- **Probabilità e contesto**: ogni token è scelto in base alla probabilità statistica e al contesto del testo già generato.
- **Pattern linguistici**: l'AI non inventa, ma riorganizza e combina complessi pattern appresi da vasti dataset di testo (frutto del [[Pre-addestramento]]).
- **Output plausibile**: il risultato è un testo coerente e logicamente plausibile, che rispecchia gli schemi linguistici acquisiti.

Questo meccanismo spiega perché il modello, pur non "comprendendo" nel senso umano del termine, riesce comunque a produrre testi originali e pertinenti: non sta recuperando frasi già scritte, ma componendo nuove sequenze di token statisticamente coerenti con quanto appreso durante l'addestramento.

Lo stesso principio di generazione token per token è alla base di come il modello linguistico ragiona sui numeri (vedi [[Come il Modello Linguistico Ragiona sui Numeri]]) e di come, in certi casi, possano emergere imprecisioni (vedi [[Principio di Approssimazione]]).

Collegamenti: [[Generative AI]], [[Pre-addestramento]], [[Come il Modello Linguistico Ragiona sui Numeri]], [[Principio di Approssimazione]]
