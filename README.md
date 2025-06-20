# Biomedical Abbreviation and Long-form Detection using Token Classification

This project addresses biomedical Named Entity Recognition (NER) using the **PLOD-CW-25** dataset. The focus is on detecting **abbreviations (AC)** and their corresponding **long forms (LF)** using token classification models. We compare traditional CRF-based models, sequential models (RNN, BiLSTM with FastText), and transformer-based architectures (fine-tuned RoBERTa with advanced optimizers).

---

## 📚 Dataset: PLOD-CW-25

- BIO-annotated token classification dataset (`B-AC`, `B-LF`, `I-LF`, `O`)
- Covers a wide range of biomedical sub-domains
- Split into train, validation, and test sets with consistent annotation

---

## 📊 Exploratory Data Analysis (EDA)

- Verified token-level annotation integrity
- Analyzed sentence length and BIO tag distributions
- Explored domain-specific abbreviation trends
- Identified ambiguity in abbreviation reuse (e.g., same acronym for different long forms)
- Measured impact of token casing and length on tagging complexity

---

## 🧠 Experiments

### 🔹 Experiment 1 – CRF + BiLSTM

- **Embeddings**: Word2Vec and concatenated character-level representations
- **Architecture**: BiLSTM followed by CRF for sequence decoding
- **Objective**: Evaluate whether character-level subword information improves detection of biomedical entities
- **Result**: Macro F1 ≈ 0.73

---

### 🔹 Experiment 2 – RNN vs BiLSTM with FastText

- **Embeddings**: Pre-trained FastText (cc.en.300.bin) with OOV handling
- **Architecture**: Shared structure for RNN and BiLSTM, differing only in directionality and hidden size
- **POS Features**: Incorporated as parallel input features to enhance syntactic understanding
- **Padding Strategy**: Max sequence length = 371, using -100 as label ignore index for padded positions
- **Result**:
  - RNN: Macro F1 ≈ 0.52
  - BiLSTM: Macro F1 ≈ 0.67

---

### 🔹 Experiment 3 – RoBERTa Fine-tuning

- **Model**: `roberta-base` with `RobertaTokenizerFast` (BPE-based subword tokenization)
- **Optimizers Compared**: AdamW, LION, LAMB
- **Training Setup**: Early stopping, stratified evaluation, batch-wise subword realignment
- **Result**:
  - RoBERTa + AdamW: Macro F1 ≈ 0.83
  - RoBERTa + LION: Macro F1 ≈ 0.855
  - RoBERTa + LAMB: Unstable convergence in small-batch settings

---

## ✅ Evaluation Summary

| Model                      | Macro F1 | Micro F1 |
|----------------------------|----------|----------|
| CRF + BiLSTM               | 0.73     | 0.76     |
| RNN + FastText             | 0.52     | 0.54     |
| BiLSTM + FastText          | 0.67     | 0.70     |
| RoBERTa + AdamW            | 0.83     | 0.84     |
| **RoBERTa + LION (Best)**  | **0.855**| **0.862**|

---

### 🏆 Best Performing Model: RoBERTa + LION

The best performance was achieved by **fine-tuning RoBERTa with the LION optimizer**, resulting in a **Macro F1 score of 0.855** and **Micro F1 of 0.862**. Key reasons:

- **Contextualized Representations**: RoBERTa captures long-range dependencies and biomedical syntax better than RNNs or CRFs.
- **Subword Tokenization (BPE)**: Improves coverage of rare and complex biomedical terms.
- **LION Optimizer**: Provides stable, fast convergence, outperforming AdamW and LAMB in generalization.
- **Fine-tuning on Domain Task**: Allows RoBERTa to adapt to biomedical abbreviation/long-form patterns.

