# Frame Semantic Role Labeling for European Portuguese

This repository contains the resources and notebooks for a **Frame Semantic Role Labeling project** in European Portuguese. The goal is to automatically predict **frames** and their **core frame elements (FEs)** for three selected frames: `Avisar`, `Lembrar`, and `Transferir`. 

The project was developed in Google Colab and leverages HuggingFace Transformers for token classification with a BERT-based model.

---

## Repository Structure

Semantic_Frame_Parser/
│
├── Data/
│   ├── Raw/                # Original XML files from Sketch Engine
│   ├── Annotated/          # Manually annotated files (JSONL)
│   └── Processed/          # BIO files and train/dev/test splits
│
├── Notebooks/
│   ├── 1_data_preprocessing.ipynb  # Parsing XMLs, tokenization, creating JSONL
│   ├── 2_filtering_annotation.ipynb # Filtering unannotated sentences, creating BIO dataset
│   └── 3_model_training.ipynb       # Train/dev/test split, model training, evaluation
│
├── README.md
└── LICENSE

---

## Task Definition

This project aims at being an experiment at predicting **frames and their core frame elements (FEs)** in European Portuguese sentences. For these experimental purposes, it focuses on three frames:

- `Avisar` (Warning)  
- `Lembrar_Experiência` (Remembering_experience)  
- `Transferir` (Transfer)  

The model performs **token-level classification** using the BIO tagging scheme for the FEs. A small set of NoFrame sentences is included to represent negative cases.

---

## Dataset

- **Unannotated Sentences (Raw):** 1,401 sentences across the three frames.  
- **NoFrame Sentences:** 200 additional sentences included to provide negative examples.  
- **Filtered Sentences:** 952 sentences (including NoFrame) after removing unannotated or ambiguous sentences.  
- **BIO Format:** Tokens with IOB2 labels per FE for model training.

**Data Folders:**  
- `Raw/` – Original XML files extracted from corpus PtTenTen23, from Sketch Engine.  
- `Annotated/` – JSONL files after manual annotation.  
- `Processed/` – BIO-formatted dataset and train/dev/test splits.

---

## Notebooks Overview

### 1. Data Preprocessing
- Reads XML files from Sketch Engine.  
- Reconstructs full sentences and extracts target words.  
- Cleans POS tags and tokenizes sentences.  
- Outputs a JSONL file for annotation (in this case, Doccano was used for annotation).

### 2. Filtering and BIO Creation
- Filters out unannotated sentences except NoFrame examples.  
- Converts JSONL annotation into a **BIO-formatted dataset**.  
- Prepares the dataset for training with train/dev/test splits.

### 3. Model Training
- Loads the train/dev/test datasets.  
- Tokenizes with **BERTimbau Base** (`neuralmind/bert-base-portuguese-cased`).  
- Aligns subword tokens with BIO labels.  
- Applies weighted cross-entropy to handle label imbalance.  
- Trains and evaluates the model using HuggingFace `Trainer`.  
- Reports per-class metrics and overall F1 score.

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
	1.	1_data_preprocessing.ipynb
	2.	2_filtering_annotation.ipynb
	3.	3_model_training.ipynb
- Review results and per-label performance in Notebook 3.

