# 《FUNDAMENTALS AND APPLICATIONS OF GENERATIVE AI》
- project-2a
- 2024/4/16
  
**Data Source**：[Kaggle HC 新聞資料集](https://www.kaggle.com/alvations/old-newspapers#old-newspaper.tsv)

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

### **RNN vs. LSTM 模型合理目標值設定**

| 模型 | val acc 合格 | 良好表現 | 優異表現 |
|------|----------------------|----------|------------|
| **RNN（10 epochs）**| **25%** ↑ | **30% ↑** | **35~40% ↑** | 
| **LSTM（30 epochs）**| **20%** ↑ | **25% ↑** | **30% ↑** | 

- **評估依據**

1. 資料集為 old-newspaper 繁體中文部分，屬**難度較高、語言特殊的領域語料**
2. 模型結構為**單純的 seq2label（embedding → RNN/LSTM → FC）**

### **Comparison and Analysis**

- **背景與超參數**

| 項目 | First-Train LSTM | Second-Train LSTM | First-Train RNN | Second-Train RNN |
|------|------------------|-------------------|------------------|-------------------|
| Model Type | LSTM | LSTM | RNN | RNN |
| RNN Layers | 2 | 2 | 2 | 2 |
| Epochs | 30 | 30 | 10 | 10 |
| Batch Size | 128 | 128 | 128 | 128 |
| Gradient Clip | 1 | 1 | 1 | 1 |
| Learning Rate (lr) | 0.001 | 0.001 | 0.001 | 0.001 |
| Weight Decay | 0.001 | 0.00001 | ✘ | 0.001 |
| Dropout | 0.5 | ✘ | ✘ | ✘ |
| Dataset | df_40_264 | df_40_264 | df_40_500 | df_40_264 |
| Embed Dim | 512 | 512 | 256 | 512 |
| Hidden Dim | 256 | 512 | 256 | 512 |

備註：
1. **df_40_264**：為 sequence 由 40 - 264 的資料範圍，共 247809 筆
2. **df_40_500**：為 sequence 由 40 - 500 的資料範圍，共 255426 筆

- Distribution of Text Lengths (0 to 500) with Outlier Bound：
 ![Distribution of Text Lengths (0 to 500) with Outlier Bound](https://github.com/user-attachments/assets/44bc9db7-526d-4fc0-9c6b-23ee36a8ecc6)


- **訓練結果比較分析**

| 訓練情境 | 模型   | Epochs | Train Loss (約) | Val Loss (約) | Train acc(約) | Val acc (約) | 觀察       |
|----------|--------|--------|---------------|---------------|-----------------|-----------------|---------------|
| First    | LSTM   | 30     | 5.0-5.1       | 5.0           | 14.5-15%        | 15.5-16%        | 收斂良好，泛化能力尚可 |
| First    | RNN    | 10     | 4.1           | 4.1           | 27-28%          | 27-28%          | 收斂穩定，表現優異    |
| Second   | LSTM   | 30     | 3.5           | 3.6           | 36%             | 34%             | 收斂良好，表現最佳    |
| Second   | RNN    | 10     | 5.25          | 5.25          | 18%             | 18%             | 訓練不穩定，有波動    |

- **LSTM**
    - First-Train：Loss 約 5.0-5.1，Acc 14.5-15%，表現較弱。
    - Second-Train：Loss 約 3.5-3.6，Acc 36%，表現顯著提升。
    - 第二次訓練的 LSTM 參數調整後表現更佳

- **RNN** 
    - First-Train：Loss 約 4.1，Acc 27-28%，表現良好。
    - Second-Train：Loss 約 5.25，Acc 18%，表現下降，且訓練過程不穩定。
    - 第二次訓練的 RNN 有顯著的損失和準確率波動，特別是在第 6 和第 8 epoch，可能是 overfitting、hidden_dim 或 embed_dim 過高的問題。
