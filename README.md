# UIH_PASC_model
A Machine Learning model to help identify patients with Post-Acute Sequelae of SARS-CoV-2 infection (PASC) using EHR data
METHODS
In this section, we outline the systematic approach utilized for data preprocessing, feature engineering,
selection, and model development.
Data Source
Data was obtained from the UI Health EHR covering a period of March, 2020 – June, 2022. Data included
patients who had a COVID-19 diagnosis only (N = 12174), who survived at least 30 days post-COVID-19
diagnosis. COVID-19 diagnoses was defined as patients with a positive laboratory test for COVID-19 (e.g.,
positive PCR for SARS-CoV-2 or diagnosis code indicating COVID-19 diagnosis. Data access was granted
based on an IRB Exempt approval. The data was pulled for this population from the CCTS Clinical
Research Datawarehouse (CRDW).

Data Preprocessing and Engineering:
Our datasets included patient demographics, diagnoses, and hospital visits, which we loaded into
pandas DataFrames. A thorough exploratory analysis was undertaken to validate the integrity of the
data.
When engineering the diagnosis data, we incorporated the definition of Long COVID, which pertains to
ongoing, relapsing, or new symptoms or conditions that manifest 30 or more days after a COVID
infection. Consequently, we filtered out all diagnosis codes before and within 30 days of the initial
COVID diagnosis, retaining only those that emerged post the 30-day period. This decision ensures that
the long-term impact of COVID on patient health is accurately reflected in our analysis.
Prior to the creation of data labels, we employed the PHEWAS (Phenome-Wide Association Studies)
catalog (https://phewascatalog.org/phecode_x) to map detailed ICD-10 codes to a higher-level parent
code, reducing data granularity while preserving clinical relevance. This upcoding process was essential
to manage the specificity of diagnoses and to ensure the practical applicability of our predictive models.
In refining our dataset, we filtered demographic and visitation records based on patient identifiers from
the diagnosis data. We merged these details, imputing missing visitation data with zeros and treating
age as a continuous variable. The comprehensive DataFrame was further merged with the PASC
diagnosis DataFrame, coding absent labels as zero to denote negative cases. Columns deemed non-
essential, such as dates, ICD-10 codes (retaining the descriptive code names), and others without
predictive value, were omitted. Column names were standardized by replacing unsupported characters,
and we ensured data type consistency by casting all features to integers.
Feature Engineering and Selection:
The class imbalance ratio of 600 negatives to 10 positives posed a significant challenge. To address this,
we initially employed LightGBM, a gradient-boosting framework, to assess feature importance. The top
500 features were selected based on their importance scores. A feature correlation analysis was then
conducted, removing features with a correlation above 0.7 to mitigate multicollinearity. The
StandardScaler was applied to normalize the feature space, crucial for models sensitive to feature
scaling, such as SVM.

Considering the substantial class imbalance, we adopted the ADASYN (Adaptive Synthetic Sampling
Approach) to enhance the representation of the minority class in the training set, thereby improving our
model&#39;s predictive performance across classes.
Model Development and Evaluation:
Our primary model, a LightGBM classifier, underwent hyperparameter tuning through Bayesian
Optimization over 50 iterations with 5-fold cross-validation, with model parameters carefully selected to
optimize performance. A suite of ensemble models, including Gradient Boosting, Bagging Classifiers,
Random Forest, and Logistic Regression, complemented our predictive framework. These models,
alongside the optimized LightGBM and SVM, were integrated as base learners into our Stacking
Classifier, with a Bagging Classifier serving as the meta-model.
The optimized LightGBM model and the Stacking Classifier were trained on the resampled training data.
Early stopping criteria based on validation set performance were implemented to prevent overfitting.
Model performance was evaluated using Accuracy, ROC AUC, and F1-score. Post-optimization, feature
importance was revisited, and a precision-recall trade-off was visualized through a curve, providing
insight into the balance between sensitivity and specificity achieved by our model.
