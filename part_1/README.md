# Diabetes Prediction Using Machine Learning

## Part 1: Data Collection & Exploratory Data Analysis (EDA)

---

# Problem Statement

Diabetes is one of the most common chronic diseases worldwide and can lead to severe health complications if not diagnosed and managed early. Early prediction of diabetes can support timely medical intervention and improve patient outcomes.

The objective of this project is to analyze patient health data and develop a machine learning-based system capable of predicting whether a patient has diabetes based on various clinical and diagnostic measurements.

This project is divided into multiple phases. In **Part 1**, the focus is on:

- Data collection
- Data cleaning and preprocessing
- Missing value treatment
- Exploratory Data Analysis (EDA)
- Outlier detection
- Feature relationship analysis
- Statistical and visual exploration

The insights discovered during this stage will serve as the foundation for developing and evaluating predictive machine learning models in subsequent phases of the project.

---

# Dataset Information

## Dataset Name

**Pima Indians Diabetes Dataset (Missing Values Version)**

## Dataset Source

The dataset was obtained from Kaggle:

### Reference

> Achhatrara, V. (n.d.). *Missing Values Pima Indians Diabetes Data*. Kaggle.

Dataset Link:  
[Missing Values Pima Indians Diabetes Data](https://www.kaggle.com/datasets/vishvaachhatrara/missing-values-pima-indians-diabetes-data)

---

## Dataset Description

The dataset contains medical diagnostic measurements collected from female patients of Pima Indian heritage.

The objective is to determine whether a patient is diabetic based on several health-related indicators.

### Features Included

| Feature | Description |
|----------|-------------|
| NoOfPregency   | Number of times pregnant |
| PlasmaGlucoseCon | Plasma glucose concentration |
| BloodPressure | Diastolic blood pressure (mm Hg) |
| SkinThickness | Triceps skin fold thickness (mm) |
| Insuline | Two-hour serum insulin |
| BMI | Body Mass Index |
| DiabetesPedigree | Diabetes pedigree function |
| Age | Patient age |
| Class | Diabetes outcome (Positive/Negative) |

---

# Data Quality Assessment

## Missing Values Analysis

Several medical variables contained missing or invalid values that required preprocessing before model development.

A detailed missing-value assessment was performed to identify affected features and determine appropriate imputation strategies.

---

## Zero Value Handling

Certain numerical features contained zero values where a value of zero is physiologically unrealistic. These values were treated as missing observations and handled during preprocessing.

Affected variables included:

- PlasmaGlucoseCon
- BloodPressure
- SkinThickness
- Insuline
- BMI

---

# Missing Value Imputation Strategy

## Why Median Imputation Was Selected

Several features exhibited substantial positive skewness, particularly the **Insuline** variable.

### Positive Skewness

Positive Skewness: Most observations are concentrated at lower values, while a few unusually large values create a long tail extending to the right. In the Insuline distribution, most patients have relatively lower insulin levels, whereas a small number of patients have extremely high insulin values. 

### Negative Skewness
Most observations are concentrated at higher values, while a few unusually small values create a long tail extending to the left.
The median was preferred over the mean because several variables in the dataset exhibit strong positive skewness. 


---

### Why Not Use Mean Imputation?

The mean is pulled toward the extreme values in the right tail. If missing values were imputed using the mean, the imputed values would likely be higher than what is typical for most patients, introducing bias into the dataset.

---

### Why Median Imputation?

The median is unaffected by these extreme values and better represents the centre of the distribution, making it the preferred choice for imputation in this project. The median is more robust to outliers and therefore provides a more reliable estimate for missing values.


---

# Duplicate Record Analysis

A duplicate check was performed to identify repeated observations within the dataset.

The analysis ensured that duplicated records would not artificially influence subsequent statistical analyses or machine learning performance.

---

# Exploratory Data Analysis (EDA)

## Descriptive Statistics

Descriptive statistical measures were calculated for all numerical features, including:

- Mean
- Median
- Standard Deviation
- Minimum
- Maximum
- Quartiles

This provided a broad understanding of feature distributions and variation within the dataset.

---

## Distribution Analysis

Distribution analysis was conducted to understand:

- Feature central tendency
- Spread
- Skewness
- Presence of potential outliers

Visualization techniques were applied to identify patterns and abnormalities within the data.

---

# Outlier Detection

## Method Used

Outliers were identified using the **Interquartile Range (IQR) Method**.

The process involved:

1. Computing Q1 and Q3.
2. Calculating IQR = Q3 − Q1.
3. Determining lower and upper bounds.
4. Detecting observations outside these thresholds.

---

## Insuline Outlier Analysis

Insulin measurements can naturally vary across patients, and extreme values may contain clinically important information related to diabetes diagnosis. Removing 314 observations would eliminate a substantial portion of the dataset and could result in the loss of valuable information. Because the Insuline feature is highly positively skewed and contains genuine extreme values, a model-based assessment in Part 2 provides a more reliable basis for deciding whether capping or transformation is necessary. Therefore, the outliers are documented and retained for now, with final treatment deferred until model evaluation is performed.


---

## Future Outlier Treatment Considerations

The final treatment strategy will be evaluated during model development.

Possible approaches include:

### Retaining Outliers

If predictive performance remains stable, the outliers may be preserved.

### Feature Transformation

Transformations such as logarithmic scaling may be applied to reduce skewness while maintaining information.

### Robust Machine Learning Algorithms

Tree-based models, which are generally less sensitive to outliers, may be used.

Final decisions will be based on model evaluation results in Part 2.

---

# Correlation Analysis

Both **Pearson Correlation** and **Spearman Correlation** analyses were performed to examine relationships between variables.

The objective was to identify:

- Strongly associated features.
- Potential predictors of diabetes.
- Multicollinearity concerns.

---

## Important Interpretation

Correlation indicates association, not causation.

A strong correlation between two variables does not imply that one variable directly causes changes in the other.

The relationship observed between **Age** and **BMI** does not necessarily mean that age directly causes increased BMI.

Other factors such as:

- Physical activity
- Diet
- Lifestyle
- Metabolic changes
- Health conditions

may influence both variables simultaneously.

---

## Diabetes Pedigree and Glucose

Analysis suggested that individuals with higher diabetes pedigree scores generally tend to have higher glucose concentrations.

However, the increase is not necessarily proportional, indicating a complex relationship between genetic predisposition and glucose levels.

---

# Group-Based Analysis by Diabetes Outcome

The dataset was grouped by the target variable (**Class**) to compare characteristics of patients who tested positive and negative for diabetes.

---

## Purpose

This analysis was conducted to:

- Compare feature distributions across classes.
- Identify variables showing predictive potential.
- Evaluate within-group variation.

---

## Importance of Within-Group Variability

A high standard deviation within a group indicates that individuals belonging to the same class can still have very different feature values.

This reduces the effectiveness of using a single variable as a standalone predictor.

---

## Insuline

The strongest example is **Insuline**.

The diabetes-positive group exhibited:

- Mean = 166.28
- Standard Deviation = 101.01

The large variability suggests insulin levels alone are insufficient for reliable diabetes classification.

---

## Other High-Variance Features

Considerable variability was also observed for:

- PlasmaGlucoseCon
- BMI
- Age
- BloodPressure

These overlapping distributions indicate that no single feature can completely separate diabetic and non-diabetic patients.

---

# Key Findings from EDA

### Major Observations
**Highest Mean Group:**<br> 
The Tested Positive group has the highest mean for all five analyzed features.
**Highest Standard Deviation Group:**<br>
The Tested Positive group has the highest standard deviation for PlasmaGlucoseCon, BMI, and Insuline, while the Tested Negative group has the highest standard deviation for Age and BloodPressure.<br>
**High within-group variance** is a concern for BMI, Insuline, Age, and BloodPressure, indicating that these variables alone are insufficient for reliable prediction.
PlasmaGlucoseCon demonstrates the strongest separation between outcome groups and appears to contain the most useful predictive signal for diabetes classification.
In Part 2, these features will be evaluated collectively within machine learning models rather than individually, as combining multiple features is expected to provide substantially better predictive performance than any single feature alone.

---

## Most Promising Predictive Feature

Among the analyzed variables, **PlasmaGlucoseCon** demonstrated the clearest separation between outcome groups and appears to contain the strongest predictive signal for diabetes classification.

---

The findings indicate that diabetes prediction cannot rely on a single clinical measurement. Instead, a combination of multiple features is expected to provide better predictive capability.

These cleaned and analyzed data will be used in **Part 2**, where machine learning models will be developed, trained, and evaluated to predict diabetes outcomes.
