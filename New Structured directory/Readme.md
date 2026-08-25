# Lifestyle & Wellbeing Pattern Analysis

> **Unsupervised analysis of lifestyle, wellbeing, and work-life-balance patterns using feature engineering, PCA, and K-Means clustering.**

This repository contains the complete analysis workflow for the **Lifestyle and Health Dataset** investigated in the project *“Analyzing the Impact of Eating Habits and Sedentary Lifestyle on Health Risk Patterns”*.

This README intentionally covers **Dataset 1 only**. The project report contains analysis of a second dataset as well, but that analysis is outside the scope of this repository section.

---

## 1. Project Overview

Lifestyle and wellbeing are influenced by several interacting factors rather than a single measurable variable. This project investigates whether multidimensional lifestyle information can be transformed into meaningful wellbeing indicators and used to identify groups of participants with similar lifestyle profiles.

The analysis follows an **unsupervised learning** approach:

```text
Raw Dataset
     │
     ▼
Data Loading & Inspection
     │
     ▼
Cleaning & Preprocessing
     │
     ▼
Exploratory Data Analysis
     │
     ▼
Feature Engineering
     │
     ▼
Engineered Feature Validation
     │
     ▼
Standardization
     │
     ▼
PCA
     │
     ▼
K-Means Clustering
     │
     ├── Original 12-feature space
     │
     └── PCA 3-component space
     │
     ▼
Cluster Evaluation & Profiling
```

The main objective is **pattern discovery**, not disease prediction or causal inference.

---

## 2. Research Questions

The analysis investigates the following questions:

1. Can multiple lifestyle variables be combined into meaningful wellbeing dimensions?
2. Do the engineered wellbeing indices show meaningful relationships with **Work-Life Balance Score**?
3. Can PCA provide a more useful representation of the multidimensional feature space?
4. Can K-Means identify distinguishable lifestyle/wellbeing profiles?
5. Which variables contribute most strongly to the separation between the resulting clusters?

---

## 3. Dataset

### Dataset

**Wellbeing and Lifestyle Dataset**

Source:

