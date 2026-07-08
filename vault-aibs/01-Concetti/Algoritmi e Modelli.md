---
tags: [aibs, fondamenta-ml]
---
# Algoritmi e Modelli

Nel [[Machine Learning]] è importante distinguere due concetti spesso confusi:

**Algoritmi**: le procedure matematiche scritte con codice software che permettono di apprendere dai dati. Rappresentano il "come" dell'apprendimento. Esempi citati nelle slide:
- Alberi decisionali
- Regressione lineare
- Reti neurali semplici
- Clustering K-means

**Modelli**: il prodotto dell'apprendimento, cioè l'output dell'algoritmo dopo essere stato addestrato sui dati. Un modello rappresenta le relazioni e i pattern appresi e può essere utilizzato per fare previsioni o prendere decisioni su nuovi dati.

In sintesi: l'algoritmo è la ricetta/procedura generica, mentre il modello è il risultato specifico ottenuto applicando quella procedura a un particolare insieme di [[Dati di Training]]. Uno stesso algoritmo (es. una rete neurale) può produrre modelli molto diversi a seconda dei dati su cui viene addestrato.

Questa distinzione è alla base di come funziona anche il [[Deep Learning]], dove l'algoritmo di addestramento regola i pesi della rete neurale (vedi [[Come Funziona il Deep Learning]]) per produrre un modello capace di prestazioni elevate su compiti complessi.

Collegamenti: [[Machine Learning]], [[Dati di Training]], [[Come Funziona il Machine Learning]], [[Deep Learning]]
