# Arabic Dialect Sentiment Analysis 🇸🇦 🇪🇬 🇯🇴

Multi-dialect Arabic sentiment analysis comparing **three transformer models** (AraBERT, MARBERT, CamelBERT) on a **self-collected dataset** spanning three Arabic dialects: **Saudi**, **Egyptian**, and **Jordanian**.

## 📊 Dataset

**This dataset was fully collected, labeled, and curated by me — it is not a public/existing dataset and was not generated or scraped by a third party.** I am solely responsible for its collection and supervision.

| File | Dialect | Source | Rows | Columns |
|------|---------|--------|------|---------|
| `Eygaptian_dataset_version2.xlsx` | Egyptian 🇪🇬 | Manually collected comments | ~10,000 | `النص` (text), `التصنيف` (sentiment label) |
| `Jordan_dataset_version2.xlsx` | Jordanian 🇯🇴 | Manually collected comments | ~7,600 | `comment`, `sentiment` |
| `saudi_youtube_comments.xlsx` | Saudi 🇸🇦 | YouTube comments | ~13,000 | `النص`, `search_query`, `التصنيف` |

- **Labels:** 3-class sentiment — إيجابي (Positive), سلبي (Negative), محايد (Neutral)
- **Annotation:** Manually labeled, fully supervised by me end-to-end
- **Total:** ~30,000+ labeled comments across the three dialects

> ⚠️ Due to the dataset being self-collected (and potentially containing personal/sensitive text from public platforms), the raw files are **not included in this public repository**. They are available upon reasonable request — please open an issue or contact me directly.

## 🧠 Project Overview

The goal is to classify sentiment (Positive / Negative / Neutral) in Arabic text **while accounting for dialectal variation**, and to determine which transformer architecture handles Arabic dialects best.

Three independent notebooks were built — one per BERT variant — so each model's full pipeline (preprocessing → classical baselines → fine-tuning → evaluation) could be run and compared fairly:

| Notebook | Model | Hugging Face Checkpoint |
|----------|-------|--------------------------|
| `Arabic_Dialect_AraBERT_v2.ipynb` | AraBERT | `aubmindlab/bert-base-arabertv2` |
| `Arabic_Dialect_MARBERT.ipynb` | MARBERT | `UBC-NLP/MARBERTv2` |
| `Arabic_Dialect_CamelBERT_UPDATED.ipynb` | CamelBERT | `CAMeL-Lab/bert-base-arabic-camelbert-mix` |

**🏆 Best performing model: full fine-tuned BERT (Multi-Task)** — outperformed all classical baselines and feature-based BERT embeddings across the three dialects.

## 🔄 Shared Pipeline (per notebook)

1. **Data Loading & Merging** — load the 3 dialect files, unify column names (`text`, `sentiment`), tag each row with its `dialect`, and concatenate into one DataFrame.
2. **Preprocessing**
   - Arabic text normalization
   - Verb-root & gender normalization (custom dictionaries)
   - Negation tagging
3. **Feature Extraction (baselines)**
   - TF-IDF (character n-grams)
   - Word2Vec (trained on the corpus)
   - Pretrained BERT embeddings (frozen, `[CLS]` pooling)
4. **Classical Models** — Logistic Regression & Linear SVM trained on each feature set above, for comparison.
5. **Multi-Task Fine-Tuning** — the BERT model is fine-tuned end-to-end with **two output heads**:
   - Sentiment classification (primary task, weight = 0.85)
   - Dialect classification (auxiliary task, weight = 0.15)

   This setup lets the model use dialect signals to support sentiment understanding without overpowering it.
6. **Evaluation**
   - Accuracy & Macro-F1 (overall and per-dialect)
   - Confusion matrices (sentiment + dialect)
   - Training/validation curves (loss, F1 per epoch)
   - Side-by-side comparison table: TF-IDF, Word2Vec, frozen-BERT, vs. fully fine-tuned multi-task BERT
7. **Interactive Demo** — a Gradio interface for testing the model live with custom Arabic text input, with dialect-aware rule-based hints as a sanity check.

## 🛠️ Tech Stack

| Category | Tools |
|----------|-------|
| Language | Python |
| Deep Learning | PyTorch, Hugging Face Transformers |
| Classical ML | scikit-learn (Logistic Regression, Linear SVM) |
| Embeddings | TF-IDF, Word2Vec (Gensim), AraBERT/MARBERT/CamelBERT |
| Visualization | Matplotlib, Seaborn |
| Demo | Gradio |
| Environment | Google Colab (GPU) |

## 🚀 Getting Started

### Requirements
```bash
pip install -r requirements.txt
```

### Run
1. Place the three dataset files in your working directory (or mount Google Drive, as in the original notebooks).
2. Open any of the three notebooks in Google Colab (GPU runtime recommended).
3. Run all cells top to bottom — each notebook is self-contained (load data → preprocess → train baselines → fine-tune → evaluate → launch demo).

## 📈 Results Summary

Each notebook prints a final comparison table like this:

| Model | Sentiment Acc | Sentiment F1 | Dialect Acc | Dialect F1 |
|-------|---------------|--------------|-------------|------------|
| TF-IDF + LR | ✓ | — | — | — |
| TF-IDF + SVM | ✓ | — | — | — |
| Word2Vec + SVM | ✓ | — | — | — |
| Frozen BERT + SVM | ✓ | — | — | — |
| **Multi-Task Fine-Tuned BERT** | **✓ (best)** | **✓ (best)** | ✓ | ✓ |

Per-dialect breakdown (accuracy & Macro-F1 for Saudi / Egyptian / Jordanian) is also generated for the best model, to check for any dialect-specific weaknesses.

> Exact numbers depend on the run/checkpoint — see each notebook's output for full results.

### 🖼️ Visualizations

<!--
Add your exported plots into the images/ folder, then they'll render here automatically.
Recommended files to export from the notebooks:
  - model_comparison.png      (bar chart: TF-IDF / Word2Vec / BERT / Fine-tuned accuracy comparison)
  - confusion_matrix.png      (sentiment confusion matrix, best model)
  - dialect_confusion_matrix.png  (dialect confusion matrix)
  - training_curves.png       (loss + F1 curves over epochs)
-->

| Model Comparison | Confusion Matrix |
|---|---|
| ![Model comparison](images/model_comparison.png) | ![Confusion matrix](images/confusion_matrix.png) |

| Training Curves | Dialect Confusion Matrix |
|---|---|
| ![Training curves](images/training_curves.png) | ![Dialect confusion matrix](images/dialect_confusion_matrix.png) |



## 📁 Notes

- All three notebooks were developed in **Google Colab**, hence Drive-mounting and pip-install cells at the top.
- Some comments/print statements are in Arabic, reflecting the original development language — functionality is unaffected.
- The dataset, labeling, and supervision are entirely my own work — no part of it was scraped from an existing public dataset or auto-generated.

## 📜 License

This project is for educational and research purposes. The dataset is proprietary (self-collected); please contact me for access or reuse permissions.
