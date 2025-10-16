

## Identifying Fraudulent Transactions:

#### Objective of the Project:

The objective of this project is to build a predictive model that classifies whether a given transaction is fraudulent or legitimate. Detecting fraudulent transactions is critical in the financial and banking sectors to minimize monetary losses, protect customers, and maintain trust.

### Business Goal:

Maximize fraud detection accuracy (high recall) while maintaining a low false positive rate (balanced precision-recall trade-off).


---- 


## Data Preparation and Missing Values:

- The dataset had 7 lakh rows and 28 features, with mixed data type. We received it in JSON format and processed it using JSONlines library. 
- The dataset was checked for missing values using the isnull().sum() function.
- Additionally, some columns contained empty strings and whitespace characters instead of actual null values, regular expressions (Regex) were used to detect and handle such columns appropriately. i.e EcoBuffer, 'posOnPremises', 'posConditionCode'. 
- Used nunique function in python to detect unique value distribution and category diversity. It provides information on feature variability and useless coulmns. i.e Merchant_city, Merchant_State, Merchant_zip with only one unique values were dropped. 
- Used pd.to_datetime to convert datetime coulmn stored as string into date object (Transactiondatetime, AcountOpenDate, ExpiryDate) 


<img width="664" height="607" alt="Screenshot 2025-10-07 at 17 17 45" src="https://github.com/user-attachments/assets/7b37e9d7-cc4f-4df0-832a-d7200e420ebf" />


#### Skewness and Outliers in Dataset:

For numerical data distribution, it is important to understand if they are skewed, calculated skewness and visualized using KDE and histrograms to check skewness. 

<img width="477" height="578" alt="Screenshot 2025-10-07 at 17 06 19" src="https://github.com/user-attachments/assets/1338cc30-6e7d-44b8-8d0b-38855e264222" />



<img width="488" height="531" alt="Screenshot 2025-10-07 at 17 06 27" src="https://github.com/user-attachments/assets/88bdd595-a414-42e4-a152-369c3a203112" />

Current Amount, Transaction Amount and  Available Money are rightly skewed with few high value transactions and we will apply log1p transformation which will help us in removing skewness as extreme outliers can disort with model part. 

---

## Data Visualisation - EDA: 

**Top Merchants detected based on Fraud Rate, Fraud Amount, Fraud Count and Temporal trends:**.

### Top 10 Merchants by FRAUD RATE:


<img width="833" height="453" alt="Screenshot 2025-10-16 at 10 08 05" src="https://github.com/user-attachments/assets/511e2a0f-c948-427c-85b4-f28eb06e407f" />

IN AND OUT Showed high fraud rate  even at moderate transaction volume indicating vulnerabiities at specific outlet. 


### TOP 10 Merchants by FRAUD AMOUNT AND FRAUD COUNT:

<img width="778" height="448" alt="Screenshot 2025-10-16 at 10 08 10" src="https://github.com/user-attachments/assets/d63cb34a-8498-44eb-be4e-19dbddde1dfc" />

<img width="766" height="460" alt="Screenshot 2025-10-16 at 10 08 16" src="https://github.com/user-attachments/assets/9ff0b5a3-d19e-4bff-bb76-222d30cd3278" />

Analysed transaction amount and fraud count by merchant. **Uber, Lyft, Walmart, Target, Sears and Amazon** consistently appreared with loss amount of **(10k to 35k)** with fraud rate 2-5%, indicating fraudsters may target major brands with high transaction volume and large traffic. 

### PEAK HOURS WHERE FRAUD OCCURENCE IS HIGHEST: 

<img width="696" height="550" alt="Screenshot 2025-10-16 at 10 08 23" src="https://github.com/user-attachments/assets/ef50db38-68ed-418b-91da-20c023984ff8" />


We grouped merchant name and transaction hours to see when and where fraud is highest. Also, how fraud varies by time and merchant. Fraud Amounts were high between 0-6 AM for merchants like UBER, LYFT AND WALMART, has loss amount of (20k-35k) indicating off hour vulnerabilities in ecommerce and transportation platform. 

-----


## Feature Engineering:


 <img width="957" height="630" alt="Screenshot 2025-10-07 at 17 05 53" src="https://github.com/user-attachments/assets/9846a1c8-f6b1-4eb7-ad24-e4551d7285bb" />




 <img width="1320" height="324" alt="image" src="https://github.com/user-attachments/assets/4a72641e-b71f-4749-ac45-528fc6bde1b3" />
 



