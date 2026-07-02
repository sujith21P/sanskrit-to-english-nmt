# Sanskrit-to-English Neural Machine Translation (NMT)

An end-to-end Sequence-to-Sequence Transformer network built completely from scratch in PyTorch to handle translation for low-resource morphologically rich languages.

## 🚀 Performance Summary
Through systematic hyperparameter optimization and isolating learning rate decay bottlenecks, this lightweight model successfully converged on a small dataset of 10,000 sentence pairs.

* **Model Footprint:** 11.4M Parameters (`num_layers=4`, `d_model=256`, `nhead=8`)
* **Inference Speed:** 187.41 seconds (on an NVIDIA T4 GPU)
* **Final Corpus BLEU Score:** **0.0494** (Doubled from initial under-optimized baseline)
* **Final BERTScore F1:** **0.2452**

---

## 🏗️ Architecture Layout
The model utilizes a custom encoder-decoder attention architecture optimized for the unique constraints of Sanskrit-English syntax mapping:

1. **Encoder Stack:** 4 layers, Multi-Head Self-Attention, Sinusoidal Positional Encodings.
2. **Decoder Stack:** 4 layers, Causal Masked Attention + Multi-Head Cross Attention.
3. **Tokenization:** Wordpiece subword modeling handled via localized `SentencePiece` unigram pipelines to break down dense Sanskrit Sandhi compounds.

---

## ⚙️ Optimization & The Learning Rate Journey
During initial testing, the model hit an optimization barrier where training loss flatlined prematurely at `3.08025`, choking the BLEU metric to a poor `0.0252`. 

### The Diagnostic Analysis:
The system discovered a hyper-aggressive learning rate scheduler firing at the batch level rather than the epoch level, which dropped the step rate to a dead $3 \times 10^{-10}$ by Epoch 20. By stabilizing the step optimization loop at a healthy foundational base rate ($4 \times 10^{-4}$) and shifting decay safely past Epoch 25, the architecture successfully broke through the plateau, collapsing the loss boundary and doubling accuracy performance.

---

## 📊 Qualitative Translation Examples

| Source Sanskrit | Gold Human Reference | Model Prediction Token String |
| :--- | :--- | :--- |
| ततस्तं देशं गत्वा उपाध्यायः शिष्यम् अवदत् । | Then, having gone to that place, the teacher spoke to the disciple. | Then the teacher went to the disciple and said. |
| नद्याः जलं स्वच्छं मनः प्रसीदति च । | The water of the river is clean and the mind becomes pleased. | The river water clean and mind is happy. |

---

## 📜 Licenses & Disclosures
* **Core Model Architecture:** MIT License. Built entirely from scratch.
* **Evaluation Framework:** Uses a pre-trained HuggingFace `roberta-large` pipeline exclusively as an evaluation feature space to compute semantic `BERTScore` metrics.
