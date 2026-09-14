# MDI3003 – Advanced Predictive Analytics | Lab 08

## Agricultural Predictive Analytics – Crop Yield Prediction with a Guided Crop-Label Classification Extension

**Student:** SRIJITH R  
**Registration Number:** 24MID0009  
**Course:** MDI3003 – Advanced Predictive Analytics  
**Experiment:** 08  
**Semester:** Fall 2026–2027  
**Data access date:** 14 September 2026

---

## 1. Project Overview

This repository contains the complete implementation and submission artifacts for MDI3003 Advanced Predictive Analytics – Experiment 08.

The core task is a **district-level Rice yield regression problem**. The objective is to predict Rice yield in tonnes per hectare (t/ha) using:

- State
- District
- Season
- Harvest year

The experiment uses a chronological train-validation-test protocol so that later years are not used to train or select the model.

A second dataset, the **Crop Recommendation Dataset**, is included as the guided classification extension. The classification implementation is present in the notebook, but empirical classification results are intentionally not reported unless the required IID/independence review is approved.

---

## 2. Main Objectives

1. Define a measurable agricultural prediction problem.
2. Audit the dataset and target units.
3. Remove target-derived predictors that could cause leakage.
4. Create a chronological train-validation-test split.
5. Perform training-only exploratory data analysis.
6. Build a leakage-safe preprocessing pipeline.
7. Compare a simple baseline with linear and nonlinear regression models.
8. Select the model using validation MAE.
9. Lock and evaluate the final test set only after model selection.
10. Perform error analysis and temporal robustness checks.
11. Save reproducible model, prediction, figure and artifact files.
12. Provide a guided crop-label classification implementation without inventing unsupported results.

---

## 3. Repository Structure

```text
24MID0009_Lab08/
│
├── 24MID0009_Lab08.ipynb
├── 24MID0009_Lab08_Report.pdf
├── 24MID0009_Lab08_Validation_Results.csv
├── 24MID0009_Lab08_Test_Results.csv
├── 24MID0009_Lab08_Error_Analysis.csv
├── 24MID0009_Lab08_README.md
├── requirements.txt
│
├── models/
│   └── selected_bundle.joblib
│
├── figures/
│   ├── training_year_coverage.png
│   ├── training_yield_distribution.png
│   ├── actual_predicted.png
│   └── residuals.png
│
└── artifacts/
    ├── config.json
    ├── versions.json
    ├── split_manifest.csv
    ├── raw_data_hash.txt
    ├── acceptance.json
    ├── rolling_origins.csv
    ├── year_robustness.csv
    ├── test_predictions.csv
    └── TEST_LOCK
```

The actual submission may keep the generated files under `outputs/core/` as produced by the notebook. They can be copied into the top-level `models/`, `figures/`, and `artifacts/` folders when preparing the GitHub repository.

---

## 4. Software Requirements

The project uses Python and the following packages:

- numpy
- pandas
- matplotlib
- scikit-learn
- joblib
- requests

Python standard-library modules such as `os`, `json`, `time`, `hashlib`, `zipfile`, `platform` and `pathlib` do not need separate installation.

Install everything using:

```bash
pip install -r requirements.txt
```

For Google Colab, the notebook can be executed directly after uploading/opening the notebook. If required, run:

```python
!pip install -r requirements.txt
```

Internet access is required for the dataset download cells.

---

## 5. Running the Project in Google Colab

### Step 1 – Open the notebook

Open:

```text
24MID0009_Lab08.ipynb
```

in Google Colab.

### Step 2 – Install dependencies

Run:

```python
!pip install -r requirements.txt
```

If the file is not uploaded to Colab, install the packages directly:

```python
!pip install numpy pandas matplotlib scikit-learn joblib requests
```

### Step 3 – Run the notebook from the first cell

Run the cells **in order**.

Do not skip cells because later cells depend on variables, fitted pipelines and saved artifacts created earlier.

---

# 6. Directory Setup

The notebook creates the following directories automatically:

```text
/content/data/

/content/outputs/core/models/
/content/outputs/core/figures/
/content/outputs/core/artifacts/

/content/outputs/classification/models/
/content/outputs/classification/figures/
/content/outputs/classification/artifacts/
```

The `data/` directory stores downloaded/input datasets.

The `outputs/core/` directory stores the regression results.

The `outputs/classification/` directory is reserved for the classification extension.

---

# 7. Dataset 1 – Regression Dataset

## ICRISAT District-Level Data

Dataset:

