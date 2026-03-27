Obesity Level Classification Using KNN & Feature Engineering (R)

📌 Overview

This project builds a classification model to predict obesity levels based on lifestyle, behavioural, and physical attributes. Using K-Nearest Neighbours (KNN) and feature engineering, the project aims to accurately classify individuals into categories such as Normal, Overweight, and Obesity types.

The workflow demonstrates data preprocessing, normalization techniques, model training, and evaluation, alongside custom feature creation to improve predictive performance.

---

🎯 Objectives

- Predict obesity categories using supervised machine learning
- Apply normalization techniques to improve model performance
- Evaluate classification accuracy using confusion matrices
- Engineer new features to better capture lifestyle behaviours

---

🛠️ Tools & Technologies

- R Programming
- Libraries:
  - "class" (KNN)
  - "gmodels" (evaluation)
  - "caret" (preprocessing)
  - "DMwR2" (SoftMax normalization)

---

📂 Project Structure

├── data/
│ └── ObesityDataSet_raw_and_data_sinthetic.csv
├── scripts/
│ └── classification_model.R
├── outputs/
│ └── confusion_matrix_results.csv
├── plots/
│ └── feature_engineering_visuals.png
└── README.md

---

🔍 Methodology

1. Data Preprocessing

- Loaded dataset with categorical target variable ("NObeyesdad")
- Checked and removed missing values
- Normalised numeric features using:
  - Min-Max scaling
  - SoftMax transformation

---

2. Train-Test Split

- 80% training data
- 20% test data

---

3. Model Development

📌 K-Nearest Neighbours (KNN)

knn_model <- knn(
  train = train_scaled,
  test = test_scaled,
  cl = Obesedata_train_labels,
  k = 5
)

- Experimented with different values of K (e.g., 5 and 41)
- Applied feature scaling (centering and standardisation)

---

4. Model Evaluation

- Used confusion matrix to assess classification performance

CrossTable(
  x = Obesedata_test_labels,
  y = knn_model
)

---

5. Feature Engineering

To improve model performance and interpretability, new variables were created:

🧠 Lifestyle Score

- Combines physical activity, water intake, and technology usage
- Higher score = healthier lifestyle

🍽️ Eating Behaviour Index

- Combines vegetable consumption and number of meals

⚖️ Activity Ratio

- Ratio of physical activity to sedentary behaviour

---

6. Data Visualisation

- Boxplots and histograms used to:
  - Understand feature distributions
  - Identify patterns across obesity categories

---

📈 Key Results & Insights

- KNN successfully classified obesity levels with reasonable accuracy
- Model performance improved with proper scaling and normalization
- Feature engineering revealed:
  - Lifestyle habits strongly influence obesity classification
  - Sedentary behaviour plays a significant role
- Choice of K value significantly impacts model performance

---

▶️ How to Run

1. Install required packages

install.packages(c("class", "gmodels", "caret", "DMwR2"))

2. Run the script

source("scripts/classification_model.R")

---

💡 Key Takeaways

- Demonstrates practical implementation of KNN classification in R
- Highlights importance of feature scaling and preprocessing
- Shows how feature engineering improves model performance

---

🔗 Future Improvements

- Hyperparameter tuning for optimal K selection
- Compare with advanced models (Random Forest, XGBoost)
- Perform cross-validation for more robust evaluation

---

👤 Author

David Asuzor
Aspiring Data Scientist | Machine Learning & Analytics
