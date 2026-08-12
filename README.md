# Course Completion Predictor

A machine-learning project that predicts whether learners will complete a 12-week online skills program using learner demographic, engagement, and activity data.

## Project Objective

The goal of this project is to identify patterns associated with course completion and build a classification model that can predict whether a learner is likely to complete the course.

The target variable is:

* `0` = Did not complete
* `1` = Completed

## Project Structure

```text
Course-completion-predictor/
│
├── Data/
│   ├── cohort_train.csv
│   └── cohort_train_cleaned.csv
│
├── Notebooks/
│   └── ML_Assignment.ipynb
│
├── Templates/
│
├── README.md
├── REPORT.md
├── LOG.md
├── AI_USAGE.md
├── predictions.csv
└── requirements.txt
```

## Dataset

The cleaned modeling dataset contains:

* 2,392 learners
* 20 columns including the target and data-quality fields
* 1,501 learners who did not complete
* 891 learners who completed

The overall completion rate is approximately **37.25%**.

## What Was Done

The project followed a complete machine-learning workflow:

1. Initial dataset exploration
2. Data-quality audit
3. Missing-value analysis
4. Duplicate detection
5. Invalid-value detection
6. Data cleaning and normalization
7. Feature preparation
8. Train-test split
9. Baseline model
10. Logistic Regression
11. Decision Tree
12. Random Forest
13. Feature interpretation
14. Error analysis
15. Probability threshold analysis
16. Cross-validation
17. Final documentation

## Models Tested

| Model               | Accuracy | ROC-AUC |
| ------------------- | -------: | ------: |
| Majority baseline   |   62.75% |       — |
| Decision Tree       |   74.74% |   0.814 |
| Random Forest       |   77.87% |   0.863 |
| Logistic Regression |   77.24% |   0.865 |

Logistic Regression was selected as the preferred model because it provided a strong combination of performance, simplicity, and interpretability.

## Final Model

The final Logistic Regression model uses a classification threshold of **0.40** rather than the default 0.50.

### Final test-set performance

| Metric    | Result |
| --------- | -----: |
| ROC-AUC   |  0.865 |
| Accuracy  | 78.71% |
| Precision | 69.39% |
| Recall    | 76.40% |
| F1-score  |  0.727 |

### Confusion Matrix

```text
                 Predicted
                 0       1

Actual 0        241     60
Actual 1         42    136
```

Lowering the threshold from 0.50 to 0.40 reduced false negatives from 68 to 42 and increased recall from 61.8% to 76.4%.

## Cross-Validation

Five-fold cross-validation produced:

```text
Mean ROC-AUC: 0.828
Standard deviation: 0.013
```

The relatively low standard deviation indicates reasonably consistent performance across the five folds.

## Important Features

The strongest positive Logistic Regression coefficients included:

* `assignments_submitted`
* `video_watch_pct`
* `payment_plan_paid`
* `weekly_logins_avg`
* `device_Laptop`

Important negative coefficients included:

* `payment_plan_free`
* `has_mentor_0.0`
* `device_Mobile`
* `days_since_last_login`
* `city_tier_3`

These are model associations and should not be interpreted as proof of causation.

## How to Run

### Option 1: Google Colab

Open:

`Notebooks/ML_Assignment.ipynb`

Upload or connect the required dataset files and run the notebook cells from top to bottom.

### Option 2: Local Python Environment

Clone the repository:

```bash
git clone https://github.com/Prototype361/Course-completion-predictor.git
cd Course-completion-predictor
```

Install the required packages:

```bash
pip install -r requirements.txt
```

Launch Jupyter:

```bash
jupyter notebook
```

Then open:

```text
Notebooks/ML_Assignment.ipynb
```

Open Data folder to access all the required fils like cohort_holdout.csv, cohort_train.csv and cohort_train_cleaned.csv.

## Limitations

* The dataset contains missing and inconsistent source values.
* Some learner groups are relatively small.
* The model only uses information available in the provided dataset.
* Model coefficients represent associations rather than causal relationships.
* The 0.40 threshold was selected using the available evaluation data and should be validated further before production use.
* Cross-validation produced a lower mean ROC-AUC than the single held-out test split.
* Predictions may perform differently on future learner populations.

## Conclusion

The project demonstrates a complete machine-learning workflow for predicting course completion.

Logistic Regression provided the best balance of predictive performance and interpretability among the tested models. The final threshold of 0.40 improved the model's ability to identify actual course completers while maintaining reasonable overall accuracy.

The analysis also showed that learner engagement, especially assignment submission, video watching, and login activity, provides useful predictive information about course completion.
