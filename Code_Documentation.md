
# BDA Source Code Documentation

## 1. Code Structure

The notebook contains the following functions and/or classes:

```python
def apply_smote_enn(df):
def evaluate_model(y_true, y_pred, y_proba, dataset_name):
def preprocess_data(df):
def train_xgboost(df, dataset_name):
```

## 2. Modules Used

Below are the external and standard Python modules used:

```python
from imblearn.combine import SMOTEENN
from pyspark.ml.feature import StringIndexer, VectorAssembler, StandardScaler
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, when
from sklearn.metrics import classification_report, roc_auc_score, accuracy_score, precision_score, recall_score, f1_score, confusion_matrix, roc_curve
import joblib
import matplotlib.pyplot as plt
import numpy as np
import seaborn as sns
import xgboost as xgb
```

## 3. Execution Steps

To execute this notebook successfully:

1. Install all the dependencies listed in the **Dependencies** section.
2. Launch a Jupyter Notebook environment.
3. Open the notebook file `BDA Source Code.ipynb`.
4. Run each cell sequentially using `Shift + Enter`.

## 4. Dependencies and Environment Setup

Install the following packages before running the code:

```bash
pip install pandas numpy matplotlib seaborn pyspark
```

Additional tools may be needed depending on your system setup. Make sure to have Java installed if using PySpark.

- **Python version:** 3.x recommended
- **Jupyter Notebook** or **JupyterLab**

## 5. Notes

Ensure that Spark and Hadoop environments are properly configured if this notebook interacts with a big data cluster.
