
# Decision Tree Variance and Overfitting

Decision Trees are often described as **high-variance models** because small changes in the training dataset can lead to substantially different tree structures and predictions. During training, the algorithm follows a greedy approach, selecting the split that provides the largest reduction in impurity at each node

## Controlled Decision Tree Results

| Model | Training Accuracy | Test Accuracy | AUC |
|---------|------------------|---------------|------|
| Unconstrained Tree (`max_depth=None`) | 100.00% | 70.29% | 0.6391 |
| Controlled Tree (`max_depth=5`, `min_samples_split=20`) | 82.07% | 74.64% | 0.7907 |

### Role of `max_depth`

`max_depth` limits how deep the tree can grow. Restricting the depth reduces variance and helps prevent overfitting, although it may introduce some bias.

### Role of `min_samples_split`

`min_samples_split` specifies the minimum number of samples required to split a node. This prevents the tree from creating splits based on very small subsets of data, reducing sensitivity to noise.

### Comparison with the Unconstrained Tree

The unconstrained tree achieved **100.00% training accuracy** but only **70.29% test accuracy**, resulting in a large train-test gap of **29.71%**, which indicates strong overfitting. The controlled tree achieved **82.07% training accuracy** and **74.64% test accuracy**, reducing the gap to **7.43%**. This smaller gap shows that the controlled tree generalizes better to unseen data while also achieving a higher AUC (**0.7907 vs. 0.6391**).
## Comparison of Gini and Entropy Criteria

Two `DecisionTreeClassifier` models were trained with `max_depth=5`, using different splitting criteria.

| Criterion | Test Accuracy |
|------------|---------------|
| Gini | 0.7464 (74.64%) |
| Entropy | 0.7609 (76.09%) |

The model using **Entropy** achieved slightly higher test accuracy than the model using **Gini**, suggesting that information gain produced marginally better splits for this dataset.
## Gini Impurity

Gini Impurity measures the probability of incorrectly classifying a randomly selected sample if class labels are assigned according to the class distribution within a node.

\[
\text{Gini} = 1 - \sum_{i=1}^{n} p_i^2
\]

where \(p_i\) represents the proportion of samples belonging to class \(i\).

A lower Gini value indicates a purer node. A Gini value of **0** means that all samples in the node belong to a single class, resulting in a perfectly pure node.

## Entropy

Entropy measures the amount of uncertainty or disorder in a node.

\[
\text{Entropy} = -\sum_{i=1}^{n} p_i \log_2(p_i)
\]

where \(p_i\) represents the proportion of samples belonging to class \(i\).

Lower entropy indicates greater purity, while higher entropy indicates greater class mixing. An entropy value of **0** means that all samples belong to the same class and there is no uncertainty in the node.

## Interpretation of Gini = 0

When **Gini = 0**, every sample within that node belongs to a single class. Such a node is called a **pure node** because no misclassification is possible within that subset of data. Decision trees aim to create increasingly pure nodes by selecting splits that reduce impurity at each step.

## How Random Forest Computes Feature Importance

Random Forest computes feature importance using the **average reduction in Gini impurity** produced by a feature across all splits in all trees of the forest. Each time a feature is used to split a node, the decrease in Gini impurity resulting from that split is calculated. These decreases are summed across all trees and then normalized to produce the final importance score.

Features that consistently create large reductions in impurity receive higher importance values because they contribute more to separating the classes.

## Difference Between Feature Importance and Linear Regression Coefficients

Random Forest feature importance and linear regression coefficients measure different concepts.

- **Feature importance** indicates how useful a feature is for making predictions by measuring its contribution to reducing impurity across the forest.
- **Linear regression coefficients** indicate the direction and magnitude of the relationship between a feature and the target variable.

A regression coefficient can be positive or negative, showing whether the target tends to increase or decrease as the feature increases. In contrast, Random Forest feature importance values are always non-negative and do not indicate the direction of the relationship. They only measure how valuable a feature is for splitting the data and improving predictions.

Therefore, while regression coefficients describe the effect of a feature on the target, Random Forest feature importance describes how much that feature contributes to predictive performance.
## Feature Ablation Study

A feature ablation study was performed using the feature importance scores from the Random Forest model. The five features with the lowest importance scores were removed:

- BloodPressure
- SkinFoldThickness
- Insuline
- NoOfPregency
- Age

The reduced model was then trained using only the following three features:

- PlasmaGlucoseCon
- BMI
- DiabetesPedigree

