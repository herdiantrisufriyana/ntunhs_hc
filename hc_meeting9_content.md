# Study Plan: Clinical Data Analysis Design

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Overview](#1-overview)
2. [Study Plan Requirements](#2-study-plan-requirements)
3. [Grading Rubric](#3-grading-rubric)
4. [Example Study Plan Outline](#4-example-study-plan-outline)
5. [Submission Instructions](#5-submission-instructions)
6. [Recap](#6-recap)

---

## 1. Overview

The study plan is worth **25% of the final grade** and is part of the capstone project. It is a written document where you design a clinical data analysis from scratch. You will define a clinical question, identify the hospital data needed to answer it, specify the study population and variables, plan the analytical approach, and consider ethical and methodological issues.

**Purpose:** The study plan demonstrates your ability to integrate the foundational knowledge from Meetings 01-08 into a coherent analysis design. It tests whether you can translate a clinical question into a structured, executable research plan using hospital data.

**Connection to the capstone (Meeting 15):** The study plan is the design component of your capstone project. Your capstone report will include the study plan plus the analysis results. In Meetings 10-14, you will learn to use Orange for data loading, cleaning, visualization, comparative analysis, and outcome modeling. In Meeting 15, you will present the results of carrying out the analysis you designed here. A well-constructed study plan makes the capstone straightforward; a poorly defined plan leads to confusion during execution.

**Submit early, revise to reach A:** The study plan can be submitted any time up to the capstone due date (Meeting 15). You are encouraged to submit as early as possible — the instructor will provide feedback and you can revise and resubmit until the due date. Earlier submission gives you more revision cycles to improve your grade.

[Back to Table of Contents](#table-of-contents)

---

## 2. Study Plan Requirements

Your study plan must include all of the following sections. Each section should be specific enough that another researcher could replicate your analysis design.

### 2.1 Clinical Question

State a clinical question that can be answered with hospital data. The question should follow the PICO framework where applicable:

- **P** (Population) — Which patients?
- **I** (Intervention/Exposure) — What exposure, treatment, or characteristic?
- **C** (Comparison) — Compared to whom?
- **O** (Outcome) — What outcome is measured?

The question must be answerable using data routinely collected in hospital information systems (demographics, diagnoses, lab results, vital signs, medications, procedures, discharge records). It should not require data from outside the hospital (e.g., patient-reported outcomes collected at home, community pharmacy records).

### 2.2 Data Source Description

Describe the hospital data source you plan to use:

- **Type of data:** Electronic health records, hospital discharge database, claims data, disease registry, or a combination
- **Hospital setting:** Single hospital, multi-center, academic medical center, community hospital
- **Time period:** Start and end dates for data extraction (e.g., January 2020 to December 2023)
- **Data systems involved:** Which hospital information systems contribute data (HIS, LIS, pharmacy, nursing documentation)

### 2.3 Study Population

Define who is included and excluded from the analysis. Use specific clinical codes where applicable.

**Inclusion criteria** — conditions that a patient must meet to be included:

- Diagnosis codes (ICD-10) that define the target population
- Age range, admission type, or time constraints
- Example: "Adults (age >= 18) admitted with a primary diagnosis of heart failure (ICD-10: I50.0, I50.1, I50.9) between January 2020 and December 2023"

**Exclusion criteria (if any)** — conditions that happen AFTER enrollment but are known BEFORE the outcome. Unlike inclusion criteria (known at the index date), exclusion criteria remove patients based on events that occur between the index date and the outcome window.

- Example: "Exclude patients discharged to hospice (readmission is not a meaningful outcome for end-of-life patients)" — determined during admission, known at discharge, but not known at admission.
- Example: "Exclude patients with a planned readmission scheduled at discharge" — determined during admission, known at discharge.
- Not all studies need exclusion criteria. If no post-enrollment events would invalidate the outcome, exclusion criteria may be empty.

> **Note:** Do not confuse exclusion criteria with inclusion criteria. Conditions known at the index date (e.g., age, diagnosis, discharge status, transfer status) belong in the inclusion criteria. See Meeting 04 for the distinction. Use specific ICD-10, ATC, or LOINC codes wherever possible.

### 2.4 Variable Definitions

Provide a table defining every variable in your analysis. Each variable must include:

| Column | Description |
|--------|-------------|
| Variable Name | Short identifier used in the dataset (e.g., charlson_score) |
| Clinical Definition | What the variable measures in clinical terms |
| Role | Exposure, Outcome, or Covariate |
| Data Type | Continuous, categorical (binary/ordinal/nominal), or text |
| Source System | Which hospital information system provides this variable (HIS, LIS, pharmacy, etc.) |
| Codes / Units | ICD-10, ATC, LOINC codes, or measurement units as applicable |

**At minimum, define:**

- **One exposure variable** — the primary factor you are investigating
- **One outcome variable** — the clinical endpoint you are measuring
- **At least three covariates** — potential confounders or adjustment variables, justified by clinical reasoning

Example row:

| Variable Name | Clinical Definition | Role | Data Type | Source System | Codes / Units |
|---------------|---------------------|------|-----------|---------------|---------------|
| diabetes_dx | Presence of diabetes mellitus diagnosis | Exposure | Binary (Yes/No) | HIS (discharge diagnoses) | ICD-10: E10.x, E11.x, E13.x |
| readmission_30d | Unplanned readmission within 30 days of discharge | Outcome | Binary (Yes/No) | HIS (admission records) | Derived from admission dates |
| age | Patient age at admission | Covariate | Continuous | HIS (demographics) | Years |
| charlson_score | Charlson Comorbidity Index score | Covariate | Continuous (0-15) | HIS (discharge diagnoses) | Derived from ICD-10 codes |
| sex | Biological sex | Covariate | Binary (M/F) | HIS (demographics) | - |

### 2.5 Data Quality Assessment Strategy

Describe how you will assess and handle data quality issues before analysis:

- **Which variables will you audit?** Identify variables most likely to have quality problems (lab values, vital signs, derived variables)
- **Plausibility checks:** What are the clinically plausible ranges for each continuous variable? (e.g., age 0-120, hemoglobin 3-20 g/dL)
- **Missing data:** How will you assess the extent and pattern of missing values? What imputation strategy will you use, and why?
- **Duplicate records:** How will you check for and handle duplicate patient entries?
- **Unexpected distributions:** What distribution shapes do you expect, and what would raise concern?

### 2.6 Analytical Approach

Describe the analysis steps in order, from descriptive to comparative to modeling. Reference the methods covered in Meetings 06-08.

**Step 1: Descriptive analysis**

- Summarize the study population (demographic and clinical characteristics)
- Create a "Table 1" describing the cohort
- Report continuous variables as mean (SD) or median (IQR) depending on distribution
- Report categorical variables as count (percentage)

**Step 2: Comparative analysis**

- Compare the exposure groups on the outcome variable
- State which statistical test or visual comparison you will use (e.g., chi-squared test for categorical outcome, t-test or Mann-Whitney for continuous outcome, box plots for visual comparison)
- Identify potential confounders and describe how you will address them

**Step 3: Outcome modeling (if applicable)**

- State the modeling approach (e.g., logistic regression for binary outcomes)
- Define the unadjusted model (exposure and outcome only)
- Define the adjusted model (exposure, outcome, and covariates)
- Describe how you will evaluate model performance (e.g., AUC, sensitivity, specificity)

### 2.7 Ethical Considerations

Address the following:

- **De-identification:** How will patient identifiers be handled? Which variables could indirectly identify patients (e.g., rare diagnoses, extreme ages)?
- **PDPA compliance:** How does your analysis comply with Taiwan's Personal Data Protection Act? What data governance measures apply?
- **Consent and IRB:** Is IRB approval or exemption needed? Under what conditions can retrospective hospital data be analyzed without individual consent?
- **Data storage and access:** Where will the data be stored, who will have access, and how long will it be retained?

### 2.8 Potential Biases and Limitations

Identify at least two potential biases or limitations and explain how they might affect your results:

- **Selection bias:** Are the patients in your study population representative of the broader clinical population of interest?
- **Information bias:** Could exposure or outcome variables be misclassified based on how they are recorded in the hospital system?
- **Confounding:** Which unmeasured confounders could affect your results? Why are they unmeasured (not available in hospital data)?
- **Generalizability:** Would your findings apply to hospitals with different patient populations, documentation practices, or care models?
- **Temporal bias:** Could changes in clinical practice over the study period affect your results?

> **Note:** Acknowledging limitations is a strength, not a weakness. Every clinical data analysis has limitations. What matters is whether you can identify them and reason about their impact.

[Back to Table of Contents](#table-of-contents)

---

## 3. Grading Rubric

The study plan is graded on a five-level rubric (A through E). Each higher grade builds on all criteria of the grade below it.

| Grade | Criteria |
|-------|----------|
| **E** | Not submitted, or content is unrelated to the course. |
| **D** | Clinical question is unclear or unfocused. Data source is inappropriate for the question (e.g., requires data not available in hospital systems). Major inconsistencies between the clinical question, study population, and variable definitions. |
| **C** | Clearly defines a clinical question. Specifies a study population with inclusion/exclusion criteria. Identifies an appropriate data source. Provides a basic analytical approach (descriptive and comparative). Variable definitions are coherent and consistent with the clinical question. |
| **B** | All C criteria, plus: addresses data quality considerations (plausibility checks, missing data strategy). Variable selection is justified with clinical domain knowledge (explains why each covariate was chosen and what confounding it addresses). |
| **A** | All B criteria, plus: includes data governance and ethical considerations (de-identification, PDPA compliance). Demonstrates understanding of potential biases and limitations (identifies specific biases, explains their direction and magnitude, discusses mitigation strategies). |

> **Note:** A grade of C requires a complete, internally consistent study plan. A grade of B additionally requires evidence of critical thinking about data quality and variable selection. A grade of A further requires awareness of the ethical and methodological complexities inherent in clinical data analysis.

[Back to Table of Contents](#table-of-contents)

---

## 4. Example Study Plan Outline

The following is a brief worked example using the hospital discharge dataset from this course. Each section contains 1-2 sentences of placeholder content to illustrate the expected level of specificity.

---

**Clinical Question:** Does diabetes mellitus increase the risk of 30-day unplanned readmission in adult patients hospitalized with heart failure?

---

**Data Source:** Hospital discharge database from a single academic medical center, covering all inpatient admissions from January 2020 to December 2023. Data extracted from the Hospital Information System (demographics, diagnoses, admissions) and Laboratory Information System (HbA1c, creatinine, hemoglobin).

---

**Study Population:**

- Inclusion: Adults (age >= 18) discharged alive from an admission with a primary diagnosis of heart failure (ICD-10: I50.0, I50.1, I50.9), between January 2020 and December 2023. Not transferred to another facility. Length of stay >= 1 day. (All known at the index date = discharge.)
- Exclusion (if any): Patients discharged to hospice or palliative care (readmission is not a meaningful outcome). Patients with a planned cardiac procedure readmission scheduled at discharge. (Both determined during admission, known at discharge but not at admission.)

---

**Variable Definitions:**

| Variable Name | Clinical Definition | Role | Data Type | Source System | Codes / Units |
|---------------|---------------------|------|-----------|---------------|---------------|
| diabetes_dx | Diabetes mellitus diagnosis | Exposure | Binary | HIS | ICD-10: E10.x, E11.x, E13.x |
| readmission_30d | Unplanned readmission within 30 days | Outcome | Binary | HIS | Derived from admission dates |
| age | Age at admission | Covariate | Continuous | HIS | Years |
| sex | Biological sex | Covariate | Binary | HIS | M/F |
| charlson_score | Charlson Comorbidity Index | Covariate | Continuous | HIS | Score (0-15) |
| hba1c | Glycated hemoglobin | Covariate | Continuous | LIS | LOINC: 4548-4, % |
| creatinine | Serum creatinine | Covariate | Continuous | LIS | LOINC: 2160-0, mg/dL |
| length_of_stay | Duration of index hospitalization | Covariate | Continuous | HIS | Days |
| icu_stay | ICU admission during index stay | Covariate | Binary | HIS | Yes/No |

---

**Data Quality Assessment:** Audit all lab values (HbA1c, creatinine) for clinically implausible values (HbA1c > 20%, creatinine > 15 mg/dL). Check for missing values in HbA1c (expected to be higher among non-diabetic patients). Use median imputation for continuous variables with less than 20% missing. Verify no duplicate patient_id entries per admission.

---

**Analytical Approach:**

1. Descriptive: Summarize demographics and clinical characteristics for the full cohort and by diabetes status (Table 1)
2. Comparative: Compare 30-day readmission rates between diabetic and non-diabetic groups using chi-squared test and box plots for continuous variables
3. Modeling: Logistic regression with readmission_30d as the outcome, diabetes_dx as the exposure, and age, sex, charlson_score, creatinine, length_of_stay, and icu_stay as covariates. Report unadjusted and adjusted odds ratios with 95% confidence intervals. Evaluate model discrimination using AUC.

---

**Ethical Considerations:** All patient identifiers will be removed before analysis (patient_id used only for linkage, then dropped). The study uses retrospective de-identified data and qualifies for IRB exemption. Data will be stored on a password-protected institutional computer and deleted after the course.

---

**Biases and Limitations:** Selection bias may arise because patients who died during admission are excluded, removing the sickest patients from both groups. Information bias may occur if diabetes diagnosis codes undercount patients with undiagnosed diabetes (exposure misclassification). Unmeasured confounders include medication adherence and socioeconomic status, which are not available in hospital discharge data.

---

> **Note:** This example is intentionally brief. Your actual study plan should expand each section with more detail, particularly the variable definitions table and the analytical approach.

[Back to Table of Contents](#table-of-contents)

---

## 5. Submission Instructions

- **Format:** Submit as PDF or DOCX
- **Length:** No strict page limit, but a well-structured plan typically requires 3-5 pages
- **Platform:** Submit via iCLASS
- **Due date:** Same as the capstone (Meeting 15, Week 15). However, submit as early as possible to receive feedback and revise.
- **File naming:** Use the format `StudentID_StudyPlan.pdf` or `StudentID_StudyPlan.docx`

> **Note:** Start your study plan early. The instructor will provide feedback on each submission, and you can revise and resubmit until the due date. More revision cycles = more opportunities to reach grade A. The clinical question and variable definitions require careful thought. Use the example in Section 4 as a structural template, but choose your own clinical question and dataset design.

[Back to Table of Contents](#table-of-contents)

---

## 6. Recap

The following table maps each section of the study plan to the meeting(s) whose content it draws from. Use this to review relevant material as you prepare your plan.

| Study Plan Section | Relevant Meeting(s) | Key Content |
|--------------------|----------------------|-------------|
| Clinical Question | Meeting 01: Introduction to Hospital Clinical Data Systems | Types of clinical data, what questions hospital data can answer |
| Data Source Description | Meeting 01 + Meeting 02: Clinical Coding and Terminology Standards | Hospital information systems (HIS, LIS, PACS), ICD-10, ATC, LOINC |
| Study Population (inclusion/exclusion) | Meeting 04: Clinical Study Design with Hospital Data | Cohort definition, inclusion vs exclusion criteria, index date |
| Variable Definitions | Meeting 02 + Meeting 04 | Clinical codes (ICD-10, ATC, LOINC) for variable specification, exposure/outcome/covariate roles |
| Data Quality Assessment | Meeting 05: Data Quality Assessment in Clinical Settings | Missing data mechanisms, plausibility checks, data audit |
| Analytical Approach (descriptive) | Meeting 06: Descriptive Analysis of Clinical Variables | Table 1, summary statistics, lab reference ranges |
| Analytical Approach (comparative) | Meeting 07: Comparative Clinical Analysis and Confounding | Group comparisons, confounding, stratification |
| Analytical Approach (modeling) | Meeting 08: Outcome Measurement and Clinical Endpoints | Clinical outcomes, Charlson index, outcome operationalization |
| Ethical Considerations | Meeting 03: Data Governance, Privacy, and Ethics | De-identification, Taiwan PDPA, IRB, data governance |
| Potential Biases and Limitations | Meeting 07: Comparative Clinical Analysis and Confounding | Selection bias, information bias (nondiff/diff), confounding |

[Back to Table of Contents](#table-of-contents)