# Course Completion Predictor

Machine Learning assignment to predict whether learners will complete a 12-week online skills program.

## Project Structure

- data/ - Dataset files
- notebooks/ - Google Colab notebook
- REPORT.md - Final report
- LOG.md - Daily work log
- AI_USAGE.md - AI usage disclosure

The project is being developed over seven days with regular Git commits.

## Results

The project tested several classification models for predicting whether a learner would complete an online course.

### Model Comparison

| Model | Accuracy | ROC-AUC |
|---|---:|---:|
| Baseline | 62.75% | - |
| Decision Tree | 74.74% | 0.814 |
| Random Forest | 77.87% | 0.863 |
| Logistic Regression | 78.50% | 0.871 |

Logistic Regression achieved the best overall accuracy and ROC-AUC among the tested models.

### Final Logistic Regression Model

After threshold analysis, a probability threshold of **0.40** was selected instead of the default 0.50 threshold.

| Metric | Result |
|---|---:|
| ROC-AUC | 0.865 |
| Accuracy | 78.71% |
| Precision | 69.39% |
| Recall | 76.40% |
| F1-score | 0.727 |

At the 0.40 threshold, the confusion matrix was:

```text
[[241, 60],
 [42, 136]]