**ICRISAT District-Level Data: Heterogeneous Climate Effect on Crop Yield and Associated Risks to Water Security in India**

Author:

**Mohapatra, S. (2023)**

Publisher:

**Mendeley Data, Version 1**

DOI:

```text
10.17632/ywp3y5j9vv.1
```

License:

```text
CC BY 4.0
```

Data access date:

```text
14 September 2026
```

The experiment uses the Rice-only portion of the district-level agricultural data.

### Important source note

The current Colab notebook contains a public reproducibility download cell. If an instructor-verified canonical CSV is supplied, that file should be used instead of the public mirror.

The manual distinguishes source-page verification from verification of the actual downloaded file, schema and units. Therefore, for an official laboratory run, the instructor-verified canonical file takes priority.

---

# 8. Regression Data Preparation

The canonical regression schema uses:

```text
state
district
season
year
yield_t_ha
row_id
```

The predictor whitelist is:

```text
state
district
season
year
```

The target is:

```text
yield_t_ha
```

The target unit is:

```text
tonnes/hectare (t/ha)
```

Production and harvested-area variables are not used as predictors because they can directly reconstruct yield and therefore create target leakage.

---

# 9. Regression Dataset Audit

The notebook checks the following conditions:

- Rice-only scope
- Required columns
- Missing target values
- Finite target values
- Non-negative yield
- Integer year
- Complete state values
- Complete district values
- Complete season values
- Unique row IDs
- No repeated district-season-year keys
- At least seven years of observations
- Correct chronological boundaries

These checks are performed before model training.

---

# 10. Unit Conversion

The canonical target used by the model is:

```text
yield_t_ha
```

If the source yield is supplied in kg/ha, it must be converted to t/ha using:

```text
yield_t_ha = yield_kg_ha / 1000
```

The conversion must be recorded and retained as part of the data-preparation evidence.

Do not mix kg/ha and t/ha in the same target column.

---

# 11. Chronological Train/Validation/Test Split

The split is based on observed years.

### Training

```text
1990–2011
```

### Validation

```text
2012–2013
```

### Final test

```text
2014–2015
```

Current reported split sizes:

```text
Training   : 13,640
Validation :    620
Test       :    620
```

The validation set is used for model comparison and selection.

The final test set remains locked until the model has been selected.

---

# 12. Exploratory Data Analysis

EDA is performed only using the training data before model selection.

The notebook produces:

```text
training_yield_distribution.png
training_year_coverage.png
```

### Training yield distribution

This plot shows the distribution of Rice yield values in the training period.

### Training year coverage

This plot verifies the historical coverage from 1990 through 2011.

EDA must not use the final test years for model decisions.

---

# 13. Preprocessing Pipeline

Categorical features:

```text
state
district
season
```

are processed using:

```python
OneHotEncoder(handle_unknown="ignore")
```

The numerical feature:

```text
year
```

uses:

```text
SimpleImputer(strategy="median")
StandardScaler()
```

The preprocessing is placed inside the model pipeline.

The pipeline is fitted only on the training data.

Validation and test data are transformed using the already-fitted preprocessing.

This prevents preprocessing statistics from being learned from held-out data.

---

# 14. Regression Models

Four approaches are compared.

## 14.1 Median Baseline

The baseline predicts the training-set median Rice yield for every observation.

This provides a simple reference for MAE.

## 14.2 Ridge Trend

Configuration:

```text
alpha = 1.0
solver = lsqr
```

The year feature allows the model to capture an overall temporal trend while categorical variables represent geographic and seasonal differences.

## 14.3 Decision Tree

Configuration:

```text
max_depth = 6
min_samples_leaf = 10
random_state = 42
```

## 14.4 Random Forest

Configuration:

```text
n_estimators = 60
max_depth = 12
min_samples_leaf = 5
n_jobs = 2
random_state = 42
```

All models use the same training and validation observations.

---

# 15. Model Selection

The primary model-selection metric is:

```text
MAE – Mean Absolute Error
```

The selected model is the model with the **lowest validation MAE**.

The final observed validation results were:

| Model | MAE | RMSE | R² |
|---|---:|---:|---:|
| Ridge Trend | 0.4282 | 0.5583 | 0.7460 |
| Random Forest | 0.5289 | 0.6548 | 0.6505 |
| Decision Tree | 0.7160 | 0.8620 | 0.3944 |
| Median | 0.9780 | 1.1902 | -0.1545 |

Therefore:

```text
Selected model = Ridge Trend
```

The test set is not used for this selection.

