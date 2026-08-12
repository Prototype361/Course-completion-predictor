# Report — Course Completion Predictor

**Name:** Ankur Mukherjee
**Dates worked:** 6th,7th,8th,9th,11th,12th
**Total hours:** 20 hours

---

## 1. Summary

I built a classification model to predict whether learners would complete a 12-week online skills program. After cleaning the data and comparing Logistic Regression, Decision Tree, and Random Forest models, I selected Logistic Regression because it provided strong performance and was easier to interpret. Using a probability threshold of 0.40, the final model achieved 78.71% accuracy, 76.40% recall, and 0.865 ROC-AUC on the held-out test set. Five-fold cross-validation produced a mean ROC-AUC of 0.828 with a standard deviation of 0.013. The most useful finding was that engagement signals such as assignments submitted, video watching, and weekly logins were strongly associated with completion, but highly engaged learners could still fail to complete.

---

## 2. The Data

### What I found when I explored it

The dataset contains learner-level information covering demographics, engagement, course activity, enrollment information, and course completion.

The cleaned dataset contained:

* 2,392 learners
* 20 columns
* 1,501 non-completers
* 891 completers

The overall completion rate was approximately 37.25%.

The main engagement variables included:

* Weekly average logins
* Assignments submitted
* Forum posts
* Video watch percentage
* Hours per week pledged
* Days since last login
* Mentor status

Several categorical columns contained inconsistent representations of the same category.

For example, education included values such as `Bachelors`, `bachelors`, `Bachelor's`, `bachelor degree`, `B.Tech`, and similar variations.

---

### Data Problems Found and What I Did

| Problem                       |                                                 How many | What I did                                                         | Why                                                           |
| ----------------------------- | -------------------------------------------------------: | ------------------------------------------------------------------ | ------------------------------------------------------------- |
| Exact duplicate records       |                                  Identified during audit | Removed duplicates                                                 | Prevent duplicate observations                                |
| Duplicate learner IDs         |                                         0 after cleaning | Verified uniqueness                                                | Each learner should appear once                               |
| Invalid age values            |               178 missing/invalid after cleaning process | Converted invalid ages to missing and imputed during preprocessing | Prevent impossible ages from influencing the model            |
| Invalid video percentages     |                    143 missing/invalid values identified | Converted invalid values and handled through preprocessing         | Video percentage should be between 0 and 100                  |
| Invalid login-gap values      |          945 missing/invalid values initially identified | Cleaned valid range and created an audit indicator                 | Prevent impossible inactivity values                          |
| Extremely high pledged hours  | Several extreme observations, including values above 100 | Investigated and capped/cleaned the extreme values                 | Values were unrealistic for the intended weekly-study feature |
| Inconsistent education labels |                                 Multiple representations | Normalized into consistent categories                              | Prevent the same education level becoming multiple categories |
| Inconsistent city-tier labels |                                 Multiple representations | Standardized to tiers 1, 2, and 3                                  | Prevent duplicate category representations                    |
| Inconsistent date formats     |                                         Multiple formats | Converted to datetime                                              | Allow consistent date handling                                |
| Missing categorical values    |                            Present across several fields | Handled through preprocessing                                      | Preserve rows while allowing models to process missing values |

---

### Columns Excluded From the Model

The following columns were excluded from the final predictive feature set:

| Column                          | Reason                                                                                                  |
| ------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `learner_id`                    | Identifier only; it does not represent learner behavior                                                 |
| `signup_date`                   | Raw date was not directly used as a predictive feature                                                  |
| `certificate_issued`            | Potentially represents an outcome occurring after or near course completion and could introduce leakage |
| `final_score`                   | Contains substantial missingness and may only be available after course activity has occurred           |
| `notes`                         | Contains administrative/free-text information with substantial missingness                              |
| `completed_course`              | Target variable, not a feature                                                                          |
| `days_since_last_login_invalid` | Data-quality indicator rather than genuine learner behavior                                             |

The `days_since_last_login_invalid` indicator was initially included during model exploration. It was later removed because it represented a data-quality artifact rather than a genuine learner characteristic.

The cleaned model therefore used the meaningful learner and engagement features rather than relying on this artifact.

---

## 3. What I Did

### Baseline

The majority-class baseline accuracy was:

**62.75%**

This means a model that always predicted the majority class would achieve approximately 62.75% accuracy.

All machine-learning models were compared against this baseline.

---

### How I Split the Data

The cleaned dataset was split into:

* Training set: 1,913 learners
* Testing set: 479 learners

A stratified split was used so that the completion rate remained similar between the training and testing sets.

