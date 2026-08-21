# Hands-on with Orange: Loading and Exploring Clinical Datasets

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Session 1: Lecture — What is Orange? (10 min)](#session-1-lecture--what-is-orange-10-min)
3. [Session 2: Hands-on — Load the Hospital Discharge Dataset (15 min)](#session-2-hands-on--load-the-hospital-discharge-dataset-15-min)
4. [Session 3: Lecture — Variable Roles in Clinical Data (10 min)](#session-3-lecture--variable-roles-in-clinical-data-10-min)
5. [Session 4: Hands-on — Set Variable Roles and Types (15 min)](#session-4-hands-on--set-variable-roles-and-types-15-min)
6. [Session 5: Hands-on — Explore with Data Table and Column Statistics (15 min)](#session-5-hands-on--explore-with-data-table-and-column-statistics-15-min)
7. [Session 6: Lecture — Identifying Data Quality Issues (10 min)](#session-6-lecture--identifying-data-quality-issues-10-min)
8. [Session 7: Hands-on — Recode Variables with Edit Domain (15 min)](#session-7-hands-on--recode-variables-with-edit-domain-15-min)

---

## Subtopics

- Introduction to Orange visual programming for clinical data analysis
- Loading and inspecting a hospital discharge CSV dataset
- Understanding variable roles: Target, Feature, and Meta
- Setting correct variable types for clinical variables
- Exploring data with Data Table and Column Statistics widgets
- Identifying data quality issues in clinical datasets
- Recoding and renaming variables with Edit Domain

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — What is Orange? (10 min)

**Orange** is a visual programming tool for data mining and machine learning. Instead of writing code, you build analysis workflows by connecting widgets on a canvas.

**Core concepts:**

- **Widget** — a single analysis block that performs one task (load data, visualize, filter, etc.)
- **Connection** — a line between two widgets that passes data from one to the next
- **Canvas** — the workspace where you drag, drop, and connect widgets
- **Workflow** — the complete chain of connected widgets that defines your analysis pipeline

**How it works:**

1. Drag a widget from the toolbox onto the canvas
2. Configure the widget (set parameters, select columns, etc.)
3. Connect widgets by drawing a line from one widget's output to another widget's input
4. Data flows through the connections automatically — change something upstream and everything downstream updates

**Why Orange for clinical data?**

- No programming required — accessible for clinicians and health sciences students
- Interactive — see results immediately as you build the workflow
- Reproducible — save the workflow file (.ows) and re-run it with new data
- Extensible — add-ons for text mining, network analysis, bioinformatics, and more

**Install:** https://orangedatamining.com/download/

> **Note:** Download the latest stable version for your operating system. Orange runs on Windows, macOS, and Linux. Installation includes all built-in widgets used in this course.

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Hands-on — Load the Hospital Discharge Dataset (15 min)

**Widgets:** File

In this session, you will load the hospital discharge dataset (CSV) into Orange and take a first look at its contents.

**Step-by-step:**

1. Open Orange and create a new workflow (File > New)
2. In the left toolbox, find the **Data** category
3. Drag the **File** widget onto the canvas
4. Double-click the **File** widget to open it
5. Click the folder icon (or **Browse**) and navigate to the hospital discharge CSV file
6. Select the file and click **Open**

**What you should see in the File widget:**

- The widget displays a summary at the top: number of rows (patients) and columns (variables)
- Below the summary, you see a table listing each variable with its:
  - **Name** — the column header from the CSV
  - **Type** — how Orange interpreted the variable (numeric, categorical, text, etc.)
  - **Role** — how the variable will be used in analysis (Feature, Target, Meta, or Skip)
- The data preview shows the first several rows of the dataset

**Variables in this dataset:**

| Variable | Description | Expected Type |
|----------|-------------|---------------|
| patient_id | Unique patient identifier | Text |
| age | Patient age in years | Numeric |
| sex | M or F | Categorical |
| admission_type | Emergency, Elective, or Urgent | Categorical |
| primary_dx_icd | ICD-10 diagnosis code (e.g., E11.65, I50.9) | Text |
| charlson_score | Comorbidity burden (0-15) | Numeric |
| length_of_stay | Hospital days | Numeric |
| wbc | White blood cell count (10^3/uL) | Numeric |
| hemoglobin | Hemoglobin level (g/dL) | Numeric |
| creatinine | Serum creatinine (mg/dL) | Numeric |
| hba1c | Glycated hemoglobin (%) | Numeric |
| systolic_bp | Systolic blood pressure (mmHg) | Numeric |
| heart_rate | Heart rate (bpm) | Numeric |
| n_medications | Number of discharge medications | Numeric |
| icu_stay | Whether the patient had an ICU stay (Yes/No) | Categorical |
| readmission_30d | Readmitted within 30 days (Yes/No) | Categorical |

**Check these before proceeding:**

- Does the row count match the expected number of patients?
- Are all 16 columns present?
- Did Orange auto-detect variable types correctly? (It often misclassifies categorical variables with only two values as numeric, and text columns as categorical.)

> **Note:** Orange auto-detects types based on the data values. If a column contains only numbers, Orange will default to numeric even if it should be categorical (e.g., a column coded as 0/1 for Yes/No). You will fix these in Session 4.

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Lecture — Variable Roles in Clinical Data (10 min)

Every variable in a clinical dataset plays a specific role in analysis. Orange uses three roles:

**Target**

- The outcome you are trying to predict or explain
- In this dataset: **readmission_30d** (whether the patient was readmitted within 30 days)
- Only one variable should be the Target
- Orange uses the Target variable to color plots, stratify results, and define the prediction task

**Feature**

- Variables used as predictors or descriptors in the analysis
- In this dataset: age, sex, admission_type, charlson_score, length_of_stay, wbc, hemoglobin, creatinine, hba1c, systolic_bp, heart_rate, n_medications, icu_stay
- Features are included in statistical models, visualizations, and clustering

**Meta**

- Variables carried along for reference but excluded from analysis
- In this dataset: **patient_id** and **primary_dx_icd**
- Meta variables appear in data tables but are not used in calculations

**Why patient_id must be Meta:**

- patient_id is a unique identifier with no clinical meaning
- If left as a Feature, Orange would treat it as a predictor — each patient would get a unique "score" that perfectly separates individuals but has zero generalizability
- This is a common data leakage mistake in clinical modeling

**Why primary_dx_icd should be Meta (for now):**

- ICD-10 codes are text strings (e.g., E11.65 = Type 2 diabetes with hyperglycemia)
- Orange cannot use raw text as a numeric or categorical feature without preprocessing
- Keeping it as Meta preserves the information for reference and later text-based analysis

**Why readmission_30d is the Target:**

- 30-day readmission is a standard hospital quality metric
- It is a binary outcome (Yes/No), making it suitable for classification
- Understanding which patients are at risk of readmission helps target discharge planning interventions

> **Tip:** Setting roles correctly is one of the most important steps in any clinical data analysis. An incorrectly assigned role (e.g., patient_id as Feature, or the outcome left as Feature) will silently produce misleading results.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Hands-on — Set Variable Roles and Types (15 min)

**Widgets:** File

Return to the **File** widget you configured in Session 2. You will now correct the variable types and assign roles.

**Step-by-step:**

1. Double-click the **File** widget to reopen it
2. In the variable table, locate each variable and adjust its **Type** and **Role** as follows:

**Setting variable types:**

For each variable, click the **Type** dropdown and set:

| Variable | Set Type To | Why |
|----------|-------------|-----|
| patient_id | Text | Unique identifier, not a number |
| age | Numeric | Continuous measurement |
| sex | Categorical | Two discrete categories (M/F) |
| admission_type | Categorical | Three discrete categories (Emergency/Elective/Urgent) |
| primary_dx_icd | Text | ICD-10 codes are text strings, not categories or numbers |
| charlson_score | Numeric | Integer score treated as continuous (range 0-15) |
| length_of_stay | Numeric | Continuous measurement in days |
| wbc | Numeric | Continuous lab value |
| hemoglobin | Numeric | Continuous lab value |
| creatinine | Numeric | Continuous lab value |
| hba1c | Numeric | Continuous lab value |
| systolic_bp | Numeric | Continuous measurement |
| heart_rate | Numeric | Continuous measurement |
| n_medications | Numeric | Count treated as continuous |
| icu_stay | Categorical | Two discrete categories (Yes/No) |
| readmission_30d | Categorical | Binary outcome (Yes/No) |

**Setting variable roles:**

For each variable, click the **Role** dropdown and set:

| Variable | Set Role To | Why |
|----------|-------------|-----|
| patient_id | Meta | Identifier only, must not be used in analysis |
| age | Feature | Clinical predictor |
| sex | Feature | Demographic predictor |
| admission_type | Feature | Clinical predictor |
| primary_dx_icd | Meta | Text reference, not directly analyzable |
| charlson_score | Feature | Comorbidity predictor |
| length_of_stay | Feature | Utilization predictor |
| wbc | Feature | Laboratory predictor |
| hemoglobin | Feature | Laboratory predictor |
| creatinine | Feature | Laboratory predictor |
| hba1c | Feature | Laboratory predictor |
| systolic_bp | Feature | Vital sign predictor |
| heart_rate | Feature | Vital sign predictor |
| n_medications | Feature | Medication burden predictor |
| icu_stay | Feature | Acuity predictor |
| readmission_30d | Target | Outcome variable |

3. After setting all types and roles, click **Apply**

**Verify your settings:**

- The widget summary should now show: N features, 1 target (class) variable, 2 meta attributes
- The target variable should appear highlighted or marked differently in the widget summary

> **Tip:** If you accidentally set the wrong type and Orange converts the data incorrectly, you can change the type back — but check the data preview to confirm the values are still correct. For categorical variables like sex (M/F), Orange should display the original text values, not numbers.

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Hands-on — Explore with Data Table and Column Statistics (15 min)

**Widgets:** File, Data Table, Column Statistics

Now that variable roles and types are set, connect the **File** widget to two exploration widgets.

**5a. Data Table**

1. Drag a **Data Table** widget onto the canvas (from the **Data** category)
2. Connect **File** → **Data Table** via **Data → Data**
3. Double-click the **Data Table** widget to open it

**What to inspect in Data Table:**

- Scroll through rows — do values look reasonable?
- Check that categorical variables display text labels (M/F, Yes/No, Emergency/Elective/Urgent), not numbers
- Check that meta columns (patient_id, primary_dx_icd) appear in a separate section or are visually distinguished
- Look for missing values — these appear as empty cells or "?" marks
- Check for obvious data entry errors (e.g., age = 999, negative lab values)

**5b. Column Statistics**

1. Drag a **Column Statistics** widget onto the canvas (from the **Data** category)
2. Connect **File** → **Column Statistics** via **Data → Data**
3. Double-click the **Column Statistics** widget to open it

**What to inspect in Column Statistics:**

For each numeric variable, check:

| Variable | Clinically Plausible Range | What to Flag |
|----------|---------------------------|--------------|
| age | 0-120 years | Values above 120 or below 0 |
| charlson_score | 0-15 | Values above 15 or below 0 |
| length_of_stay | 0-365 days | Negative values; extremely long stays (>100 days) may need review |
| wbc | 1.0-30.0 (10^3/uL) | Normal range 4.5-11.0; values below 1 or above 50 are implausible |
| hemoglobin | 3.0-20.0 g/dL | Normal range 12-17; values below 3 or above 20 are implausible |
| creatinine | 0.1-15.0 mg/dL | Normal range 0.6-1.2; values above 15 are extremely rare (severe renal failure) |
| hba1c | 3.0-20.0 % | Normal below 5.7; diabetic above 6.5; values above 15 are rare |
| systolic_bp | 50-300 mmHg | Normal 90-120; values below 50 (incompatible with life) or above 300 are implausible |
| heart_rate | 20-250 bpm | Normal 60-100; values below 20 or above 250 are implausible |
| n_medications | 0-50 | Extremely high counts (>30) may warrant review |

**Record these for later reference:**

- Number of missing values per variable
- Min, max, mean, and standard deviation for each numeric variable
- Any variables with implausible values outside the ranges above
- Distribution of categorical variables (how many M vs. F, how many Yes vs. No for readmission)

> **Note:** Column Statistics shows summary statistics (mean, median, min, max, standard deviation, missing count) for numeric variables and value counts for categorical variables. This is your first line of defense for identifying data quality issues before any modeling.

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Lecture — Identifying Data Quality Issues (10 min)

Before any analysis, clinical datasets must be checked for quality problems. Undetected issues lead to misleading results.

**Common data quality issues in hospital datasets:**

**1. Missing values**

- Why they occur: lab tests not ordered, vital signs not recorded, documentation gaps
- Impact: reduces sample size; if missingness is non-random (e.g., sicker patients more likely to have missing labs), it introduces bias
- What to check: Column Statistics shows the missing count per variable — any variable with more than 20-30% missing values may need special handling or exclusion

**2. Implausible values (out-of-range)**

These are values that are biologically or clinically impossible:

| Variable | Implausible If | Likely Cause |
|----------|---------------|--------------|
| heart_rate | > 300 bpm or < 0 | Data entry error, monitor artifact |
| systolic_bp | > 300 mmHg or < 0 | Data entry error, transducer error |
| creatinine | > 15 mg/dL | Possible but extremely rare; verify if patient was on dialysis |
| hemoglobin | > 20 g/dL or < 3 g/dL | Lab error, or post-transfusion artifact |
| wbc | > 100 (10^3/uL) | Lab error, or extreme leukocytosis (verify diagnosis) |
| age | > 120 years or < 0 | Data entry error |
| hba1c | > 20% | Lab error; extreme values are unreliable |
| length_of_stay | < 0 days | Data extraction error |

**3. Unexpected distributions**

- A variable that should be roughly symmetric (e.g., systolic_bp) but is heavily skewed
- A categorical variable with a class that is too rare (e.g., only 2% readmission rate) — this creates class imbalance problems in modeling
- A continuous variable with suspicious spikes at round numbers (e.g., many patients with creatinine = 1.0 exactly) — suggests imputation or default values

**4. Wrong variable types**

- A categorical variable (e.g., icu_stay coded as 0/1) treated as numeric — Orange will compute a mean of 0.35, which is meaningless as a clinical value
- A numeric variable with text entries (e.g., "pending" in a lab result column) — causes the entire column to be treated as text

**5. Duplicate records**

- Same patient appearing multiple times — could be multiple admissions (expected) or data duplication (error)
- Check patient_id for duplicates if each row is supposed to represent one unique admission

> **Tip:** Data quality assessment is not optional. In clinical data analysis, a model built on dirty data does not just perform poorly — it can lead to incorrect clinical conclusions. Always document every quality issue you find and how you resolved it.

[Back to Table of Contents](#table-of-contents)

---

## Session 7: Hands-on — Recode Variables with Edit Domain (15 min)

**Widgets:** File, Edit Domain, Data Table

The **Edit Domain** widget allows you to rename variables, change their display labels, and verify that types are correct. This is useful for making the dataset more readable and confirming your Session 4 settings.

**Step-by-step:**

1. Drag an **Edit Domain** widget onto the canvas (from the **Data** category)
2. Connect **File** → **Edit Domain** via **Data → Data**
3. Double-click the **Edit Domain** widget to open it

**Renaming variables for clarity:**

In the left panel of Edit Domain, select each variable and update its name in the right panel. Suggested renames:

| Original Name | Rename To | Reason |
|---------------|-----------|--------|
| patient_id | Patient ID | Readability |
| age | Age (years) | Include unit |
| sex | Sex | Already clear |
| admission_type | Admission Type | Readability |
| primary_dx_icd | Primary Dx (ICD-10) | Clarify content |
| charlson_score | Charlson Score | Readability |
| length_of_stay | Length of Stay (days) | Include unit |
| wbc | WBC (10^3/uL) | Include unit |
| hemoglobin | Hemoglobin (g/dL) | Include unit |
| creatinine | Creatinine (mg/dL) | Include unit |
| hba1c | HbA1c (%) | Include unit |
| systolic_bp | Systolic BP (mmHg) | Include unit |
| heart_rate | Heart Rate (bpm) | Include unit |
| n_medications | Number of Medications | Readability |
| icu_stay | ICU Stay | Readability |
| readmission_30d | 30-Day Readmission | Readability |

4. For each variable, also verify in the right panel that:
   - The **type** matches what you set in Session 4 (Numeric, Categorical, or Text)
   - Categorical variables show the correct value list (e.g., Sex should show M, F — not 0, 1)
   - The **role** is correct (Feature, Target, or Meta)

5. Click **Apply** after all changes

**Verify with Data Table:**

6. Drag a second **Data Table** widget onto the canvas (label it "Recoded Data")
7. Connect **Edit Domain** → **Data Table** (Recoded Data) via **Data → Data**
8. Double-click **Recoded Data** to open it

**What to verify:**

- Column headers now show the renamed variable names with units
- Variable types are correct (categorical variables show text labels, numeric variables show numbers)
- Meta variables (Patient ID, Primary Dx ICD-10) are still in the meta section
- The Target variable (30-Day Readmission) is still correctly assigned
- No data values were lost or changed during renaming — compare a few rows with the original Data Table from Session 5

**Your final workflow should look like this:**

```
File → Data Table (raw inspection)
File → Column Statistics (summary statistics)
File → Edit Domain → Data Table (recoded data)
```

All three branches originate from the same **File** widget. The **File** widget sends its data to three downstream widgets simultaneously.

> **Note:** Edit Domain only changes how variables are displayed and labeled. It does not modify the underlying data values. This means you can safely rename variables without worrying about breaking anything. However, if you later load new data through the same File widget, you will need to re-apply the Edit Domain settings.

> **Tip:** Save your workflow frequently (File > Save). The .ows file preserves all widget configurations, connections, and settings. You will build on this workflow in future meetings.

[Back to Table of Contents](#table-of-contents)
