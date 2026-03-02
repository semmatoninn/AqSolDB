# Predicting Aquaeous Drug Solubility

# Problem
Poor aqueous solubility is a major challenge in pharmaceutical research, with a large proportion of new drug candidates exhibiting low water solubility that limits absorption and bioavailability. According to reviews in the pharmaceutical literature, up to 70–90% of drug candidates in the development pipeline are poorly water-soluble, significantly reducing their drug-likeness and complicating formulation efforts. Poor solubility often leads to inadequate drug absorption in vivo, necessitating higher doses or formulation changes, and is a major contributor to costly failures in drug development. Predicting solubility early enables scientists to select appropriate drug delivery methods and formulation strategies that improve bioavailability and increase the likelihood of clinical success.

# Dataset Description: AqSolDB
AqSolDB is a curated dataset of experimentally measured aqueous solubility values (logS) for diverse organic compounds collected from multiple literature sources. It is commonly used as a benchmark for molecular property prediction because solubility is a fundamental physicochemical property that strongly influences drug absorption, formulation strategy, and bioavailability.

# Model 

## XGBoost Baseline 
An XGBoost regression model was trained on molecular descriptors to capture nonlinear relationships between physicochemical properties and aqueous solubility. Both models were evaluated using scaffold splitting to ensure structural dissimilarity between training and validation sets, prevent overfitting performance due to similar chemical scaffolds appearing across splits.

### Untuned XGBoost Results:
Train R² = 0.876
Val 0.642
Test 0.607

## GNN Model
A 2 layers Graph Isomorphism Network with Edge features (GINEConv) was implemented with hidden size 128, using one-hot encoded atom features and bond attributes.

### Why GINEConv?

* GCN aggregates via normalized averaging → may oversmooth structural signals.
* GIN improves expressiveness via learnable MLP aggregation.
* GINE extends GIN by incorporating edge features explicitly, which is important for chemistry because bond type and conjugation directly influence solubility.

### GNN Results:
Train R² ≈ 0.879
Val R² ≈ 0.634
Test R² ≈ 0.600

# Limitations: GNN vs XGBoost

* XGBoost relies on descriptors, which entails the information of physicochemical properties such as logP, TPSA, molecular weight, and hydrogen bond donors/acceptors, features directly correlated with solubility, especially logS.
* GNN learns representations from graph topology, but with moderate dataset size, it may not fully capture all physicochemical details. Unfortunately, increasing amount of layers (3) and more regularization reduce performance and increase overfitting. 
* The similar R² values (~0.60 on test set) suggest that, for AqSolDB at this scale, learned graph representations do not substantially outperform classical descriptor-based models.
* In literature, descriptor-rich models using hundreds of calculated molecular features have reported R² values up to ~0.90–0.92 with ensemble methods such as XGBoost, highlighting the strength of curated chemical descriptors for solubility prediction.

# Which Model Is Better?
For this dataset size and split strategy, XGBoost slightly outperforms or matches the GNN while being simpler and more computationally efficient. This suggests that solubility, with understood physicochemical descriptors, may not require deep graph representation learning to achieve strong predictive performance.
GNN performance outperformed SolTraNet 

# Future Work
Incorporate richer molecular descriptors in XGBoost. There were papers that used 196 physicochemical descriptors to achieve R2 of 0.92 to predict solubility. 
There was one paper that worked on same dataset with GNN but achieved 0.81 R2, at the same time, they removed and re-curated dataset 2 times by removing logS >0.5 and cut off compounds with molecular weight >900
Pretrain graph embeddings on larger molecular datasets (Husskonen, SoluProtMut) before finetuning.
Perform cross-validation to assess robustness.

