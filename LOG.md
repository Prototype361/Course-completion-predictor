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


## Day 5

**Time Spent:** ~2 hours

### Tasks Completed

- Established the majority-class baseline at 62.75% accuracy.
- Trained and evaluated a Decision Tree classifier.
- Trained and evaluated a Random Forest classifier.
- Compared both models with the existing Logistic Regression model.
- Evaluated models using accuracy, precision, recall, F1-score, confusion matrix, and ROC-AUC.
- Examined Logistic Regression coefficients to understand feature associations.
- Identified `days_since_last_login_invalid` as a data-quality indicator rather than a meaningful learner feature and removed it from the model.
- Retrained the Logistic Regression model using only meaningful learner features.

### Model Comparison

| Model | Accuracy | ROC-AUC |
|---|---:|---:|
| Majority baseline | 62.75% | — |
| Decision Tree | 74.74% | 0.814 |
| Random Forest | 77.87% | 0.863 |
| Logistic Regression | 77.24% | 0.865 |

### Observations

All machine-learning models substantially outperformed the majority-class baseline.

The Decision Tree performed weakest among the three machine-learning models.

Random Forest achieved the highest accuracy at 77.87%, while Logistic Regression achieved the highest ROC-AUC at 0.865. The difference between the two models was small.

Logistic Regression was retained as the preferred model because its predictive performance was competitive with Random Forest while remaining simpler and easier to interpret.

### Feature Interpretation

The strongest positive model coefficients were associated with:

- `assignments_submitted`
- `video_watch_pct`
- `payment_plan_paid`
- `weekly_logins_avg`

The strongest negative coefficients were associated with:

- `payment_plan_free`
- `has_mentor_0.0`
- `device_Mobile`
- `referral_source_other`
- `city_tier_3`
- `days_since_last_login`

These coefficients represent associations learned by the model and should not be interpreted as evidence of causation.

### Data Quality Consideration

The `days_since_last_login_invalid` indicator was initially included as a feature to preserve information about invalid source records. It improved the initial model slightly, but it represents a data-quality artifact rather than genuine learner behavior. It was therefore removed from the final model feature set.

After removing this feature, Logistic Regression achieved 77.24% accuracy and 0.865 ROC-AUC.

### Next Steps

- Perform deeper error analysis on the preferred model.
- Examine false positives and false negatives.
- Investigate whether model performance is consistent across learner groups.
- Consider feature engineering and model tuning only after completing error analysis.


## Day 6

**Time Spent:** ~2 hours

### Tasks Completed

- Performed error analysis on the preferred Logistic Regression model.
- Compared correct and incorrect predictions.
- Separated false positives and false negatives.
- Examined the characteristics of misclassified learners.
- Compared numerical features between correctly and incorrectly classified learners.
- Analyzed model error rates across education, city tier, mentor status, device, referral source, and payment plan groups.
- Checked group sizes before interpreting categorical error rates.

### Error Summary

The test set contained 479 learners.

- Correct predictions: 370
- Incorrect predictions: 109
- False positives: 41
- False negatives: 68

False negatives were more common than false positives. This means the model more frequently predicted non-completion for learners who actually completed the course.

### Numerical Error Analysis

Compared with correctly classified learners, incorrectly classified learners had higher average values for several engagement-related features:

- Video watch percentage was approximately 6.4 percentage points higher.
- Assignments submitted were approximately 0.94 higher.
- Hours per week pledged were approximately 0.75 higher.
- Weekly logins were approximately 0.44 higher.

This suggests that some learners with relatively strong engagement signals are difficult for the model to classify correctly. Some learners appear engaged but ultimately do not complete, while some moderately engaged learners do complete.

### Categorical Error Analysis

Model error rates varied across several learner groups.

Education error rates ranged from 15.6% for Diploma learners to 28.8% for High School learners. Bachelors learners had an error rate of 17.9%.

City-tier error rates were 22.6% for Tier 1, 28.4% for Tier 2, and 10.5% for Tier 3.

Device error rates were 18.2% for Desktop, 24.2% for Laptop, 21.3% for Mobile, and 34.2% for Tablet users.

School-partner referrals had the lowest referral-source error rate at 17.9%, while the Other category had the highest at 27.3%.

Free-plan learners had an error rate of 17.8%, compared with 27.1% for paid learners and 27.2% for scholarship learners.

### Interpretation

The model performs reasonably well overall but has difficulty with learners whose observed behavior does not clearly match the typical completion patterns learned during training.

The relatively high error rates for some smaller groups, such as Unknown city tier and Unknown education, should be interpreted cautiously because of their small sample sizes.

The Tablet group had a higher error rate than other device groups, but the group contained only 38 learners. This should therefore be treated as a pattern worth investigating rather than a definitive conclusion.

The error analysis suggests that additional learner characteristics or better feature representation may be needed to explain some of the outcomes that the current model cannot predict correctly.

### Challenges

- Interpreting false positives and false negatives.
- Distinguishing meaningful error patterns from patterns caused by small group sizes.
- Avoiding causal interpretations of model associations.
- Understanding why some highly engaged learners are still misclassified.

### Next Steps

- Investigate prediction probabilities and classification thresholds.
- Examine whether changing the decision threshold can improve the balance between false positives and false negatives.
- Continue model evaluation before selecting a final production threshold.


## Day 7

**Time Spent:** ~2 hours

### Tasks Completed

- Analyzed the probability outputs of the final Logistic Regression model.
- Evaluated multiple classification thresholds from 0.30 to 0.70.
- Compared accuracy, precision, recall, and F1-score across different thresholds.
- Compared the default threshold of 0.50 with alternative thresholds.
- Selected 0.40 as the preferred classification threshold.
- Evaluated the final model using the selected threshold.

### Probability Analysis

The Logistic Regression model produced predicted probabilities ranging from approximately 0.007 to 0.990.

The average predicted probability was approximately 0.367, which was close to the actual course completion rate in the test set.

### Threshold Comparison

The default threshold of 0.50 produced:

- Accuracy: 77.24%
- Precision: 72.8%
- Recall: 61.8%
- F1-score: 0.669

A threshold of 0.40 produced:

- Accuracy: 78.71%
- Precision: 69.39%
- Recall: 76.40%
- F1-score: 0.727

The 0.40 threshold therefore improved accuracy, recall, and F1-score compared with the default 0.50 threshold, while slightly reducing precision.

### Confusion Matrix

At the selected threshold of 0.40:

[[241, 60],
 [42, 136]]

This resulted in:

- True negatives: 241
- False positives: 60
- False negatives: 42
- True positives: 136

Compared with the 0.50 threshold, false negatives decreased from 68 to 42. This means the model identified 26 additional learners who actually completed the course.

### Final Threshold Decision

A classification threshold of 0.40 was selected for the current model because it provided a better balance between identifying actual completers and maintaining overall prediction accuracy.

The threshold was selected based on the current test-set evaluation and should be validated further before being used in a production environment.

### Current Model Performance

- Model: Logistic Regression
- ROC-AUC: 0.865
- Classification threshold: 0.40
- Accuracy: 78.71%
- Precision: 69.39%
- Recall: 76.40%
- F1-score: 0.727

### Next Steps

- Evaluate the final model across different learner groups.
- Check whether the selected threshold generalizes beyond the current test split.
- Consider whether additional validation or cross-validation is needed.
- Document the final modeling pipeline and limitations.