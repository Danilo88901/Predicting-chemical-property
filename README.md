# Predicting-chemical-property
Using sklearn pandass numpy and matplotlib
 images/  
  Saved PNGs of all scatter plots, correlation heatmaps, and other visualizations used in the README.
- notebooks/  
  Jupyter notebook illustrating step‑by‑step data loading, descriptor computation, EDA, model training, and evaluation.
- src/model_pipeline.py  
  A standalone Python script containing the full pipeline: descriptor calculation, feature selection, model training, hyperparameter tuning, stacking, and final prediction.
- data/  
  Contains the original Problem-4-train.csv and Problem-4-test.csv files (SMILES + target property).
- requirements.txt  
  Lists all Python packages required to run the notebook or script.
- README.md  
  This document.

---

## 📋 Data Description

- Problem‑4‑train.csv  
  - Column 1: SMILES string for each molecule  
  - Column “X”: Numerical target property to predict  
  - No header row beyond these two columns  

- Problem‑4‑test.csv  
  - Column 1: SMILES string for each molecule  
  - The goal is to predict “X” for each SMILES in this test set.

---

## 📈 Molecular Descriptors

We compute the following descriptors (per SMILES) using RDKit:

1. MolWeight (Molecular Weight)  
   - Captures size of the molecule.  
2. Tpsa (Topological Polar Surface Area)  
   - Measures polarity, often correlates with solubility/permeability.  
3. LogP (Octanol–Water Partition Coefficient)  
   - Indicates lipophilicity; correlates with bioavailability.  
4. Interaction (MolWeight × LogP)  
   - A nonlinear interaction term combining size and lipophilicity, which may capture synergistic effects.

> Why these four?  
> - Scatter plots (`images/scatter_*.png`) show each has clear correlation with the target.  
> - They provide orthogonal chemical information (size, polarity, lipophilicity, plus combined effect), reducing multicollinearity.  
> - Using only four features avoids overfitting on small training sets while still achieving high accuracy.

---

## 🔍 Exploratory Data Analysis

1. Scatter Plots of Each Descriptor vs. Target  
   - images/scatter_MolWeight.png  
   - images/scatter_Tpsa.png  
   - images/scatter_LogP.png  
   - images/scatter_Interaction.png  

2. Correlation Matrix of the Four Descriptors  
   - images/corr_four_features.png  
   - Demonstrates that MolWeight, Tpsa, LogP, and Interaction are not overly collinear, justifying their joint use.

3. Descriptor Distributions (Histograms)  
   - For training set only:  
     - Histograms of “Overall Quality” and “Overall Condition” were part of the previous housing project example, but not directly relevant here.  
   - We focus on scatter plots above for feature selection.

---

## 🚀 Modeling Workflow

### 1. Load Data & Compute Descriptors

- Read Problem‑4‑train.csv into DataFrame a.  
- Read Problem‑4‑test.csv into DataFrame b.  
- For each SMILES in train and test:  
  - Compute MolWeight, Tpsa, LogP, HeteroAtoms, HeavyAtoms, NumRings, NumAromaticRings, NumHDonors, NumHAcceptors, NumRotatableBonds, StereoCenters, and Interaction (MolWeight × LogP).  
  - Append computed values as new columns.

### 2. Prepare Features & Target

- Training feature matrix X_train: All descriptor columns (`MolWeight`, Tpsa, LogP, Interaction, plus extra descriptors, although we ultimately focus on the four chosen).  
- Training target y_train: Column X.  
- Test feature matrix X_test: All computed descriptor columns for the test SMILES.

### 3. Feature Selection

- We retain only the four chosen descriptors:  
  ```python
  features_to_use = ["MolWeight", "Tpsa", "LogP", "Interaction"]
  X_train = a[features_to_use]
  X_test  = b[features_to_use]
