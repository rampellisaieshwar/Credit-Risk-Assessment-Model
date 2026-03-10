```markdown
# Credit Risk Assessment Model

## Project Overview
This project aims to develop a credit risk assessment model using a Kaggle dataset to predict the `QUALITY_OF_LOAN`. The goal is to classify loans as 'Good' (G) or 'Bad' (B) to help financial institutions make informed lending decisions. The process involved comprehensive data cleaning, preprocessing, training an XGBoost classifier, and evaluating its performance with a critical focus on recall to minimize false negatives.

## Data Used

### Source
The dataset, titled "Credit Risk Assessment," was obtained from Kaggle. It consists of two files: `train-FIN_ANA_DATA .xls` for training and `test-FIN_ANA_DATA .xls` for testing.

### Size and Structure
- **Training Data (`train_df`)**: 37,408 rows and 11 columns.
- **Test Data (`test_df`)**: 4,310 rows and 11 columns.

### Key Columns
- `ACC_NO`: Account Number (dropped as an identifier).
- `INVESTMENT_TOTAL`: Total investment amount (numerical).
- `ACCCURRENTBALANCE`: Current account balance (numerical).
- `INF_MARITAL_STATUS`: Marital status of the applicant (categorical).
- `INF_GENDER`: Gender of the applicant (categorical).
- `INSTALL_SIZE`: Installment size (numerical).
- `DUE_PAYMENT`: Amount of due payment (numerical).
- `COMPENSATION_CHARGED`: Whether compensation was charged (categorical).
- `CLIENT_TYPE`: Type of client (categorical).
- `QUALITY_OF_LOAN`: Target variable, indicating loan quality ('G' for Good, 'B' for Bad, 'SS' and 'DF' also present in test set).
- `REPAY_MODE`: Repayment mode (categorical).

## Data Cleaning and Preprocessing

### 1. Missing Value Imputation
- **Numerical Columns**: `INSTALL_SIZE` was imputed with the median value for both training and test datasets.
- **Categorical Columns**: `INF_MARITAL_STATUS`, `INF_GENDER`, `COMPENSATION_CHARGED`, and `CLIENT_TYPE` were imputed with their respective mode values.

### 2. Duplicate Removal
- Checked for and removed any duplicate rows in both `train_df` and `test_df`. No duplicates were found.

### 3. Feature Engineering and Encoding
- **Target Variable Separation**: The `QUALITY_OF_LOAN` column was separated as the target variable (`y`), and `ACC_NO` was dropped from the features (`X`).
- **Categorical Feature Encoding**:
    - **Binary Features**: `INF_MARITAL_STATUS`, `INF_GENDER`, `COMPENSATION_CHARGED`, and `REPAY_MODE` were converted to numerical representations using `LabelEncoder`.
    - **Non-Binary Features**: `CLIENT_TYPE` was transformed using `OneHotEncoder` to create new binary columns, handling the `0` category by converting it to string type before encoding.
- **Numerical Feature Scaling**: `INVESTMENT_TOTAL`, `ACCCURRENTBALANCE`, `INSTALL_SIZE`, and `DUE_PAYMENT` were scaled using `StandardScaler` to normalize their ranges and prevent features with larger values from dominating the model.

### 4. Target Variable Encoding for Training
- The target variable `QUALITY_OF_LOAN` (containing 'B' and 'G' in the training set) was encoded using `LabelEncoder` to `0` and `1` for model training. A separate `LabelEncoder` was used for evaluation to consistently handle all possible labels found in both train and test sets ('G', 'B', 'SS', 'DF').

## Model Training

### Chosen Model: XGBoost Classifier
An XGBoost Classifier was selected due to its proven efficiency, scalability, and robust performance in machine learning tasks, especially with tabular data. XGBoost is an ensemble learning method that uses gradient boosting, known for handling complex datasets and providing high predictive accuracy.

### Training Process
The model was trained on the preprocessed `X_train` features and the encoded `y_train_encoded_for_training` target variable. The `objective` was set to `'multi:softmax'` and `num_class` was set to 2, corresponding to the two classes ('B' and 'G') present in the training target variable. `eval_metric` was set to `'mlogloss'`.

## Model Evaluation

The model's performance was evaluated on the preprocessed test set (`X_test`) against the filtered and encoded test labels (`y_test_filtered_encoded`), focusing on 'B' and 'G' classes that the model was trained to predict. The key metrics evaluated were Precision, Recall, and F1-scores, with a specific emphasis on Recall to minimize false negatives in credit risk assessment.

### Evaluation Metrics
- **Overall Weighted Recall**: 0.9914

### Classification Report
```
              precision    recall  f1-score   support

           B       0.00      0.00      0.00        19
           G       1.00      1.00      1.00      4286

    accuracy                           0.99      4305
   macro avg       0.50      0.50      0.50      4305
weighted avg       0.99      0.99      0.99      4305
```

### Confusion Matrix
![Confusion Matrix for XGBoost Classifier](confusion_matrix.png) <!-- Assuming this plot will be saved as an image -->

### Discussion of Performance
The model exhibits a high overall recall (0.9914), primarily driven by its excellent performance in identifying 'Good' (G) loans. The recall for class 'G' is effectively 1.00, meaning it correctly identifies almost all good loans. However, the model completely fails to identify 'Bad' (B) loans, with a recall score of 0.00 for class 'B'. This means that all 19 instances of 'Bad' loans in the filtered test set were misclassified as 'Good' loans. This is a significant issue for a credit risk assessment model, as false negatives (classifying a bad loan as good) can lead to substantial financial losses.

The model, in its current state, is highly biased towards the majority class ('G') and cannot effectively identify the minority class ('B'). This imbalance needs to be addressed through techniques such as oversampling, undersampling, or using algorithms specifically designed for imbalanced datasets.

## Potential Business Value
While the current model's inability to detect 'Bad' loans is a major limitation, a refined model that effectively addresses the class imbalance and improves recall for the 'Bad' loan category would offer immense business value. Such a model could:
- **Reduce Financial Losses**: By accurately identifying high-risk applicants, banks and financial institutions could avoid approving loans that are likely to default.
- **Optimize Lending Decisions**: Enable more strategic and data-driven decisions, leading to a healthier loan portfolio.
- **Enhance Risk Management**: Provide a robust tool for proactive risk assessment and mitigation.

Future work should focus on improving the model's ability to detect 'Bad' loans, possibly by experimenting with different sampling techniques, cost-sensitive learning, or alternative models.
```