### ROC-AUC Comparison

| Model | Features Used | ROC-AUC |
|---------|---------|---------:|
| Random Forest (Full) | All 8 features | 0.8187 |
| Random Forest (Reduced) | PlasmaGlucoseCon, BMI, DiabetesPedigree | 0.7755 |

### Interpretation

The reduced model achieved a ROC-AUC of **0.7755**, compared to **0.8187** for the full model. This decrease of **0.0432** indicates that the five removed features were contributing useful predictive information to the model. Although each of these features had relatively low individual importance scores, their combined contribution improved the model's ability to distinguish between diabetic and non-diabetic patients.

The results suggest that the removed features were **not merely noise**. Eliminating them led to a noticeable decline in predictive performance, demonstrating that low-importance features can still provide complementary information when used together with more influential predictors.

### Implications for Production Deployment

A lower-dimensional model offers several practical benefits, including:

- Reduced data collection requirements
- Lower computational and storage costs
- Faster prediction times
- Simpler feature pipelines
- Easier monitoring and maintenance

However, these benefits must be balanced against predictive performance. In this study, removing five features reduced the ROC-AUC from **0.8187** to **0.7755**. Whether this degradation is acceptable depends on the application's tolerance for reduced predictive accuracy. For a healthcare screening task such as diabetes prediction, maintaining strong discrimination between positive and negative cases is important, making the full model the preferable choice unless operational constraints strongly favor a simpler model.

## Cross-Validated Model Comparison

To obtain a more robust estimate of model performance, 5-fold stratified cross-validation was performed using ROC-AUC as the evaluation metric. Stratified sampling preserves the class distribution in each fold, ensuring a fair and consistent comparison across models.

### 5-Fold Cross-Validation Results

| Model | Mean ROC-AUC | Std ROC-AUC |
|---------|---------:|---------:|
| Logistic Regression | 0.8178 | 0.0392 |
| Decision Tree (max_depth=5) | 0.7423 | 0.0271 |
| Random Forest | 0.8030 | 0.0399 |
| Gradient Boosting | 0.7914 | 0.0360 |

### Interpretation

The Logistic Regression model achieved the highest mean ROC-AUC (**0.8178**), indicating the strongest overall ability to distinguish between diabetic and non-diabetic patients across the five folds. Random Forest achieved the second-highest ROC-AUC (**0.8030**), followed by Gradient Boosting (**0.7914**). The controlled Decision Tree produced the lowest mean ROC-AUC (**0.7423**), suggesting weaker predictive performance compared with the ensemble methods and Logistic Regression.

The standard deviation of ROC-AUC provides an indication of performance stability across folds. All models exhibited relatively low standard deviations, suggesting reasonably consistent performance across different train-validation splits.

### Why Cross-Validation Is More Reliable Than a Single Train-Test Split

A single train-test split evaluates a model on only one partition of the dataset. The resulting performance estimate can be influenced by how the data happened to be divided, potentially leading to optimistic or pessimistic results.

Cross-validation reduces this dependence by repeatedly training and evaluating the model on multiple different splits of the data. In 5-fold cross-validation, each observation is used for validation exactly once and for training four times. The final score is obtained by averaging performance across all folds, producing a more reliable estimate of how the model is expected to generalize to unseen data.

Because cross-validation evaluates performance across multiple subsets of the dataset rather than a single split, it provides a more stable and representative assessment of real-world model performance.



## Hyperparameter Tuning with Grid Search

A Random Forest classifier was tuned using **GridSearchCV** with 5-fold stratified cross-validation and ROC-AUC as the evaluation metric. A preprocessing pipeline was used to ensure that missing-value imputation and feature scaling were performed consistently within each cross-validation fold.

### Parameter Grid

- `n_estimators`: [50, 100, 200]
- `max_depth`: [5, 10, None]
- `min_samples_leaf`: [1, 5]

### Best Hyperparameters

| Parameter | Value |
|------------|--------|
| max_depth | 5 |
| min_samples_leaf | 5 |
| n_estimators | 200 |

### Best Cross-Validated ROC-AUC

| Metric | Value |
|----------|---------:|
| Best CV ROC-AUC | 0.8188 |

### Comparison with Baseline Random Forest

| Model | Mean ROC-AUC |
|---------|---------:|
| Baseline Random Forest (100 trees, max_depth=10) | 0.8030 |
| Tuned Random Forest | 0.8188 |

