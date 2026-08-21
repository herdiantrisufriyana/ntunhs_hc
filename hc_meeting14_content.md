# Hands-on with Orange: Clinical Outcome Modeling and Evaluation

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Prerequisites](#prerequisites)
3. [Session 1: Lecture — Clinical Prediction Models (15 min)](#session-1-lecture--clinical-prediction-models-15-min)
4. [Session 2: Hands-on — Split Data for Development and Validation (10 min)](#session-2-hands-on--split-data-for-development-and-validation-10-min)
5. [Session 3: Hands-on — Train Clinical Prediction Models (20 min)](#session-3-hands-on--train-clinical-prediction-models-20-min)
6. [Session 4: Lecture — Model Evaluation in Clinical Context (15 min)](#session-4-lecture--model-evaluation-in-clinical-context-15-min)
7. [Session 5: Hands-on — Evaluate and Compare Models (20 min)](#session-5-hands-on--evaluate-and-compare-models-20-min)
8. [Session 6: Hands-on — Apply Model to Validation Set (15 min)](#session-6-hands-on--apply-model-to-validation-set-15-min)

---

## Subtopics

- Clinical prediction models and their role in hospital quality improvement
- Splitting data into development and validation sets
- Training logistic regression and decision tree models
- ROC curves, confusion matrices, and clinical interpretation of metrics
- Comparing models and selecting the best for a readmission alert system
- Validating the chosen model on held-out data and detecting overfitting

[Back to Table of Contents](#table-of-contents)

---

## Prerequisites

Continue from the Meeting 13 workflow. You should have a cleaned dataset loaded in Orange with the following variables:

- **Continuous**: age, charlson_score, length_of_stay, wbc, hemoglobin, creatinine, hba1c, systolic_bp, heart_rate, n_medications
- **Categorical**: sex, admission_type, icu_stay, plus any derived flags from Meeting 13
- **Target**: readmission_30d (binary: 0 = not readmitted, 1 = readmitted within 30 days)

Ensure your data has been through the preprocessing steps from Meeting 13 (missing value handling, feature selection, association testing). The output of your cleaned pipeline should be connected to the starting point of this session.

No additional add-ons are required beyond the default Orange installation.

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — Clinical Prediction Models (15 min)

- A **clinical prediction model** is a mathematical tool that estimates the probability of a patient outcome based on measured characteristics
- It produces a **risk score** for each patient — for example, "this patient has a 35% chance of being readmitted within 30 days"
- Prediction models are used across hospital medicine:
  - **APACHE** (Acute Physiology and Chronic Health Evaluation) — predicts ICU mortality based on physiologic variables, age, and chronic health conditions
  - **LACE index** — predicts 30-day readmission using Length of stay, Acuity of admission, Charlson comorbidity score, and Emergency department visits
  - **SOFA score** — predicts sepsis-related organ dysfunction in critical care
- Why do we need prediction models in hospitals?
  - **Identify high-risk patients early** — allocate resources (e.g., discharge planning, follow-up calls) to those most likely to be readmitted
  - **Standardize clinical judgment** — reduce variability in how clinicians assess risk
  - **Measure hospital quality** — readmission rates are a key performance indicator; prediction models help distinguish expected vs. unexpected readmissions
- Why split data into **development** and **validation** sets?
  - A model trained on data will always perform well on that same data — this is called **overfitting**
  - Overfitting means the model memorizes noise in the training data instead of learning true patterns
  - To know if the model generalizes, we must test it on data it has never seen (the validation set)
  - Typical split: 70% development (training), 30% validation (testing)
- What metrics matter clinically?
  - **Sensitivity** (recall) — proportion of truly readmitted patients the model correctly identifies. High sensitivity = catch most at-risk patients
  - **Specificity** — proportion of truly non-readmitted patients the model correctly identifies. High specificity = avoid false alarms
  - **AUC** (Area Under the ROC Curve) — overall measure of how well the model discriminates between readmitted and not readmitted, across all possible thresholds

*If you were a hospital administrator, would you prefer a model with high sensitivity or high specificity for a readmission alert system? Why?*

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Hands-on — Split Data for Development and Validation (10 min)

**Widgets:** Data Sampler, Data Table (x2)

1. Connect your cleaned data output (from Meeting 13) to **Data Sampler**
   - Set sampling type to **Fixed proportion of data**
   - Set the proportion to **70%**
   - Check **Stratify sample by: readmission_30d** — this ensures both the development and validation sets have the same proportion of readmitted patients
   - Check **Replicable sampling** so results are reproducible

2. Connect **Data Sampler** → **Data Table** (label it "Development Set") via **Data Sample → Data**
   - This output contains the 70% development (training) data

3. Connect **Data Sampler** → **Data Table** (label it "Validation Set") via **Remaining Data → Data**
   - This output contains the 30% validation (held-out) data

**Check these:**
- Open both Data Tables and verify the sizes (approximately 70/30 split)
- Check the distribution of **readmission_30d** in both tables — the proportions should be similar (stratification worked)
- Note the exact counts: how many readmitted vs. not readmitted in each set?

> **Recall Meeting 13:** You tested associations between variables and readmission_30d. The variables that showed significant associations are the ones most likely to be useful predictors in the models we are about to build.

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Hands-on — Train Clinical Prediction Models (20 min)

**Widgets:** Logistic Regression, Tree, Test and Score

**Set up learners:**

4. Drag a **Logistic Regression** widget onto the canvas
   - Use default settings (regularization type: Ridge L2)
   - Logistic regression is the standard approach for clinical prediction — it estimates the probability of the outcome as a weighted sum of predictors, passed through a sigmoid function

5. Drag a **Tree** widget onto the canvas
   - Use default settings (or set max depth to 5 to prevent overly complex trees)
   - Decision trees split patients into groups based on threshold values (e.g., "if charlson_score > 3 and length_of_stay > 7 days, then high risk")

**Connect to Test and Score:**

6. Connect **Data Sampler** → **Test and Score** via **Data Sample → Data**
   - This sends the 70% development set for model training and evaluation

7. Connect **Logistic Regression** → **Test and Score** via **Learner → Learner**

8. Connect **Tree** → **Test and Score** via **Learner → Learner**

**Configure Test and Score:**

9. Open **Test and Score** and set:
   - Evaluation method: **Cross validation**
   - Number of folds: **5**
   - Target class: **readmission_30d = 1** (we care about predicting readmission)

**Read the results table:**

| Metric | What it means clinically |
|--------|------------------------|
| AUC | Overall discrimination — can the model tell apart readmitted vs. not readmitted patients? |
| CA | Classification accuracy — what fraction of all predictions are correct? |
| F1 | Harmonic mean of precision and recall — balances false positives and false negatives |
| Precision | Of patients flagged as high risk, what fraction was actually readmitted? |
| Recall | Of patients who were actually readmitted, what fraction did the model catch? |

**Check these:**
- Which model has a higher AUC?
- Which model has higher Recall (sensitivity)? This matters for a screening system
- Is there a large gap between Precision and Recall for either model? (A large gap suggests the model favors one type of error over the other)

> **Recall Meeting 13:** The comparative analysis you performed (t-tests, chi-square tests) identified which variables differ between readmitted and non-readmitted groups. The prediction models now use all variables together to produce a combined risk estimate.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Lecture — Model Evaluation in Clinical Context (15 min)

- **ROC curve** (Receiver Operating Characteristic):
  - X-axis = False Positive Rate (1 - Specificity) — proportion of non-readmitted patients incorrectly flagged
  - Y-axis = True Positive Rate (Sensitivity) — proportion of readmitted patients correctly identified
  - The curve shows model performance at every possible classification threshold
  - A model that performs no better than random guessing follows the diagonal line (AUC = 0.5)
  - The further the curve bows toward the upper-left corner, the better the model discriminates
  - **AUC interpretation**:
    - 0.5 = random (no discrimination)
    - 0.6-0.7 = poor
    - 0.7-0.8 = acceptable
    - 0.8-0.9 = good
    - 0.9+ = excellent (rare in clinical practice)

- **Sensitivity vs. specificity tradeoff**:
  - Moving the classification threshold changes the balance between sensitivity and specificity
  - For a **readmission screening program**, high sensitivity is typically preferred — you want to catch most patients at risk, even if it means some false positives
  - A false negative (missing a patient who will be readmitted) has greater clinical cost: the patient may not receive discharge planning, follow-up, or transitional care
  - A false positive (flagging a patient who will not be readmitted) has lower cost: an unnecessary follow-up call is a minor resource expenditure
  - The optimal threshold depends on hospital resources and the clinical intervention being triggered

- **Confusion matrix** in clinical terms:

| | Predicted: Readmitted | Predicted: Not Readmitted |
|---|---|---|
| **Actual: Readmitted** | True Positive (TP) — correctly identified at-risk patient | False Negative (FN) — missed at-risk patient |
| **Actual: Not Readmitted** | False Positive (FP) — unnecessary alert | True Negative (TN) — correctly identified low-risk patient |

- From the confusion matrix:
  - Sensitivity = TP / (TP + FN) — "Of all readmitted patients, how many did we catch?"
  - Specificity = TN / (TN + FP) — "Of all non-readmitted patients, how many did we correctly clear?"
  - Positive Predictive Value = TP / (TP + FP) — "When we flag someone, how often are we right?"

*A hospital readmission alert system flags 100 patients. 30 are actually readmitted (TP = 30), 70 are not (FP = 70). Meanwhile, 10 readmitted patients were missed (FN = 10). What is the sensitivity? What is the positive predictive value? Is this system clinically useful?*

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Hands-on — Evaluate and Compare Models (20 min)

**Widgets:** ROC Analysis, Confusion Matrix

**Visualize ROC curves:**

10. Connect **Test and Score** → **ROC Analysis** via **Evaluation Results → Evaluation Results**
    - The ROC Analysis widget displays the ROC curve for each model (Logistic Regression and Tree) on the same plot
    - The model with the curve closer to the upper-left corner has better discrimination
    - Compare the AUC values shown in the legend

**Examine confusion matrices:**

11. Open the **Test and Score** widget and click on each model row
    - The confusion matrix is displayed at the bottom of the Test and Score widget
    - For each model, note the values of TP, FP, TN, FN

**Clinical interpretation exercise:**

For each model, calculate:
- Sensitivity = TP / (TP + FN)
- Specificity = TN / (TN + FP)
- Positive Predictive Value = TP / (TP + FP)

**Discussion — Which model for a hospital readmission alert system?**

Consider these factors:
- Which model has higher sensitivity? (Important: we want to catch at-risk patients)
- Which model has higher AUC? (Overall discrimination ability)
- Which model is more interpretable? (Clinicians need to trust and understand the model)
  - Logistic regression provides coefficients — "each additional point on charlson_score increases readmission odds by X%"
  - Decision tree provides visual rules — "if length_of_stay > 7 and icu_stay = yes, then high risk"
- For a bedside clinical decision support tool, interpretability may matter as much as raw performance

> **Recall Meeting 13:** The associations you found (e.g., higher charlson_score in readmitted patients) now appear as the driving factors in these prediction models. The logistic regression coefficients quantify the strength of each association after adjusting for all other variables simultaneously.

**Check these:**
- Do both ROC curves clearly separate from the diagonal (random) line?
- Which model has more false negatives (missed readmissions)?
- If you had to choose one model for clinical deployment, which would you choose and why?

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Hands-on — Apply Model to Validation Set (15 min)

**Widgets:** Predictions, Data Table

**Apply the chosen model to unseen data:**

12. Identify which learner performed better in Session 3 and Session 5 — use that learner for this step

13. Connect the chosen learner (e.g., **Logistic Regression**) → **Predictions** via **Learner → Predictors** (note: this sends the untrained learner; it will be trained on the data provided)

14. Connect **Data Sampler** → **Predictions** via **Data Sample → Data**
    - This sends the development set as training data — the learner trains on this data

15. Connect **Data Sampler** → **Predictions** via **Remaining Data → Data**
    - Alternative approach: connect **Remaining Data** to a separate **Test and Score** widget along with the learner, using "Test on test data" mode
    - For direct inspection: connect the chosen learner and both Data Sampler outputs to **Predictions**

**Inspect predictions on the validation set:**

16. Connect **Predictions** → **Data Table** (label it "Validation Predictions") via **Predictions → Data**
    - Each row shows the patient's actual readmission_30d value and the model's predicted probability
    - Sort by predicted probability (descending) — the patients at the top are those the model considers highest risk

**Compare development vs. validation performance:**

17. To get a formal AUC on the validation set, set up a second **Test and Score** widget:
    - Connect the chosen learner → **Test and Score** (label it "Validation Evaluation") via **Learner → Learner**
    - Connect **Data Sampler** → **Validation Evaluation** via **Data Sample → Data** (training data)
    - Connect **Data Sampler** → **Validation Evaluation** via **Remaining Data → Test Data**
    - Set evaluation method to **Test on test data**
    - Compare the validation AUC to the cross-validated development AUC from Session 3

**Interpreting the AUC comparison:**

| Scenario | Development AUC vs. Validation AUC | What it means |
|----------|--------------------------------------|---------------|
| Small drop (< 0.05) | Model generalizes well — safe to deploy |
| Moderate drop (0.05-0.10) | Some overfitting — consider simplifying the model or adding more data |
| Large drop (> 0.10) | Substantial overfitting — the model learned noise, not true patterns. Rebuild with fewer variables or stronger regularization |

**Discussion — Clinical deployment considerations:**

- If the validation AUC is acceptable (> 0.7), the model could be embedded in the hospital electronic health record (EHR) as a readmission risk alert
- The alert could trigger automatic referral to a transitional care program for patients above a risk threshold
- Regular model recalibration is needed as patient populations and care practices change over time
- A prediction model is a support tool, not a replacement for clinical judgment

> **Recall Meeting 13:** The entire pipeline from data cleaning (missing values, outlier handling) through comparative analysis (identifying significant predictors) to prediction modeling (this session) mirrors the real-world workflow of developing a clinical decision support tool. Each step builds on the previous one.

**Check these:**
- How much did the AUC drop from development to validation?
- Look at the patients with the highest predicted probabilities — do they have the clinical characteristics you would expect (high charlson_score, long length_of_stay, ICU stay)?
- Would you feel confident deploying this model in a hospital? What additional steps would you want before clinical use?

[Back to Table of Contents](#table-of-contents)
