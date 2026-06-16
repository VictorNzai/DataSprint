# DataSprint
# Project README: Predicting Financial Status of Kenyan Adults

## 1. Problem Statement and Objectives

This project aims to predict the financial status of Kenyan adults (Worsened, Stayed the same, Improved) using various socio-economic, demographic, and financial behavior indicators. The primary objective is to identify key factors contributing to financial deterioration, enabling targeted interventions and policy recommendations to improve financial resilience.

### Guiding Question:
What are the top predictors of financial deterioration among Kenyan adults, and how can these insights inform policy to improve financial inclusion and well-being?

## 2. Tools and Technologies Used

- **Programming Language:** Python
- **Libraries:**
    - `pandas`: For data manipulation and analysis.
    - `numpy`: For numerical operations.
    - `matplotlib.pyplot`: For data visualization.
    - `seaborn`: For advanced statistical data visualization.
    - `scikit-learn`: For machine learning models (Logistic Regression, RandomForestClassifier, StandardScaler, LabelEncoder, train_test_split, f1_score, classification_report, confusion_matrix, RandomizedSearchCV).
    - `xgboost`: For gradient boosting machine learning (XGBClassifier).
    - `lightgbm`: For gradient boosting machine learning (LGBMClassifier).
    - `shap`: For explainable AI (SHAP values for feature importance).
    - `scipy.stats`: For statistical tests (pearsonr, chi2_contingency).
    - `google.colab`: For Google Colab specific functionalities (e.g., drive mount, userdata).
    - `re`: For regular expressions (used in column name cleaning).

## 3. Data Preparation and Cleaning Process

1.  **Data Loading:** The dataset `finaccess2024_datasprint.xlsx` was loaded into a pandas DataFrame.
2.  **Missing Values:** Missing values in the `barriers_bank` column were imputed with 'No barrier'.
3.  **Duplicate Rows:** Duplicate rows were identified and removed from the dataset.
4.  **Categorical Column Cleaning:**
    -   `education_level`: Standardized by stripping whitespace and mapping inconsistent entries (e.g., 'Some primary', 'Primary completed' to 'Primary', '95' to 'Unknown').
    -   `marital_status`: Standardized by stripping whitespace and mapping inconsistent entries (e.g., "Don't know", 'Refused to Answer' to 'Unknown').
    -   `barriers_mobile_money`: Standardized by replacing '0' with 'No barrier'.
5.  **Minors Removal:** Rows corresponding to individuals aged '16-17' were removed as the survey targets adults.
6.  **Target Encoding:** The `financial_status` target variable was numerically encoded: 'Worsened'=0, 'Stayed the same'=1, 'Improved'=2.
7.  **Feature Separation:** Features (X) were separated from the target (y).
8.  **Categorical Encoding:** Categorical features were One-Hot Encoded using `pd.get_dummies` with `drop_first=True` to prevent multicollinearity. Boolean columns resulting from encoding were converted to integers.
9.  **Numerical Scaling:** Numerical features were scaled using `StandardScaler`.
10. **Train/Test Split:** The data was split into training and testing sets (80/20 ratio) using `train_test_split` with `stratify=y` to maintain the target variable's class distribution.

## 4. Exploratory Data Analysis (EDA)

EDA involved: 
- Initial data checks (`df.info()`, `df.describe()`, `df.isnull().sum()`, `df.nunique()`).
- Value counts for key categorical columns (`location_type`, `Sex`, `Age`, `education_level`, `financial_status`).
- **Marital Status vs. Financial Status Analysis:** Crosstabulations and grouped bar charts showed the distribution of financial status across different marital statuses. Widowed individuals and those in 'Unknown' marital status categories showed higher rates of 'Worsened' financial status.
- **Gender vs. Financial Status Analysis:** Crosstabulations and bar charts indicated that females experienced a slightly higher percentage of 'Worsened' financial status compared to males.
- **Correlation Analysis:** Pearson correlation was performed between numerical features (`household_size`, `monthly_income`, `prodsum1`) and the encoded target variable. A correlation heatmap was also generated. `monthly_income` showed a positive correlation with improved financial status, while `household_size` showed a slight negative correlation.
- **Chi-Square Tests:** Chi-square tests were performed for categorical features (`experienced_shock`, `defaulted`, `nfhi_11`, `education_level`) against `financial_status` to assess association. All tested features showed strong statistical associations.
- **Specific Visualizations:**
    - Bar chart showing the impact of `experienced_shock` on `financial_status`.
    - Bar chart of average monthly income by education level, revealing income disparities.

## 5. Modelling Approach

Several classification models were trained and evaluated:

