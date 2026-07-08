---
tags: [aibs, fondamenta-ml]
---
# Vantaggi del RAG

Il deck mette a confronto i limiti dei tradizionali sistemi LLM con i vantaggi introdotti dal [[RAG (Retrieval-Augmented Generation)]].

**Limitazioni dei tradizionali sistemi LLM:**
- **Conoscenza limitata**: bloccati ai dati su cui sono stati addestrati, non hanno accesso a informazioni recenti o specifiche non presenti nel loro dataset di training (vedi [[Pre-addestramento]]).
- **"Allucinazioni"**: a volte generano risposte che sembrano plausibili ma sono in realtà imprecise o inventate.
- **Mancanza di fonti**: non possono sempre citare le fonti delle loro informazioni, rendendo difficile verificare la veridicità delle risposte.

**Vantaggi del RAG:**
- **Informazioni aggiornate**: permette agli LLM di utilizzare informazioni generate dopo l'addestramento iniziale, con più trasparenza e veridicità (citando anche le fonti).
- **Maggiore accuratezza**: riduce le "allucinazioni", con risposte più precise e affidabili, basate su fatti concreti (con fonti).
- **Costo-efficacia**: permette di aggiornare solo la base di conoscenza interna invece di riaddestrare un intero LLM (processo costoso e dispendioso).
- **Personalizzazione**: adatta le risposte a domini specifici o integra basi di conoscenza aziendali, rendendo l'IA più utile (es. supporto clienti, analisi finanziaria, medicina).

Collegamenti: [[RAG (Retrieval-Augmented Generation)]], [[Come Funziona un Sistema RAG]], [[Limiti della IA Generativa]]
