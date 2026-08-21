# Data Quality Assessment in Clinical Settings

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Session 1: Lecture — Why Clinical Data Has Quality Issues (15 min)](#session-1-lecture--why-clinical-data-has-quality-issues-15-min)
3. [Session 2: Lecture — Missing Data Mechanisms (20 min)](#session-2-lecture--missing-data-mechanisms-20-min)
4. [Session 3: Lecture — Data Plausibility and Validation (20 min)](#session-3-lecture--data-plausibility-and-validation-20-min)
5. [Session 4: Lecture — Temporal Data Quality (15 min)](#session-4-lecture--temporal-data-quality-15-min)
6. [Session 5: Hands-on — Data Quality Audit Checklist (25 min)](#session-5-hands-on--data-quality-audit-checklist-25-min)
7. [Session 6: Lecture — Handling Data Quality Issues (15 min)](#session-6-lecture--handling-data-quality-issues-15-min)

---

## Subtopics

- Why hospital data has inherent quality limitations (care-driven documentation, billing bias, provider variability)
- Three mechanisms of missing data: MCAR, MAR, and MNAR with clinical examples
- Reference ranges for plausibility checking of vital signs and laboratory values
- Detecting duplicate records, temporal inconsistencies, and logical contradictions
- How coding practice changes and system migrations introduce temporal data quality issues
- Structured data quality audit workflow: completeness, plausibility, consistency, timeliness
- Strategies for handling data quality problems and documenting decisions

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — Why Clinical Data Has Quality Issues (15 min)

### Data Is Collected for Care, Not Research

Hospital data exists because clinicians need to document patient encounters, order tests, and bill payers. Research is a secondary use. This fundamental mismatch means that clinical datasets are never as clean, complete, or standardized as purpose-built research databases.

**Key sources of data quality problems:**

**Billing-driven coding bias**

- **Upcoding for reimbursement.** Hospitals are reimbursed based on diagnosis codes. A patient with borderline heart failure may be coded as definitive heart failure if the reimbursement is higher. This inflates the apparent prevalence of certain conditions.
- **Undercoding for complexity.** Conversely, a patient with multiple comorbidities may have only the primary diagnosis coded because the coder ran out of time or the secondary diagnoses were not documented clearly in the notes.
- The result: diagnosis frequencies in administrative data do not perfectly reflect true disease prevalence.

**Documentation variability**

- **Provider differences.** One physician documents heart rate as "82 bpm" in a structured vital signs field. Another writes "HR 82" in a free-text note. The structured field is captured in the dataset; the free-text note is not.
- **Shift and unit variation.** ICU nurses record vital signs every 15 minutes with automated monitors. General ward nurses record them every 4-8 hours manually. The same patient transferred between units will have dramatically different data density.
- **Note fatigue.** Clinicians working overnight shifts or during high-census periods may enter minimal documentation, leading to missing values that are not clinically random but driven by workload.

**System migrations and data discontinuities**

- Hospitals change electronic health record (EHR) systems. When a hospital migrates from one EHR to another, historical data may be partially transferred, reformatted, or lost entirely.
- Variable names, coding schemes, and data structures may change across systems. A variable called "SBP" in the old system may become "systolic_blood_pressure" in the new one, and the mapping may not be perfect.
- These transitions create artificial breakpoints in longitudinal data that can be mistaken for clinical trends.

> **Note:** Understanding why clinical data has quality problems is not about blaming the system. It is about recognizing that every analysis must begin with a quality assessment, because the data was never designed to be a research-ready dataset.

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Lecture — Missing Data Mechanisms (20 min)

### Three Mechanisms of Missingness

Missing data is not just "missing." The reason why a value is missing determines how seriously it affects your analysis and which strategies are valid for handling it.

**MCAR: Missing Completely At Random**

- The probability of a value being missing is unrelated to any variable in the dataset, observed or unobserved.
- This is the most benign mechanism but also the rarest in clinical data.
- **Clinical example:** A blood sample is hemolyzed during transport to the lab, destroying the specimen. The hemolysis has nothing to do with the patient's clinical status. The lab result is missing for a purely mechanical reason.
- **Consequence:** Analyses using only complete cases are unbiased (but may lose statistical power due to smaller sample size).

**MAR: Missing At Random**

- The probability of a value being missing depends on other observed variables, but not on the missing value itself.
- This is the most common mechanism in clinical data.
- **Clinical example:** HbA1c is ordered primarily for patients with a diabetes diagnosis. Among patients without a diabetes diagnosis, HbA1c is missing. The missingness is explained by an observed variable (diabetes diagnosis), not by the HbA1c value itself.
- **Another example:** Older patients and sicker patients are more likely to have a complete metabolic panel ordered. Younger, healthier patients may have missing creatinine values. The missingness is predicted by age and comorbidity burden, both of which are observed.
- **Consequence:** Complete-case analysis is biased. Imputation methods that condition on observed variables (e.g., multiple imputation) can produce valid results.

**MNAR: Missing Not At Random**

- The probability of a value being missing depends on the value itself, even after accounting for observed variables.
- This is the most problematic mechanism and the hardest to detect.
- **Clinical example:** A critically high creatinine value triggers an instrument error flag, and the lab suppresses the result pending manual review. The result is missing precisely because it was extreme. The missing values are systematically different from the observed values.
- **Another example:** Patients who are too ill to tolerate a procedure (e.g., stress test) do not have the result recorded. The missing result is missing because the outcome would have been abnormal.
- **Consequence:** No standard imputation method can fully correct for MNAR. Sensitivity analyses are required.

### Comparison Table

| Mechanism | Missingness Depends On | Clinical Example | Analysis Impact |
|-----------|----------------------|------------------|-----------------|
| MCAR | Nothing (purely random) | Hemolyzed blood sample | Unbiased but less powerful |
| MAR | Observed variables | HbA1c missing for non-diabetics | Imputation using observed variables works |
| MNAR | The missing value itself | Extreme creatinine suppressed by lab instrument | No standard fix; sensitivity analysis needed |

### Why the Mechanism Matters

The mechanism determines your analysis strategy:

- If MCAR, you can safely drop incomplete rows (though you lose sample size).
- If MAR, you should use imputation methods that incorporate the variables predicting missingness.
- If MNAR, you must acknowledge the limitation and run sensitivity analyses comparing results under different assumptions.

In practice, you cannot definitively prove which mechanism is operating. But you can gather evidence by examining whether missingness patterns are associated with other observed variables. If they are, MAR is more plausible than MCAR. If clinical reasoning suggests the value itself determines whether it was recorded, MNAR should be suspected.

> **Note:** Most clinical datasets contain a mixture of mechanisms across different variables. HbA1c may be MAR (driven by diabetes diagnosis), while a missing creatinine in an ICU patient may be MCAR (specimen handling error). Assess each variable individually.

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Lecture — Data Plausibility and Validation (20 min)

### Reference Ranges for Plausibility Checking

Even when a value is present, it may be wrong. Data entry errors, unit conversion mistakes, and device malfunctions can produce values that are biologically impossible. Plausibility checking identifies these values by comparing them against known physiological limits.

The ranges below are not normal clinical ranges. They are the outer bounds of what is physiologically possible. Values outside these ranges are almost certainly errors.

| Variable | Plausible Range | Values Outside This Range |
|----------|----------------|--------------------------|
| Heart rate | 40-180 bpm | Below 40: possible if patient is on beta-blockers or has severe bradycardia, but verify. Above 180: possible in SVT, but verify. |
| Systolic BP | 60-250 mmHg | Below 60: incompatible with consciousness in most patients. Above 250: hypertensive emergency is real but rare above 250. |
| WBC | 2-30 (10^3/uL) | Below 2: severe neutropenia (possible but confirm). Above 30: possible in leukemia or severe infection, but verify. |
| Hemoglobin | 4-20 g/dL | Below 4: near-fatal anemia (possible but rare). Above 20: polycythemia vera or dehydration artifact. |
| Creatinine | 0.1-15 mg/dL | Below 0.1: likely a decimal error. Above 15: severe renal failure (possible in dialysis patients). |
| HbA1c | 3-16 % | Below 3: lab error. Above 16: extreme uncontrolled diabetes (rare but documented). |
| Temperature | 34-42 C | Below 34: hypothermia (intentional cooling or exposure). Above 42: heat stroke, malignant hyperthermia. |

**What to do with implausible values:**

1. **Flag, do not automatically delete.** An implausible value may be a real extreme case. A hemoglobin of 3.5 g/dL is rare but can occur in acute hemorrhage.
2. **Check the context.** Is the patient in the ICU? On dialysis? Post-surgical? Extreme values may be plausible in specific clinical contexts.
3. **Look for patterns.** If all implausible values come from the same date range or the same hospital unit, this suggests a systematic issue (device calibration, data entry protocol) rather than individual errors.

### Duplicate Record Detection

Duplicate records inflate sample sizes and bias results. In hospital data, duplicates can arise from:

- The same admission being entered twice due to system errors
- Data extraction pulling overlapping date ranges
- Merging data from multiple systems without proper deduplication

**How to detect duplicates:**

- Check for rows where the same patient_id has the same admission date, same diagnosis codes, and same lab values. Identical values across all fields strongly suggest duplication.
- Check for rows where the same patient_id appears with very similar (but not identical) values. Small differences in a single field (e.g., one lab value differs) may indicate re-entry with a correction.

### Temporal Consistency

Dates and times in clinical data must follow logical order:

- **Admission date must be before or equal to discharge date.** A discharge date before the admission date is a data error.
- **Lab results must fall within the encounter dates.** A lab result dated after discharge (unless it is an outpatient follow-up test linked to the wrong encounter) suggests a date mapping error.
- **Vital signs should have physiologically plausible rates of change.** A heart rate jumping from 70 to 200 between two measurements taken 5 minutes apart, without any documented clinical event, warrants investigation.

> **Note:** Plausibility checking is not about defining what is "normal." It is about defining what is possible. Normal ranges are narrower (e.g., heart rate 60-100). Plausibility ranges are wider and serve only to catch errors, not to identify clinical abnormalities.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Lecture — Temporal Data Quality (15 min)

### How Time Affects Data Quality

Clinical data collected over months or years is subject to temporal shifts that can introduce artifacts into your analysis. These are not errors in individual records but systematic changes in how data is generated.

**ICD coding version transitions**

- The transition from ICD-9 to ICD-10 (in the US, October 2015; in Taiwan, ICD-10-CM/PCS adoption followed a similar timeline) dramatically changed coding granularity.
- ICD-9 had approximately 13,000 codes. ICD-10 has approximately 68,000 codes.
- A condition coded as a single ICD-9 code may map to multiple ICD-10 codes with greater specificity. This can cause apparent changes in disease prevalence that are purely artifacts of the coding system, not real clinical trends.
- **Example:** "Diabetes mellitus" in ICD-9 (250.xx) becomes multiple codes in ICD-10 (E08-E13) distinguishing type 1, type 2, drug-induced, and other forms. A study spanning the transition may see an apparent drop in "diabetes" if only looking for the old code, or an apparent increase if the new codes capture cases that were previously lumped together.

**New lab assay adoption**

- When a hospital adopts a new laboratory analyzer or a new assay methodology, reference ranges may shift.
- **Example:** A new high-sensitivity troponin assay detects lower levels of cardiac injury than the previous assay. After adoption, more patients will test "positive" for troponin elevation. This is not an increase in cardiac events; it is a change in measurement sensitivity.
- If your dataset spans the transition, you must account for the assay change or restrict your analysis to one assay period.

**EHR system migrations**

- When a hospital switches EHR vendors (e.g., from a legacy system to Epic or Cerner), documentation patterns change.
- Structured fields may be added or removed. Previously free-text information may become structured, or vice versa.
- The migration period itself (typically weeks to months) often produces incomplete or inconsistent data as staff adapt to the new system.

**Seasonal patterns vs data artifacts**

- Some clinical patterns are genuinely seasonal (e.g., influenza admissions peak in winter, heat-related illness in summer).
- Other apparent seasonal patterns are artifacts: staffing changes in July (new residents in teaching hospitals), holiday-period documentation gaps, or fiscal-year-end coding rushes.
- **How to distinguish:** If the pattern aligns with known clinical seasonality and is consistent across multiple years, it is likely real. If it appears only once or correlates with administrative events, it is likely an artifact.

### How to Detect Temporal Issues

The most effective approach is to plot variable distributions over time:

1. **Plot monthly or quarterly summary statistics** (mean, median, proportion) for key variables. Look for sudden shifts, gradual trends, or discontinuities.
2. **Mark known events** on the timeline: EHR migration dates, ICD version transitions, lab assay changes, policy changes.
3. **Compare pre- and post-event distributions.** If a variable's distribution changes abruptly at a known event date, the change is likely an artifact rather than a clinical trend.

> **Note:** Temporal data quality issues are often invisible in cross-sectional summaries (means, medians, totals). They only become apparent when you examine data longitudinally. Always plot key variables over time before conducting any time-spanning analysis.

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Hands-on — Data Quality Audit Checklist (25 min)

In this session, you will work through a structured data quality audit using the hospital discharge dataset. This is a paper-based exercise: you will use summary statistics (provided or computed by hand) to fill in a data quality report.

### The Audit Framework

A data quality audit examines four dimensions:

1. **Completeness** — Are all expected values present?
2. **Plausibility** — Are the values within physiologically possible ranges?
3. **Consistency** — Do related variables agree with each other logically?
4. **Timeliness** — Are there temporal patterns that suggest data artifacts?

### Step 1: Completeness Assessment

For each variable in the hospital discharge dataset, determine the percentage of missing values and classify the likely missing data mechanism.

| Variable | % Missing | Likely Mechanism | Reasoning |
|----------|-----------|-----------------|-----------|
| patient_id | | | |
| age | | | |
| sex | | | |
| admission_type | | | |
| primary_dx_icd | | | |
| charlson_score | | | |
| length_of_stay | | | |
| wbc | | | |
| hemoglobin | | | |
| creatinine | | | |
| hba1c | | | |
| systolic_bp | | | |
| heart_rate | | | |
| n_medications | | | |
| icu_stay | | | |
| readmission_30d | | | |

**Questions to consider:**

- Which variables have the highest missingness? Why might that be?
- For hba1c, is the missingness more likely MCAR, MAR, or MNAR? What observed variable might predict whether HbA1c is missing?
- If a demographic variable like sex has missing values, what does that suggest about the data extraction process?

### Step 2: Plausibility Assessment

Using the reference ranges from Session 3, identify any values that fall outside plausible limits.

| Variable | Min in Data | Max in Data | Plausible Range | Any Implausible? |
|----------|-------------|-------------|-----------------|-----------------|
| age | | | 0-120 years | |
| heart_rate | | | 40-180 bpm | |
| systolic_bp | | | 60-250 mmHg | |
| wbc | | | 2-30 (10^3/uL) | |
| hemoglobin | | | 4-20 g/dL | |
| creatinine | | | 0.1-15 mg/dL | |
| hba1c | | | 3-16 % | |
| length_of_stay | | | 0-365 days | |

**Questions to consider:**

- If you find a hemoglobin value of 22 g/dL, what would you do? Delete it? Flag it? Check the diagnosis?
- If creatinine = 0.0, is this a plausible value or an error?
- How would you handle a length_of_stay of 0 days?

### Step 3: Consistency Assessment

Check for logical contradictions between related variables.

**Checks to perform:**

- Is there any patient with age < 18 and a diagnosis typically seen only in elderly patients (e.g., dementia, hip fracture from osteoporosis)?
- Is there any patient with length_of_stay = 0 but icu_stay = Yes? (If a patient was in the ICU, they should have stayed at least 1 day.)
- Is there any patient with hba1c < 5.7 (non-diabetic range) but a primary diagnosis code for diabetes (E11.x)?
- Is there any patient with readmission_30d = Yes but no discharge date recorded? (They cannot be readmitted if they were never discharged.)

| Consistency Check | Found Issues? | Description |
|-------------------|--------------|-------------|
| Pediatric age + geriatric diagnosis | | |
| Zero LOS + ICU stay | | |
| Normal HbA1c + diabetes diagnosis | | |
| Readmission without discharge | | |

### Step 4: Timeliness Assessment

If the dataset includes a date variable (or if you know the data collection period), check for temporal patterns.

**Questions to consider:**

- Does the number of records per month remain roughly constant, or are there sudden drops or spikes?
- If there is a gap in records (e.g., no data for one month), does this coincide with a known system migration or holiday period?
- Do any lab values show sudden shifts in their distributions at a specific time point?

### Completing the Audit Report

Summarize your findings in a data quality report with the following structure:

1. **Dataset overview:** Number of records, number of variables, data collection period
2. **Completeness summary:** Variables with >5% missing, likely mechanisms
3. **Plausibility issues:** Variables with values outside reference ranges, number of affected records
4. **Consistency issues:** Logical contradictions found, number of affected records
5. **Temporal issues:** Any detected discontinuities or artifacts
6. **Recommended actions:** For each issue, state whether you would exclude, impute, flag, or investigate further

> **Note:** This audit checklist is not just a course exercise. In real clinical data analysis, a version of this checklist should be completed before any statistical analysis begins. Many published studies in clinical informatics include a data quality assessment section describing exactly these steps.

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Lecture — Handling Data Quality Issues (15 min)

### Strategies for Addressing Data Quality Problems

Once you have identified data quality issues through your audit, you must decide how to handle each one. There is no single correct approach. The right strategy depends on the severity of the issue, the proportion of data affected, and the goals of your analysis.

**Strategy 1: Exclude implausible values**

- Replace values outside plausible reference ranges with missing values (do not delete the entire row).
- **Example:** Set hemoglobin = missing for any value above 20 g/dL or below 4 g/dL.
- **When to use:** When the value is clearly an error and cannot be verified against the source record.
- **Risk:** If many values are excluded, the resulting missingness may itself introduce bias.

**Strategy 2: Impute missing values**

- Replace missing values with estimated values based on observed data.
- **Common methods:**
  - **Mean/median imputation:** Replace with the variable's mean (for symmetric distributions) or median (for skewed distributions). Simple but ignores relationships between variables.
  - **Mode imputation:** Replace categorical missing values with the most frequent category.
  - **Multiple imputation:** Generate multiple plausible values based on a statistical model that accounts for relationships between variables. More sophisticated and statistically valid, but more complex to implement.
  - **Last observation carried forward (LOCF):** For longitudinal data, carry the last recorded value forward. Common in clinical trials but can introduce bias if the patient's condition is changing.
- Detailed hands-on practice with imputation will be covered in Meeting 11 using Orange.

**Strategy 3: Flag uncertain records**

- Add a binary flag variable (e.g., `data_quality_flag = 1`) to mark records with any identified quality issue.
- Do not remove these records from the primary analysis.
- **When to use:** When you are unsure whether a value is an error or a genuine extreme. Flagging preserves the data while allowing you to easily exclude flagged records in sensitivity analyses.

**Strategy 4: Sensitivity analysis**

- Run your analysis twice: once with the full dataset (including problematic records) and once with problematic records excluded.
- Compare the results. If the conclusions are the same, the data quality issue is not materially affecting your findings.
- If the conclusions differ, report both results and discuss the potential impact of data quality on the findings.
- **Example:** Your model predicts 30-day readmission with AUC = 0.72 using the full dataset. After excluding 50 records with implausible lab values, AUC = 0.74. The difference is small, suggesting the implausible values were not driving the model's performance.

### Document Everything

Every data quality decision must be documented:

- What issue was identified
- How many records were affected
- What action was taken (exclude, impute, flag, keep as-is)
- Why that action was chosen over alternatives

This documentation serves two purposes:

1. **Reproducibility.** Another analyst can replicate your decisions and reach the same cleaned dataset.
2. **Transparency.** Reviewers and readers of your results can assess whether your data cleaning choices may have influenced the findings.

> **Note:** There is a tension between data cleaning and data integrity. Aggressive cleaning (removing all questionable values) produces a "cleaner" dataset but may introduce selection bias if the removed records differ systematically from the retained ones. Conservative cleaning (keeping everything and using flags) preserves sample size but includes noise. The best approach is usually in between: remove clear errors, flag borderline cases, and test the impact with sensitivity analyses.

[Back to Table of Contents](#table-of-contents)
