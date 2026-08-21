# Hands-on with Orange: Clinical Data Cleaning and Transformation

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Session 1: Lecture — Missing Data in Clinical Settings (10 min)](#session-1-lecture--missing-data-in-clinical-settings-10-min)
2. [Session 2: Hands-on — Handle Missing Values with Impute (15 min)](#session-2-hands-on--handle-missing-values-with-impute-15-min)
3. [Session 3: Lecture — Derived Clinical Variables (10 min)](#session-3-lecture--derived-clinical-variables-10-min)
4. [Session 4: Hands-on — Create Clinical Flags with Formula Widget (20 min)](#session-4-hands-on--create-clinical-flags-with-formula-widget-20-min)
5. [Session 5: Hands-on — Filter Study Population with Select Rows (15 min)](#session-5-hands-on--filter-study-population-with-select-rows-15-min)
6. [Session 6: Lecture — Combining Data from Multiple Sources (10 min)](#session-6-lecture--combining-data-from-multiple-sources-10-min)
7. [Session 7: Hands-on — Merge Clinical Datasets with Merge Data (15 min)](#session-7-hands-on--merge-clinical-datasets-with-merge-data-15-min)

**Subtopics:**

- Missing data mechanisms in clinical settings (MCAR, MAR, MNAR)
- Imputation strategies for clinical variables
- Deriving clinical flags from lab values and vital signs
- Formula widget expressions for clinical thresholds
- Filtering study populations with inclusion/exclusion criteria
- Merging data from multiple hospital information systems

---

## Prerequisites

- Completed Meeting 10 workflow: hospital discharge dataset loaded into Orange using the **File** widget, with variable roles assigned (patient_id and primary_dx_icd as Meta, readmission_30d as Target, all others as Features)
- Orange Data Mining installed with all default add-ons
- Hospital discharge CSV dataset available

> **Recall Meeting 10:** Your Orange workflow should have the **File** widget connected to a **Data Table** widget, with the dataset containing 15 variables: patient_id (Meta), age, sex, admission_type, primary_dx_icd (Meta), charlson_score, length_of_stay, wbc, hemoglobin, creatinine, hba1c, systolic_bp, heart_rate, n_medications, icu_stay, and readmission_30d (Target).

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — Missing Data in Clinical Settings (10 min)

### Why Clinical Data Has Missing Values

Hospital data is not collected for research purposes. It is collected for patient care, billing, and regulatory compliance. This means that variables are recorded only when clinically indicated, not systematically for every patient. Understanding why data is missing is essential before deciding how to handle it.

**Common reasons for missing clinical data:**

- **Labs not ordered unless suspected.** A WBC count is drawn when infection is suspected. A patient admitted for elective knee replacement may never have a WBC recorded during the encounter.
- **HbA1c only for diabetics.** HbA1c is ordered to monitor glycemic control. A 25-year-old trauma patient will almost certainly not have an HbA1c value. This is not random — it is driven by the patient's diagnosis.
- **Vital signs during transfers.** A patient transferred from the emergency department to the ICU may have a gap in vital sign documentation during the handoff period.
- **Documentation burden.** Nurses and physicians may skip fields in the electronic health record when the clinical situation is urgent.
- **System differences.** Lab results, vitals, and medication records may come from separate information systems, and merges can produce missing values when records do not align.

### Three Mechanisms of Missing Data

| Mechanism | Abbreviation | Meaning | Clinical Example |
|-----------|-------------|---------|-----------------|
| Missing Completely At Random | MCAR | Missingness is unrelated to any variable, observed or unobserved | A lab tube is dropped in transit — the result is missing for a purely mechanical reason |
| Missing At Random | MAR | Missingness depends on observed variables but not the missing value itself | HbA1c is missing because the patient has no diabetes diagnosis (diagnosis is observed) |
| Missing Not At Random | MNAR | Missingness depends on the unobserved value itself | A very high creatinine result is missing because the lab instrument flagged it as an error and suppressed the value |

### Impact on Analysis

- **Biased results.** If sicker patients are more likely to have lab values recorded, then analyses using only complete cases will be biased toward sicker populations.
- **Reduced sample size.** Dropping rows with any missing value can dramatically reduce the dataset, especially when many variables each have a small percentage missing.
- **Invalid assumptions.** Many machine learning algorithms assume complete data. Feeding them data with missing values will either produce errors or silently exclude rows.

> **Note:** In this course, we will use simple imputation (median for continuous, mode for categorical) as a practical starting point. In real clinical research, the choice of imputation method requires careful consideration of the missing data mechanism.

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Hands-on — Handle Missing Values with Impute (15 min)

### Step 1: Check Missing Values Before Imputation

Before imputing, examine where missing values exist.

1. In your existing workflow from Meeting 10, find the **File** widget that loads the hospital discharge dataset.
2. From the **Data** tab in the widget toolbox, drag the **Column Statistics** widget onto the canvas.
3. Connect **File** to **Column Statistics** via **Data -> Data**.
4. Double-click the **Column Statistics** widget to open it.
5. Examine the **Missing** column. Note which variables have missing values and how many.

> **Note:** You should expect to see missing values in variables like hba1c (missing for non-diabetic patients), creatinine, and wbc. Variables like age, sex, and admission_type should have zero or very few missing values.

### Step 2: Add the Impute Widget

1. From the **Data** tab in the widget toolbox, drag the **Impute** widget onto the canvas.
2. Connect **File** to **Impute** via **Data -> Data**.

### Step 3: Configure Imputation Strategy

1. Double-click the **Impute** widget to open its settings.
2. Under **Default method**, select **Average/Most frequent**. This applies median imputation for continuous (numeric) variables and mode imputation for categorical (discrete) variables.
3. If you want to set variable-specific strategies:
   - Select individual variables from the list on the left.
   - For continuous variables (age, charlson_score, length_of_stay, wbc, hemoglobin, creatinine, hba1c, systolic_bp, heart_rate, n_medications), the default median is appropriate.
   - For categorical variables (sex, admission_type, icu_stay), the default most-frequent value is appropriate.
4. Click **Apply** (or ensure **Apply Automatically** is checked).

### Step 4: Verify Imputation Results

1. From the **Data** tab, drag a **Data Table** widget onto the canvas.
2. Connect **Impute** to **Data Table** via **Data -> Data**.
3. Double-click the **Data Table** widget and browse the data. Check that previously missing cells now contain values.

### Step 5: Compare Column Statistics After Imputation

1. Drag a second **Column Statistics** widget onto the canvas.
2. Connect **Impute** to this second **Column Statistics** via **Data -> Data**.
3. Open this second **Column Statistics** widget and compare it with the first one. The **Missing** column should now show zero for all variables.

> **Note:** Your workflow should now look like: **File** -> **Impute** -> **Data Table**. The **Column Statistics** widgets are connected in parallel for before/after comparison: **File** -> **Column Statistics** (before) and **Impute** -> **Column Statistics** (after).

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Lecture — Derived Clinical Variables (10 min)

### Why Create Derived Variables?

Raw clinical values are continuous measurements. But clinical decisions are often based on thresholds: Is the patient diabetic? Is the white blood cell count elevated? Is blood pressure too high? Creating binary flags from continuous values translates raw data into clinically meaningful categories.

**Benefits of derived clinical variables:**

- **Clinical interpretability.** A flag for "diabetes_flag = 1" is immediately meaningful to a clinician, while "hba1c = 7.2" requires the reader to recall the diagnostic threshold.
- **Feature engineering.** Machine learning models can benefit from both the raw value and the derived flag. The flag captures the nonlinear clinical threshold that a linear model might miss.
- **Standardized definitions.** Using established clinical thresholds ensures that your analysis aligns with clinical guidelines and is reproducible.

### Clinical Thresholds Used in This Session

| Derived Variable | Source Variable(s) | Threshold | Clinical Meaning |
|-----------------|-------------------|-----------|-----------------|
| diabetes_flag | hba1c | >= 6.5% | Diabetes mellitus (ADA diagnostic criterion) |
| leukocytosis | wbc | > 11 x 10^3/uL | Elevated white blood cell count, suggests infection or inflammation |
| anemia | hemoglobin, sex | < 12 g/dL (female), < 13 g/dL (male) | Low hemoglobin (WHO definition, sex-specific) |
| hypertension | systolic_bp | >= 140 mmHg | Stage 2 hypertension (JNC/ACC guideline) |
| age_group | age | 18-44, 45-64, 65-79, 80+ | Standard age stratification for hospital outcomes research |
| high_comorbidity | charlson_score | >= 3 | High comorbidity burden (Charlson Comorbidity Index) |

### A Note on eGFR

Estimated glomerular filtration rate (eGFR) is derived from creatinine, age, and sex using the CKD-EPI equation. The full CKD-EPI formula is complex and not straightforward to implement in the Orange Formula widget. In practice, eGFR is often pre-calculated in the electronic health record. For this course, we will note the concept but focus on simpler derived variables that the Formula widget can handle directly.

> **Note:** The thresholds above are simplified for teaching purposes. In clinical research, thresholds may vary by guideline version, patient population, and study design. Always document which thresholds and guidelines you used.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Hands-on — Create Clinical Flags with Formula Widget (20 min)

### Step 1: Add the Formula Widget

1. From the **Data** tab in the widget toolbox, drag the **Formula** widget onto the canvas.
2. Connect **Impute** to **Formula** via **Data -> Data**.
3. Double-click the **Formula** widget to open it.

### Step 2: Create diabetes_flag

1. In the **Formula** widget, click **Add** (or the "+" button) to create a new variable.
2. Set the variable name to `diabetes_flag`.
3. Enter the following expression:

```
1 if hba1c >= 6.5 else 0
```

4. Click **Apply** or press Enter.

> **Note:** This creates a numeric variable with value 1 when HbA1c is 6.5% or higher (consistent with the ADA diagnostic criterion for diabetes) and 0 otherwise.

### Step 3: Create leukocytosis

1. Click **Add** to create another new variable.
2. Set the variable name to `leukocytosis`.
3. Enter the following expression:

```
1 if wbc > 11 else 0
```

4. Click **Apply**.

### Step 4: Create anemia (Sex-Specific)

1. Click **Add** to create another new variable.
2. Set the variable name to `anemia`.
3. Enter the following expression:

```
1 if (sex == "F" and hemoglobin < 12) or (sex == "M" and hemoglobin < 13) else 0
```

4. Click **Apply**.

> **Note:** Anemia thresholds differ by sex. The WHO defines anemia as hemoglobin below 12 g/dL for females and below 13 g/dL for males. The Formula widget supports logical operators (`and`, `or`) and string comparisons for categorical variables.

### Step 5: Create hypertension

1. Click **Add** to create another new variable.
2. Set the variable name to `hypertension`.
3. Enter the following expression:

```
1 if systolic_bp >= 140 else 0
```

4. Click **Apply**.

### Step 6: Create age_group

1. Click **Add** to create another new variable.
2. Set the variable name to `age_group`.
3. Enter the following expression:

```
"18-44" if age < 45 else ("45-64" if age < 65 else ("65-79" if age < 80 else "80+"))
```

4. Click **Apply**.

> **Note:** This creates a categorical (string) variable with four age groups commonly used in hospital outcomes research. The nested `if-else` structure assigns each patient to exactly one group.

### Step 7: Create high_comorbidity

1. Click **Add** to create another new variable.
2. Set the variable name to `high_comorbidity`.
3. Enter the following expression:

```
1 if charlson_score >= 3 else 0
```

4. Click **Apply**.

### Step 8: Verify the New Variables

1. From the **Data** tab, drag a new **Data Table** widget onto the canvas.
2. Connect **Formula** to this **Data Table** via **Data -> Data**.
3. Double-click the **Data Table** widget and scroll right to see the newly created columns: diabetes_flag, leukocytosis, anemia, hypertension, age_group, and high_comorbidity.
4. Spot-check a few rows:
   - Find a patient with hba1c >= 6.5 and confirm diabetes_flag = 1.
   - Find a female patient with hemoglobin < 12 and confirm anemia = 1.
   - Find a patient aged 70 and confirm age_group = "65-79".

> **Note:** Your workflow should now look like: **File** -> **Impute** -> **Formula** -> **Data Table**. Each widget transforms the data and passes it downstream.

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Hands-on — Filter Study Population with Select Rows (15 min)

### Step 1: Add the Select Rows Widget

1. From the **Data** tab in the widget toolbox, drag the **Select Rows** widget onto the canvas.
2. Connect **Formula** to **Select Rows** via **Data -> Data**.
3. Double-click the **Select Rows** widget to open it.

### Step 2: Apply Inclusion Criteria

Clinical studies define inclusion criteria to specify which patients belong in the analysis. For a hospital discharge study, typical criteria include:

1. In the **Select Rows** widget, click **Add Condition**.
2. Select the variable `age` from the dropdown.
3. Set the operator to `>=` (is greater than or equal to).
4. Enter the value `18`.
5. This ensures only adult patients (age 18 and older) are included.

### Step 3: Apply Exclusion Criteria

Exclusion criteria remove patients who do not fit the study design:

1. Click **Add Condition** again.
2. Select the variable `length_of_stay`.
3. Set the operator to `>=`.
4. Enter the value `1`.
5. This removes outpatient encounters or same-day discharges (length_of_stay < 1 day), which represent a different clinical population than inpatient stays.

> **Note:** You can add multiple conditions. By default, **Select Rows** uses AND logic, meaning all conditions must be satisfied for a row to be included.

### Step 4: Check Row Counts Before and After Filtering

1. Look at the status bar of the **Select Rows** widget. It shows how many rows match the conditions and how many are filtered out.
2. Connect **Select Rows** to a new **Data Table** widget via **Matching Data -> Data**.
3. Open this **Data Table** and verify the row count.
4. Compare this with the row count in the **Data Table** connected to the **Formula** widget (before filtering).

### Step 5: Access Filtered-Out Rows (Optional)

1. The **Select Rows** widget has two outputs: **Matching Data** and **Non-Matching Data**.
2. If you want to inspect excluded patients, drag another **Data Table** widget onto the canvas.
3. Connect **Select Rows** to this **Data Table** via **Non-Matching Data -> Data**.
4. Open it to see which patients were excluded and why.

> **Note:** Documenting the number of patients excluded at each step is standard practice in clinical research. This is typically reported in a CONSORT-style flow diagram: total patients -> excluded (with reasons) -> final study population.

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Lecture — Combining Data from Multiple Sources (10 min)

### Why Hospital Data Comes from Multiple Systems

Modern hospitals do not store all patient data in a single database. Data is distributed across specialized information systems, each designed for a specific clinical function:

- **Hospital Information System (HIS):** Demographics, admissions, discharges, diagnoses, billing codes.
- **Laboratory Information System (LIS):** Lab orders and results (CBC, metabolic panels, HbA1c, cultures).
- **Pharmacy System:** Medication orders, dispensing records, drug interactions.
- **Radiology Information System (RIS):** Imaging orders and reports.
- **Nursing Documentation System:** Vital signs, assessments, care plans.

When researchers need a comprehensive dataset for analysis, they must extract data from each system separately and merge them. The key to merging is a **common identifier** — typically the patient_id (or medical record number) and sometimes an encounter_id.

### Challenges in Merging Clinical Data

| Challenge | Example |
|-----------|---------|
| One-to-many relationships | One patient has multiple lab results; merging produces duplicate rows or requires aggregation |
| Mismatched identifiers | LIS uses a lab accession number that does not directly map to the HIS patient_id |
| Temporal alignment | A lab result drawn at 02:00 needs to be matched with vitals recorded at 02:15, not 14:00 |
| Missing matches | A patient in the HIS may have no pharmacy records if no medications were ordered |

### Types of Joins

| Join Type | Result | When to Use |
|-----------|--------|-------------|
| Inner join | Only rows that match in both datasets | When you need complete data from both sources |
| Left join | All rows from the first dataset, matched rows from the second (unmatched rows get missing values) | When you want to keep all patients even if they lack records in the second source |
| Outer join | All rows from both datasets | When you want a complete picture, accepting missing values |

> **Note:** In Orange, the **Merge Data** widget supports these join types. For clinical data, a left join is often the safest choice: keep all patients from your primary cohort and accept missing values for those without records in the secondary data source.

[Back to Table of Contents](#table-of-contents)

---

## Session 7: Hands-on — Merge Clinical Datasets with Merge Data (15 min)

### Scenario

Your hospital discharge dataset contains demographics, diagnoses, labs, and vitals. Suppose the pharmacy system provides a separate CSV file with medication details for each patient. You need to merge these two datasets on patient_id.

The second CSV file (e.g., `medication_details.csv`) contains:

| Variable | Description |
|----------|-------------|
| patient_id | Patient identifier (matches the main dataset) |
| polypharmacy | Whether the patient is on 5 or more medications (Yes/No) |
| high_risk_meds | Whether the patient is on high-risk medications such as anticoagulants or opioids (Yes/No) |

### Step 1: Load the Second Dataset

1. Drag a new **File** widget onto the canvas.
2. Double-click it and load the `medication_details.csv` file.
3. Connect this **File** widget to a **Data Table** widget to inspect the data.
4. Verify that patient_id values match those in the main dataset.

### Step 2: Add the Merge Data Widget

1. From the **Data** tab in the widget toolbox, drag the **Merge Data** widget onto the canvas.
2. Connect the **Select Rows** widget (from your main workflow) to the **Merge Data** widget via **Matching Data -> Data**.
3. Connect the new **File** widget (medication data) to the **Merge Data** widget via **Data -> Extra Data**.

> **Note:** The connection order matters. The first input (**Data**) is the primary dataset. The second input (**Extra Data**) is the dataset being merged in. In a left join, all rows from **Data** are kept.

### Step 3: Configure the Merge

1. Double-click the **Merge Data** widget to open its settings.
2. Under **Merge type**, select **Left join** (to keep all patients from the main dataset).
3. Under **Row matching**, ensure that `patient_id` is selected as the matching variable for both datasets.
4. If patient_id does not appear automatically, click the dropdown to select it manually for each side.
5. Click **Apply**.

### Step 4: Verify the Merged Output

1. Drag a new **Data Table** widget onto the canvas.
2. Connect **Merge Data** to this **Data Table** via **Data -> Data**.
3. Double-click the **Data Table** widget and check the following:
   - The row count should match the row count from **Select Rows** (since we used a left join, no patients are lost).
   - Scroll right to find the new columns: polypharmacy and high_risk_meds.
   - Check a few patients: those present in both datasets should have values for the new columns; those absent from the medication file should show missing values.

### Step 5: Review the Complete Workflow

Your final Orange workflow from Meetings 10 and 11 should follow this pipeline:

**File** (main dataset) -> **Impute** -> **Formula** -> **Select Rows** -> **Merge Data** -> **Data Table**

With a parallel branch:

**File** (medication dataset) -> **Merge Data** via **Data -> Extra Data**

And optional diagnostic branches:

- **File** -> **Column Statistics** (before imputation)
- **Impute** -> **Column Statistics** (after imputation)
- **Select Rows** -> **Data Table** (non-matching data, via **Non-Matching Data** output)

> **Note:** Save your Orange workflow (.ows file) at this point. Meeting 12 will build on this cleaned, transformed, and merged dataset to perform exploratory analysis and build predictive models for 30-day readmission.

[Back to Table of Contents](#table-of-contents)
