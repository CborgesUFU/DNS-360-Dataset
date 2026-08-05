# Machine Learning Baseline Experiments

This directory contains the six Python scripts used to execute the supervised machine-learning baseline experiments associated with UFU-Do53-EXF and UFU-DoH-EXF.

The expected labels are:

```text
Benign
Exfiltration
```

The experiments are intra-dataset evaluations. They measure internal discriminative capability under the controlled scenarios and do not independently demonstrate generalization to unseen networks, resolvers, capture periods, or exfiltration implementations.

## Directory contents

```text
Machine Learning Models/
├── ML_Do53_RF_V3.py
├── ML_Do53_SVM_V1.py
├── ML_Do53_XGBoost_V1.py
├── ML_DoH_RF_V1.py
├── ML_DoH_SVM_V1.py
├── ML_DoH_XGBoost_V1.py
└── README.md
```

| Script | Dataset | Model | Input strategy | Interpretation output |
|---|---|---|---|---|
| `ML_Do53_RF_V3.py` | UFU-Do53-EXF | Random Forest | Predefined training and testing directories | SHAP |
| `ML_Do53_SVM_V1.py` | UFU-Do53-EXF | SVM with radial basis function kernel | Predefined training and testing directories | Classification metrics |
| `ML_Do53_XGBoost_V1.py` | UFU-Do53-EXF | XGBoost | Predefined training and testing directories | SHAP |
| `ML_DoH_RF_V1.py` | UFU-DoH-EXF | Random Forest | One dataset with internal split | Feature importance |
| `ML_DoH_SVM_V1.py` | UFU-DoH-EXF | Linear SVM | One dataset with internal split | Model coefficients |
| `ML_DoH_XGBoost_V1.py` | UFU-DoH-EXF | XGBoost | One dataset with internal split | Feature importance |

---

## Requirements

Recommended Python version:

```text
Python 3.11
```

Main packages:

```text
numpy
pandas
scikit-learn
xgboost
matplotlib
shap
```

Recommended hardware for complete execution:

- 32 virtual CPUs;
- 128 GB RAM;
- 500 GB to 1 TB high-performance SSD or NVMe storage.

Recommended high-capacity configuration:

- 48 to 64 virtual CPUs;
- 192 GB to 256 GB RAM;
- at least 1 TB high-performance SSD or NVMe storage.

A GPU is not required.

---

# UFU-Do53-EXF experiments

The Do53 scripts use separate training and testing directories.

The predefined 80/20 partition is created before script execution. The scripts do not perform `train_test_split` for the Do53 experiment.

Complete dataset:

| Property | Value |
|---|---:|
| Input features | 29 |
| Total samples | 5,351,064 |
| Benign samples | 4,812,119 |
| Exfiltration samples | 538,945 |

Predefined test partition:

| Class | Samples |
|---|---:|
| Benign | 962,436 |
| Exfiltration | 107,778 |
| Total | 1,070,214 |

## Common Do53 procedure

1. recursively identify training CSV files;
2. recursively identify testing CSV files;
3. sort and concatenate the files;
4. fit the target encoder using training labels;
5. reject unseen test labels;
6. identify feature types using training data;
7. align the testing schema with the training schema;
8. impute missing numerical and categorical values;
9. encode categorical variables;
10. train the selected classifier;
11. save metrics, predictions, confusion matrices, and diagnostics.

## `ML_Do53_RF_V3.py`

Execution:

```bash
python "scripts/Machine Learning Models/ML_Do53_RF_V3.py"
```

Configuration:

```text
Model: RandomForestClassifier
Number of trees: 100
Model random state: 50
Class weight: balanced
Training and testing strategy: predefined 80/20 partition
```

Expected results:

| Metric | Value |
|---|---:|
| Accuracy | 0.999996 |
| Balanced Accuracy | 0.999981 |
| Macro Precision | 0.999998 |
| Macro Recall | 0.999981 |
| Macro F1 | 0.999990 |
| Weighted F1 | 0.999996 |
| False Positives | 0 |
| False Negatives | 4 |

The script also performs SHAP analysis on a bounded sample.

## `ML_Do53_XGBoost_V1.py`

Execution:

```bash
python "scripts/Machine Learning Models/ML_Do53_XGBoost_V1.py"
```

Configuration:

```text
Number of estimators: 300
Maximum depth: 6
Learning rate: 0.1
Subsample: 0.8
Column sample by tree: 0.8
Model random state: 50
Training and testing strategy: predefined 80/20 partition
```

Expected results:

| Metric | Value |
|---|---:|
| Accuracy | 0.999998 |
| Balanced Accuracy | 0.999991 |
| Macro Precision | 0.999999 |
| Macro Recall | 0.999991 |
| Macro F1 | 0.999995 |
| Weighted F1 | 0.999998 |
| False Positives | 0 |
| False Negatives | 2 |

The script also performs SHAP analysis on a bounded sample.

## `ML_Do53_SVM_V1.py`

Execution:

