# Frame Semantic Role Labeling for European Portuguese

This repository contains the resources and notebooks for a **Frame Semantic Role Labeling project** in European Portuguese. The goal is to automatically predict **frames** and their **core frame elements (FEs)**. In its current form, this is a small-scale experiment restricted to three frames — `Avisar`, `Lembrar_Experiência`, and `Transferir` — and is intended as a prototype to be expanded with additional frames and larger annotated datasets in the future.

The project was developed in Google Colab and leverages HuggingFace Transformers for token classification with a BERT-based model.
For the annotation phase, Doccano was used for the manual annotation.

---

## Repository Structure
```
Semantic_Frame_Parser/
│
├── Data/
│   ├── Raw/                # Original XML files from Sketch Engine
│   ├── Annotated/          # Manually annotated files (JSONL)
│   └── Processed/          # BIO files and train/dev/test splits
│
├── Notebooks/
│   ├── 1_data_preprocessing.ipynb   # Parsing XMLs, tokenization, creating JSONL
│   ├── 2_filtering_annotation.ipynb # Filtering unannotated sentences, creating BIO dataset
│   └── 3_model_training.ipynb       # Train/dev/test split, model training, evaluation
│
└── README.md
```

---

## Task Definition

This project aims at being an experiment at predicting **frames and their core frame elements (FEs)** in European Portuguese sentences. For these experimental purposes, it focuses on three frames extracted from **FrameNet Brasil**:

- `Avisar` (Warning)
- `Lembrar_Experiência` (Remembering Experience)
- `Transferir` (Transfer)

The model performs **token-level classification** using the BIO tagging scheme for the FEs. A small set of NoFrame sentences is included to represent negative cases.



---

## Dataset

- **Annotated Sentences:** 1,401 sentences manually annotated across the three frames.
- **NoFrame Sentences:** 200 additional sentences included to provide negative examples.
- **Filtered Dataset:** 952 sentences (including NoFrame) after removing ambiguous or structurally unclear sentences.
- **BIO Format:** Tokens with IOB2 labels per FE for model training.
- **Train/Dev/Test Split:** 761 / 95 / 96 sentences respectively.

**Data Folders:**
- `Raw/` – Original XML files extracted from corpus PtTenTen23, via Sketch Engine.
- `Annotated/` – JSONL files after manual annotation in Doccano.
- `Processed/` – BIO-formatted dataset and train/dev/test splits.

---

## Notebooks Overview

### 1. Data Preprocessing
- Reads XML files from Sketch Engine.
- Reconstructs full sentences and extracts target words.
- Cleans POS tags and tokenizes sentences.
- Outputs a JSONL file for annotation (Doccano was used for annotation).

### 2. Filtering and BIO Creation
- Filters out unannotated sentences, preserving NoFrame examples.
- Converts JSONL annotations into a **BIO-formatted dataset**.
- Prepares the dataset for training with train/dev/test splits.

### 3. Model Training
- Loads the train/dev/test datasets.
- Tokenizes with **BERTimbau Base** (`neuralmind/bert-base-portuguese-cased`).
- Aligns subword tokens with BIO labels.
- Applies weighted cross-entropy to handle label imbalance.
- Trains and evaluates the model using HuggingFace `Trainer`.
- Reports per-class metrics and overall F1 score.

---

## Model

**BERTimbau Base** (`neuralmind/bert-base-portuguese-cased`) was selected for this project — a 110M parameter BERT model trained on Portuguese, compatible with the HuggingFace `AutoModelForTokenClassification` API. An initial attempt with Albertina 1.5B (a larger European Portuguese encoder) was abandoned due to memory constraints and unstable training in the Colab environment.

---

## Results

Four experiments were conducted, varying class weighting and regularisation strategies:

| Configuration          | Precision | Recall | F1       |
|------------------------|-----------|--------|----------|
| Baseline (no weights)  | 0.52      | 0.58   | 0.52     |
| Full class weights     | 0.26      | 0.67   | 0.38     |
| **Sqrt class weights** | **0.52**  | **0.65** | **0.55** |
| Sqrt weights + dropout | 0.52      | 0.65   | 0.55     |

The best configuration used **square root class weights** with a learning rate of 3e-5, 15 epochs, and early stopping (patience 5). Well-represented labels such as `Transferir_Tema`, `Transferir_Recetor`, and `Lembrar_Pensador` achieved F1 scores in the **0.70–0.83** range. Rare labels with fewer than 30 training instances (e.g., `Lembrar_Impressao`, `Avisar_Meio`) remain challenging across all configurations.

---

## Usage

1. Clone the repository:
```bash
git clone https://github.com/<your-username>/FrameSemantic-PT.git
cd FrameSemantic-PT
```

2. Open the notebooks in Google Colab:
   - Ensure your Google Drive is mounted.
   - Run the notebooks sequentially:
     1. `1_data_preprocessing.ipynb`
     2. `2_filtering_annotation.ipynb`
     3. `3_model_training.ipynb`
   - Review results and per-label performance in Notebook 3.