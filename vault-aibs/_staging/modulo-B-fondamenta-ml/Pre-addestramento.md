---
tags: [aibs, fondamenta-ml]
---
# Pre-addestramento

Il pre-addestramento è la fase iniziale e fondamentale del processo di addestramento dei modelli LLM (Large Language Models) e NLP (Natural Language Processing). Viene definito "il fulcro dell'apprendimento".

Due componenti chiave del pre-addestramento:

- **Analisi di dati massivi**: l'IA legge e analizza enormi quantità di testo (centinaia di miliardi di parole), imparando a predire il prossimo "token" (parola o parte di parola).
- **Apprendimento statistico**: il modello non "capisce" davvero, ma apprende le regole statistiche del linguaggio, come grammatica, logica discorsiva, associazioni semantiche, coerenza e relazioni concettuali.

Dato numerico citato nelle slide: GPT-3/GPT-4 sono stati pre-addestrati su centinaia di miliardi di token.

Il pre-addestramento è il pilastro su cui si fonda la capacità di un modello LLM di interagire efficacemente con gli utenti: costruisce il "cervello" linguistico del modello, permettendogli di interpretare e rispondere ai prompt con coerenza e pertinenza (vedi [[Dal Prompt all'Output]]).

Il deck nota anche che nel fine-tuning (fase successiva al pre-addestramento) avviene l'adattamento allo stile narrativo e al target di pubblico, e che per ChatGPT le capacità predittive del pre-addestramento sono sviluppate anche in modo non supervisionato (vedi [[Apprendimento Non Supervisionato]]).

Collegamenti: [[Dal Prompt all'Output]], [[Dati di Training]], [[Apprendimento Non Supervisionato]], [[Evoluzione Attorno al Pre-addestramento]]
