# FP-Growth Online Retail

## Project overview
This repository implements frequent itemset mining and association-rule-based recommendations on an online-retail style transactional dataset. The notebooks demonstrate a scalable PySpark-based FP-Growth approach and a recommendation prototype. A precomputed associations table (associationrules.csv) is included for quick exploration.

## Objectives
- Clean and preprocess transactional data (now performed in the Spark notebooks).
- Run FP-Growth to discover frequent itemsets and association rules using PySpark for scalability.
- Provide a rule-based recommendation prototype that consumes mined rules.
- Persist association rules for downstream use.

## Repository contents
- project_spark.ipynb — Primary Spark-based pipeline: data cleaning, transaction assembly, and PySpark FPGrowth.
- project_spark_test.ipynb, project_spark_test (2).ipynb — Additional Spark experiments and tests (also include data-cleaning steps in some runs).
- project_recommendation_system.ipynb — Load association rules (or associationrules.csv) and demonstrate a simple rule-based recommendation helper. Also the final file that contains everything.
- data_clean_project.ipynb — Legacy/reference notebook (kept for historical reference). The main cleaning logic has been migrated into the Spark notebooks to integrate with PySpark workflows.
- associationrules.csv — Precomputed association rules exported from a Spark run (useful to skip recomputing).

## Code Execution Instructions
### Prerequisites
- Python 3.8+ (3.10 recommended)
- Java 8 or 11 for PySpark
- pip (or conda) and JupyterLab or Jupyter Notebook

### Recommended Python packages
Install the basics with:
pip install pandas numpy matplotlib seaborn scikit-learn pyspark jupyterlab openpyxl tqdm

(If you prefer conda, create a conda env and install the same packages via conda/pip.)

### Running locally (notebooks)
1. Clone the repository:
   git clone https://github.com/maikzero/fp-growth-online-retail.git
   cd fp-growth-online-retail

2. Create and activate a virtual environment (recommended):
   python -m venv .venv
   source .venv/bin/activate   # Linux/macOS
   .\.venv\Scripts\activate    # Windows PowerShell

3. Install dependencies:
   pip install pandas numpy matplotlib seaborn scikit-learn pyspark jupyterlab openpyxl tqdm

4. Launch Jupyter:
   jupyter lab
   or
   jupyter notebook

5. Open and run the notebooks 
   - project_recommendation_system.ipynb: Main file that has all of the main logic.
      - Has data cleaning, spark system, fp-growth, and rule mining
      - test recommendation helper that ranks consequents by lift/confidence.

Notes for Spark notebooks:
- Ensure JAVA_HOME points to a valid JDK (8 or 11).
- For local Spark runs, you can increase driver memory, e.g.:
  export PYSPARK_SUBMIT_ARGS="--driver-memory 4g pyspark-shell"
  or set SPARK_DRIVER_MEMORY accordingly.
- The Spark notebooks include the data cleaning steps (filter cancelled invoices, normalize descriptions, aggregate transactions) so you do not need to run data_clean_project.ipynb before the Spark notebooks.


## High-level code logic (updated)
1. Spark-based data cleaning and transactionization
   - The cleaning and preprocessing steps are implemented in the Spark notebooks (project_spark*.ipynb).
   - Tasks include: load raw data, filter cancelled transactions and negative quantities, normalize product descriptions, deduplicate as needed, and group records by InvoiceNo to form transaction arrays ready for FPGrowth.

2. Frequent itemset mining (PySpark)
   - Convert cleaned transactions into a DataFrame with an array-type column (each row = array of item IDs).
   - Run pyspark.ml.fpm.FPGrowth to obtain frequentItemsets and associationRules at scale.
   - Tune minSupport and minConfidence to balance rule quantity and quality.

3. Postprocessing and persistence
   - Filter and sort rules by metrics such as lift, confidence, and support.
   - Save final rules to associationrules.csv for quick access and reproducibility.

4. Recommendation prototype
   - Given an input basket, find matching antecedent rules and return consequents ranked by lift/confidence.
   - Map product identifiers back to human-readable descriptions for display in the recommendation notebook.

## Troubleshooting & tips
- Java/PySpark compatibility: Use Java 8 or 11; check pyspark release notes for supported versions.
- If Spark complains about memory, increase driver/executor memory or run on a machine with more RAM.
- If you get too many trivial rules, raise minSupport/minConfidence or prune by lift.
- Keep associationrules.csv as a cached artifact to accelerate recommendation development.