[Wellbeing and Lifestyle Analysis on Kaggle](https://carlespan.github.io/Wellbeing-and-lifestyle-Kaggle/)

### Dataset characteristics

| Property | Value |
|---|---:|
| Original observations | 15,972 |
| Original variables | 24 |
| Observations after cleaning | **15,489** |
| Final clustering variables | **12** |
| Analysis type | Unsupervised / exploratory |

The dataset contains questionnaire-based lifestyle and wellbeing measurements covering areas such as:

- Physical activity
- Dietary habits
- Stress
- Social relationships
- Productivity
- Recovery
- Personal wellbeing
- Work-life balance

The questionnaire variables use different rating scales, including 0–2, 0–5 and 0–10 scales. Therefore, direct Euclidean-distance-based clustering without appropriate scaling would give disproportionate influence to variables with larger numerical ranges.

---

## 4. Features Used for Final Clustering

The final clustering representation contained **12 variables**.

### Original questionnaire variables

| Feature | Description |
|---|---|
| `PLACES_VISITED` | Number of places/countries visited |
| `DONATION` | Donation-related rating |
| `BMI_RANGE` | BMI category/range |
| `SUFFICIENT_INCOME` | Perceived income sufficiency |
| `PERSONAL_AWARDS` | Personal awards/achievements |
| `AGE` | Encoded age category |

### Engineered wellbeing variables

| Feature | Purpose |
|---|---|
| `MENTAL_WELLNESS_INDEX` | Represents mental wellbeing-related characteristics |
| `SOCIAL_WELLNESS_INDEX` | Represents social support and social connection |
| `PHYSICAL_WELLNESS_INDEX` | Represents physical-health-related lifestyle behaviour |
| `PRODUCTIVITY_INDEX` | Represents achievement and task completion |
| `RECOVERY_WELLBEING_INDEX` | Represents recovery, personal time and restorative behaviour |
| `STRESS_BURDEN_INDEX` | Represents stress-related burden |

---

## 5. Data Preprocessing

The preprocessing workflow included:

### 5.1 Duplicate handling

Duplicate records were identified and removed.

The original dataset contained **482 duplicate rows**.

### 5.2 Invalid/malformed value handling

An anomalous value in `DAILY_STRESS` was identified and removed before converting the feature to an integer representation.

### 5.3 Identifier removal

`Timestamp` was removed because it was not used as a modelling feature.

### 5.4 Categorical encoding

`AGE` was converted into an ordinal numerical representation:

```text
Less than 20  → 0
21 to 35      → 1
36 to 50      → 2
51 or more    → 3
```

`GENDER` was converted using one-hot encoding with the first category dropped.

### 5.5 Standardization

The final 12 modelling variables were standardized using `StandardScaler`.

This produced approximately:

```text
mean ≈ 0
standard deviation ≈ 1
```

Standardization was important because the clustering algorithm uses distance calculations.

---

## 6. Exploratory Data Analysis

The EDA stage examined:

- Univariate distributions
- Feature distributions and skewness
- Bivariate relationships
- Correlation structure
- Pairwise relationships
- Age-group patterns
- Rating-scale validity
- Outlier counts using the IQR rule
- Multivariate relationships

Correlation analysis was also used to understand relationships among lifestyle variables before feature engineering and clustering.

---

# 7. Feature Engineering

A major part of the analysis was the construction of six interpretable wellbeing indices.

Before constructing the indices, questionnaire variables were transformed to a common **0–1 scale** using theoretical minimum and maximum values.

For negatively oriented variables, reverse coding was applied:

```text
Reverse-coded value = 1 − normalized value
```

This ensured that higher values consistently represented better wellbeing for the positive indices.

---

## 7.1 Mental Wellness Index

Constructed from:

- `DAILY_STRESS` (reverse coded)
- `FLOW`
- `LIVE_VISION`

Conceptually:

```text
Mental Wellness
= mean(reverse stress, flow, live vision)
```

---

## 7.2 Social Wellness Index

Constructed from:

- `CORE_CIRCLE`
- `SUPPORTING_OTHERS`
- `SOCIAL_NETWORK`

```text
Social Wellness
= mean(core circle, supporting others, social network)
```

---

## 7.3 Physical Wellness Index

Constructed from:

- `FRUITS_VEGGIES`
- `DAILY_STEPS`
- `SLEEP_HOURS`

```text
Physical Wellness
= mean(fruits & vegetables, daily steps, sleep hours)
```

---

## 7.4 Productivity Index

Constructed from:

- `ACHIEVEMENT`
- `TODO_COMPLETED`

```text
Productivity
= mean(achievement, tasks completed)
```

---

## 7.5 Recovery & Wellbeing Index

Constructed from:

- `WEEKLY_MEDITATION`
- `TIME_FOR_PASSION`
- Reverse-coded `LOST_VACATION`
- Reverse-coded `DAILY_SHOUTING`

```text
Recovery & Wellbeing
= mean(meditation, passion time,
       reverse lost vacation,
       reverse daily shouting)
```

---

## 7.6 Stress Burden Index

Constructed from:

- `DAILY_STRESS`
- `DAILY_SHOUTING`
- Inverse of `WEEKLY_MEDITATION`

```text
Stress Burden
= mean(daily stress,
       daily shouting,
       1 − meditation)
```

---

# 8. Engineered Feature Validation

The six engineered indices were validated using:

- Range checks
- Variance analysis
- Pearson correlation
- Spearman correlation
- Expected-direction checks
- Redundancy analysis against parent variables

All six engineered indices remained within the intended **0–1 range**.

### Relationship with Work-Life Balance

| Engineered Feature | Pearson r |
|---|---:|
| Productivity Index | **0.685** |
| Social Wellness Index | **0.662** |
| Mental Wellness Index | **0.644** |
| Recovery & Wellbeing Index | **0.629** |
| Physical Wellness Index | **0.574** |
| Stress Burden Index | **−0.516** |

All six observed correlation directions matched the expected conceptual direction.

The strongest positive relationship was observed for **Productivity Index**, while **Stress Burden Index** showed the expected negative relationship.

Spearman correlations showed the same general pattern, supporting the robustness of the observed monotonic relationships.

> These correlations indicate association. They do not establish that an engineered index causes changes in Work-Life Balance.

---

# 9. PCA Analysis

PCA was applied after standardization.

The objective was not simply to maximize variance retention. The PCA representation was also evaluated based on whether it produced a more useful clustering structure.

### Explained variance

| Component | Explained Variance | Cumulative |
|---|---:|---:|
| PC1 | **28.65%** | 28.65% |
| PC2 | **12.41%** | 41.06% |
| PC3 | **10.41%** | **51.47%** |

The final PCA representation reduced:

```text
12 original modelling features
             ↓
       3 principal components
```

Although three components retained only about half of the total standardized variance, their usefulness was evaluated through downstream clustering performance rather than variance retention alone.

---

# 10. Clustering Experiments

K-Means was evaluated for:

```text
k = 2, 3, 4, ..., 10
```

The following metrics were considered:

- WCSS / Inertia
- Silhouette Score
- Davies-Bouldin Index
- Calinski-Harabasz Index

Two feature representations were compared:

1. Original standardized 12-feature space
2. PCA representation with 3 components

---

## 11. Original vs PCA Representation

### k = 2

| Representation | Features | Silhouette ↑ | Davies-Bouldin ↓ | Calinski-Harabasz ↑ |
|---|---:|---:|---:|---:|
| Original | 12 | 0.1606 | 2.0634 | 3509.94 |
| **PCA** | **3** | **0.3034** | **1.2439** | **8670.23** |

PCA improved all three cluster-quality metrics:

- Higher Silhouette Score
- Lower Davies-Bouldin Index
- Higher Calinski-Harabasz Index

### k = 5

| Representation | Features | Silhouette ↑ | Davies-Bouldin ↓ | Calinski-Harabasz ↑ |
|---|---:|---:|---:|---:|
| Original | 12 | 0.1042 | 2.2314 | 1805.98 |
| PCA | 3 | 0.2434 | 1.1433 | 6053.45 |

PCA again produced better internal clustering metrics.

---

# 12. Final Model

The final clustering configuration was:

```text
Representation : PCA
Components     : 3
Algorithm      : K-Means
Number of      : 2
clusters
Random state   : 42
n_init         : 20
```

### Final evaluation

| Metric | Result |
|---|---:|
| Silhouette Score | **0.3034** |
| Davies-Bouldin Index | **1.2439** |
| Calinski-Harabasz Index | **8670.23** |

The PCA-based representation was therefore selected over the original feature representation.

The original and PCA-based k=2 assignments also produced an:

```text
Adjusted Rand Index = 0.9910
```

This indicates that the dimensionality-reduction step preserved the broad two-group partition very closely while producing substantially better internal clustering metrics.

---

# 13. Final Cluster Profiles

The final K-Means model produced two approximately balanced clusters.

| Cluster | Participants | Percentage |
|---|---:|---:|
| Cluster 0 | **7,683** | **49.60%** |
| Cluster 1 | **7,806** | **50.40%** |
| **Total** | **15,489** | **100%** |

### Mean feature values

| Feature | Cluster 0 | Cluster 1 |
|---|---:|---:|
| Places Visited | 3.731 | **6.698** |
| Donation | 1.968 | **3.453** |
| BMI Range | 1.447 | 1.371 |
| Sufficient Income | 1.600 | **1.859** |
| Personal Awards | 4.302 | **7.096** |
| Age | 1.435 | **1.778** |
| Mental Wellness Index | 0.277 | **0.479** |
| Social Wellness Index | 0.474 | **0.699** |
| Physical Wellness Index | 0.520 | **0.665** |
| Productivity Index | 0.366 | **0.607** |
| Recovery & Wellbeing Index | 0.501 | **0.684** |
| Stress Burden Index | **0.502** | 0.318 |

---

# 14. Main Cluster-Separating Features

The largest absolute centroid differences were observed in:

1. **Productivity Index**
2. **Mental Wellness Index**
3. **Recovery & Wellbeing Index**
4. **Social Wellness Index**
5. **Stress Burden Index**

The strongest centroid difference was observed for:

```text
Productivity Index ≈ 1.112
```

followed by:

```text
Mental Wellness Index ≈ 1.065
Recovery & Wellbeing Index ≈ 1.055
Social Wellness Index ≈ 0.992
Stress Burden Index ≈ 0.951
```

---

# 15. Interpretation of the Clusters

### Cluster 0

Cluster 0 shows comparatively lower values for most positive wellbeing and lifestyle indicators.

Its most notable characteristic is a **higher Stress Burden Index**:

```text
Cluster 0 = 0.502
Cluster 1 = 0.318
```

The cluster also has lower:

- Mental Wellness
- Social Wellness
- Physical Wellness
- Productivity
- Recovery & Wellbeing

### Cluster 1

Cluster 1 exhibits higher values across most positive wellbeing-related dimensions:

- Mental Wellness
- Social Wellness
- Physical Wellness
- Productivity
- Recovery & Wellbeing

It also shows higher:

- Places Visited
- Donation
- Sufficient Income
- Personal Awards
- Age category

while having a lower Stress Burden Index.

These differences suggest two broad lifestyle/wellbeing profiles within the dataset.

**Important:** the clusters should be interpreted as data-derived associations, not as clinically defined groups or causal categories.

---

# 16. Key Findings

### Feature Engineering

- Six interpretable wellbeing indices were created.
- All indices were transformed to the intended 0–1 range.
- All six showed meaningful variability.
- Productivity had the strongest positive Pearson correlation with Work-Life Balance (`r = 0.685`).
- Stress Burden had a negative relationship with Work-Life Balance (`r = −0.516`).

### PCA

- 12 standardized modelling variables were reduced to 3 components.
- PC1 explained 28.65% of variance.
- PC1–PC3 explained 51.47% cumulatively.
- PCA was more useful for clustering than the original feature space.

### Clustering

- K-Means was evaluated across k = 2–10.
- The final solution used PCA + K-Means with k = 2.
- Silhouette improved from **0.1606 → 0.3034**.
- Davies-Bouldin improved from **2.0634 → 1.2439**.
- Calinski-Harabasz improved from **3509.94 → 8670.23**.
- ARI between original and PCA k=2 assignments was **0.9910**.

### Cluster structure

The resulting clusters were almost evenly divided:

```text
Cluster 0 → 49.60%
Cluster 1 → 50.40%
```

The major separating dimensions were wellbeing, productivity, recovery and stress-related indices.

---

# 17. Limitations

The findings should be interpreted with the following limitations:

- The dataset is **cross-sectional**, so temporal or causal relationships cannot be established.
- Many variables are **self-reported questionnaire responses**, making them dependent on participant perception and reporting.
- The engineered indices were constructed using a conceptual framework and were **not validated against external medical or psychological instruments**.
- Some engineered features may contain information overlapping with their parent variables.
- The clustering quality is improved by PCA, but the final Silhouette Score of approximately 0.30 still indicates that the clusters are not perfectly separated.
- The analysis identifies patterns within this dataset and should not be treated as a clinical classification system.

---

# 18. Project Structure

The notebooks are organized according to the analysis pipeline:

```text
.
├── Data_Loading.ipynb
├── preprocessing.ipynb
├── EDA.ipynb
├── feature_engineering.ipynb
├── feature_validation.ipynb
├── PCA_evaluation.ipynb
├── clustering.ipynb
│
├── dataset/
│   ├── main_dataset.csv
│   ├── clean_dataset.csv
│   ├── engineered_dataset.csv
│   ├── model_dataset.csv
│   ├── preprocessed_dataset.csv
│   ├── scaled_features.csv
│   └── pca_features.csv
│
└── README.md
```

The generated intermediate datasets make it possible to inspect the transformation stages rather than treating the final clustering result as a black box.

---

# 19. How to Run

## 1. Clone the repository

```bash
git clone [REPOSITORY_URL](https://github.com/Adityaa-KC/Analyzing-the-Impact-of-Eating-Habits-and-Sedentary-Lifestyle-on-Health-Risk-Patterns.git)
cd Analyzing the Impact of Eating Habits and Sedentary Lifestyle on Health Risk Patterns
```

## 2. Create a Python environment

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

On Linux/macOS:

```bash
source .venv/bin/activate
```

## 3. Install dependencies

```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy statsmodels factor-analyzer jupyter
```

## 4. Run the notebooks in order

```text
1. Data_Loading.ipynb
2. preprocessing.ipynb
3. EDA.ipynb
4. feature_engineering.ipynb
5. feature_validation.ipynb
6. PCA_evaluation.ipynb
7. clustering.ipynb
```

The notebooks save intermediate datasets into the `dataset/` directory.

---

# 20. Software and Hardware

### Software

- Python 3.x
- Jupyter Notebook / VS Code
- NumPy
- Pandas
- Matplotlib
- Seaborn
- Scikit-learn
- SciPy
- Statsmodels
- Factor Analyzer

### Minimum hardware

| Component | Requirement |
|---|---|
| Processor | Dual-core Intel/AMD |
| RAM | 4 GB |
| Storage | 5–10 GB free |
| OS | Windows 10/11, Linux or macOS |
| GPU | Not required |
| Internet | Mainly required for package installation and research resources |

---

# 21. Reproducibility

The analysis uses fixed K-Means settings:

```python
KMeans(
    n_clusters=2,
    random_state=42,
    n_init=20
)
```

PCA is performed using:

```python
PCA(n_components=3)
```

The preprocessing, feature engineering, validation, PCA and clustering stages are separated into individual notebooks so that each stage can be independently inspected.

---

# 22. What This Project Does Not Claim

This project **does not** claim that:

- the identified clusters are medical diagnoses;
- a lifestyle factor directly causes a particular health outcome;
- the clusters can be used as a clinical decision-making system;
- the clustering results represent universally valid population groups.

The purpose is to identify **associations and structural patterns** within the analyzed dataset.

---

# 23. Conclusion

The analysis demonstrates that multidimensional lifestyle and wellbeing variables can be transformed into interpretable indices and analyzed using unsupervised learning.

For this dataset, PCA produced a substantially more useful clustering representation than the original 12-feature space. The final PCA + K-Means configuration produced two approximately balanced clusters, with the strongest differences occurring across productivity, mental wellness, recovery, social wellness and stress burden.

The main takeaway is not that the population can be divided into two perfectly distinct groups. Rather, the results suggest that **different combinations of lifestyle and wellbeing characteristics form broad patterns that are more informative when analyzed jointly than when individual variables are considered in isolation**.

---

## References

1. Panyella, C. (2021). *Wellbeing and Lifestyle Analysis on Kaggle*.  
   https://carlespan.github.io/Wellbeing-and-lifestyle-Kaggle/

2. The accompanying research report for this project documents the methodology, experimental results and interpretation of the analysis.

---

## Author

**Aditya Kumar Chaudhary**

B.Sc. (Hons) Computer Science, II Year  
Acharya Narendra Dev College, University of Delhi

Project conducted as part of the **ELITE Summer Research Project, 2026**.