Training completion rate:

**37.27%**

Testing completion rate:

**37.16%**

The test set was kept separate so that the final model could be evaluated on data it had not used for training.

Evaluating on training data would produce an overly optimistic estimate because the model would already have seen those observations.

Five-fold cross-validation was later performed on the training data to check performance stability.

---

### Features

The final model used learner demographic and engagement features including:

* Age
* City tier
* Education
* Hours per week pledged
* Weekly logins
* Assignments submitted
* Forum posts
* Video watch percentage
* Mentor status
* Device
* Referral source
* Payment plan
* Days since last login

Categorical variables were encoded and numerical variables were processed through the modeling pipeline.

---

### Models I Tried

| Model                   | Accuracy | Precision | Recall |   F1 | Notes                                       |
| ----------------------- | -------: | --------: | -----: | ---: | ------------------------------------------- |
| Majority-class baseline |   62.75% |         — |      — |    — | No model                                    |
| Decision Tree           |   74.74% |       67% |    62% | 0.65 | Lower overall performance                   |
| Random Forest           |   77.87% |       76% |    58% | 0.66 | Strong accuracy but lower completion recall |
| Logistic Regression     |   77.24% |       73% |    62% | 0.67 | Strong ROC-AUC and interpretable            |

The initial Logistic Regression experiment before the final cleaning decision achieved 78.50% accuracy and 0.871 ROC-AUC. The cleaner final version achieved 77.24% accuracy and 0.865 ROC-AUC after removing the data-quality indicator.

---

### The Model I Chose, and Why

I selected Logistic Regression.

Random Forest achieved slightly higher accuracy than the cleaned Logistic Regression model, but Logistic Regression achieved slightly higher ROC-AUC and provided much clearer feature interpretation.

For this project, interpretability was important because the goal was not only to predict completion but also to understand which learner behaviors were associated with the prediction.

---

## 4. How Well Does It Work, Really?

### Confusion Matrix

Using the final threshold of 0.40:

```text
                 Predicted
                 0       1

Actual 0        241     60
Actual 1         42    136
```

Therefore:

* True negatives: 241
* False positives: 60
* False negatives: 42
* True positives: 136

Final test-set metrics:

* Accuracy: 78.71%
* Precision: 69.39%
* Recall: 76.40%
* F1-score: 0.727
* ROC-AUC: 0.865

---

### What the Two Kinds of Mistake Mean

A **false positive** means the model predicts that a learner will complete the course, but the learner actually does not complete.

For a program team, this could mean that a learner who appears to be doing well does not receive additional intervention because the model considers them low-risk.

A **false negative** means the model predicts that a learner will not complete, but the learner actually completes.

This can cause unnecessary intervention or make the program team spend resources on a learner who would have completed anyway.

For this project, false negatives are particularly interesting because the model's purpose includes identifying learners who may need support. Missing an actual completer is less harmful than failing to identify a genuinely struggling learner, but false positives still have a resource cost.

The appropriate tradeoff depends on how the program intends to use the predictions.

---

### My Decision Threshold

The default Logistic Regression threshold of 0.50 was not automatically accepted.

Several thresholds between 0.30 and 0.70 were evaluated.

The 0.40 threshold produced:

* 78.71% accuracy
* 69.39% precision
* 76.40% recall
* 0.727 F1-score

At the default 0.50 threshold:

* Accuracy: 77.24%
* Precision: 72.8%
* Recall: 61.8%
* F1-score: 0.669

The 0.40 threshold reduced false negatives from 68 to 42 while increasing true positives from 110 to 136.

I therefore selected 0.40 as the preferred operating threshold for this project.

---

### Where This Model Should Not Be Trusted

The model should not be treated as a definitive judgment about an individual learner.

The model struggled with some learners who had relatively strong engagement but still did not complete, and with some moderately engaged learners who ultimately completed.

The model also showed different error rates across some categorical groups.

For example:

* High School learners had a 28.8% error rate.
* Tier 2 learners had a 28.4% error rate.
* Tablet users had a 34.2% error rate.

Smaller groups, such as unknown city tier, had higher apparent error rates but should not be interpreted strongly because their sample sizes were small.

The model should therefore be used as a decision-support tool rather than an automatic decision-maker.

---

## 5. What Actually Predicts Dropping Out

The strongest positive Logistic Regression coefficients included:

| Feature                 | Coefficient |
| ----------------------- | ----------: |
| `assignments_submitted` |      +1.056 |
| `video_watch_pct`       |      +0.476 |
| `payment_plan_paid`     |      +0.428 |
| `weekly_logins_avg`     |      +0.384 |
| `device_Laptop`         |      +0.194 |

