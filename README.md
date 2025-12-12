# FP-Growth Online Retail

## Project overview
This repository implements frequent itemset mining and association-rule-based recommendations on an online-retail style transactional dataset. The notebooks show both a pandas-based pipeline and a scalable PySpark-based FP-Growth approach. The repository also contains an exported associations table (associationrules.csv) for quick exploration and demonstration.

## Objectives
- Clean and preprocess transactional data.
- Run FP-Growth to discover frequent itemsets and association rules.
- Provide a small recommendation prototype using the mined rules.
- Demonstrate both single-machine and Spark-based approaches.

## Repository contents
- data_clean_project.ipynb — Data cleaning and transaction assembly.
- project_spark.ipynb — Spark-based FP-Growth example (scalable approach).
- project_spark_test.ipynb, project_spark_test (2).ipynb — Additional Spark tests and experiments.
- project_recommendation_system.ipynb — Build and test a rule-based recommendation helper; demonstrate consuming associationrules.csv.
- associationrules.csv — Precomputed association rules exported from one of the runs (useful to save time).

## Code Execution Instructions
### Prerequisites
- Python 3.8+ (3.10 recommended)
- Java 8 or 11 for PySpark
- pip (or conda) and JupyterLab or Jupyter Notebook

### Recommended Python packages
You can install the basics with:
pip install pandas numpy matplotlib seaborn scikit-learn mlxtend pyspark jupyterlab openpyxl tqdm

(If you prefer conda, create a conda env and install the same packages via conda/pip.)

### Running locally (notebooks)
1. Clone the repository:
   git clone https://github.com/maikzero/fp-growth-online-retail.git
   cd fp-growth-online-retail

2. Create and activate a virtual environment (recommended):
   python -m venv .venv
   source .venv/bin/activate   # Linux/macOS
   .\.venv\Scripts\activate    # Windows PowerShell

3. Install dependencies (example):
   pip install pandas numpy matplotlib seaborn scikit-learn mlxtend pyspark jupyterlab openpyxl tqdm

4. Launch Jupyter:
   jupyter lab
   or
   jupyter notebook

5. Open and run the notebooks in order:
   - data_clean_project.ipynb: Clean data, form transaction lists (group by InvoiceNo -> list of items).
   - project_spark.ipynb (or project_spark_test*.ipynb): Run PySpark FPGrowth on transaction data; tune minSupport/minConfidence.
   - project_recommendation_system.ipynb: Build and test a small recommendation function using associationrules.csv or rules generated from Spark.

Notes for Spark notebooks:
- Ensure JAVA_HOME is set and points to a valid JDK.
- If running Spark locally, you can increase memory:
  export PYSPARK_SUBMIT_ARGS="--driver-memory 4g pyspark-shell"
  or set environment variable SPARK_DRIVER_MEMORY appropriately.

### Using precomputed rules
If you want to skip mining, load associationrules.csv into pandas and use the recommendation notebook to test rule-based suggestions.

## High-level code logic
1. Data cleaning (data_clean_project.ipynb)
   - Load raw transactional data (CSV or Excel).
   - Filter out cancelled transactions and negative quantities.
   - Normalize product descriptions and aggregate identical product codes.
   - Group records by InvoiceNo to form transaction lists (each transaction is a list/array of item identifiers).

2. Frequent itemset mining
   - Pandas/mlxtend approach: one-hot encode transactions and run apriori/association_rules (suitable for smaller datasets).
   - Spark approach: Convert transactions to an RDD or DataFrame with array-type column and apply pyspark.ml.fpm.FPGrowth. Extract frequentItemsets and associationRules.

3. Postprocessing and persistence
   - Filter rules by minConfidence/minLift and sort by metrics of interest (lift, confidence, support).
   - Save top rules to associationrules.csv for analysis and faster loading.

4. Recommendation prototype
   - Given an input basket, match antecedents and return consequent items ranked by lift/confidence.
   - Map StockCode back to human-readable description for display.

## Troubleshooting & tips
- Java/PySpark compatibility: Use Java 8 or 11; check pyspark release notes for supported versions.
- If you run out of memory on pandas approach, switch to the Spark notebooks or increase resources.
- Experiment with different minSupport/minConfidence thresholds to get manageable sets of meaningful rules.
- Use associationrules.csv (precomputed) to iterate quickly on recommendation logic.

## Next steps / Improvements
- Add requirements.txt or environment.yml for reproducibility.
- Add a small sample data file with a few dozen transactions for quick notebook demos.
- Provide a Dockerfile or binder configuration so others can launch the environment reliably.
- Package the recommendation logic into a Python module / API for integration.

## License & contact
- No license file present in the repo — add a LICENSE if you want to reuse or share this work publicly.
- For questions, open an issue on the repository: https://github.com/maikzero/fp-growth-online-retail/issues