The tuned Random Forest achieved a cross-validated ROC-AUC of **0.8188**, outperforming the baseline Random Forest, which achieved a mean ROC-AUC of **0.8030**. The optimal model used a relatively shallow tree depth (`max_depth=5`) together with a larger number of trees (`n_estimators=200`) and a minimum leaf size of five samples (`min_samples_leaf=5`).

These results suggest that limiting tree complexity helped improve generalization performance by reducing overfitting. The larger ensemble size compensated for the reduced complexity of individual trees, allowing the model to maintain strong predictive performance while producing more stable predictions across different training folds.

### Why Grid Search Is Useful

Grid Search systematically evaluates multiple combinations of hyperparameters and selects the configuration that achieves the best cross-validated performance. Rather than relying on manually chosen settings, this approach helps identify models that generalize more effectively to unseen data and provides a more objective method for model selection.
## Manual Learning Curve Analysis

The tuned Random Forest pipeline was trained on progressively larger subsets of the training data (20%, 40%, 60%, 80%, and 100%). For each subset, both the training ROC-AUC and test ROC-AUC were computed.

### Results

| Training Fraction | Training AUC | Test AUC |
|------------------:|-------------:|---------:|
| 0.2 | 0.9815 | 0.8336 |
| 0.4 | 0.9450 | 0.8475 |
| 0.6 | 0.9459 | 0.8385 |
| 0.8 | 0.9313 | 0.8480 |
| 1.0 | 0.9166 | 0.8447 |


#### (i) Does Training AUC Decrease as the Training Set Grows?

Yes. The training AUC decreased from **0.9815** at 20% of the training data to **0.9166** when the full training dataset was used. This is expected behavior for high-variance models. With smaller datasets, the model can fit the training examples very closely, resulting in extremely high training performance. As more data is added, the model must generalize across a wider range of observations, causing training performance to become more realistic.

#### (ii) Does Test AUC Increase with More Training Data?

Overall, yes. The test AUC improved from **0.8336** at 20% of the training data to values around **0.845–0.848** for larger training fractions. Although some fluctuations are present, the general trend indicates that the model benefits from having access to more training examples. This suggests that additional data helps the model learn patterns that generalize better to unseen patients.


The model successfully loaded from the saved `best_model.pkl` file and generated predictions without errors.

- **Patient 1** was classified as **Class 0 (tested_negative)** with a predicted probability of **31.03%** for diabetes and **68.97%** for the non-diabetic class.
- **Patient 2** was classified as **Class 1 (tested_positive)** with a predicted probability of **82.88%** for diabetes and **17.12%** for the non-diabetic class.

These results confirm that the serialized model can be restored and used for inference without requiring retraining, making it suitable for deployment and future predictions.
## Final Model Comparison

The table below compares all classification models using both cross-validated ROC-AUC and test-set ROC-AUC.

| Model | 5-Fold CV Mean AUC | 5-Fold CV Std AUC | Test AUC |
|---------|---------:|---------:|---------:|
| Logistic Regression (C=1.0) | 0.8178 | 0.0392 | 0.8220 |
| Decision Tree (max_depth=5) | 0.7423 | 0.0271 | 0.6391 |
| Random Forest (n_estimators=100, max_depth=10) | 0.8030 | 0.0399 | 0.8187 |
| Gradient Boosting | 0.7914 | 0.0360 | *Insert Gradient Boosting Test AUC* |
| Tuned Random Forest (GridSearchCV) | 0.8188 | N/A* | 0.8447** |

\* GridSearchCV reports the mean cross-validated ROC-AUC of the selected hyperparameter configuration. A fold-by-fold standard deviation was not retained from the final grid search output.

\** Using the tuned model evaluated during the manual learning-curve analysis.

## Recommended Model

I would recommend the **tuned Random Forest** model for deployment. It achieved the highest cross-validated ROC-AUC (**0.8188**) and also produced the strongest test-set performance (**0.8447**), indicating excellent discrimination between diabetic and non-diabetic patients. The model benefited from controlled tree depth (`max_depth=5`) and a larger ensemble size (`n_estimators=200`), which improved generalization while reducing the risk of overfitting. Additionally, the learning-curve analysis showed stable performance as more training data was introduced, suggesting that the model generalizes well and is suitable for real-world diabetes risk prediction. While Logistic Regression performed competitively and offers greater interpretability, the tuned Random Forest delivered the best overall predictive performance and is therefore the preferred choice.