---

# 16. Test Lock

After model selection, the final test stage is opened.

The test lock is represented by:

```text
outputs/core/TEST_LOCK
```

The selected model is evaluated on:

```text
2014–2015
```

The same locked test rows are also used for the median baseline comparison.

---

# 17. Final Test Results

The reported final test results are:

| Model | MAE | RMSE | R² |
|---|---:|---:|---:|
| Ridge Trend | 0.4332 t/ha | 0.5707 t/ha | 0.7596 |
| Median baseline | 1.0095 t/ha | 1.2346 t/ha | -0.1248 |

The selected Ridge Trend model therefore performs substantially better than the median baseline for this defined historical prediction task.

---

# 18. Error Analysis

The notebook saves the final predictions to:

```text
outputs/core/artifacts/test_predictions.csv
```

The five largest absolute errors are saved in:

```text
24MID0009_Lab08_Error_Analysis.csv
```

The analysis includes:

- row ID
- state
- district
- season
- year
- actual yield
- prediction
- error
- absolute error

Possible agricultural explanations must be treated as hypotheses because the model does not contain detailed weather, irrigation, variety or soil variables.

---

# 19. Error Cases in This Run

The five largest test errors were:

| Row ID | District | Year | Actual | Prediction | Absolute Error |
|---|---|---:|---:|---:|---:|
| 37_2015 | Sehore | 2015 | 3.5355 | 1.0129 | 2.5225 |
| 27_2015 | Bhind | 2015 | 4.5550 | 2.2274 | 2.3276 |
| 37_2014 | Sehore | 2014 | 3.2028 | 0.9972 | 2.2056 |
| 138_2014 | Surendranagar | 2014 | 0.0000 | 1.7621 | 1.7621 |
| 909_2014 | Bhagalpur | 2014 | 3.3770 | 1.6302 | 1.7468 |

These values are empirical outputs from the run and should not be replaced with manually invented values.

---

# 20. Actual vs Predicted Plot

The figure:

```text
outputs/core/figures/actual_predicted.png
```

compares observed and predicted Rice yield on the locked test set.

The identity line represents perfect prediction.

Points farther from the identity line indicate larger prediction errors.

---

# 21. Residual Plot

The figure:

```text
outputs/core/figures/residuals.png
```

uses:

```text
residual = actual - predicted
```

Therefore:

- Positive residual → underprediction
- Negative residual → overprediction

Large residuals indicate observations where the selected model does not explain the observed yield well.

---

# 22. Rolling-Origin Robustness

Three development-year rolling origins are evaluated.

Reported MAE values:

| Origin | Model | Training Maximum Year | MAE |
|---|---|---:|---:|
| 2011 | Ridge Trend | 2010 | 0.3930 |
| 2012 | Ridge Trend | 2011 | 0.4128 |
| 2013 | Ridge Trend | 2012 | 0.4213 |

This is descriptive temporal robustness evidence.

It should not be interpreted as a statistical confidence interval analysis.

---

# 23. Year-wise Test Robustness

The final test set is also evaluated separately by year.

| Test Year | Observations | MAE | RMSE | R² |
|---|---:|---:|---:|---:|
| 2014 | 310 | 0.4593 | 0.6035 | 0.7304 |
| 2015 | 310 | 0.4070 | 0.5360 | 0.7866 |

This shows some year-to-year variation in prediction difficulty.

---

# 24. Model Saving and Reload Verification

The selected model is saved as:

```text
outputs/core/models/selected_bundle.joblib
```

The notebook reloads the saved bundle and predicts the same test observations again.

The reload check verifies that:

```text
original predictions == reloaded-model predictions
```

This is part of the reproducibility/acceptance check.

---

# 25. Reproducibility Artifacts

The regression output folder contains:

```text
outputs/core/
├── models/
│   └── selected_bundle.joblib
│
├── figures/
│   ├── actual_predicted.png
│   ├── residuals.png
│   ├── training_year_coverage.png
│   └── training_yield_distribution.png
│
├── artifacts/
│   ├── acceptance.json
│   ├── split_manifest.csv
│   ├── test_predictions.csv
│   ├── rolling_origins.csv
│   └── year_robustness.csv
│
├── selection.json
└── TEST_LOCK
```

The model-selection record stores the dataset SHA-256 hash, selected model, features, target and evaluation years.

---

# 26. Classification Dataset – Guided Extension

Dataset:

**Atharva Ingle – Crop Recommendation Dataset**

Source:

**Kaggle**

Features:

