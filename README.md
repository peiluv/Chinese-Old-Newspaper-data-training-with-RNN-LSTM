# 《FUNDAMENTALS AND APPLICATIONS OF GENERATIVE AI》
- Project 2a — Sequence Modeling with RNN and LSTM
- 2024/4/16

**Data Source**：[Kaggle HC 新聞資料集](https://www.kaggle.com/alvations/old-newspapers#old-newspaper.tsv)

---

### **Table of Contents**：
- [Load Data](#載入資料)
- [Data Preprocessing](#前處理)
- [Tokenization Dic](#建立字典)
- [Data Batching](#資料分批)
- [Model Architecture](#模型設計)
- [Hyperperamemters](#超參數)
- [Training](#訓練)
    - [RNN First-Train](#訓練)
    - [RNN Second-Train](#訓練)
    - [LSTM First-Train](#訓練)
    - [LSTM Second-Train](#訓練)
    - [Comparison and Analysis](#訓練)
- [Generation](#生成)

---

### Target Accuracy Expectations for RNN vs. LSTM

| Model | Passing Threshold | Good Performance | Excellent Performance |
|-------|--------------------|------------------|------------------------|
| **RNN (10 epochs)** | ≥ 25% | ≥ 30% | ≥ 35–40% |
| **LSTM (30 epochs)** | ≥ 20% | ≥ 25% | ≥ 30% |

**Evaluation Basis**:
1. Dataset involves traditional Chinese newspapers — linguistically challenging and domain-specific.
2. Model architecture follows a straightforward seq2label pipeline: Embedding → RNN/LSTM → Fully Connected Layer.

---

### Comparison and Analysis

#### Training Setup and Hyperparameters

| Parameter | LSTM First Train | LSTM Second Train | RNN First Train | RNN Second Train |
|----------|------------------|-------------------|------------------|-------------------|
| Model Type | LSTM | LSTM | RNN | RNN |
| RNN Layers | 2 | 2 | 2 | 2 |
| Epochs | 30 | 30 | 10 | 10 |
| Batch Size | 128 | 128 | 128 | 128 |
| Gradient Clipping | 1 | 1 | 1 | 1 |
| Learning Rate | 0.001 | 0.001 | 0.001 | 0.001 |
| Weight Decay | 0.001 | 0.00001 | ✘ | 0.001 |
| Dropout | 0.5 | ✘ | ✘ | ✘ |
| Dataset | df_40_264 | df_40_264 | df_40_500 | df_40_264 |
| Embedding Dim | 512 | 512 | 256 | 512 |
| Hidden Dim | 256 | 512 | 256 | 512 |

**Notes**:
- `df_40_264`: Sequence length range from 40 to 264, total 247,809 samples.
- `df_40_500`: Sequence length range from 40 to 500, total 255,426 samples.

**Distribution of Text Lengths (with Outlier Bounds):**  
![Text Length Distribution](https://github.com/user-attachments/assets/44bc9db7-526d-4fc0-9c6b-23ee36a8ecc6)

---

### Training Performance Summary

| Scenario | Model | Epochs | Train Loss (approx) | Val Loss (approx) | Train Acc (approx) | Val Acc (approx) | Notes |
|----------|--------|--------|---------------------|-------------------|---------------------|-------------------|-------|
| First    | LSTM   | 30     | 5.0–5.1             | 5.0               | 14.5–15%            | 15.5–16%          | Good convergence, moderate generalization |
| First    | RNN    | 10     | 4.1                 | 4.1               | 27–28%              | 27–28%            | Stable training, strong performance |
| Second   | LSTM   | 30     | 3.5                 | 3.6               | 36%                 | 34%               | Excellent results after parameter tuning |
| Second   | RNN    | 10     | 5.25                | 5.25              | 18%                 | 18%               | Unstable training, suspected overfitting |

---

### Observations

#### LSTM:
- **First Training**: Loss around 5.0–5.1, Accuracy 14.5–15%. Performance was relatively weak.
- **Second Training**: Loss reduced to ~3.5–3.6, Accuracy up to 36%. Marked improvement due to parameter optimization.

#### RNN:
- **First Training**: Loss ~4.1, Accuracy 27–28%. Very good initial results.
- **Second Training**: Loss increased to ~5.25, Accuracy dropped to 18%. Performance degraded, especially around epochs 6 and 8, possibly due to overfitting or excessive embedding/hidden dimensions.

