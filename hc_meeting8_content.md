# Outcome Measurement and Clinical Endpoints

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Session 1: Lecture — What Makes a Good Clinical Outcome? (15 min)](#session-1-lecture--what-makes-a-good-clinical-outcome-15-min)
3. [Session 2: Lecture — Standard Hospital Outcomes (25 min)](#session-2-lecture--standard-hospital-outcomes-25-min)
4. [Session 3: Lecture — Comorbidity Measurement (20 min)](#session-3-lecture--comorbidity-measurement-20-min)
5. [Session 4: Lecture — Composite Endpoints and Competing Risks (15 min)](#session-4-lecture--composite-endpoints-and-competing-risks-15-min)
6. [Session 5: Lecture — Operationalizing Outcomes from Hospital Data (15 min)](#session-5-lecture--operationalizing-outcomes-from-hospital-data-15-min)
7. [Session 6: Hands-on — Define and Compute Clinical Outcomes (20 min)](#session-6-hands-on--define-and-compute-clinical-outcomes-20-min)

---

## Subtopics

- Criteria for clinically meaningful and measurable outcomes
- Hard outcomes vs. soft outcomes and surrogate endpoints
- Standard hospital outcomes: mortality, readmission, length of stay, ICU transfer, hospital-acquired complications
- Comorbidity measurement: Charlson Comorbidity Index and Elixhauser categories
- Composite endpoints: combining outcomes for statistical power and their trade-offs
- Competing risks in hospital data and how to handle them
- Operationalizing clinical outcomes from administrative and clinical data sources

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — What Makes a Good Clinical Outcome? (15 min)

Not every variable that changes during a hospital stay qualifies as a good clinical outcome for research. A well-chosen outcome must meet several criteria.

**Criteria for a good clinical outcome:**

**1. Clinically meaningful**

The outcome must matter to patients, clinicians, or the healthcare system. "Did the patient die?" matters. "Did the patient's albumin level change by 0.1 g/dL?" usually does not.

**2. Objectively measurable from available data**

The outcome must be identifiable from the data you actually have. You cannot use "patient satisfaction" as an outcome if your dataset contains only administrative records.

**3. Ascertainable within the follow-up window**

The outcome must occur (or be confirmed as not occurring) within the time frame your data covers. If your dataset captures only the index hospitalization, you cannot measure 1-year mortality unless you have linked follow-up data.

**4. Sufficient events for analysis**

There must be enough outcome events to support statistical analysis. A rule of thumb for logistic regression: at least 10 events per predictor variable. If only 15 patients died in your dataset and you have 13 predictors, your analysis will be unreliable.

**Hard outcomes vs. soft outcomes:**

| Category | Examples | Characteristics |
|----------|----------|----------------|
| Hard outcomes | Death, readmission, reoperation, major complication | Unambiguous, clinically important, clearly defined, hard to dispute |
| Soft outcomes | Lab value improvement, symptom score change, pain scale reduction | Subject to measurement error, may not reflect meaningful clinical change, easier to achieve |

Hard outcomes are preferred because they are objective and clinically unambiguous. A patient is either alive or dead; there is no measurement error. However, hard outcomes are often rare, requiring larger sample sizes.

Soft outcomes are more common but introduce subjectivity. A 10-point improvement on a pain scale may or may not represent meaningful relief for the patient.

**Surrogate endpoints:**

A surrogate endpoint is a measurable outcome that substitutes for a hard clinical outcome.

- **Example:** HbA1c is a surrogate for diabetes complications. Lowering HbA1c is expected to reduce cardiovascular events, but the surrogate (HbA1c) is not the same as the clinical outcome (heart attack, stroke).
- **Advantage:** Surrogate endpoints occur more frequently and sooner, making studies cheaper and faster.
- **Danger:** A treatment may improve the surrogate without improving the clinical outcome — or may even worsen it. This has happened historically (e.g., drugs that improved cardiac arrhythmia markers but increased mortality).

> **Note:** When choosing an outcome for hospital data analysis, start with hard outcomes. If the sample size is too small for hard outcomes, consider soft outcomes or surrogates — but explicitly acknowledge that the clinical relevance is weaker.

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Lecture — Standard Hospital Outcomes (25 min)

Hospital data supports several standard outcomes, each with a specific definition, data source, and set of considerations.

**1. In-hospital mortality**

- **Definition:** Death occurring during the hospitalization, identified from the discharge status field (e.g., discharge_status = "Expired" or "Deceased")
- **Data source:** Discharge summary, administrative records
- **Typical rate:** 2-5% for general hospital admissions; 10-25% for ICU admissions
- **Considerations:** Clear and unambiguous. Does not capture deaths occurring shortly after discharge. Influenced by hospital discharge practices (hospitals that discharge patients to hospice will have lower in-hospital mortality rates even if patients die soon after).

**2. 30-day readmission**

- **Definition:** An unplanned admission to any acute care hospital within 30 days of discharge from the index hospitalization
- **Data source:** Requires linking admissions by patient_id and computing the interval between discharge date (index) and admission date (subsequent)
- **CMS definition and exclusions:** The Centers for Medicare and Medicaid Services excludes planned readmissions (e.g., scheduled chemotherapy, staged surgeries), transfers to other acute care hospitals, and admissions for certain diagnoses unrelated to the index stay
- **Typical rate:** 10-20% depending on the patient population and condition
- **Considerations:** Requires follow-up data beyond the index admission. A patient discharged from Hospital A and readmitted to Hospital B will be missed if you have data from Hospital A only. The 30-day window is somewhat arbitrary but is a widely accepted standard.

**3. Length of stay (LOS)**

- **Definition:** Number of days from admission to discharge (discharge date minus admission date)
- **Data source:** Administrative records with admission and discharge dates
- **Typical values:** Median 3-5 days for general admissions; highly right-skewed (a few patients with stays of 30+ days pull the mean up)
- **Considerations:** Right-skewed distribution means the mean is misleading — always report the median. LOS is influenced by clinical factors (severity, complications) and non-clinical factors (bed availability, insurance authorization delays, disposition planning). A long LOS is not necessarily bad — it may mean thorough care.

**4. ICU transfer**

- **Definition:** Transfer from a general ward to an intensive care unit during the hospitalization
- **Data source:** Ward transfer records, bed management systems
- **Typical rate:** 5-10% of general admissions
- **Considerations:** Indicates clinical deterioration. Different from patients admitted directly to the ICU (who are typically sicker at baseline). Useful as an outcome for early warning score studies.

**5. Hospital-acquired complications**

| Complication | Definition | How Identified | Typical Rate |
|-------------|------------|----------------|--------------|
| Pressure injury | New skin breakdown developing after admission | ICD-10 codes (L89.x) flagged as present on admission = No | 2-5% of hospitalized patients |
| CAUTI | Catheter-associated urinary tract infection | ICD-10 codes + catheter documentation | 1-3 per 1,000 catheter-days |
| CLABSI | Central line-associated bloodstream infection | ICD-10 codes + central line documentation | 0.5-2 per 1,000 line-days |
| Falls | Patient fall during hospitalization | Incident reports, ICD-10 codes (W01-W19) | 3-5 per 1,000 patient-days |
| Medication errors | Wrong drug, dose, route, or timing | Incident reporting systems, pharmacy records | Varies widely by definition |

**Summary table of standard outcomes:**

| Outcome | Data Source | Type | Typical Rate | Key Limitation |
|---------|-----------|------|--------------|----------------|
| In-hospital mortality | Discharge status | Binary | 2-5% | Misses post-discharge deaths |
| 30-day readmission | Linked admissions by patient_id | Binary | 10-20% | Misses readmissions at other hospitals |
| Length of stay | Admission/discharge dates | Continuous | Median 3-5 days | Right-skewed; influenced by non-clinical factors |
| ICU transfer | Ward transfer records | Binary | 5-10% | Definition varies by hospital |
| Hospital-acquired complications | ICD-10 codes, quality indicators | Binary | Varies | Under-coded in administrative data |

> **Note:** The choice of outcome determines the entire analysis design. Define your outcome precisely before writing any analysis code. Ambiguous definitions (e.g., "readmission" without specifying planned vs. unplanned, or the time window) make results uninterpretable and non-comparable across studies.

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Lecture — Comorbidity Measurement (20 min)

Patients with more comorbidities have worse outcomes regardless of the exposure or treatment under study. Comorbidity measurement is therefore essential for risk adjustment in any hospital data analysis.

**Why comorbidity matters:**

A study comparing surgical outcomes between two hospitals must account for the possibility that one hospital serves sicker patients. Without comorbidity adjustment, differences in outcomes may reflect patient mix rather than quality of care.

**Charlson Comorbidity Index (CCI):**

Developed by Mary Charlson in 1987, this is the most widely used comorbidity measure. It assigns weighted scores to 17 conditions based on their association with 1-year mortality.

**All 17 Charlson conditions with ICD-10 code ranges and weights:**

| Weight | Condition | ICD-10 Code Range |
|--------|-----------|-------------------|
| 1 | Myocardial infarction | I21.x, I22.x, I25.2 |
| 1 | Congestive heart failure | I09.9, I11.0, I13.0, I13.2, I25.5, I42.0, I42.5-I42.9, I43.x, I50.x, P29.0 |
| 1 | Peripheral vascular disease | I70.x, I71.x, I73.1, I73.8, I73.9, I77.1, I79.0, I79.2, K55.1, K55.8, K55.9, Z95.8, Z95.9 |
| 1 | Cerebrovascular disease | G45.x, G46.x, H34.0, I60.x-I69.x |
| 1 | Dementia | F00.x-F03.x, F05.1, G30.x, G31.1 |
| 1 | Chronic pulmonary disease | I27.8, I27.9, J40.x-J47.x, J60.x-J67.x, J68.4, J70.1, J70.3 |
| 1 | Connective tissue disease | M05.x, M06.x, M31.5, M32.x-M34.x, M35.1, M35.3, M36.0 |
| 1 | Peptic ulcer disease | K25.x-K28.x |
| 1 | Mild liver disease | B18.x, K70.0-K70.3, K70.9, K71.3-K71.5, K71.7, K73.x, K74.x, K76.0, K76.2-K76.4, K76.8, K76.9, Z94.4 |
| 1 | Diabetes without complications | E10.0, E10.1, E10.6, E10.8, E10.9, E11.0, E11.1, E11.6, E11.8, E11.9, E12.0, E12.1, E12.6, E12.8, E12.9, E13.0, E13.1, E13.6, E13.8, E13.9, E14.0, E14.1, E14.6, E14.8, E14.9 |
| 2 | Diabetes with complications | E10.2-E10.5, E10.7, E11.2-E11.5, E11.7, E12.2-E12.5, E12.7, E13.2-E13.5, E13.7, E14.2-E14.5, E14.7 |
| 2 | Hemiplegia or paraplegia | G04.1, G11.4, G80.1, G80.2, G81.x, G82.x, G83.0-G83.4, G83.9 |
| 2 | Renal disease | I12.0, I13.1, N03.2-N03.7, N05.2-N05.7, N18.x, N19.x, N25.0, Z49.0-Z49.2, Z94.0, Z99.2 |
| 2 | Any malignancy (including leukemia and lymphoma) | C00.x-C26.x, C30.x-C34.x, C37.x-C41.x, C43.x, C45.x-C58.x, C60.x-C76.x, C81.x-C85.x, C88.x, C90.x-C97.x |
| 3 | Moderate to severe liver disease | I85.0, I85.9, I86.4, I98.2, K70.4, K71.1, K72.1, K72.9, K76.5, K76.6, K76.7 |
| 6 | Metastatic solid tumor | C77.x, C78.x, C79.x, C80.x |
| 6 | AIDS/HIV | B20.x-B22.x, B24.x |

**Computing the CCI:**

1. Identify which of the 17 conditions the patient has, based on ICD-10 codes in the medical record
2. Assign the corresponding weight to each condition present
3. Sum all weights to produce the Charlson score

**Interpretation:**

| Charlson Score | 10-Year Survival Estimate | Clinical Meaning |
|---------------|---------------------------|------------------|
| 0 | ~98% | No significant comorbidity burden |
| 1-2 | ~90% | Mild comorbidity |
| 3-4 | ~75% | Moderate comorbidity |
| 5+ | ~50% or lower | Severe comorbidity burden |

**Elixhauser Comorbidity Index:**

An alternative to the CCI, developed by Anne Elixhauser in 1998. It identifies 31 comorbid conditions and does not assign fixed weights — instead, each condition enters a regression model separately, allowing the data to determine the relative importance.

**Key differences between CCI and Elixhauser:**

| Feature | Charlson | Elixhauser |
|---------|----------|------------|
| Number of conditions | 17 | 31 |
| Weighting | Fixed weights (1, 2, 3, 6) | No fixed weights; model-derived |
| Breadth | Focused on mortality-predictive conditions | Broader; includes obesity, depression, substance abuse, fluid/electrolyte disorders |
| Output | Single summary score | 31 binary indicators (or a derived summary score) |
| Best for | Quick risk stratification; comparing across studies | Detailed risk adjustment in regression models |

**When to use which:**

- Use **CCI** when you need a single summary score for quick risk stratification, or when comparing across studies that also used CCI
- Use **Elixhauser** when building detailed regression models where each comorbidity may have a different effect on the outcome

**In the hospital discharge dataset:**

The variable charlson_score provides the pre-computed CCI. In practice, this score would have been computed from the patient's ICD-10 diagnosis codes using the table above. The dataset simplifies this step for you, but understanding how it is computed is essential for interpreting it correctly.

> **Note:** Both CCI and Elixhauser depend entirely on the completeness and accuracy of ICD-10 coding. If a condition is present but not coded, it will not be captured. Administrative data tends to under-code certain conditions (e.g., dementia, depression), which means comorbidity scores from administrative data are systematically lower than those derived from clinical chart review.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Lecture — Composite Endpoints and Competing Risks (15 min)

**Composite endpoints:**

A composite endpoint combines two or more individual outcomes into a single binary variable: the event occurred if any one of the component outcomes occurred.

**Example:** A composite of "death OR readmission OR ICU transfer." A patient who experienced any one of these events is counted as having the composite outcome.

**Why use composite endpoints:**

- **More events:** Each individual outcome may be rare. Combining them increases the total number of events, providing more statistical power.
- **Broader clinical picture:** A single outcome may miss the full spectrum of adverse events. A patient who avoids readmission but is transferred to the ICU during the same stay has still had a bad outcome.

**The trade-off:**

| Advantage | Disadvantage |
|-----------|-------------|
| More events for analysis | Mixes outcomes of different clinical severity |
| Increased statistical power | A "positive" result may be driven by the least severe component |
| Captures broader spectrum of adverse events | Harder to interpret clinically: what does "death OR readmission" mean for decision-making? |

**Example of the severity problem:**

Suppose 100 patients have the composite outcome. Of these, 5 died, 10 were transferred to ICU, and 85 were readmitted. The composite result is dominated by readmission, which is the least severe component. A treatment that reduces the composite by 20% may be reducing readmissions without affecting mortality at all. Reporting only the composite hides this distinction.

**Best practice:** Always report both the composite and each component outcome separately. This allows the reader to judge whether the effect is clinically meaningful.

**Competing risks:**

A competing risk is an event that prevents the outcome of interest from occurring.

**The classic example in hospital data: discharge competes with in-hospital death.**

- If you are studying in-hospital mortality, a patient who is discharged alive on day 3 can no longer die in the hospital. Discharge is a competing risk for in-hospital death.
- If you are studying 30-day readmission, a patient who dies in the hospital cannot be readmitted. Death is a competing risk for readmission.

**Why competing risks matter:**

Standard survival analysis (Kaplan-Meier curves, Cox regression) assumes that patients who are censored (i.e., whose outcome is not observed) have the same future risk as those who remain under observation. But a patient who died has zero future risk of readmission — they are fundamentally different from a patient who was lost to follow-up. Treating death the same as loss to follow-up overestimates the probability of readmission.

**How to handle competing risks:**

1. **Define outcome windows carefully:** For 30-day readmission, clearly state whether patients who died within 30 days are counted as "no readmission" (which inflates the apparent success rate) or excluded (which reduces sample size) or treated as a competing event.
2. **Use competing risk methods:** The Fine-Gray subdistribution hazard model accounts for competing risks by modeling the cumulative incidence of the outcome while keeping competing events in the risk set.
3. **Report both outcomes:** When death competes with readmission, report both the readmission rate and the mortality rate. A hospital with low readmission but high mortality is not providing better care — it has fewer readmissions because more patients died.

> **Note:** Competing risks are easy to overlook but can fundamentally change the conclusions of a study. Whenever your outcome is a non-fatal event (readmission, complication, ICU transfer), ask: "What other events could prevent this outcome from occurring?" If the answer is death or discharge, you have a competing risk problem.

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Lecture — Operationalizing Outcomes from Hospital Data (15 min)

Operationalizing an outcome means translating a clinical concept into a specific, computable definition using the data available. The same clinical concept can have different operational definitions depending on the data source.

**Administrative vs. clinical outcome definitions:**

| Outcome | Administrative Definition | Clinical Definition |
|---------|--------------------------|---------------------|
| 30-day readmission | Any admission within 30 days of discharge (from billing records) | Unplanned admission for a condition related to the index hospitalization (requires chart review) |
| Pneumonia | ICD-10 code J18.x in any diagnosis position | Positive chest X-ray + clinical symptoms + microbiological confirmation |
| Acute kidney injury | ICD-10 code N17.x | Serum creatinine increase of 0.3 mg/dL within 48 hours (KDIGO criteria) |
| Sepsis | ICD-10 codes A41.x, R65.2x | Sequential Organ Failure Assessment (SOFA) score increase of 2+ with suspected infection |

Administrative definitions are convenient (already coded in the data) but less accurate. Clinical definitions are precise but require access to granular clinical data (lab values, imaging results, clinical notes).

**Date arithmetic for time-based outcomes:**

Many hospital outcomes are defined by time intervals. Computing these requires careful date handling.

**Example: 30-day readmission**

```
readmission_30d = 1 if (next_admission_date - index_discharge_date) <= 30 days
readmission_30d = 0 if (next_admission_date - index_discharge_date) > 30 days OR no next admission
```

**Common pitfalls in date arithmetic:**

- **Same-day events:** Is a patient who is discharged and readmitted on the same day counted as day 0 or day 1? The definition must specify.
- **Missing discharge dates:** If the discharge date is missing, the outcome cannot be computed. These patients must be excluded, not assumed.
- **Transfers vs. readmissions:** A patient transferred from Hospital A to Hospital B on the same day is not readmitted — they are transferred. Distinguishing transfers from readmissions requires admission source information.

**Creating binary outcome variables from raw data:**

Most outcomes start as raw data and must be converted to binary (Yes/No or 1/0) variables for analysis.

**Steps:**

1. Define the outcome precisely in words
2. Identify which variables in the dataset are needed to compute it
3. Write the logical rule that converts raw data to binary
4. Apply the rule and verify against known cases

**Examples using the hospital discharge dataset variables:**

| Outcome Variable | Input Variables | Rule |
|-----------------|-----------------|------|
| readmission_30d | admission_date, discharge_date, next_admission_date | 1 if next admission within 30 days; 0 otherwise |
| prolonged_stay | length_of_stay | 1 if length_of_stay > 7 days; 0 otherwise |
| high_comorbidity | charlson_score | 1 if charlson_score >= 3; 0 otherwise |
| abnormal_creatinine | creatinine | 1 if creatinine > 1.2 mg/dL; 0 otherwise |
| polypharmacy | n_medications | 1 if n_medications >= 5; 0 otherwise |

**Important:** The threshold for converting a continuous variable to binary (e.g., "prolonged stay" = LOS > 7 days) is a judgment call. Different thresholds will produce different results. Always justify the threshold with clinical rationale or published standards, and consider sensitivity analyses using alternative thresholds.

> **Note:** Operationalizing outcomes is where clinical knowledge meets data analysis. A technically correct analysis with a poorly defined outcome is clinically useless. Spend time getting the definition right before running any statistics.

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Hands-on — Define and Compute Clinical Outcomes (20 min)

In this exercise, you will work through four tasks using the hospital discharge dataset. The goal is to practice defining, computing, and interpreting clinical outcomes.

**Task 1: Define readmission_30d from admission and discharge dates**

The dataset already contains readmission_30d as a variable. But how would it be computed from raw data?

Discussion points:

- The computation requires two linked admissions for the same patient_id: the index admission (with its discharge date) and the next admission (with its admission date)
- The rule: readmission_30d = Yes if (next_admission_date - index_discharge_date) is between 0 and 30 days
- What about patients with no subsequent admission? They are coded as readmission_30d = No
- What about patients who died during the index hospitalization? They cannot be readmitted. They should be excluded from readmission analysis (this is a competing risk, as discussed in Session 4)
- How would you handle patients readmitted to a different hospital? With single-center data, you cannot — this is a limitation

**Task 2: Interpret the Charlson Comorbidity Index**

The dataset contains charlson_score. Using the scoring table from Session 3:

1. What does a charlson_score of 0 mean? (No comorbidities from the 17-condition list)
2. What does a charlson_score of 6 mean? (Could be one condition with weight 6, such as metastatic cancer or AIDS, or a combination of lower-weighted conditions)
3. If you wanted to compute this from raw ICD-10 codes instead of using the pre-computed score:
   - You would need all diagnosis codes for each patient (primary and secondary)
   - Map each code to the 17 Charlson conditions using the code ranges in the Session 3 table
   - Assign weights, sum them
   - Handle hierarchy rules: if a patient has both "diabetes without complications" (weight 1) and "diabetes with complications" (weight 2), count only the higher weight
4. What are the limitations of using the pre-computed score without access to the underlying codes?

**Task 3: Create a composite outcome**

Define a composite outcome: composite_adverse = Yes if the patient died OR was readmitted within 30 days.

Using the dataset:

1. The dataset has readmission_30d. If in-hospital mortality is available (e.g., from a discharge status variable), combine them.
2. If in-hospital mortality is not directly available, discuss which available variables might serve as proxies (e.g., very short length_of_stay with certain diagnoses could suggest death, but this is speculative without a mortality variable).
3. Create the composite rule:
   - composite_adverse = Yes if readmission_30d = Yes OR died = Yes
   - composite_adverse = No if readmission_30d = No AND died = No
4. What is the expected rate of the composite outcome compared to readmission alone? (It should be equal to or higher, since it adds death events)
5. Discuss the severity mixing problem: is death and readmission the same thing clinically? Why or why not?

**Task 4: Connect outcomes to comparison groups and confounders (linking to Meeting 07)**

For each of the three outcomes (readmission_30d, charlson_score as a risk factor, composite_adverse), discuss:

1. What is the primary comparison group?
   - For readmission: readmitted vs. not readmitted — what factors differ between groups?
   - For Charlson score as a predictor: high comorbidity vs. low comorbidity — is the threshold 2? 3? Justify.
   - For composite: adverse event vs. no adverse event
2. What confounders (from Meeting 07) would you need to adjust for?
   - Age, sex, admission type, and other clinical variables
   - Which variables are confounders vs. mediators for each outcome?
3. What unmeasured confounders remain?
   - Socioeconomic status, health literacy, discharge disposition, caregiver support
4. Which adjustment strategy from Meeting 07 (restriction, stratification, regression, propensity scores) would be most appropriate for each outcome and why?

**Summary discussion:**

- Outcome definition is not a mechanical exercise. It requires clinical judgment at every step: what to measure, how to define it, what threshold to use, how to handle edge cases.
- The same dataset can support multiple outcomes, each answering a different clinical question.
- Every outcome definition involves trade-offs between clinical precision and data availability.
- Connecting outcome definition (this meeting) to confounding and bias (Meeting 07) is the foundation of a well-designed clinical data analysis.

> **Note:** In a real analysis, you would document every decision made during outcome operationalization: the exact definition, the variables used, the thresholds chosen, and the justification for each. This documentation is part of the study protocol and ensures that the analysis is reproducible and transparent.

[Back to Table of Contents](#table-of-contents)
