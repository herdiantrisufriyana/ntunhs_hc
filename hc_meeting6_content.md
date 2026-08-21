# Descriptive Analysis of Clinical Variables

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Session 1: Lecture — Purpose of Descriptive Analysis (10 min)](#session-1-lecture--purpose-of-descriptive-analysis-10-min)
3. [Session 2: Lecture — Summarizing Continuous Clinical Variables (20 min)](#session-2-lecture--summarizing-continuous-clinical-variables-20-min)
4. [Session 3: Lecture — Summarizing Categorical Clinical Variables (15 min)](#session-3-lecture--summarizing-categorical-clinical-variables-15-min)
5. [Session 4: Lecture — The Table 1 Convention (20 min)](#session-4-lecture--the-table-1-convention-20-min)
6. [Session 5: Lecture — Laboratory Reference Ranges and Clinical Thresholds (15 min)](#session-5-lecture--laboratory-reference-ranges-and-clinical-thresholds-15-min)
7. [Session 6: Hands-on — Create a Table 1 (30 min)](#session-6-hands-on--create-a-table-1-30-min)

---

## Subtopics

- Purpose of descriptive analysis as the first step in clinical data analysis
- Choosing between mean +/- SD and median [IQR] for continuous variables
- Summarizing categorical variables with counts and percentages
- The Table 1 convention in clinical research publications
- Standardized mean difference (SMD) as an alternative to p-values
- Laboratory reference ranges and diagnostic thresholds for creating analysis variables
- Constructing a complete Table 1 comparing patient groups

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — Purpose of Descriptive Analysis (10 min)

### Why Descriptive Analysis Comes First

Before testing hypotheses, fitting models, or making predictions, you must understand what your data looks like. Descriptive analysis answers the most basic questions: Who are the patients in this dataset? What are their clinical characteristics? Are there patterns or anomalies that need attention before proceeding?

**Objectives of descriptive analysis in clinical data:**

**Characterize the study population**

- How old are the patients? What is the sex distribution? How sick are they?
- These characteristics determine whether your findings are generalizable. A study of readmission risk in a population that is 90% male and 80% over age 70 cannot be directly applied to a young, mixed-sex population.

**Identify patterns and potential issues**

- Is the distribution of length of stay roughly what you expect for this type of hospital?
- Are lab values within clinically plausible ranges? (This connects back to the data quality assessment from Meeting 05.)
- Are there unexpected imbalances? For example, if only 3% of patients were readmitted, you have a severe class imbalance problem for any prediction task.

**Inform subsequent analysis choices**

- Skewed distributions require non-parametric tests. Symmetric distributions can use parametric tests. You cannot make this decision without first looking at the data.
- Variables with very low variance (e.g., 99% of patients have icu_stay = No) may not be useful as predictors and may need to be reconsidered.

### The "Table 1" Convention

In virtually every clinical research publication, the first table is a descriptive summary of the study population. This is called "Table 1" by convention, regardless of whether the paper has other tables. It serves as the reader's first introduction to the data: who was studied, how they were characterized, and how groups compare.

You will learn the structure and construction of Table 1 in Session 4 and practice building one in Session 6.

> **Note:** Descriptive analysis is not a formality. It is where you discover problems that would invalidate downstream analyses. A model built on data you have not described is a model built on assumptions you have not verified.

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Lecture — Summarizing Continuous Clinical Variables (20 min)

### Two Ways to Summarize Continuous Variables

Continuous variables (measurements that can take any numeric value within a range) are summarized differently depending on their distribution shape.

**Mean +/- SD (standard deviation)**

- Use when the variable is approximately normally distributed (symmetric, bell-shaped).
- The mean represents the central tendency, and the SD describes how spread out the values are.
- **Interpretation:** "Mean age was 68.4 +/- 12.1 years" means the average patient was 68.4 years old, and most patients fell within roughly 56 to 81 years (one SD above and below the mean).

**Median [IQR] (interquartile range)**

- Use when the variable is skewed (asymmetric, with a long tail in one direction).
- The median is the middle value when all observations are sorted. The IQR spans the 25th to 75th percentile.
- **Interpretation:** "Median length of stay was 5 [3-9] days" means half the patients stayed 5 days or fewer, and the middle 50% of patients stayed between 3 and 9 days.

### When to Use Which

| Distribution Shape | Summary Statistic | Clinical Variables Typically This Shape |
|-------------------|-------------------|----------------------------------------|
| Symmetric (normal) | Mean +/- SD | Age, systolic BP, hemoglobin |
| Right-skewed | Median [IQR] | Length of stay, creatinine, number of medications, cost |
| Left-skewed | Median [IQR] | Rare in clinical data; sometimes percent adherence |

**How to check distribution shape:**

- Plot a histogram or density curve. If the peak is roughly centered with symmetric tails, use mean +/- SD. If there is a long tail to the right (right-skewed), use median [IQR].
- A practical shortcut: if the mean is substantially higher than the median, the distribution is right-skewed.

### Clinical Interpretation of Summary Statistics

Summary statistics are not just numbers. They tell you something about the patient population.

| Variable | Summary | Clinical Interpretation |
|----------|---------|----------------------|
| Age | Mean 68.4 +/- 12.1 years | This is an elderly population, typical of a general medical ward |
| Systolic BP | Mean 132.5 +/- 18.7 mmHg | Average BP is in the pre-hypertensive range; some patients likely have uncontrolled hypertension |
| Length of stay | Median 5 [3-9] days | Half of patients are discharged within 5 days; the right tail suggests a subset with prolonged stays (complications, social barriers) |
| Creatinine | Median 1.1 [0.8-1.8] mg/dL | The median is near normal, but the upper IQR (1.8) suggests a substantial proportion of patients have renal impairment |
| Hemoglobin | Mean 12.3 +/- 2.4 g/dL | Mean is below the lower limit of normal for males (13 g/dL), suggesting many patients in this population are anemic |
| HbA1c | Median 6.2 [5.5-7.8] % | The median is above the pre-diabetes threshold (5.7%), indicating a population with high diabetes prevalence |

### Summary Statistics for the Hospital Discharge Dataset

| Variable | Suggested Statistic | Reasoning |
|----------|-------------------|-----------|
| age | Mean +/- SD | Typically symmetric in adult hospital populations |
| charlson_score | Median [IQR] | Integer scores are often right-skewed (many patients with low scores, few with very high) |
| length_of_stay | Median [IQR] | Strongly right-skewed (most stays are short; a few are very long) |
| wbc | Median [IQR] | Often right-skewed due to patients with infections or leukemia |
| hemoglobin | Mean +/- SD | Typically approximately normal |
| creatinine | Median [IQR] | Right-skewed (most patients have normal renal function; a subset has elevated values) |
| hba1c | Median [IQR] | Right-skewed (bimodal: non-diabetic cluster near 5.5, diabetic cluster near 7-9) |
| systolic_bp | Mean +/- SD | Typically approximately normal |
| heart_rate | Mean +/- SD | Typically approximately normal |
| n_medications | Median [IQR] | Count data, often right-skewed |

> **Note:** The choice between mean +/- SD and median [IQR] is not arbitrary. Using mean +/- SD for a skewed variable gives a misleading picture. For example, if length of stay has a mean of 8.2 days but a median of 5 days, the mean is inflated by a few patients with very long stays. The median more accurately represents the "typical" patient.

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Lecture — Summarizing Categorical Clinical Variables (15 min)

### Counts and Percentages

Categorical variables are summarized using the number of observations in each category (n) and the percentage of the total (%).

**Standard format:** n (%)

**Examples from the hospital discharge dataset:**

| Variable | Category | n (%) | Clinical Interpretation |
|----------|----------|-------|----------------------|
| sex | Male | 245 (49.0%) | Roughly equal sex distribution |
| | Female | 255 (51.0%) | |
| admission_type | Emergency | 175 (35.0%) | One-third emergency admissions suggest a hospital with significant acute care volume |
| | Elective | 225 (45.0%) | Nearly half elective; this is typical of a hospital with active surgical programs |
| | Urgent | 100 (20.0%) | |
| icu_stay | Yes | 85 (17.0%) | 17% ICU utilization is within the typical range for general hospitals |
| | No | 415 (83.0%) | |
| readmission_30d | Yes | 75 (15.0%) | 15% readmission rate is moderate; national benchmarks vary by condition (heart failure ~25%, pneumonia ~18%) |
| | No | 425 (85.0%) | |

### What the Proportions Tell You

The proportions of categorical variables tell you about the hospital and the patient population:

- **35% emergency admissions vs 65% elective.** This suggests a hospital that performs a large volume of planned procedures (surgical, diagnostic). A trauma center would have a much higher emergency proportion.
- **17% ICU stay.** This reflects overall acuity. A hospital specializing in cardiac surgery or transplantation might have 30-40% ICU utilization.
- **15% 30-day readmission.** This is the outcome variable. A rate of 15% is typical for general medical admissions. Very high rates (>25%) suggest a population with complex chronic disease or inadequate discharge planning.

### Handling Missing Categories

Categorical variables can also have missing values. There are two approaches:

**Approach 1: Report missing as a separate category**

| Variable | Category | n (%) |
|----------|----------|-------|
| icu_stay | Yes | 85 (17.3%) |
| | No | 400 (81.6%) |
| | Missing | 5 (1.0%) |

- The denominator is the total sample (490 in this example).
- This approach is transparent and shows the reader exactly how much data is missing.

**Approach 2: Report percentages among non-missing only**

| Variable | Category | n (%) |
|----------|----------|-------|
| icu_stay | Yes | 85 (17.5%) |
| | No | 400 (82.5%) |

- The denominator excludes the 5 missing values (485 total).
- A footnote states: "5 patients (1.0%) had missing ICU stay status."

Both approaches are acceptable. The key is to be consistent throughout the table and to clearly state which denominator was used.

### Proportions by Group

When comparing groups (e.g., readmitted vs not readmitted), categorical variables are summarized within each group:

| Variable | Category | Readmitted (n=75) | Not Readmitted (n=425) |
|----------|----------|-------------------|----------------------|
| sex | Male | 40 (53.3%) | 205 (48.2%) |
| | Female | 35 (46.7%) | 220 (51.8%) |
| admission_type | Emergency | 35 (46.7%) | 140 (32.9%) |
| | Elective | 25 (33.3%) | 200 (47.1%) |
| | Urgent | 15 (20.0%) | 85 (20.0%) |

From this table, you can immediately see that readmitted patients had a higher proportion of emergency admissions (46.7% vs 32.9%) and a lower proportion of elective admissions (33.3% vs 47.1%). This pattern makes clinical sense: emergency admissions are often sicker and less stable at discharge.

> **Note:** Percentages should be calculated within each column (group), not across rows. Each column should sum to 100% for a given variable. This allows direct comparison of the distribution of a variable between groups.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Lecture — The Table 1 Convention (20 min)

### Structure of a Table 1

Table 1 is the standard descriptive summary table in clinical research publications. Its structure is consistent across journals:

- **Rows** = variables (one row per variable, or one row per category for categorical variables)
- **Columns** = overall sample + comparison groups (e.g., readmitted vs not readmitted)
- **Cells** = summary statistics (mean +/- SD, median [IQR], or n (%))
- **Final column** = p-value or standardized mean difference (SMD)

### Complete Example: Table 1 for the Hospital Discharge Dataset

| Variable | Overall (N=500) | Readmitted (n=75) | Not Readmitted (n=425) | P-value |
|----------|----------------|-------------------|----------------------|---------|
| Age, years, mean +/- SD | 68.4 +/- 12.1 | 72.1 +/- 11.3 | 67.8 +/- 12.2 | 0.004 |
| Sex, n (%) | | | | 0.43 |
|   Male | 245 (49.0) | 40 (53.3) | 205 (48.2) | |
|   Female | 255 (51.0) | 35 (46.7) | 220 (51.8) | |
| Admission type, n (%) | | | | 0.04 |
|   Emergency | 175 (35.0) | 35 (46.7) | 140 (32.9) | |
|   Elective | 225 (45.0) | 25 (33.3) | 200 (47.1) | |
|   Urgent | 100 (20.0) | 15 (20.0) | 85 (20.0) | |
| Charlson score, median [IQR] | 2 [1-4] | 3 [2-5] | 2 [1-3] | <0.001 |
| Length of stay, days, median [IQR] | 5 [3-9] | 8 [5-14] | 5 [3-8] | <0.001 |
| WBC, 10^3/uL, median [IQR] | 8.5 [6.2-11.8] | 10.2 [7.1-14.0] | 8.2 [6.0-11.2] | 0.003 |
| Hemoglobin, g/dL, mean +/- SD | 12.3 +/- 2.4 | 11.2 +/- 2.6 | 12.5 +/- 2.3 | <0.001 |
| Creatinine, mg/dL, median [IQR] | 1.1 [0.8-1.8] | 1.5 [0.9-2.4] | 1.0 [0.8-1.6] | 0.001 |
| HbA1c, %, median [IQR] | 6.2 [5.5-7.8] | 7.1 [5.8-8.9] | 6.0 [5.4-7.5] | 0.008 |
| Systolic BP, mmHg, mean +/- SD | 132.5 +/- 18.7 | 128.3 +/- 20.1 | 133.3 +/- 18.3 | 0.03 |
| Heart rate, bpm, mean +/- SD | 82.1 +/- 14.5 | 88.4 +/- 16.2 | 81.0 +/- 13.8 | <0.001 |
| Number of medications, median [IQR] | 7 [4-10] | 10 [7-14] | 6 [4-9] | <0.001 |
| ICU stay, n (%) | 85 (17.0) | 22 (29.3) | 63 (14.8) | 0.003 |

### Choosing the Right Test for Each Variable

The p-value in Table 1 tests whether the variable differs between the comparison groups. The appropriate test depends on the variable type and distribution.

| Variable Type | Distribution | Test | When to Use |
|--------------|-------------|------|-------------|
| Continuous | Normal | Independent t-test | Comparing means between two groups |
| Continuous | Skewed | Wilcoxon rank-sum (Mann-Whitney U) | Comparing medians/distributions between two groups |
| Categorical | 2x2 or larger | Chi-squared test | Expected cell counts all >= 5 |
| Categorical | 2x2 or larger | Fisher's exact test | Any expected cell count < 5 |

**Matching the test to the summary statistic:**

- If you report mean +/- SD, the corresponding test is the t-test.
- If you report median [IQR], the corresponding test is the Wilcoxon rank-sum test.
- If you report n (%), the corresponding test is chi-squared or Fisher's exact.

### Standardized Mean Difference (SMD)

An alternative to p-values for quantifying group differences is the standardized mean difference (SMD).

**What it is:** The difference between two group means divided by the pooled standard deviation. For categorical variables, a similar formula based on proportions is used.

**Why use SMD instead of p-values:**

- P-values are influenced by sample size. With a very large sample, even trivially small differences become "statistically significant." With a small sample, clinically important differences may not reach significance.
- SMD measures the magnitude of the difference regardless of sample size.
- **Rule of thumb:** SMD < 0.1 indicates negligible difference. SMD 0.1-0.2 indicates a small difference. SMD > 0.2 indicates a meaningful imbalance.

**When SMD is preferred:**

- In observational studies comparing matched cohorts (e.g., propensity score matching), SMD is the standard metric for assessing balance between groups.
- In randomized controlled trials, SMD is used alongside p-values to confirm that randomization achieved balance.

> **Note:** Table 1 is a description of the data, not a hypothesis test. The p-values indicate whether observed differences are likely due to chance, but they do not establish causation. A significant p-value for age (older patients readmitted more often) does not mean that age causes readmission; it means age differs between groups and should be considered in subsequent analyses.

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Lecture — Laboratory Reference Ranges and Clinical Thresholds (15 min)

### Normal Reference Ranges

Reference ranges define the interval within which a lab value falls for approximately 95% of healthy individuals. Values outside the reference range are "abnormal" and may indicate disease, though some healthy individuals naturally fall outside these bounds.

| Lab Value | Normal Range | Unit |
|-----------|-------------|------|
| WBC | 4-11 | 10^3/uL |
| Hemoglobin (female) | 12-16 | g/dL |
| Hemoglobin (male) | 13-17 | g/dL |
| Creatinine | 0.6-1.2 | mg/dL |
| HbA1c | < 5.7 | % |
| eGFR | > 90 | mL/min/1.73m^2 |

### Diagnostic Thresholds

Diagnostic thresholds are specific cutpoints used to classify patients into clinical categories. Unlike reference ranges (which describe the healthy population), diagnostic thresholds are decision points that trigger clinical actions.

| Threshold | Value | Clinical Classification |
|-----------|-------|----------------------|
| HbA1c >= 6.5% | 6.5 | Diabetes mellitus (ADA criterion) |
| HbA1c 5.7-6.4% | 5.7 | Pre-diabetes |
| HbA1c < 5.7% | 5.7 | Normal glycemia |
| eGFR < 60 mL/min/1.73m^2 | 60 | CKD stage 3 or worse |
| eGFR < 15 mL/min/1.73m^2 | 15 | CKD stage 5 (kidney failure) |
| WBC > 11 (10^3/uL) | 11 | Leukocytosis (elevated WBC) |
| WBC < 4 (10^3/uL) | 4 | Leukopenia (low WBC) |
| Hemoglobin < 12 g/dL (female) | 12 | Anemia (WHO definition) |
| Hemoglobin < 13 g/dL (male) | 13 | Anemia (WHO definition) |
| Systolic BP >= 140 mmHg | 140 | Hypertension stage 2 |
| Systolic BP >= 130 mmHg | 130 | Hypertension stage 1 (ACC/AHA 2017) |

### Why Thresholds Matter for Creating Analysis Variables

In clinical data analysis, you often need to convert continuous lab values into categorical variables based on these thresholds. This is because:

**Clinical decisions are threshold-based.** A clinician does not treat "creatinine 1.4 mg/dL" the same as "creatinine 3.8 mg/dL," even though both are continuous values above the reference range. The higher value triggers nephrology consultation, medication dose adjustments, and different risk stratification.

**Analysis variables must be clinically meaningful.** A variable called "diabetes_flag" (HbA1c >= 6.5%) is immediately interpretable in a multivariable model. A coefficient for "each 1% increase in HbA1c" is harder to translate into clinical action.

**Thresholds create natural groupings for Table 1.** Instead of reporting "mean creatinine 1.8 mg/dL," you can report "CKD stage 3 or worse (eGFR < 60): 120 (24.0%)" which is more actionable.

### Key Lab Thresholds Summary Table

| Derived Variable | Source Lab | Threshold | Clinical Meaning | Guideline Source |
|-----------------|-----------|-----------|-----------------|-----------------|
| Diabetes | HbA1c | >= 6.5% | Diabetes mellitus | ADA |
| Pre-diabetes | HbA1c | 5.7-6.4% | Impaired glucose metabolism | ADA |
| Anemia (F) | Hemoglobin | < 12 g/dL | Below normal for females | WHO |
| Anemia (M) | Hemoglobin | < 13 g/dL | Below normal for males | WHO |
| Leukocytosis | WBC | > 11 (10^3/uL) | Elevated WBC | Standard |
| Leukopenia | WBC | < 4 (10^3/uL) | Low WBC | Standard |
| CKD stage 3+ | eGFR | < 60 mL/min/1.73m^2 | Moderate-severe kidney disease | KDIGO |
| Hypertension | Systolic BP | >= 140 mmHg | Stage 2 hypertension | JNC |

> **Note:** Thresholds vary by guideline version and clinical context. The ACC/AHA 2017 guidelines lowered the hypertension threshold to 130 mmHg, while older JNC guidelines used 140 mmHg. Always document which guideline and threshold you used in your analysis, as this affects how many patients are classified as having the condition.

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Hands-on — Create a Table 1 (30 min)

In this session, you will construct a Table 1 for the hospital discharge dataset by hand (on paper or in a spreadsheet). This is not yet in Orange. The goal is to practice the full decision-making workflow of descriptive clinical analysis before automating it.

### The Task

Create a Table 1 comparing patients who were readmitted within 30 days (readmission_30d = Yes) versus those who were not (readmission_30d = No).

### Step 1: Classify Each Variable

For each variable in the dataset, determine whether it is continuous or categorical, and if continuous, whether it is likely normally distributed or skewed.

| Variable | Type | Distribution | Summary Statistic | Test |
|----------|------|-------------|-------------------|------|
| age | Continuous | Normal | Mean +/- SD | t-test |
| sex | Categorical | — | n (%) | Chi-squared |
| admission_type | Categorical | — | n (%) | Chi-squared |
| charlson_score | Continuous | Skewed | Median [IQR] | Wilcoxon |
| length_of_stay | Continuous | Skewed | Median [IQR] | Wilcoxon |
| wbc | Continuous | Skewed | Median [IQR] | Wilcoxon |
| hemoglobin | Continuous | Normal | Mean +/- SD | t-test |
| creatinine | Continuous | Skewed | Median [IQR] | Wilcoxon |
| hba1c | Continuous | Skewed | Median [IQR] | Wilcoxon |
| systolic_bp | Continuous | Normal | Mean +/- SD | t-test |
| heart_rate | Continuous | Normal | Mean +/- SD | t-test |
| n_medications | Continuous | Skewed | Median [IQR] | Wilcoxon |
| icu_stay | Categorical | — | n (%) | Chi-squared |

### Step 2: Compute Summary Statistics

Using the dataset (or provided summary values), fill in the following table. For each variable:

- Calculate the summary statistic for the overall sample
- Calculate the summary statistic for the readmitted group
- Calculate the summary statistic for the not-readmitted group

| Variable | Overall (N=___) | Readmitted (n=___) | Not Readmitted (n=___) |
|----------|----------------|-------------------|----------------------|
| Age, years, mean +/- SD | | | |
| Sex, n (%) | | | |
|   Male | | | |
|   Female | | | |
| Admission type, n (%) | | | |
|   Emergency | | | |
|   Elective | | | |
|   Urgent | | | |
| Charlson score, median [IQR] | | | |
| Length of stay, days, median [IQR] | | | |
| WBC, 10^3/uL, median [IQR] | | | |
| Hemoglobin, g/dL, mean +/- SD | | | |
| Creatinine, mg/dL, median [IQR] | | | |
| HbA1c, %, median [IQR] | | | |
| Systolic BP, mmHg, mean +/- SD | | | |
| Heart rate, bpm, mean +/- SD | | | |
| Number of medications, median [IQR] | | | |
| ICU stay, n (%) | | | |

### Step 3: Select and Apply the Right Test

For each variable, apply the appropriate statistical test (from the table in Step 1) and record the p-value.

**Decision rules:**

- Continuous + normal distribution: t-test. Report p-value.
- Continuous + skewed distribution: Wilcoxon rank-sum test. Report p-value.
- Categorical with all expected cell counts >= 5: Chi-squared test. Report p-value.
- Categorical with any expected cell count < 5: Fisher's exact test. Report p-value.

### Step 4: Interpret Your Table 1

After completing the table, answer the following questions:

1. **Which variables differ significantly between readmitted and not-readmitted patients?** List all variables with p < 0.05.

2. **What is the clinical profile of readmitted patients?** Based on the summary statistics, describe the "typical" readmitted patient compared to the "typical" non-readmitted patient. For example: "Readmitted patients were older (mean 72 vs 68 years), had higher comorbidity burden (median Charlson 3 vs 2), longer hospital stays (median 8 vs 5 days), and were more likely to have been admitted through the emergency department (47% vs 33%)."

3. **Are there any variables that do NOT differ between groups?** What does this tell you? For example, if sex does not differ, it suggests readmission risk is not strongly associated with sex in this population.

4. **Based on this descriptive analysis, which variables would you prioritize as predictors in a readmission prediction model?** Justify your choices based on both statistical significance and clinical plausibility.

### Step 5: Check Your Work

Common mistakes in Table 1 construction:

- **Wrong denominator for percentages.** Column percentages should sum to 100% within each group, not across groups.
- **Wrong summary statistic for the distribution.** If you used mean +/- SD for a skewed variable (e.g., length of stay), the mean will overestimate the "typical" value.
- **Mismatched test and summary statistic.** If you report median [IQR], the test should be Wilcoxon, not t-test.
- **Reporting overall p-value for multi-category variables.** For admission_type (3 categories), report one p-value for the overall chi-squared test, not separate p-values for each category.
- **Forgetting to state the sample size in each column header.** Always include N (overall) and n (per group).

> **Note:** Constructing Table 1 by hand before using software builds the clinical reasoning skills that no tool can automate. When you later use Orange or statistical software to generate Table 1, you will understand what each number means and can catch errors that the software will not flag.

[Back to Table of Contents](#table-of-contents)
