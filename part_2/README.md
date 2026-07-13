## Part 2 — Supervised Machine Learning Model — Build, Train, and Evaluate

### Why not Label Enoding

The target variable **Class** represents diabetes status, where one label indicates a diabetic patient and the other indicates a non-diabetic patient. Because these categories have no natural ranking or order, they are treated as nominal categories rather than ordinal values. 
### Why One-Hot Encoding Was Used

One-hot encoding was selected instead of label encoding because label encoding would assign numeric values (for example, 0 and 1) that may cause some machine learning algorithms to interpret one category as being greater than or more important than the other. This introduces a false ordinal relationship that does not exist in reality. One-hot encoding preserves the categorical meaning of the labels and prevents this issue. 

### Preventing Data Leakage During Scaling

The scaler must be fitted only on the training data. Fitting the scaler on the full dataset would constitute data leakage because information from the test set, such as its mean and standard deviation, would be incorporated into the training process. This would allow the model to indirectly learn from unseen data and lead to overly optimistic evaluation results.

### Class Imbalance Handling

The class distribution showed a moderate imbalance rather than a severe one. As a result, no oversampling or weighting methods were applied, and the Logistic Regression model was trained on the data in its original form.
### Precision, Recall, and AUC

Precision measures the proportion of predicted positive cases that are actually positive:

Precision = {TP}\\{TP + FP}

Recall measures the proportion of actual positive cases correctly identified:

Recall = {TP}\\{TP + FN}

For diabetes prediction, **Recall** is more important than Precision because failing to identify a diabetic patient (false negative) can delay diagnosis and treatment, whereas a false positive typically results only in additional testing. The model achieved a Recall of **0.67**, correctly identifying approximately 67% of diabetic patients.<br>

The model achieved an **AUC of 0.822**, indicating good class-separation ability. An AUC of 0.822 means there is approximately an 82.2% probability that a randomly selected diabetic patient receives a higher predicted probability than a randomly selected non-diabetic patient. 

### Threshold Analysis

The threshold that maximizes the F1-score is **0.40**, where the highest F1-score of **0.703** was observed. This threshold provides the best balance between Precision and Recall on this dataset. <br>
However, **Recall** is the more important metric for diabetes screening, the threshold should be lowered when prioritizing the identification of diabetic patients. Lower thresholds increase Recall by capturing more true positives, but they also reduce Precision by increasing false positives. This trade-off is generally acceptable in healthcare screening because missing a diabetic patient is typically more costly than conducting additional follow-up tests.

### Comparison of Logistic Regression Models

| Model | Precision | Recall | AUC |
|---------|-----------|--------|------|
| Baseline (C = 1.0) | 0.667 | 0.667 | 0.822 |
| Regularized (C = 0.01) | 0.677 | 0.500 | 0.825 |


In Logistic Regression, **C** controls the inverse strength of L2 regularization. Smaller values of `C` apply stronger regularization and shrink model coefficients toward zero, reducing model complexity and helping prevent overfitting. In this dataset, reducing `C` from 1.0 to 0.01 slightly improved Precision and AUC, but substantially reduced Recall. Since Recall is the more critical metric for diabetes screening, stronger regularization worsened the model's practical usefulness despite the small improvement in AUC. 

### Bootstrap AUC Comparison

The mean AUC difference between the models is **-0.0035**, indicating that the `C=0.01` model achieved a slightly higher AUC on average. The 95% bootstrap confidence interval is:

\[
-0.0162 < 0 < 0.0090
\]

Because the confidence interval includes zero, there is no reliable evidence that either model consistently outperforms the other in terms of AUC. Therefore, the observed AUC difference may be due to sampling variability rather than a true performance advantage. Although the `C=0.01` model achieved a marginally higher AUC, the baseline `C=1.0` model remains preferable because it achieved substantially higher Recall, which is more important for diabetes prediction.
