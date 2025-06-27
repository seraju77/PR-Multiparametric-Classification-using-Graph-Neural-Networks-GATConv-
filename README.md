# PR-Multiparametric-Classification-using-Graph-Neural-Networks-GATConv-

Welcome to an advanced application of Graph Attention Networks (GAT) for multi-class classification of nuclei within PR-stained histopathology images. This repository introduces a graph-based pipeline where each nucleus is modeled as a node, and inter-nuclei relationships define the graph structure—enabling spatial, topological, and phenotypic learning at scale.


🔍 Problem Setting
In classical IHC image classification, spatial context is often overlooked. This project remedies that by constructing cell-level graphs from segmentation masks and predicting the class of each nucleus:

-Input: PR IHC-stained image patches + nucleus segmentation masks
-Task: Classify each nucleus into one of the following categories:

| Label | Description       | Color Code             |
| ----- | ----------------- | ---------------------- |
| 0     | Normal            | Blue `(0, 0, 255)`     |
| 1     | Weak Expression   | Green `(0, 255, 0)`    |
| 2     | Moderate          | Orange `(255, 159, 0)` |
| 3     | Strong Expression | Cyan `(0, 216, 255)`   |

📁 Dataset Overview (Private dataset, will be share upon request)
| Source Folder       | Description                    |
| ------------------- | ------------------------------ |
| `/ER_IHC_Int_Image` | Raw histology images (256×256) |
| `/Mask_WO_BG`       | Segmentation masks (per image) |

Each mask encodes nuclei by color. The GNN operates on extracted patches and corresponding graph nodes built from these masks.

🔧 Graph Construction Pipeline
Each nucleus becomes a node, connected by proximity-based edges using a k-nearest neighbor approach (k=8):
Segment mask → Connected components → RGB → Class label
     ↓
Centroids → KD-Tree → kNN Graph (nodes + edges)
     ↓
Node features: [x, y, R, G, B] (normalized)

🧠 Model Architecture
The classifier is a node-level GNN with the following structure:
Input Features: [x, y, R, G, B] → GATConv → ReLU → GATConv → ReLU → Linear → Softmax
| Layer  | Type    | Details                       |
| ------ | ------- | ----------------------------- |
| Conv1  | GATConv | 5 → 32, heads=2 (concat=True) |
| Conv2  | GATConv | 64 → 32                       |
| Output | Linear  | 32 → 4                        |

🧪 Training and Evaluation
Loss: CrossEntropy.
Optimizer: Adam.
Hardware: Kaggle GPU runtime.
Training Loop: Stratified across graph samples with per-node label loss
✅ Evaluation Metrics
| Metric      | Value           |
| ----------- | --------------- |
| Accuracy    | \~88–90%        |
| AUC (macro) | 0.92            |
| Classes     | 4 (multi-class) |

📈 Visual Results
🔹Classification Report
![image (12)](https://github.com/user-attachments/assets/882ee0f1-4b2a-4bfa-8ae8-5a7f6c17549f)

🔹 Confusion Matrix
![WhatsApp Image 2025-06-27 at 5 59 02 PM](https://github.com/user-attachments/assets/b273dbdf-f51f-411f-8d42-35f80dac502a)

🔹 ROC Curve (Macro AUC)
![download (2)](https://github.com/user-attachments/assets/24443ebd-e9b4-4917-aa47-d61bd8c976c6)

🔹 t-SNE Embedding of Node Features
![download (1)](https://github.com/user-attachments/assets/76af555c-e37a-4c2e-afc1-d9d0be4d1739)


