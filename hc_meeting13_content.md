# Hands-on with Orange: Comparative Analysis and Hypothesis Testing

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Prerequisites](#prerequisites)
3. [Session 1: Lecture — From Clinical Questions to Testable Comparisons (10 min)](#session-1-lecture--from-clinical-questions-to-testable-comparisons-10-min)
4. [Session 2: Hands-on — Define Comparison Groups with Select Rows (15 min)](#session-2-hands-on--define-comparison-groups-with-select-rows-15-min)
5. [Session 3: Hands-on — Visual Group Comparisons (10 min)](#session-3-hands-on--visual-group-comparisons-10-min)
6. [Session 4: Lecture — Logistic Regression for Association Testing (15 min)](#session-4-lecture--logistic-regression-for-association-testing-15-min)
7. [Session 5: Hands-on — Test Associations with Logistic Regression (20 min)](#session-5-hands-on--test-associations-with-logistic-regression-20-min)
8. [Session 6: Lecture — Stratified Analysis (10 min)](#session-6-lecture--stratified-analysis-10-min)
9. [Session 7: Hands-on — Stratified Comparison (15 min)](#session-7-hands-on--stratified-comparison-15-min)

---

## Subtopics

- Translating clinical questions into group comparisons
- Defining comparison groups using Select Rows
- Visual inspection of group differences with Box Plot and Distributions
- Logistic regression for binary outcomes (odds ratios and confidence intervals)
- Unadjusted vs adjusted models for confounder control
- Stratified analysis and Simpson's paradox

[Back to Table of Contents](#table-of-contents)

---

## Prerequisites

This hands-on continues from **Meeting 12**. Open your Meeting 12 workflow — you will need:

- The **cleaned hospital discharge dataset** with all derived flags (diabetes_flag, leukocytosis, anemia, hypertension, age_group, high_comorbidity)
- Variables available: age, sex, admission_type, charlson_score, length_of_stay, wbc, hemoglobin, creatinine, hba1c, systolic_bp, heart_rate, n_medications, icu_stay, readmission_30d (Target)
- Your Meeting 12 visualizations should already confirm key distributional patterns across these variables

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — From Clinical Questions to Testable Comparisons (10 min)

Clinical research often starts with a question like "Does X affect Y?" To answer this rigorously, we must translate the question into a formal group comparison with a clearly defined exposure, outcome, and comparison structure.

**Translating clinical questions into testable comparisons:**

| Clinical question | Exposure (group variable) | Outcome (what to compare) | Comparison |
|-------------------|--------------------------|---------------------------|------------|
| Do patients with high comorbidity have higher readmission rates? | high_comorbidity (Yes vs No) | readmission_30d | Proportion of readmission in each group |
| Does ICU stay increase readmission risk? | icu_stay (Yes vs No) | readmission_30d | Proportion of readmission in each group |
| Do emergency admissions have longer hospital stays? | admission_type (Emergency vs Elective) | length_of_stay | Mean/median length_of_stay in each group |

**The comparison structure:**

1. **Define the exposure variable** — the grouping factor (binary or categorical)
2. **Define the outcome variable** — what you measure in each group
3. **Identify potential confounders** — variables that differ between groups AND affect the outcome independently
4. **Compare** — first visually (box plots, distributions), then formally (logistic regression)

**Example walkthrough:** "Do patients with high comorbidity have higher readmission rates?"

- Split patients into two groups: high_comorbidity = Yes vs high_comorbidity = No
- Count readmission_30d = Yes in each group
- If 25% of the high-comorbidity group is readmitted vs 12% of the low-comorbidity group, is this difference real or due to confounding (e.g., high-comorbidity patients are also older)?

> **Recall Meeting 07:** Confounding occurs when a third variable is associated with both the exposure and the outcome, creating a spurious or inflated association. For example, age may confound the comorbidity-readmission relationship if older patients both have higher comorbidity scores and are more likely to be readmitted. To isolate the effect of comorbidity, we must adjust for confounders — either by stratification or by including them as covariates in a regression model.

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Hands-on — Define Comparison Groups with Select Rows (15 min)

**Widgets:** Select Rows (x3), Data Table (x6)

This step creates three comparison groups from the cleaned dataset. Each **Select Rows** widget splits the data into two groups via its **Matching Data** and **Remaining Data** outputs.

**2a. High vs low comorbidity**

1. Connect your cleaned data (from Meeting 12) → **Select Rows** (label it "High vs Low Comorbidity") via **Data**
   - Add condition: **high_comorbidity** is equal to **Yes**
2. Connect **High vs Low Comorbidity** → **Data Table** (label it "High Comorbidity Group") via **Matching Data → Data**
3. Connect **High vs Low Comorbidity** → **Data Table** (label it "Low Comorbidity Group") via **Remaining Data → Data**
4. Open both Data Tables — note the group sizes (number of rows in each)

**2b. ICU vs non-ICU**

5. Connect your cleaned data → **Select Rows** (label it "ICU vs Non-ICU") via **Data**
   - Add condition: **icu_stay** is equal to **Yes**
6. Connect **ICU vs Non-ICU** → **Data Table** (label it "ICU Group") via **Matching Data → Data**
7. Connect **ICU vs Non-ICU** → **Data Table** (label it "Non-ICU Group") via **Remaining Data → Data**
8. Open both Data Tables — note the group sizes

**2c. Emergency vs elective admission**

9. Connect your cleaned data → **Select Rows** (label it "Emergency vs Elective") via **Data**
   - Add condition: **admission_type** is equal to **Emergency**
10. Connect **Emergency vs Elective** → **Data Table** (label it "Emergency Group") via **Matching Data → Data**
11. Connect **Emergency vs Elective** → **Data Table** (label it "Elective Group") via **Remaining Data → Data**
12. Open both Data Tables — note the group sizes

**Check these:**
- Are the group sizes reasonably balanced? Very unequal groups (e.g., 95% vs 5%) may limit statistical power
- Do the group definitions match your clinical expectations? (e.g., high_comorbidity = Yes should correspond to patients with charlson_score above the threshold set in Meeting 12)

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Hands-on — Visual Group Comparisons (10 min)

**Widgets:** Box Plot (x3), Distributions (x3)

Before running any formal test, visually inspect the differences between groups. This step connects each comparison group's Data Tables to Box Plot and Distributions widgets.

**3a. Compare lab values and length_of_stay between high vs low comorbidity**

1. Connect **High Comorbidity Group** → **Box Plot** (label it "Box Plot: Comorbidity Comparison") via **Data**
   - Select variable: **length_of_stay**
   - Group by: leave ungrouped (this shows a single group)
2. To see both groups side by side, connect your cleaned data → **Box Plot** directly via **Data**
   - Select variable: **length_of_stay**
   - Set **Subgroups** to **high_comorbidity**
   - This shows two side-by-side box plots: Yes vs No
3. Connect your cleaned data → **Distributions** (label it "Distributions: Comorbidity") via **Data**
   - Select variable: **readmission_30d**
   - Set **Split by** to **high_comorbidity**
   - Compare the proportion of readmission_30d = Yes in each group

**3b. Compare ICU vs non-ICU**

4. Connect your cleaned data → **Box Plot** (label it "Box Plot: ICU Comparison") via **Data**
   - Select variable: **length_of_stay**
   - Set **Subgroups** to **icu_stay**
5. Connect your cleaned data → **Distributions** (label it "Distributions: ICU") via **Data**
   - Select variable: **readmission_30d**
   - Set **Split by** to **icu_stay**

**3c. Compare emergency vs elective**

6. Connect your cleaned data → **Box Plot** (label it "Box Plot: Admission Type") via **Data**
   - Select variable: **length_of_stay**
   - Set **Subgroups** to **admission_type**
7. Connect your cleaned data → **Distributions** (label it "Distributions: Admission Type") via **Data**
   - Select variable: **readmission_30d**
   - Set **Split by** to **admission_type**

**Check these:**
- Do the box plots show a visible difference in length_of_stay between groups?
- In the Distributions widget, is the proportion of readmission_30d = Yes higher in one group?
- Which comparison shows the largest visual difference?

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Lecture — Logistic Regression for Association Testing (15 min)

Visual comparisons reveal patterns, but they cannot tell us whether a difference is statistically meaningful or whether it persists after accounting for confounders. Logistic regression formalizes the comparison.

**Why logistic regression?**

- The outcome variable **readmission_30d** is **binary** (Yes/No)
- Logistic regression models the probability of the outcome as a function of one or more predictors
- It outputs **coefficients** on the log-odds scale — exponentiate to get the **odds ratio (OR)**

**Interpreting the odds ratio (OR):**

| OR value | Interpretation |
|----------|---------------|
| OR = 1.0 | No association — the exposure does not change the odds of the outcome |
| OR > 1.0 | Higher odds — the exposure is associated with increased odds of the outcome |
| OR < 1.0 | Lower odds — the exposure is associated with decreased odds of the outcome |
| OR = 2.5 | Patients with the exposure have 2.5 times the odds of the outcome compared to those without |
| OR = 0.4 | Patients with the exposure have 60% lower odds of the outcome |

**Confidence intervals (CI):**

- The 95% CI gives a range of plausible values for the true OR
- If the 95% CI **crosses 1.0** (e.g., OR = 1.3, 95% CI: 0.8 to 2.1), the association is **not statistically significant** at the 0.05 level
- If the 95% CI **does not cross 1.0** (e.g., OR = 2.1, 95% CI: 1.4 to 3.2), the association **is statistically significant**

**Adjusting for confounders:**

- **Unadjusted model**: readmission_30d ~ high_comorbidity (one predictor)
- **Adjusted model**: readmission_30d ~ high_comorbidity + age + sex + charlson_score (multiple predictors)
- The adjusted OR for high_comorbidity tells you the association **after removing the influence** of age, sex, and charlson_score
- If the OR changes substantially after adjustment, confounding was present

> **Recall Meeting 07:** Confounding by indication is especially relevant in clinical data. For example, patients who receive aggressive treatment may appear to have worse outcomes — not because the treatment is harmful, but because sicker patients are more likely to receive it. Including severity indicators (like charlson_score) as covariates helps disentangle the treatment effect from severity.

**Computing OR from Orange output:**

- Orange's **Logistic Regression** widget outputs a **Coefficients** table
- Each row shows a variable name and its coefficient (log-odds)
- To get the OR: **OR = exp(coefficient)**
- Example: coefficient = 0.916 means OR = exp(0.916) = 2.50
- Example: coefficient = -0.357 means OR = exp(-0.357) = 0.70

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Hands-on — Test Associations with Logistic Regression (20 min)

**Widgets:** Select Columns (x2), Logistic Regression (x2), Data Table (x4), Formula (x2), Test and Score

This step builds two logistic regression models: one unadjusted (single predictor) and one adjusted (with covariates).

**5a. Unadjusted model (one predictor)**

1. Connect your cleaned data → **Select Columns** (label it "Select: Unadjusted Variables") via **Data**
   - Move **high_comorbidity** to **Features**
   - Move **readmission_30d** to **Target**
   - Move all other variables to **Meta**
2. Drag **Logistic Regression** (label it "Unadjusted Logistic Regression") onto the canvas
   - Set **Regularization** to **No regularization**
3. Connect **Select: Unadjusted Variables** → **Unadjusted Logistic Regression** via **Data**
4. Connect **Unadjusted Logistic Regression** → **Data Table** (label it "Unadjusted Coefficients") via **Coefficients → Data**
5. Connect **Unadjusted Coefficients** → **Formula** (label it "Compute Unadjusted OR") via **Selected Data → Data**
   - Add new feature: **OR = exp(coefficient)**
   - This adds an OR column to the coefficients table
6. Connect **Compute Unadjusted OR** → **Data Table** (label it "Unadjusted OR Table") via **Data**
7. Read the table:
   - Find the row for **high_comorbidity**
   - Note the **coefficient** and the computed **OR**
   - OR > 1 means high comorbidity is associated with higher odds of readmission

**5b. Adjusted model (add covariates)**

8. Connect your cleaned data → **Select Columns** (label it "Select: Adjusted Variables") via **Data**
   - Move **high_comorbidity**, **age**, **sex**, **charlson_score** to **Features**
   - Move **readmission_30d** to **Target**
   - Move all other variables to **Meta**
9. Drag **Logistic Regression** (label it "Adjusted Logistic Regression") onto the canvas
   - Set **Regularization** to **No regularization**
10. Connect **Select: Adjusted Variables** → **Adjusted Logistic Regression** via **Data**
11. Connect **Adjusted Logistic Regression** → **Data Table** (label it "Adjusted Coefficients") via **Coefficients → Data**
12. Connect **Adjusted Coefficients** → **Formula** (label it "Compute Adjusted OR") via **Selected Data → Data**
    - Add new feature: **OR = exp(coefficient)**
13. Connect **Compute Adjusted OR** → **Data Table** (label it "Adjusted OR Table") via **Data**
14. Read the table:
    - Find the row for **high_comorbidity**
    - Note the **coefficient** and the computed **OR**
    - Compare with the unadjusted OR from step 7

**5c. Evaluate model performance**

15. Connect **Select: Adjusted Variables** → **Test and Score** via **Data**
    - Set evaluation to **Cross Validation**, number of folds: **5**
16. Connect **Adjusted Logistic Regression** → **Test and Score** via **Learner**
17. Note the **AUC** and **CA** (classification accuracy) — this tells you how well the model predicts readmission overall

**5d. Compare unadjusted vs adjusted OR**

18. Compare the OR for **high_comorbidity** across both models:

| Comparison | What it means |
|------------|---------------|
| OR barely changed | Confounders (age, sex, charlson_score) did not explain the association |
| OR moved closer to 1.0 | Part of the association was due to confounding |
| OR moved further from 1.0 | Confounders were masking a stronger effect |
| OR crossed 1.0 (changed direction) | Simpson's paradox — the crude association is misleading |

> **Recall Meeting 04:** Descriptive statistics (means, medians, proportions) summarize each group, but they do not account for confounding. Logistic regression extends descriptive comparison by holding confounders constant, isolating the independent effect of the exposure on the outcome.

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Lecture — Stratified Analysis (10 min)

Adjusting for confounders in a regression model assumes the association is the same across all subgroups. But what if the association differs by subgroup? This is called **effect modification** (or interaction), and stratified analysis reveals it.

**Why stratify?**

- A regression model gives one overall OR — but the OR might differ between elderly and young patients
- Stratification splits the data into subgroups and runs the same analysis within each
- If results are consistent across strata, the overall OR is a fair summary
- If results differ across strata, the overall OR is misleading

**Simpson's paradox:**

- An association that appears in the overall data can **reverse** within subgroups
- Classic example: a treatment appears harmful overall, but is beneficial within every age group — because older (sicker) patients were more likely to receive the treatment
- Stratified analysis is the only way to detect this

**When to stratify:**

| Situation | Action |
|-----------|--------|
| You suspect the association differs by subgroup | Stratify by that variable |
| A confounder has a strong effect on both exposure and outcome | Stratify to check consistency |
| The overall OR seems surprising or counterintuitive | Stratify to check for Simpson's paradox |

**In this session:**

- We will stratify by **age_group** (e.g., elderly vs young)
- Run the same comparison (ICU vs readmission, or comorbidity vs readmission) within each age stratum
- Check whether the association is consistent or modified by age

[Back to Table of Contents](#table-of-contents)

---

## Session 7: Hands-on — Stratified Comparison (15 min)

**Widgets:** Select Rows (x2), Box Plot (x2), Select Columns (x2), Logistic Regression (x2), Data Table (x2), Formula (x2)

This step splits the data by age_group and repeats the visual and logistic regression analysis within each stratum.

**7a. Split data by age group**

1. Connect your cleaned data → **Select Rows** (label it "Select Elderly Patients") via **Data**
   - Add condition: **age_group** is equal to **Elderly**
2. Connect your cleaned data → **Select Rows** (label it "Select Young Patients") via **Data**
   - Add condition: **age_group** is not equal to **Elderly**
   - (This captures all non-elderly patients)

**7b. Visual comparison within each stratum**

3. Connect **Select Elderly Patients** → **Box Plot** (label it "Box Plot: Elderly") via **Matching Data → Data**
   - Select variable: **length_of_stay**
   - Set **Subgroups** to **icu_stay**
4. Connect **Select Young Patients** → **Box Plot** (label it "Box Plot: Young") via **Matching Data → Data**
   - Select variable: **length_of_stay**
   - Set **Subgroups** to **icu_stay**
5. Compare the two Box Plots:
   - Is the ICU vs non-ICU difference in length_of_stay similar in both age groups?
   - Or is the difference larger in one group?

**7c. Logistic regression within each stratum**

6. Connect **Select Elderly Patients** → **Select Columns** (label it "Select Variables: Elderly") via **Matching Data → Data**
   - Move **icu_stay** to **Features**
   - Move **readmission_30d** to **Target**
   - Move all other variables to **Meta**
7. Drag **Logistic Regression** (label it "LR: Elderly Stratum") onto the canvas
   - Set **Regularization** to **No regularization**
8. Connect **Select Variables: Elderly** → **LR: Elderly Stratum** via **Data**
9. Connect **LR: Elderly Stratum** → **Data Table** (label it "Coefficients: Elderly") via **Coefficients → Data**
10. Connect **Coefficients: Elderly** → **Formula** (label it "OR: Elderly") via **Selected Data → Data**
    - Add new feature: **OR = exp(coefficient)**
11. Connect **Select Young Patients** → **Select Columns** (label it "Select Variables: Young") via **Matching Data → Data**
    - Move **icu_stay** to **Features**
    - Move **readmission_30d** to **Target**
    - Move all other variables to **Meta**
12. Drag **Logistic Regression** (label it "LR: Young Stratum") onto the canvas
    - Set **Regularization** to **No regularization**
13. Connect **Select Variables: Young** → **LR: Young Stratum** via **Data**
14. Connect **LR: Young Stratum** → **Data Table** (label it "Coefficients: Young") via **Coefficients → Data**
15. Connect **Coefficients: Young** → **Formula** (label it "OR: Young") via **Selected Data → Data**
    - Add new feature: **OR = exp(coefficient)**

**7d. Compare stratum-specific ORs**

16. Open both Formula outputs and compare the OR for **icu_stay** across strata:

| Stratum | OR for icu_stay | Interpretation |
|---------|----------------|----------------|
| Elderly | ? | ICU effect on readmission in elderly patients |
| Young | ? | ICU effect on readmission in young patients |

17. Discuss the results:

| Finding | Interpretation |
|---------|---------------|
| OR similar in both strata | Association is consistent — age does not modify the effect |
| OR larger in one stratum | Effect modification — the ICU-readmission association depends on age |
| OR in opposite directions | Simpson's paradox — the overall association is misleading |

> **Recall Meeting 07:** Simpson's paradox occurs when an association reverses after stratification. If ICU stay appears to increase readmission overall, but decreases readmission within each age group, the paradox arises because elderly patients are more likely to be in the ICU AND more likely to be readmitted — age confounds both. Stratified analysis makes this visible, and including age as a covariate in the adjusted model (Session 5b) addresses it in regression.

[Back to Table of Contents](#table-of-contents)
