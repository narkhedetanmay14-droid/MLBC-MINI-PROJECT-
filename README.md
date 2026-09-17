# Automated Data Cleaning & ML System

A complete end-to-end Python system for automated dataset profiling, cleaning, and machine learning, designed to work seamlessly on Windows with a local graphical interface for file selection.

## Features

- **GUI-driven Workflow**: Use the built-in Tkinter GUI directly from the Jupyter notebook to select files, load data, run cleaning, trigger ML, and export results.
- **Support for Standard Formats**: Loads `.csv`, `.xlsx`, and `.xls` files. Handles various encoding issues automatically.
- **Dataset Profiling**: Generates a comprehensive report on data structure, missing values, outliers, distributions, data types, and potential quality issues.
- **Automated Non-Destructive Cleaning**:
  - Drops duplicate rows.
  - Automatically handles missing values: medians for numeric, mode/'Unknown' for categorical, keeps `NaT` for missing datetimes without inventing dates.
  - Fixes disguised missing values (e.g., `'N/A'`, `'??'`, `'null'`).
  - Corrects common whitespace issues.
  - Safely coerces datetimes, numeric values stored as text, and boolean-like strings (`'yes'`/`'no'`, `'true'`/`'false'`).
  - Detects and winsorizes (caps) severe outliers using an IQR-based method.
  - **Maintains an Audit Log** of every change made to the data.
- **Leakage-Free Machine Learning**:
  - Automatically identifies whether your target represents a Classification, Regression, or Clustering task.
  - **No Data Leakage**: Builds strict `scikit-learn` `Pipeline` and `ColumnTransformer` blocks that encapsulate imputation and scaling/encoding perfectly after train/test splits.
  - Trains standard baseline models (LogisticRegression, RandomForest, GradientBoosting, LinearRegression, KMeans).
  - Reports standardized evaluation metrics (Accuracy, F1, ROC-AUC, MAE, R², Silhouette).
- **Post-Cleaning Exports**: Exports the fully cleaned dataset as CSV or Excel and a `TXT` summary report with the full audit log and ML summary.

## Project Structure

```text
Automated_Data_Cleaning_ML/
│
├── .venv/                              # Python Virtual Environment
├── Automated_Data_Cleaning_ML.ipynb    # Main application (Jupyter Notebook)
├── requirements.txt                    # Python dependencies
├── README.md                           # This document
├── data/                               # Input data directory (contains test datasets)
├── output/                             # Exported CSV / XLSX files go here
├── reports/                            # Generated TXT cleaning/ML reports go here
└── models/                             # Pickled/Joblib scikit-learn best models go here
```

## System Requirements

- Windows OS
- Python 3.9+ (Built and tested with 3.13)
- PowerShell or Command Prompt

## Environment Setup

Open PowerShell and navigate to the project directory:

```powershell
cd C:\Users\ASUS\Desktop\Automated_Data_Cleaning_ML
```

### 1. Activate the Virtual Environment

The virtual environment is already created. Activate it:

```powershell
.\.venv\Scripts\Activate.ps1
```
*(If you get a script execution policy error, you can bypass it temporarily with `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` and then run the activation script again, or activate using Command Prompt via `.\.venv\Scripts\activate.bat`)*

### 2. Verify Packages

The dependencies in `requirements.txt` should already be installed, but to be sure:

```powershell
pip install -r requirements.txt
```

### 3. Launch Jupyter Notebook

```powershell
jupyter notebook
```

This will automatically open your default web browser.

## How to Use

1. Click on **`Automated_Data_Cleaning_ML.ipynb`** in the Jupyter interface.
2. In the notebook top menu, click **Restart & Run All** (or run the cells sequentially from top to bottom).
3. The Tkinter GUI window will launch!
   - *Note: Look out for the tk window, it might open slightly behind your browser depending on your OS configuration!*
4. **Follow the buttons from left to right**:
   - Click **Select CSV/Excel File** and navigate to `data/test_dataset.csv` (or use your own data).
   - Click **Load Dataset** for profiling results.
   - Click **Clean Dataset** to execute the AutoCleaner and generate the Audit Log.
   - Select a `ML Target Column` in the dropdown (for `test_dataset.csv`, try `Performance` for Classification or `Salary` for Regression), and click **Run ML Analysis**.
   - Click **Export Cleaned Data** to save the result.
5. Exit the GUI to interact with the supplementary visualisations at the bottom of the notebook.

## Key Limitations

- **Not for Big Data**: This is an in-memory Pandas tool. Very large datasets (> 1-2 GB) may crash the process due to Memory constraints.
- **Conservative Outliers**: Outliers are only winsorized currently if they constitute `< 5%` of the data to aggressively prevent unintentional data destruction.
- **Categorical Encoders**: Text features with high cardinality (`>50` unique categories) are dropped from the ML pipelines by default to prevent explosive dummy variable sets (One-Hot Encoded variables).
