# Employee Workforce Analytics

A completed end-to-end workforce analytics project that transforms a messy employee dataset into validated, analysis-ready, SQL-integrated, and dashboard-ready outputs.

The project focuses on reliable data preparation, structured analysis, reproducible reporting, and evidence-based model evaluation using Python, Pandas, SQLite, Matplotlib, and Scikit-learn.

## Project Highlights

- Cleaned and validated 1,020 employee records containing inconsistent data types and missing values.
- Preserved the raw data while creating separate cleaned, imputed, and modeling datasets.
- Standardized numeric, categorical, Boolean, and date variables using Pandas.
- Split combined department-region values into independent analytical features.
- Engineered tenure, joining-period, performance, missingness, and attrition variables.
- Applied department-level median imputation while preserving missing-value indicators.
- Built SQLite staging, analytical, modeling, and dashboard-ready tables.
- Used SQL aggregations to analyze salary, employment status, performance, remote work, departments, regions, and joining trends.
- Generated reproducible visualizations, analytical tables, and dashboard outputs.
- Benchmarked Logistic Regression and Random Forest against a majority-class baseline.
- Evaluated models using holdout testing, confusion matrices, ROC-AUC, subgroup analysis, and five-fold cross-validation.
- Documented the negative modeling result rather than overstating predictive performance.

## Project Workflow

### Data Inspection

The dataset was inspected immediately after loading to understand its structure and quality.

The inspection covered:

- Dataset dimensions
- Column names
- Data types
- Missing-value representations
- Unique identifiers
- Exact duplicate rows
- Repeated names and email addresses
- Category distributions
- Numeric ranges

Missing Age values were stored as blank strings, while missing Salary values were represented as `N/A`. These values were converted into standard missing-value representations before numeric conversion.

Repeated names and email addresses were also examined. Since the records had distinct employee IDs, phone numbers, departments, dates, and salary values, they were retained as separate employee observations.

### Data Cleaning and Wrangling

The cleaning process included:

- Converting Age and Salary into numeric variables
- Parsing Join Date into a consistent datetime format
- Splitting `Department_Region` into `Department` and `Region`
- Standardizing remote-work values into Boolean form
- Converting performance categories into an ordinal score
- Replacing nonstandard missing-value tokens
- Validating transformed columns
- Preserving the original row count

The transformations were checked through:

- Missing-value counts
- Row-count validation
- Category summaries
- Reconstruction checks
- Data-type verification
- Range checks

### Feature Engineering

The following features were created:

- `Department`
- `Region`
- `Performance_Score_Ordinal`
- `Tenure_Years`
- `Join_Year`
- `Join_Month`
- `Join_Quarter`
- `Age_Was_Missing`
- `Salary_Was_Missing`
- `Attrition_Flag`

Performance categories were encoded as:

| Performance Category | Ordinal Score |
|---|---:|
| Poor | 1 |
| Average | 2 |
| Good | 3 |
| Excellent | 4 |

The attrition target was defined as:

- `0` — Active or Pending
- `1` — Inactive

### Missing-Value Treatment

Missingness was first measured by department using both counts and percentages.

Missing-value indicator columns were created before imputation so that the original missingness information remained available for analysis and modeling.

Age and Salary were imputed using department-level medians rather than a single overall value.

This approach preserved department-level differences while avoiding unnecessary row deletion.

Validation confirmed:

- Missing Age values after imputation: 0
- Missing Salary values after imputation: 0
- Total retained rows: 1,020

### SQL Analytics

The prepared datasets were loaded into SQLite for structured querying and reporting.

The database included tables for:

- Cleaned employee records
- Imputed employee records
- Modeling data
- Dashboard KPIs
- Department summaries
- Region summaries
- Yearly summaries

SQL was used to calculate:

- Workforce counts
- Unique employee counts
- Missing-data totals
- Average salary by department
- Employee counts by region
- Employment status by department
- Remote-work distribution
- Performance summaries
- Join-year trends
- Department-level missingness

The SQL layer separated staging, modeling, and reporting outputs to support clearer data organization.

## Exploratory Analysis

The exploratory analysis examined:

- Employee distribution by department
- Employee distribution by region
- Average salary by department
- Employment status by department
- Remote-work percentage by department
- Salary differences by remote-work status
- Average performance score by department
- Salary differences across performance categories
- Employee joins by year
- Employee joins by year and department
- Missing Age and Salary rates by department

### Selected Findings

- DevOps had the largest employee count.
- Sales had the highest average salary.
- California had the highest employee count among the available regions.
- Pending was the most common employment status, followed closely by Active.
- DevOps had the highest remote-work proportion at approximately 52.91%.
- Non-remote employees had a slightly higher average salary than remote employees.
- Sales had the highest average ordinal performance score.
- Employees rated Good had the highest average salary.
- Employee joins peaked in 2023.
- Cloud Tech had the highest missing Age and Salary rates.

These findings are descriptive and do not establish causal relationships.

## Model Development

The modeling dataset excluded direct identifiers and target leakage variables.

Excluded fields included:

- Employee ID
- First name
- Last name
- Email
- Phone
- Status

Status was excluded because it was used to construct the target variable.

The modeling workflow used:

- Stratified train-test splitting
- Numeric standardization
- One-hot encoding
- Missingness indicators
- Scikit-learn pipelines
- Class weighting
- Fixed random seeds

Models evaluated:

- Majority-class Dummy Classifier
- Logistic Regression
- Random Forest

## Model Results

