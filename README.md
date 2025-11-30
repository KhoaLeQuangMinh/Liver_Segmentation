# Liver Tumor Segmentation using Deep Learning

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-2.0-orange)
![Status](https://img.shields.io/badge/Status-Research%20Prototype-success)
[![Kaggle](https://img.shields.io/badge/Kaggle-Competition-20BEFF?logo=kaggle&logoColor=white)](https://www.kaggle.com/competitions/aio2025liverseg/overview)

## 📌 Project Overview
This project focuses on the automated segmentation of liver tumors from CT scans. Accurate segmentation is critical for treatment planning and monitoring disease progression. The challenge lies in the low contrast between liver tissue and tumors, as well as the variability in tumor shapes and the high number of "empty" (non-liver) slices in abdominal scans.

This repository documents an iterative research process, moving from volumetric 3D approaches to a sophisticated **Cascade Ensemble pipeline** that achieves State-of-the-Art (SOTA) performance on the validation set.

## 🔬 Methodology & Experimentation Log

I approached this problem by iterating through four distinct architectural strategies. The goal was to solve the "Class Imbalance" problem where the liver occupies a small portion of the total CT volume.

### Experiment 1: Volumetric 3D CNN (Baseline)
*   **Approach:** Attempted to capture the full spatial context using a standard 3D CNN on voxel data.
*   **Preprocessing:** Basic normalization and resizing.
*   **Outcome:** **Suboptimal.** The model struggled to converge, likely due to the limited dataset size relative to the parameter explosion in 3D convolutions.
*   *Status:* Archived (`notebooks/LTS_version1.ipynb`)

### Experiment 2: UNet++ with Connected Component Analysis (High Performance)
*   **Approach:** Shifted to a 2D slice-by-slice segmentation using **UNet++**, utilizing nested skip connections to capture multi-scale features.
*   **Post-Processing:** Implemented **Largest 3D Connected Component Analysis**. By reconstructing the 3D volume from 2D predictions and keeping only the largest connected mass, false positives were significantly reduced.
*   **Outcome:** Excellent baseline. The post-processing alone provided a massive boost in Dice score.
*   **Metric:** **0.9717 (Dice)**
*   *Status:* `notebooks/LTS_version2.ipynb`

### Experiment 3: 2.5D Spatiotemporal Modeling (ConvLSTM)
*   **Approach:** Hypothesized that treating CT slices as a time-series could help the model understand continuity. Implemented a **Convolutional LSTM (ConvLSTM)** to capture 2.5D context.
*   **Outcome:** **Exploratory.** Performance did not exceed the UNet++ baseline. However, it offered insights into how inter-slice consistency affects boundary definitions.
*   *Status:* `notebooks/LTS_version3.ipynb`

### Experiment 4: Cascade Classifier & Specialist Ensemble (🏆 Best Performance)
*   **Hypothesis:** A single model struggles to balance "finding the liver" and "segmenting the tumor." A specialized pipeline should perform better.
*   **Approach:** Designed a multi-stage **Cascade Pipeline**:
    1.  **Slice Classification:** A lightweight binary classifier filters slices as "Liver-containing" vs. "Background."
    2.  **Specialist Segmentation:**
        *   *Model A:* Trained on mixed data.
        *   *Model B:* Trained strictly on liver-only crops.
    3.  **Gated Inference:** If the classifier is positive, the specialist Model B is triggered; otherwise Model A is used.
*   **Outcome:** **Winning Solution.** This approach minimized false positives on empty slices and improved boundary precision on difficult tumor slices.
*   **Metric:** **0.9720 (Dice)**
*   *Status:* `notebooks/LTS_version4.ipynb`

## 📊 Results Summary

| Experiment | Architecture | Key Technique | Performance (Dice) |
| :--- | :--- | :--- | :--- |
| **V4** | **Cascade Ensemble** | **Classification Gating** | **0.9720** (Best) |
| V2 | UNet++ (2D) | 3D Post-Processing (CCA) | 0.9717 |
| V3 | ConvLSTM | 2.5D Sequence Modeling | Comparable to Baseline |
| V1 | 3D CNN | Volumetric Convolution | Low |

## 🖼️ Visualizations

**Comparison of Experiments:**
*(Please insert a screenshot here showing: Original CT | Ground Truth | UNet++ Prediction)*
![Segmentation Result](https://via.placeholder.com/800x200?text=Insert+Your+Segmentation+Screenshots+Here)

## 🛠️ Installation & Usage

1. Clone the repository:
   ```bash
   git clone https://github.com/KhoaLeQuangMinh/Liver_Segmentation.git