Important negative coefficients included:

| Feature                 | Coefficient |
| ----------------------- | ----------: |
| `payment_plan_free`     |      -0.563 |
| `has_mentor_0.0`        |      -0.423 |
| `device_Mobile`         |      -0.280 |
| `days_since_last_login` |      -0.194 |
| `city_tier_3`           |      -0.216 |

The strongest positive signal was the number of assignments submitted.

Video engagement and weekly login activity were also important.

However, these coefficients show statistical associations learned by the model. They do not prove that changing one of these variables will directly cause a learner to complete the course.

---

### If I Were Advising the Program Team

1. **Monitor assignment activity early.** Learners submitting fewer assignments could be prioritized for support because assignment submission was the strongest positive model signal.

2. **Watch engagement trends rather than relying on one metric.** Weekly logins, video watching, and days since last login should be considered together instead of treating any single measure as a definitive indicator.

3. **Use predictions to trigger human follow-up, not automatic decisions.** Learners with lower predicted completion probabilities could receive a reminder or mentor check-in, while staff should still consider the learner's individual circumstances.

---

## 6. Honesty Section

### Things I Did Not Fully Understand

At the beginning of the project, concepts such as ROC-AUC, probability thresholds, confusion matrices, and cross-validation were not fully clear to me.

I became more comfortable with these concepts through the project, but I still consider some of the deeper statistical interpretation of model coefficients and threshold selection areas where I need more practice.

---

### Things I Got Wrong Along the Way

One issue occurred during the Git workflow when local and remote branches diverged. A merge/rebase conflict occurred in `LOG.md`, and I accidentally deleted part of the log while resolving the conflict.

I had to stop the rebase, inspect the conflict, restore the intended content, stage the resolved file, and continue the Git workflow.

There were also several data-quality issues that initially looked like normal values. For example, some `hours_per_week_pledged` values were extremely high, including values above 100 hours per week. Investigating these values showed why data validation is important before training a model.

Another important lesson was that the default 0.50 classification threshold should not automatically be treated as optimal.

---

### What I Would Do With Two More Weeks

1. **Validate the model on a completely separate holdout dataset.** This would be the highest priority because the current test-set ROC-AUC was higher than the cross-validation mean.

2. **Improve feature engineering.** I would investigate additional time-based and engagement features that could better capture changes in learner behavior.

3. **Test calibrated probabilities and additional threshold-selection methods.** This could make the probability outputs more reliable for program-level decisions.

4. **Investigate group-level performance more carefully.** Larger datasets could help determine whether differences between learner groups persist across multiple splits.

5. **Build a simple intervention dashboard.** This could allow program staff to identify learners who may benefit from support.

---

### What Is Unfinished or Broken

The project is complete for the current assignment scope, but the model has not been deployed as a production system.

The 0.40 threshold has been evaluated on the available test data but would need further validation before real-world deployment.

The model also does not include every possible factor that could affect course completion.

---

## 7. Level 3 — What I Chose to Do

I interpreted "make this useful to a program manager" as making the model useful for identifying learners who may need attention rather than simply maximizing accuracy.

The most useful addition was therefore the threshold analysis.

Instead of accepting the default 0.50 threshold, I compared several thresholds and selected 0.40 because it increased recall and reduced false negatives.

This makes the model more useful for a support-oriented scenario where identifying more potential completers and understanding learners who might otherwise be missed is important.

The model should still be treated as a support tool, with final decisions made by people rather than automatically by the model.

---

## 8. Cross-Validation

Five-fold cross-validation produced the following ROC-AUC scores:

```text
0.820
0.824
0.851
0.833
0.811
```

Mean ROC-AUC:

```text
0.828
```

Standard deviation:

```text
0.013
```

The low standard deviation indicates relatively consistent performance across the five folds.

The lower cross-validation mean compared with the 0.865 held-out test ROC-AUC is a reminder that the single test split may be somewhat optimistic.

---

## Final Conclusion

The project successfully developed a Logistic Regression model for predicting course completion.

The final operating threshold of 0.40 produced:

* **78.71% accuracy**
* **69.39% precision**
* **76.40% recall**
* **0.727 F1-score**
* **0.865 ROC-AUC**

Cross-validation produced a mean ROC-AUC of **0.828 ± 0.013**.

The most important predictive signals were related to learner engagement, particularly assignments submitted, video watching, and weekly login activity.

The project also demonstrated that good machine-learning work involves more than choosing the model with the highest accuracy. Data quality, error analysis, threshold selection, interpretability, and validation all affected the final decision.
