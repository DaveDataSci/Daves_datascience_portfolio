Obesity Data Analysis & Predictive Modeling (R)

📌 Overview

This project performs a comprehensive statistical and machine learning analysis on an obesity dataset to uncover key factors influencing obesity levels and build predictive models.

The workflow combines data cleaning, exploratory data analysis (EDA), statistical testing, dimensionality reduction, and regression modeling to generate meaningful insights and predictive performance.

---

🎯 Objectives

- Identify key variables influencing obesity levels
- Assess data distribution and normality
- Explore relationships between variables using correlation analysis
- Reduce dimensionality using Principal Component Analysis (PCA)
- Build and evaluate predictive models using regression techniques

---

🛠️ Tools & Technologies

- R Programming
- Libraries:
  - "ggplot2", "corrplot", "GGally"
  - "glmnet", "caret"
  - "psych", "ppcor"
  - "Amelia", "nortest", "rcompanion"

---

📂 Project Structure

├── data/
│ └── ObesityDataSet_raw_and_data_sinthetic.csv
├── plots/
│ ├── histograms/
│ ├── boxplots/
│ ├── qqplots/
│ ├── correlation/
│ ├── pca/
│ └── regression/
├── scripts/
│ └── analysis.R
├── outputs/
│ ├── model_performance_summary.csv
│ └── normality_shapiro_ks.csv
└── README.md

---

🔍 Methodology

1. Data Preparation

- Loaded dataset and inspected structure
- Handled missing values and checked data quality
- Converted numeric-like variables to numeric format
- Cleaned and standardized column names

---

2. Exploratory Data Analysis (EDA)

- Generated:
  - Histograms and density plots
  - Boxplots for outlier detection
  - Q-Q plots for normality assessment
- Visualized missing data patterns

---

3. Statistical Testing

- Conducted:
  - Shapiro-Wilk test
  - Kolmogorov-Smirnov test
- Evaluated normality across all numeric variables

---

4. Correlation Analysis

- Computed:
  - Correlation matrix
  - Partial correlation matrix
- Visualized relationships using heatmaps

---

5. Dimensionality Reduction

- Applied Principal Component Analysis (PCA)
- Evaluated:
  - Scree plot
  - Cumulative variance
- Selected optimal components based on:
  - Eigenvalues (>1)
  - 80% variance threshold
- Interpreted rotated component loadings

---

6. Sampling Adequacy Test

- Performed Kaiser-Meyer-Olkin (KMO) test
- Assessed suitability of variables for PCA

---

7. Predictive Modeling

📈 Linear Regression

- Modeled relationships between:
  - Age vs Height
  - Age vs Weight

🔍 Stepwise Regression (AIC)

- Selected optimal predictors automatically

🧮 LASSO Regression

cv_lasso <- cv.glmnet(x_train, y_train, alpha = 1, nfolds = 10)
best_lambda <- cv_lasso$lambda.min
lasso_fit <- glmnet(x_train, y_train, alpha = 1, lambda = best_lambda)

⚖️ Elastic Net (Caret)

- Used cross-validation for tuning
- Combined L1 and L2 regularization

---

8. Model Evaluation

- Metrics used:
  - RMSE (Root Mean Squared Error)
  - R² (Coefficient of Determination)
- Compared performance across models

---

📈 Key Results & Insights

- Identified strong relationships between weight-related variables and obesity levels
- PCA successfully reduced dimensionality while preserving ~80% of variance
- Regularized models (LASSO & Elastic Net) improved prediction performance
- Correlation analysis revealed multicollinearity among key predictors

---

▶️ How to Run the Project

1. Install dependencies

install.packages(c("ggplot2", "corrplot", "GGally", "glmnet", "caret", "psych", "ppcor", "Amelia", "nortest", "rcompanion"))

2. Run the analysis

source("scripts/analysis.R")

3. Outputs

- Visualizations saved in "/plots"
- Model results saved in "/outputs"

---

💡 Key Takeaways

- Demonstrates end-to-end data science workflow in R
- Combines statistical rigor with machine learning techniques
- Emphasizes data-driven decision making and model evaluation

---

🔗 Future Improvements

- Incorporate classification models for obesity categories
- Deploy model using a web app (e.g., Shiny)
- Perform feature engineering for improved performance

---

👤 Author

David Asuzor
Aspiring Data Scientist | Data Analysis & Machine Learning Enthusiast
