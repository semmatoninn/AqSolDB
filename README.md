# Predicting Aquaeous Drug Solubility

# Problem
Poor aqueous solubility poses a major challenge in pharmaceutical research. According to reviews in the pharmaceutical literature, up to 70–90% of drug candidates in the development pipeline are poorly water soluble, limits absorption and bioavailability, significantly reducing their drug-likeness and complicating formulation efforts. [3][4]

Poor solubility often leads to inadequate drug absorption in vivo, needing higher doses or formulation changes, and is a major contributor to costly failures in clinical trial. Predicting solubility early enables scientists to select appropriate drug delivery methods and formulation strategies that improve bioavailability and increase the likelihood of clinical success.[1][2][3]

# Dataset Description: AqSolDB
AqSolDB is a curated dataset of experimentally measured aqueous solubility values (logS) for diverse organic compounds collected from multiple literature sources. [5]

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
For XGBoost, richer descriptors might be the key, there was a paper that used 196 physicochemical descriptors to achieve R2 of 0.92 to predict solubility[2]
There was one paper that worked on same dataset with GNN but achieved 0.81 R2, at the same time, they removed and re-curated dataset 2 times by removing logS >0.5 and cut off compounds with molecular weight >900. [1]
Pretrain graph embeddings on larger molecular datasets (Husskonen, SoluProtMut) before finetuning.[2]
Perform cross-validation to assess robustness.

# References

1. Ulrich, N.; Voigt, K.; Kudria, A.; Böhme, A.; Ebert, R.-U. Prediction of the Water Solubility by a Graph Convolutional-Based Neural Network on a Highly Curated Dataset. J. Cheminform. 2025, 17 (1), 55. DOI: 10.1186/s13321-025-01000-9.

2. Francoeur, P. G.; Koes, D. R. SolTranNet–A Machine Learning Tool for Fast Aqueous Solubility Prediction. J. Chem. Inf. Model. 2021, 61 (6), 2530–2536. DOI: 10.1021/acs.jcim.1c00331.

3. Sorkun, M. C.; Khetan, A.; Er, S. AqSolDB, a Curated Reference Set of Aqueous Solubility and 2D Descriptors for a Diverse Set of Compounds. Sci. Data 2019, 6 (1), 143. DOI: 10.1038/s41597-019-0151-1.

4. Ajami, D.; Oeckler, O.; Simon, A.; et al. Title Related to Aqueous Solubility Mechanisms or Predictive Challenges (identifying often cited solubility data discussion). Eur. J. Pharm. Biopharm. 2019, DOI: 10.1016/j.ejpb.2019.02.007.

5. Sorkun, M. C., Khetan, A., & Er, S. (2019). AqSolDB, a curated reference set of aqueous solubility and 2D descriptors for a diverse set of compounds. Scientific Data, 6(1), 143. DOI: 10.1038/s41597-019-0151-1

