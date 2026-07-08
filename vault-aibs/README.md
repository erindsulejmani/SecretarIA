# Vault AIBS — Appunti corso AI Business Specialist

Vault Obsidian con gli appunti del corso di certificazione **APF AI Business Specialist** (DigitalStrategies Academy), costruito a partire dai PDF delle lezioni.

> Nota: questo vault è indipendente dal progetto SecretarIA (che vive nella root del repo). Vive qui solo per comodità di versionamento personale.

## Come aprirlo in Obsidian

1. Apri Obsidian → **Apri cartella come vault** → seleziona questa cartella (`vault-aibs/`).
2. Vai su **Grafo** (icona nella barra laterale, o `Ctrl/Cmd+G`) per vedere il grafo delle note collegate.
3. Parti da [[MOC - Corso AIBS]] in `00-MOC/` per navigare tutti i moduli.

## Struttura

- `00-MOC/` — Map of Content: note-indice che collegano i temi principali (una per modulo + una master).
- `01-Concetti/` — note atomiche, una per concetto (es. *Transformer*, *RLHF*, *CRAAP*). Sono i nodi principali del grafo, collegati tra loro con `[[wikilink]]`.
- `02-Lezioni/` — una nota per ogni lezione/PDF sorgente, con la struttura della lezione e i link ai concetti trattati.

## Fonti elaborate

| Lezione | Argomento |
|---|---|
| BASI e Storia dell'AI | Storia del deep learning, NLP, Transformer, GPT/BERT, RLHF |
| BASI 2 | Gerarchia AI/ML/DL/GenAI, fondamenta del Machine Learning |
| Seconda lezione, parte 1 | Osservare le tendenze dell'IA (hype, trend, horizon scanning) |
| Seconda lezione, parte 2 | Identificare le informazioni affidabili (fonti, modello CRAAP) |

## Convenzioni

- Ogni nota concetto ha frontmatter con `tags` e (quando utile) `aliases`.
- I collegamenti tra note sono `[[wikilink]]`, mai link a file esterni.
- Le note di lezione fungono da "hub" tematici; le note di concetto sono i nodi riutilizzabili tra lezioni diverse.