```bash
python "scripts/Machine Learning Models/ML_Do53_SVM_V1.py"
```

Configuration:

```text
Model: SVC
Kernel: radial basis function
C: 1.0
Gamma: scale
Class weight: balanced
Numerical scaling: StandardScaler
Training and testing strategy: predefined 80/20 partition
```

Expected results:

| Metric | Value |
|---|---:|
| Accuracy | 0.999915 |
| Balanced Accuracy | 0.999817 |
| Macro Precision | 0.999714 |
| Macro Recall | 0.999817 |
| Macro F1 | 0.999765 |
| Weighted F1 | 0.999915 |
| False Positives | 58 |
| False Negatives | 33 |

---

# UFU-DoH-EXF experiments

The DoH scripts load the dataset, remove exact duplicate rows, and create an internal stratified 80/20 split.

Complete dataset:

| Property | Value |
|---|---:|
| Input features | 28 |
| Total samples | 1,048,575 |
| Benign samples | 1,016,438 |
| Exfiltration samples | 32,137 |

After exact duplicate removal:

| Property | Value |
|---|---:|
| Total samples | 1,022,257 |
| Benign samples | 992,111 |
| Exfiltration samples | 30,146 |

Test partition:

| Class | Samples |
|---|---:|
| Benign | 198,423 |
| Exfiltration | 6,029 |
| Total | 204,452 |

## Common DoH procedure

1. load the dataset;
2. verify the target column;
3. remove exact duplicate rows;
4. separate labels and features;
5. create a stratified 80/20 split with `random_state=42`;
6. fit the target encoder using training labels;
7. identify numerical and categorical columns using training data;
8. apply model-specific preprocessing;
9. train the selected classifier;
10. save metrics, predictions, confusion matrices, and interpretation outputs.

## `ML_DoH_RF_V1.py`

Execution:

```bash
python "scripts/Machine Learning Models/ML_DoH_RF_V1.py"
```

Configuration:

```text
Model: RandomForestClassifier
Number of trees: 100
Random state: 42
Class weight: balanced
Exact duplicate removal: enabled
Stratified 80/20 split: enabled
```

Expected results:

| Metric | Value |
|---|---:|
| Accuracy | 0.999985 |
| Balanced Accuracy | 0.999832 |
| Macro Precision | 0.999912 |
| Macro Recall | 0.999832 |
| Macro F1 | 0.999872 |
| Weighted F1 | 0.999985 |
| False Positives | 1 |
| False Negatives | 2 |

The script exports feature importance values.

## `ML_DoH_XGBoost_V1.py`

Execution:

```bash
python "scripts/Machine Learning Models/ML_DoH_XGBoost_V1.py"
```

Configuration:

```text
Number of estimators: 300
Maximum depth: 6
Learning rate: 0.1
Subsample: 0.8
Column sample by tree: 0.8
Objective: binary:logistic
Evaluation metric: logloss
Random state: 42
Exact duplicate removal: enabled
Stratified 80/20 split: enabled
```

Expected results:

| Metric | Value |
|---|---:|
| Accuracy | 0.999985 |
| Balanced Accuracy | 0.999832 |
| Macro Precision | 0.999912 |
| Macro Recall | 0.999832 |
| Macro F1 | 0.999872 |
| Weighted F1 | 0.999985 |
| False Positives | 1 |
| False Negatives | 2 |

The script exports feature importance values.

## `ML_DoH_SVM_V1.py`

Execution:

```bash
python "scripts/Machine Learning Models/ML_DoH_SVM_V1.py"
```

Configuration:

```text
Model: LinearSVC
C: 1.0
Maximum iterations: 10,000
Class weight: balanced
Random state: 42
Numerical scaling: StandardScaler
Exact duplicate removal: enabled
Stratified 80/20 split: enabled
```

Expected results:

| Metric | Value |
|---|---:|
| Accuracy | 0.999863 |
| Balanced Accuracy | 0.999688 |
| Macro Precision | 0.997927 |
| Macro Recall | 0.999688 |
| Macro F1 | 0.998806 |
| Weighted F1 | 0.999863 |
| False Positives | 25 |
| False Negatives | 3 |

The script exports model coefficients and the highest absolute coefficients.

---

## Common output files

Depending on the selected script, outputs may include:

```text
report.csv
classification_report.csv
confusion_matrix.csv
confusion_matrix_normalized.csv
predictions.csv
class_distributions.csv
split_metadata.json
feature_inventory.csv
feature_importance.csv
svm_coefficients.csv
svm_coefficients_top200.csv
train_schema.csv
test_schema.csv
schema_missing_in_test.csv
schema_extra_in_test.csv
test_missing_percent_after_align.csv
summary.txt
```

Recommended locations:

```text
results/Machine Learning/Do53
results/Machine Learning/DoH
```

## Interpretation

The reported results demonstrate internal consistency and discriminative capability under the evaluated scenarios. They do not independently establish production effectiveness or cross-environment generalization.