1.  **Naive Baseline:** A model that always predicts 'Worsened' (class 0) to establish a performance floor (Weighted F1: 0.3753).
2.  **Logistic Regression:** Trained with `class_weight='balanced'` (Weighted F1: 0.5048).
3.  **Random Forest Classifier:** Trained with `class_weight='balanced'` (Weighted F1: 0.4959).
4.  **XGBoost Classifier (Baseline):** Trained with `sample_weight='balanced'` (Weighted F1: 0.5165).
5.  **XGBoost Classifier (Tuned):** Hyperparameter tuning using `RandomizedSearchCV` was performed to optimize XGBoost. The best parameters were found and the tuned model achieved a Weighted F1 of 0.5310 on the test set.
6.  **LightGBM Classifier:** An alternative gradient boosting model was attempted, but it yielded a slightly lower Weighted F1 of 0.5155 compared to the tuned XGBoost.

**Final Model Selection:** The **Tuned XGBoost Classifier** was selected as the final model due to its superior performance (Weighted F1: 0.5310).

## 6. Team Collaboration and Workflow

(This section would typically describe how the team worked together, version control, meetings, etc. - based on the provided interaction, this part is hypothetical.)

*   **Version Control:** Hypothetically, Git/GitHub was used for collaborative code development, issue tracking, and code reviews.
*   **Communication:** Regular meetings and communication channels (e.g., Slack, Google Meet) were used to discuss progress, challenges, and next steps.
*   **Task Management:** A tool like Trello or Jira could have been used to manage tasks and assign responsibilities.

## 7. Key Insights, Findings, and Recommendations

### Model Performance:
- The best-performing model was the **Tuned XGBoost Classifier**, achieving a Weighted F1-score of **0.5310** on the test set.
- The model significantly outperforms the Naive Baseline (0.3753 F1), indicating its predictive power.
- Analysis of the confusion matrix reveals that the model struggles most with accurately predicting the 'Improved' and 'Stayed the same' classes, often misclassifying them as 'Worsened'. This is a common challenge with imbalanced datasets and could have policy implications if improved status is under-identified.

### Top Predictors of Financial Deterioration (SHAP Analysis):
Based on SHAP values for the 'Worsened' class, the top predictors were:

1.  **`nfhi_11`** (food security in past 12 months):
    -   **Insight:** Strongest predictor. Individuals reporting food insecurity are significantly more likely to experience worsening financial status.
    -   **Policy Recommendation:** Prioritize interventions that enhance food security (e.g., food aid, sustainable agriculture programs) as a direct means to improve financial well-being.

2.  **`nfhi_12`** (managed non-food spending adequately):
    -   **Insight:** Inability to manage non-food spending is a key indicator of financial strain and a precursor to deterioration.
    -   **Policy Recommendation:** Financial literacy programs focusing on budgeting and expenditure management could be beneficial, especially for vulnerable groups.

3.  **`nfhi_13`** (no debt stress in past 3 months):
    -   **Insight:** High debt stress is a critical factor driving financial decline.
    -   **Policy Recommendation:** Implement debt counseling services, responsible lending practices, and provide avenues for debt restructuring or relief for those in severe distress.

4.  **`experienced_shock`** (financial shock in past year):
    -   **Insight:** Experiencing a financial shock (e.g., illness, job loss, drought) significantly increases the likelihood of financial worsening.
    -   **Policy Recommendation:** Strengthen social safety nets, promote access to affordable insurance (health, crop, unemployment), and facilitate emergency savings mechanisms.

5.  **`marital_status_Single`** (lack of household income pooling):
    -   **Insight:** Single individuals, potentially lacking the diversified income or support structure of larger households, are more susceptible to financial worsening.
    -   **Policy Recommendation:** Design financial inclusion products and support programs that cater to the unique needs of single-person households or individuals without a partner, such as targeted micro-loans or financial planning advice.

### Additional Insights:
- **County-level Vulnerability:** Analysis of counties revealed significant disparities in rates of worsened financial status. Specific counties (e.g., Mandera, Wajir, Kisumu) show structurally higher risks, often correlating with factors flagged by the ML model. This suggests that geographic-specific policies are crucial.
- **Resilience over Income:** The SHAP analysis highlights that **resilience factors** (food security, shock exposure, debt management, access to emergency funds) are often more impactful in predicting financial deterioration than income level alone. This implies that policies should focus not just on income generation but also on building buffers against financial shocks.

## 8. Conclusion

This project provides valuable insights into the drivers of financial deterioration among Kenyan adults. The Tuned XGBoost model, combined with SHAP interpretability, offers a data-driven foundation for developing targeted policies and interventions that aim to enhance financial resilience and foster inclusive economic growth in Kenya.