| Model | Accuracy | ROC-AUC | Inactive Recall | Inactive F1 |
|---|---:|---:|---:|---:|
| Dummy Classifier | 0.70 | 0.500 | 0.00 | 0.00 |
| Logistic Regression | 0.51 | 0.455 | 0.39 | 0.33 |
| Random Forest | 0.70 | 0.447 | 0.06 | 0.11 |

The Dummy Classifier achieved higher accuracy by predicting every employee as non-inactive. However, it failed to identify any inactive employees.

Logistic Regression identified more inactive employees but produced a ROC-AUC below 0.50.

Random Forest achieved approximately 70% accuracy but identified only 4 of 62 inactive employees in the test set.

Five-fold stratified cross-validation for Logistic Regression produced:

| Metric | Mean | Standard Deviation |
|---|---:|---:|
| Accuracy | 0.531 | 0.052 |
| ROC-AUC | 0.502 | 0.055 |
| Precision | 0.313 | 0.056 |
| Recall | 0.436 | 0.055 |
| F1-score | 0.364 | 0.056 |

The mean ROC-AUC of 0.502 indicates performance close to random ranking.

## Subgroup Error Analysis

Logistic Regression performance was also evaluated by department.

Department-level inactive recall ranged from:

- Finance: 70.0%
- Admin: 62.5%
- DevOps: 50.0%
- HR: 33.3%
- Sales: 16.7%
- Cloud Tech: 8.3%

Each department contained only 6 to 14 inactive test cases. These subgroup results were therefore treated as diagnostic rather than reliable evidence of department-specific performance.

## Final Modeling Conclusion

None of the tested models demonstrated reliable predictive performance.

The available variables support descriptive workforce analysis but do not contain sufficient signal for defensible inactivity prediction.

The models were not recommended for operational use.

Improved prediction would require additional variables such as:

- Attendance history
- Overtime
- Promotion history
- Engagement scores
- Manager changes
- Workload
- Compensation changes
- Leave patterns
- Performance history
- Actual exit dates

## Dashboard Outputs

The project created dashboard-ready tables for:

- Overall workforce KPIs
- Department-level summaries
- Region-level summaries
- Join-year summaries
- Model comparison results
- Department-level recall analysis

Generated KPIs include:

- Total employees
- Inactive employees
- Inactive rate
- Average salary
- Remote-work rate

## Repository Structure

```text
employee-workforce-analytics/
├── data/
│   ├── interim/
│   │   └── employee_cleaned_checkpoint.csv
│   └── processed/
│       ├── employee_imputed.csv
│       └── employee_modeling_dataset.csv
├── notebooks/
│   └── employee_workforce_analytics.ipynb
├── reports/
│   ├── figures/
│   ├── cross_validation_results.csv
│   ├── dashboard_department.csv
│   ├── dashboard_kpis.csv
│   ├── dashboard_region.csv
│   ├── dashboard_year.csv
│   ├── department_recall_summary.csv
│   ├── logistic_test_predictions.csv
│   └── model_comparison.csv
├── .gitignore
├── README.md
└── requirements.txt
```

## Technologies Used

- Python
- Pandas
- NumPy
- SQL
- SQLite
- Matplotlib
- Scikit-learn
- Jupyter Notebook
- Git
- GitHub
- Visual Studio Code

## Running the Project

Clone the repository:

```bash
git clone https://github.com/elejuliusrafael/employee-workforce-analytics
```

Move into the project directory:

```bash
cd employee-workforce-analytics
```

Install the required packages:

```bash
pip install -r requirements.txt
```

Add the source dataset to:

```text
data/raw/
```

Open the notebook:

```text
notebooks/employee_workforce_analytics.ipynb
```

Restart the kernel and run all cells to regenerate the processed datasets, reports, database tables, and figures.

## Dataset Credits

This project uses the **Messy Employee Dataset** published on Kaggle by **Aanuoluwapo John Shodipo**.

The dataset is fully synthetic and was designed for practicing data cleaning, transformation, exploratory analysis, data visualization, feature engineering, and machine learning preprocessing. It contains more than 1,000 employee records with intentionally introduced data-quality issues, including missing values, inconsistent date formats, invalid salary entries, redundant identity fields, and compound department-region values.

View the original dataset on Kaggle:

[Messy Employee Dataset](https://www.kaggle.com/datasets/desolution01/messy-employee-dataset)

The dataset is published under the **CC0 1.0 Universal Public Domain** license, allowing it to be used, modified, and distributed without restriction.

### Acknowledgment

Credit is given to **Aanuoluwapo John Shodipo** for creating and publishing the original dataset used in this project.

All data-cleaning procedures, validation checks, feature engineering, SQL analysis, visualizations, modeling workflows, reporting outputs, interpretations, and conclusions in this repository were independently developed as part of this project.

## Data Privacy

Although the dataset is fully synthetic and does not contain information about real individuals, the raw dataset and generated SQLite database are excluded from the repository to maintain a clean project structure and avoid unnecessarily republishing source-level records.

Only prepared analytical outputs required to review the project are included.

## Limitations

- The dataset is synthetic and may not reflect the full complexity of real workforce data.
- Age contains only a small number of discrete values.
- Identity fields are repeatedly reused across records.
- The Inactive label does not include an observed exit date or prediction horizon.
- The available predictors contain weak information about inactivity.
- Missing values were imputed using department-level medians.
- Department-level model metrics are based on small subgroup samples.
- The model results should not be interpreted as causal or production-ready.

## Conclusion

This project completed the full progression from messy source records to validated datasets, feature-engineered tables, SQL analytics, visual reports, dashboard-ready outputs, and classification benchmarks.

The strongest result was not a high predictive score, but a defensible analytical conclusion: the dataset is useful for structured workforce reporting, while additional behavioral and employment-history variables are required for reliable attrition prediction.