```text
N
P
K
temperature
humidity
ph
rainfall
```

Target:

```text
label
```

License:

```text
Apache License 2.0
```

Data access date:

```text
14 September 2026
```

---

# 27. Classification Implementation

The notebook contains code for:

1. Downloading the dataset.
2. Extracting the ZIP file.
3. Canonicalizing the feature names.
4. Checking the required classification columns.
5. Checking label support.
6. Checking duplicate vectors.
7. Requiring an explicit IID/independence review flag.
8. Performing a stratified 60/20/20 split.
9. Median-imputing and standardizing numerical features.
10. Training:
   - Majority baseline
   - Logistic Regression
   - Random Forest
11. Calculating:
   - Accuracy
   - Macro precision
   - Macro recall
   - Macro F1
12. Producing classification evaluation artifacts after approval.

---

# 28. Important Classification Restriction

The classification section is intentionally guarded by:

```python
iid_justified = False
```

The classification models are not executed for empirical results until an instructor confirms that the random IID split is justified.

When the instructor approves the independence assumption, change the flag in the notebook to:

```python
iid_justified = True
```

Then rerun the classification section.

Do not create or report classification scores while the required review has not been approved.

Do not invent classification accuracy, F1 scores or confusion matrices.

---

# 29. Classification Output Location

If the classification extension is approved and executed, results belong under:

```text
outputs/classification/
```

with:

```text
outputs/classification/models/
outputs/classification/figures/
outputs/classification/artifacts/
```

---

# 30. Important Execution Order

For the regression core, use this order:

```text
1. Imports and setup
2. Download/load dataset
3. Canonicalize data
4. Audit data
5. Create chronological split
6. Training-only EDA
7. Build preprocessing
8. Train baseline
9. Train Ridge
10. Train Decision Tree
11. Train Random Forest
12. Evaluate validation
13. Select model
14. Rolling-origin analysis
15. Create test lock
16. Evaluate locked test
17. Save predictions
18. Error analysis
19. Year-wise robustness
20. Generate plots
21. Save model
22. Reload model
23. Run acceptance checks
24. Optional classification extension
```

Do not evaluate the final test set before model selection.

---

# 31. How to Run in a Fresh Local Python Environment

Create a virtual environment:

### Windows

```bash
python -m venv .venv
.venv\Scripts\activate
```

### Linux/macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Start Jupyter:

```bash
jupyter notebook
```

Then open:

```text
24MID0009_Lab08.ipynb
```

---

# 32. Running Without Jupyter

The supplied notebook is the main student implementation.

If a standalone `lab08.py` implementation is included in the repository, the intended manual workflow is:

```bash
python lab08.py --config config.json
```

The canonical data should be placed under:

```text
data/
```

before running the standalone implementation.

Run validation before test evaluation.

---

# 33. Checking Generated Files

After execution, check:

```text
outputs/core/
```

Expected core files include:

```text
outputs/core/selection.json
outputs/core/TEST_LOCK
outputs/core/models/selected_bundle.joblib
outputs/core/artifacts/acceptance.json
outputs/core/artifacts/split_manifest.csv
outputs/core/artifacts/test_predictions.csv
outputs/core/artifacts/rolling_origins.csv
outputs/core/artifacts/year_robustness.csv
```

Expected figures:

```text
outputs/core/figures/training_yield_distribution.png
outputs/core/figures/training_year_coverage.png
outputs/core/figures/actual_predicted.png
outputs/core/figures/residuals.png
```

---

# 34. Preparing the Final Submission

The final submission should contain:

```text
24MID0009_Lab08.ipynb
24MID0009_Lab08_Report.pdf
24MID0009_Lab08_Validation_Results.csv
24MID0009_Lab08_Test_Results.csv
24MID0009_Lab08_Error_Analysis.csv
24MID0009_Lab08_README.md
24MID0009_Lab08_requirements.txt
models/
figures/
artifacts/
```

The report should contain the required problem contract, data provenance, method, evidence tables, figures, five error cases and limitations.

---

# 35. Creating a ZIP for Submission

From a Colab environment, a ZIP can be created with:

```python
!zip -r 24MID0009_Lab08_Submission.zip \
    24MID0009_Lab08.ipynb \
    24MID0009_Lab08_Report.pdf \
    24MID0009_Lab08_Validation_Results.csv \
    24MID0009_Lab08_Test_Results.csv \
    24MID0009_Lab08_Error_Analysis.csv \
    24MID0009_Lab08_README.md \
    requirements.txt \
    outputs/
```

