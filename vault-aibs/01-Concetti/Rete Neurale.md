---
tags: [aibs, storia-ai]
---
# Rete Neurale

Una rete neurale artificiale è un modello matematico ispirato al cervello umano, composta da nodi ("neuroni") collegati tra loro, organizzati in strati (layer).

La rete più semplice prevede un layer (strato) di input, più un layer di ragionamento e un layer di risposta (output). Ogni neurone riceve dei numeri (token) in ingresso, li elabora con una funzione matematica e produce un risultato in uscita.

Durante l'addestramento, la rete fa una previsione (ad esempio predice "gatto" invece di "cane"), confronta il risultato con il valore corretto; la previsione è basata su quanto la rete è stata già usata per addestrarla (fase chiamata inferenza). La rete corregge infine i pesi dei collegamenti tra neuroni con un algoritmo chiamato [[Backpropagation]], che ripete questo processo milioni di volte sui dati.

Le reti neurali "profonde" (Deep Learning), con molti layer come nel caso di [[AlexNet]] o del [[Transformer]], sono alla base dell'IA moderna: il [[Modello AI|modello AI]] è infatti l'output dell'algoritmo di machine learning basato su reti neurali.

Collegamenti: [[Backpropagation]], [[Tokenizzazione]], [[AlexNet]], [[Transformer]], [[Modello AI]]