<img width="610" height="120" alt="Screenshot 2025-10-07 at 17 06 54" src="https://github.com/user-attachments/assets/ab5053e9-4cdb-446d-9417-00a87fcf3a16" />




### Data Transformation using Coulmn transformer and Pipeline. 


<img width="1234" height="906" alt="image" src="https://github.com/user-attachments/assets/d3687817-566d-4d29-94bf-b4a1e58a0c6e" />

I've created a pipeline for preprocessing, imputation, scaling and handling class imbalance. I used coulmn transformer inside pipeline for seperate preprocessing of numerical, categorical, Log-transformed and binary features and to avoid data leakage. 

-----

## Model Development and Evaluation: 

### 1.Results from Logistic Regression with tuned Thresold:


<img width="792" height="648" alt="Screenshot 2025-10-16 at 09 20 09" src="https://github.com/user-attachments/assets/d8011189-e647-465b-a82f-65d5c0604bf8" />

The sklearn pipeline has encapsulated coulmn transformer, SMOTE to handle class imbalance, by creating synthetic samples of minority class and Logistic Regression serves as the final classifier with max_iter=1000 for convergence. 

Raw Input data is trained and transformed during training and prediction.

Stored perforrmance metrics from log model like classfication report, confusion matrix and AUC ROC Score. With recall of 70% and precision of 0.03, model did a good job caputring most of the fraudulent transaction i.e minimizing false negative and mazimizing Recall. While high number of false positives (>8000) were detected at thresold of 0.5, its less riskier then missing an actual fraudulent transaction. 

Decreasing thresold a little 0.4, decreased false negative, but hurted precision which can lead to false alarms. 

### 2.Results from Xgboost:


<img width="943" height="265" alt="Screenshot 2025-10-15 at 21 57 31" src="https://github.com/user-attachments/assets/8dbc205f-aa77-4e4a-87ac-83ffe13c92df" />

<img width="655" height="456" alt="Screenshot 2025-10-15 at 21 57 47" src="https://github.com/user-attachments/assets/ef9cb7a7-6324-4565-9122-b5ea9d2cc9f7" />

XGBoost performed notably better than the Logistic Regression model due to its ability to capture non-linear relationships and complex feature interactions that linear models cannot. It achieved an increase in precision by 0.04 and a recall of 0.69, indicating a balanced trade-off between identifying true positives and minimizing false positives.

The model effectively reduced false positives to 6,000 and false negatives to 121, showing that it can accurately detect positive cases while keeping misclassifications low. Overall, XGBoost delivered a strong balance between predictive accuracy and generalization, making it well-suited for this classification task.. 


------


### Which Models to Deploy in Production: 

XGBoost performs best for this problem, primarily because it achieves the lowest number of false negatives, which aligns with our business goal of minimizing missed fraudulent transactions.

Missing a fraudulent transaction (false negative) can lead to substantial financial losses. Therefore, maximizing recall (0.69) — the proportion of actual frauds correctly identified — is critical. XGBoost successfully maximizes recall while still maintaining a reasonable balance with precision (0.4) for class 1. 

Additionally, XGBoost’s ability to handle non-linear relationships, missing values, and class imbalance makes it a strong candidate for production deployment. Its performance consistency and scalability also make it suitable for real-time predictions in fraud detection systems.

As a rollback option, Logistic Regression with a 0.5 threshold can serve as a backup model. Although it achieves slightly higher false positives, which could cause unnecessary alerts and damage customer trust.

-----

### TOP PREDICTORS OF FRAUD:

**Merchants and Peak Hours to Watch out:**

Fresh Flowers, Uber, Lyft, Ebay.com, Walmart and Sears consistently appeared in list where Fraud transaction volume and  fraud counts were high. This evidented from the temporal analysis where hours like 12:00 AM, 01:Q0 AM and 03:00 AM were targeted mostly and these specific merchants showed fraudulent activity indicating low monitoring hours or weak verification system.

### FUTURE WORK:

For future work, I’d deploy the model using FastAPI or Flask and manage it through MLflow or AWS SageMaker for versioning and scaling.
I’d set up model monitoring with tools like EvidentlyAI or WhyLabs to track drift, precision, and recall over time.
For explainability, I’d use SHAP or LIME to show key drivers behind each fraud prediction, improving transparency for business teams. 