Then:

```python
from google.colab import files
files.download("24MID0009_Lab08_Submission.zip")
```

Do not include unnecessary temporary files.

---

# 36. GitHub Upload

Recommended repository:

```text
24MID0009_Lab08
```

Upload the notebook, report, CSVs, README, requirements file and required artifact folders.

A large raw dataset should not be uploaded unless its licence and the instructor's instructions permit redistribution.

---

# 37. Reproducibility Checklist

Before submission, verify:

- [ ] Notebook opens correctly.
- [ ] Notebook cells are in the correct execution order.
- [ ] Regression dataset loads correctly.
- [ ] Rice-only filter is applied.
- [ ] Target is in t/ha.
- [ ] Predictor whitelist is correct.
- [ ] Production/area leakage variables are excluded.
- [ ] Train years are 1990–2011.
- [ ] Validation years are 2012–2013.
- [ ] Test years are 2014–2015.
- [ ] Test data is not used for model selection.
- [ ] Median baseline is included.
- [ ] Ridge model is included.
- [ ] Decision Tree is included.
- [ ] Random Forest is included.
- [ ] Validation MAE is used for selection.
- [ ] Test lock exists.
- [ ] Test predictions are saved.
- [ ] Five largest errors are saved.
- [ ] Actual-vs-predicted figure exists.
- [ ] Residual figure exists.
- [ ] Rolling-origin results exist.
- [ ] Year-wise robustness results exist.
- [ ] Selected model is saved.
- [ ] Model reload check passes.
- [ ] Acceptance record exists.
- [ ] Split manifest exists.
- [ ] Dataset hash is stored.
- [ ] Classification results are not fabricated.
- [ ] Report values match CSV values.
- [ ] GitHub repository contains the required files.
- [ ] Registration number is `24MID0009` everywhere.

---

# 38. Current Core Result Summary

The current completed regression run selected:

```text
Ridge Trend
```

Validation:

```text
MAE  = 0.4282 t/ha
RMSE = 0.5583 t/ha
R²   = 0.7460
```

Locked test:

```text
MAE  = 0.4332 t/ha
RMSE = 0.5707 t/ha
R²   = 0.7596
```

Median baseline test:

```text
MAE  = 1.0095 t/ha
RMSE = 1.2346 t/ha
R²   = -0.1248
```

These values should remain consistent with the submitted CSV files and report.

---

# 39. Limitations

The model uses only:

```text
state
district
season
year
```

It does not include detailed:

- Weather
- Rainfall
- Soil properties
- Irrigation
- Crop variety
- Fertilizer application
- Farm-level information

Therefore, the model should not be interpreted as a farm-level forecasting system or an agronomic crop-selection system.

The error analysis gives possible explanations, not confirmed causes.

---

# 40. Academic and Responsible Use

This project is a laboratory predictive-analytics exercise.

The results are intended for:

- student predictive analysis
- model comparison
- reproducibility demonstration
- historical district-level yield prediction

The results should not be used alone to make real agricultural decisions.

Any future operational system would require additional agricultural variables, validation on current data, domain-expert review and appropriate monitoring.

---

# 41. Dataset References

### Regression Dataset

Mohapatra, S. (2023). *ICRISAT District-Level Data: Heterogeneous Climate Effect on Crop Yield and Associated Risks to Water Security in India*. Mendeley Data, Version 1.

DOI:

```text
10.17632/ywp3y5j9vv.1
```

License:

```text
CC BY 4.0
```

Data access date:

```text
14 September 2026
```

### Classification Dataset

Ingle, A. *Crop Recommendation Dataset*. Kaggle.

Features include:

```text
N, P, K, temperature, humidity, pH, rainfall
```

Target:

```text
crop label
```

License:

```text
Apache License 2.0
```

Data access date:

```text
14 September 2026
```

---

# 42. Technical References

- VIT Vellore, MDI3003 Advanced Predictive Analytics, Experiment 08 Student Manual, Fall Semester 2026–2027.
- Scikit-learn documentation on pipelines, preprocessing and prevention of data leakage.
- Dataset sources listed above.

---

# 43. Final Note

This repository is designed so that the notebook, report, result CSV files and saved artifacts remain consistent.

If the instructor supplies an updated or canonical dataset, replace the public dataset source with the instructor-verified file, rerun the complete pipeline from the beginning, regenerate all empirical outputs, and update the report only with the newly generated values.

Never manually change model scores or error values.

**Registration Number: 24MID0009**
