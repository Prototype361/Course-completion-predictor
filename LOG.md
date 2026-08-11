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


## Day 3

**Time Spent:** ~2 hours

### Tasks Completed

- Cleaned duplicate learner records using `learner_id`.
- Standardized inconsistent categorical values in:
  - `education`
  - `city_tier`
  - `has_mentor`
  - `device`
- Converted mixed `signup_date` formats into proper datetime values.
- Identified and corrected invalid ages.
- Identified and corrected implausibly high values in `hours_per_week_pledged`.
- Validated `weekly_logins_avg` against its expected 0–7 range.
- Corrected `video_watch_pct` values above 100%.
- Identified 987 invalid `days_since_last_login` values in the original dataset because they exceeded the maximum possible 21 days.
- After duplicate removal, 945 invalid login-day values remained and were replaced with the median valid value of 12.
- Created `days_since_last_login_invalid` to preserve information about the original invalid values.
- Handled remaining missing values using median imputation or an `Unknown` category where appropriate.
- Identified `final_score` and `certificate_issued` as post-program data and therefore potential data leakage.
- Excluded `notes` from future model inputs because its timing cannot be reliably established.
- Performed a final data-quality audit.

### Important Findings

- The original dataset contained duplicate learner records.
- Several fields contained inconsistent text representations.
- Some numerical fields contained implausible values.
- `days_since_last_login` contained 987 values greater than the logically possible 21 days.
- `final_score` and `certificate_issued` contain information that would only become available after the prediction point.
- The cleaned dataset contains 2,392 unique learners.

### Challenges

- Handling mixed date formats initially caused valid dates to become `NaT`. This was investigated and corrected using mixed-format date parsing.
- The missing-value indicator for `days_since_last_login` was initially created after imputation, resulting in an incorrect indicator. This was identified and corrected using the original raw dataset.

### Next Steps

- Establish the majority-class baseline.
- Split the cleaned data into training and testing sets.
- Train the first machine-learning model.
- Evaluate the model against the baseline.


## Day 4

**Time Spent:** ~2 hours

### Tasks Completed

- Loaded the cleaned training dataset.
- Examined the target variable `completed_course`.
- Established a majority-class baseline.
- The baseline predicts every learner as a non-completer and achieves approximately 62.75% accuracy.
- Defined the features available by the end of Week 3.
- Excluded `learner_id`, `final_score`, `certificate_issued`, and `notes` from the model because they are either identifiers, post-program information, or have uncertain timing.
- Split the dataset into 80% training data and 20% testing data using stratification.
- Built a preprocessing pipeline using:
  - `StandardScaler` for numerical features.
  - `OneHotEncoder` for categorical features.
- Trained the first Logistic Regression classification model.
- Evaluated the model using accuracy, precision, recall, F1-score, confusion matrix, and ROC-AUC.

### Results

| Metric | Result |
|---|---:|
| Majority-class baseline accuracy | 62.75% |
| Logistic Regression accuracy | 78.50% |
| ROC-AUC | 0.871 |
| Non-completer recall | 87% |
| Completer recall | 65% |

### Confusion Matrix

The model correctly classified:

- 261 non-completers.
- 115 completers.

It incorrectly classified:

- 40 non-completers as completers.
- 63 completers as non-completers.

### Observations

The Logistic Regression model substantially outperformed the majority-class baseline, improving accuracy by approximately 15.75 percentage points.

The ROC-AUC of 0.871 indicates that the model has good ability to distinguish between learners who complete the course and those who do not.

The model performs better at identifying non-completers than completers. This is reflected in the higher recall for non-completers (87%) compared with completers (65%).

### Challenges

- Understanding the difference between a majority-class baseline and an actual machine-learning model.
- Understanding why categorical variables need to be encoded before training.
- Understanding precision, recall, F1-score, and ROC-AUC.
- Ensuring that post-program variables were excluded to prevent data leakage.

### Next Steps

- Compare Logistic Regression with additional classification algorithms.
- Evaluate whether other models can improve predictive performance.
- Compare models using multiple evaluation metrics rather than accuracy alone.