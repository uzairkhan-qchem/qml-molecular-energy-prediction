# Quantum Machine Learning for Molecular Formation Energies

> 🚧 **Work in Progress** – This repository contains a complete pipeline for predicting molecular formation energies using hybrid quantum‑classical models. Current results show no quantum advantage (expected for small qubit count), but the code is ready for extension (more qubits, better feature maps, larger datasets, real hardware).

## 📌 Project Overview

**Goal:** Build and benchmark a quantum kernel SVR (4 qubits) against classical baselines (XGBoost, Random Forest, linear SVR) for predicting **atomization energy (u0_atom)** of small organic molecules from the QM9 dataset.

**Why this matters:**  
- Bridges classical ML and quantum computing for computational chemistry.  
- Demonstrates a complete QML pipeline: data loading → molecular fingerprints → PCA → quantum kernel → evaluation.  
- Provides an honest discussion of **why quantum did not outperform classical** in this limited setting.

## 🔬 Dataset

- **Source:** QM9 (DeepChem version: `gdb9.sdf.csv` + `gdb9.sdf`)  
- **Subset:** 500 molecules (first 500 of 133k)  
- **Target:** `u0_atom` – atomization energy (formation energy) in Hartree  
- **Features:** Morgan fingerprints (radius=2, 2048 bits) → PCA to 4 dimensions (for 4 qubits)

## 🧪 Models

| Model | Features | MAE (Ha) | RMSE (Ha) | R² |
|-------|----------|----------|-----------|-----|
| Quantum Kernel SVR | PCA (4) | 203.17 | 268.16 | -0.094 |
| Linear SVR (baseline) | PCA (4) | 191.46 | 255.92 | 0.004 |
| Random Forest | PCA (4) | 170.83 | 206.26 | 0.353 |
| XGBoost | PCA (4) | 170.24 | 211.08 | 0.322 |
| Random Forest | Full fingerprints (2048) | 123.85 | 163.91 | 0.591 |
| **XGBoost (best classical)** | Full fingerprints (2048) | **112.45** | **147.04** | **0.671** |

## 🧠 Quantum Kernel Details

- **Framework:** PennyLane  
- **Number of qubits:** 4  
- **Encoding:** Angle embedding (RY + RZ) + linear CNOT entanglement  
- **Kernel:** Fidelity kernel = |⟨ψ(x) | ψ(x’)⟩|²  
- **Regressor:** `sklearn.svm.SVR` with `kernel='precomputed'`

## ❌ Why No Quantum Advantage (Yet)?

1. **Extreme PCA compression** – The first 4 principal components explain only **3.3%** of the variance in the 2048‑bit fingerprints. Most chemical information is lost.  
2. **Limited qubit count** – 4 qubits are insufficient to capture complex molecular similarity.  
3. **Shallow circuit** – Only one round of entanglement; no data re‑uploading.  
4. **Small dataset** – 399 training samples lead to noisy kernel matrix and poor generalisation.  
5. **Heuristic kernel** – Not tailored to formation energy; literature suggests graph‑based quantum kernels perform better.

## 🚀 Future Directions (WIP)

- ✅ Increase qubits to 6‑8 (requires more PCA components or different feature extraction).  
- ✅ Implement **IQP‑style feature maps** or **data re‑uploading** for higher expressivity.  
- ✅ Replace PCA with a **graph neural network (GNN)** to produce chemically meaningful embeddings.  
- ✅ Use the full QM9 dataset (130k molecules) with **virtual sample generation** (KDE).  
- ✅ Run the kernel on **real quantum hardware** (IBM, AWS Braket) with error mitigation.  
- ✅ Implement a **Variational Quantum Regressor (VQC)** as an alternative to kernel methods.

## Acknowledgements
- QM9 dataset (Ramakrishnan et al.)
- DeepChem for providing processed QM9 files
- PennyLane team for the quantum machine learning library
