# Daily Work Log

## Day 1

**Time Spent:** 2 hours

### Tasks Completed

* Created GitHub repository.
* Set up Google Colab notebook.
* Uploaded the datasets.
* Loaded the training and holdout CSV files.
* Explored the dataset using:

  * head()
  * shape
  * columns
  * info()
  * describe()
* Read the Data Dictionary and understood each feature.

### Challenges

* Learning the basics of Pandas and DataFrames.

### Next Steps

* Explore missing values, duplicates, and data quality issues.

## Day 2

**Time Spent:** ~2 hours

### Tasks Completed

* Investigated missing values across the training dataset.
* Calculated missing-value percentages.
* Checked for duplicate learner IDs and exact duplicate rows.
* Investigated duplicated learner records and found 84 rows with repeated learner IDs.
* Identified 83 exact duplicate rows and one duplicated learner with a missing completion outcome.
* Investigated numerical ranges and identified impossible age values.
* Identified impossible pledged study hours above 168 hours per week.
* Found many `days_since_last_login` values exceeding the stated three-week measurement window.
* Investigated video watch percentages above 100% and compared this with the explanation in the Data Dictionary.
* Explored categorical columns and identified inconsistent education, mentor, device, and city-tier labels.
* Confirmed inconsistent signup-date formats.
* Identified `final_score` and `certificate_issued` as future-information/data-leakage columns.

### Problems Encountered

* Several columns contain missing values.
* The dataset contains duplicate learner records.
* Some numerical values are clearly impossible.
* Several categorical columns use inconsistent labels.
* Dates are stored in different formats.
* Some potentially useful information is unavailable or unreliable.

### Key Findings

* 84 rows contain duplicated learner IDs.
* 83 rows are exact duplicates.
* 9 training records have no `completed_course` value.
* Several age values are impossible, including `-3`, `199`, and `300`.
* Some learners have impossible pledged study hours above 168 hours per week.
* 987 records have `days_since_last_login` values greater than 21.
* Several categorical fields contain inconsistent representations of the same category.
* `signup_date` uses multiple formats and is currently stored as text.
* `final_score` and `certificate_issued` contain information that would not be available at the time the prediction is supposed to be made.

### Next Steps

* Clean duplicate records.
* Handle missing values.
* Standardize categorical values.
* Convert signup dates into a consistent datetime format.
* Handle impossible numerical values.
* Create a cleaned dataset for modelling.
