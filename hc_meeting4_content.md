# Clinical Study Design with Hospital Data

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Session 1: Lecture — Why Study Design Matters for Clinical Data (10 min)](#session-1-lecture--why-study-design-matters-for-clinical-data-10-min)
3. [Session 2: Lecture — Retrospective Cohort Design (20 min)](#session-2-lecture--retrospective-cohort-design-20-min)
4. [Session 3: Lecture — Case-Control and Cross-Sectional Designs (15 min)](#session-3-lecture--case-control-and-cross-sectional-designs-15-min)
5. [Session 4: Lecture — Defining Study Populations (20 min)](#session-4-lecture--defining-study-populations-20-min)
6. [Session 5: Lecture — Common Biases in Hospital Data Studies (15 min)](#session-5-lecture--common-biases-in-hospital-data-studies-15-min)
7. [Session 6: Hands-on — Design a Clinical Study (30 min)](#session-6-hands-on--design-a-clinical-study-30-min)

---

## Subtopics

- Why study design choices determine the validity of hospital data analysis
- Retrospective cohort design: cohort entry, exposure, follow-up, and outcome
- Case-control and cross-sectional designs with hospital data
- Defining study populations using ICD-10 codes, inclusion/exclusion criteria, and index dates
- Common biases in observational hospital data studies
- Practical exercise in designing a clinical study from a hospital dataset

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — Why Study Design Matters for Clinical Data (10 min)

Hospital data was collected for patient care, billing, and administration — not for research. Every clinical dataset is a byproduct of healthcare delivery. This means the data reflects how care was actually delivered, not how a study would ideally be designed.

**The fundamental challenge:**

When you analyze hospital data, you are not running an experiment. You did not randomly assign patients to treatments. You did not control which patients received which tests. You did not decide who was admitted and who was not. The data simply records what happened.

**Design choices determine validity:**

Two researchers can use the same hospital database and reach opposite conclusions — not because they made calculation errors, but because they made different design choices:

- How they defined "who is in the study" (study population)
- When they started counting (index date)
- How they defined "exposed" vs. "unexposed" (exposure definition)
- How long they followed patients (follow-up period)
- What outcome they measured and when they measured it (outcome ascertainment)

**Garbage in, garbage out:**

- If your study population includes patients who should have been excluded (e.g., patients already on treatment when you are studying treatment initiation), your results will be biased.
- If your data does not cover a full 30 days after discharge for every patient (e.g., patients discharged near the end of the dataset), their readmission outcome is missing — not "No." This is right censoring, and should be handled appropriately (e.g., using time-to-event methods that account for censoring, or restricting the outcome window to a period fully covered by the data).
- If you count time before treatment starts as treated time, patients in the treatment group appear to survive longer simply because they had to be alive to receive treatment — creating a false treatment effect.

**Most hospital data analysis is observational:**

- **Observational:** The researcher observes what happened without intervening. Treatments, exposures, and outcomes were determined by clinical decisions, not random assignment.
- **Randomized (experimental):** Patients are randomly assigned to treatment or control groups. This eliminates confounding by balancing known and unknown factors between groups.

Hospital data is almost always observational. This means confounding, selection bias, and measurement error are always present. Study design is the primary tool for minimizing these threats.

> **Note:** A well-designed observational study with hospital data can produce valid, clinically useful findings. A poorly designed one will produce confident but wrong conclusions. The difference is not in the statistical method — it is in the design.

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Lecture — Retrospective Cohort Design (20 min)

The retrospective cohort study is the most common design for hospital data analysis. You start with a defined group of patients (the cohort), identify their exposures, and follow them forward in time to observe outcomes — but all of this is done by looking backward through existing records.

**Key Components**

**1. Cohort Entry (Index Date)**

The moment a patient enters the study. This must be clearly defined and consistently applied to every patient.

Common index date definitions:

| Index Date | When to Use | Example |
|-----------|-------------|---------|
| Admission date | Studies of in-hospital events | "Patients admitted with pneumonia" |
| Date of first diagnosis | Studies of disease progression | "Patients first diagnosed with heart failure" |
| Date of first prescription | Studies of medication effects | "Patients who started metformin" |
| Discharge date | Studies of post-discharge outcomes | "Patients discharged after cardiac surgery" |

**2. Exposure Definition**

The factor you are investigating. In hospital data, common exposures include:

- Medications (e.g., metformin use vs. no metformin use)
- Procedures (e.g., received percutaneous coronary intervention vs. medical management only)
- Clinical characteristics (e.g., ICU stay vs. no ICU stay)
- Care processes (e.g., early rehabilitation vs. no early rehabilitation)

**3. Follow-up Period**

The time window during which you observe patients for the outcome. Must be the same for all patients (or accounted for with survival analysis).

**4. Outcome Ascertainment Window**

When and how you determine whether the outcome occurred.

**Example: Metformin and 30-Day Readmission in Heart Failure**

**Research question:** Among patients admitted with heart failure (ICD-10: I50.x), does metformin use reduce 30-day readmission?

**Design:**

| Component | Definition |
|-----------|-----------|
| **Study population** | Adults (age >= 18) discharged alive from an admission with a primary diagnosis of heart failure (I50.x). Excludes in-hospital deaths and inter-facility transfers (these patients never reach the index date). |
| **Index date** | Discharge date |
| **Exposed group** | Patients with metformin on their discharge medication list |
| **Unexposed group** | Patients without metformin on their discharge medication list |
| **Follow-up period** | 30 days from discharge |
| **Outcome** | Any hospital readmission within 30 days of discharge |
| **Exclusion criteria** | Discharged to hospice or palliative care (readmission is not a meaningful outcome), planned readmission already scheduled at discharge (not an unplanned event) |

**Timeline Diagram**

```
Admission     Discharge        30 days post-discharge
   |              |                      |
   |   Index      |                      |
   | Admission    |<--- Follow-up ------>|
   |              |                      |
   |   Exposure   |   Outcome            |
   |   window:    |   ascertainment      |
   |   Discharge  |   window:            |
   |   med list   |   Any readmission    |
   |              |   within 30 days     |
   v              v                      v

--- Cohort entry is DISCHARGE DATE ---

Not in cohort (never reach index date):
  Patient X: Admitted --- Died during admission --- No discharge date
  Patient Y: Admitted --- Transferred to another facility --- No discharge date

In cohort but excluded (known at discharge):
  Patient Z: Discharged to hospice --- Readmission not meaningful --- Excluded
  Patient W: Planned readmission scheduled at discharge ----------- Excluded

In cohort and analyzed:
  Patient A: Discharged Day 0 --- No readmission by Day 30 --- Outcome: No
  Patient B: Discharged Day 0 --- Readmitted Day 12 ---------- Outcome: Yes
  Patient C: Discharged Day 0 --- Died Day 8 (not readmitted)- Outcome: No (competing risk)
  Patient D: Discharged Day 0 --- Data ends Day 15 ----------- Outcome: Missing (right censored)
```

**Critical Design Decisions**

- **Why discharge date, not admission date?** Because the exposure (discharge medications) is not known until discharge. Using admission date would include time before the exposure is determined.
- **Why are in-hospital deaths and transfers in the population definition, not exclusion criteria?** Because these patients never reach the index date (discharge). They do not enter the cohort in the first place — there is no inference point for them.
- **Why exclude hospice discharges?** Because readmission is not a clinically meaningful outcome for patients discharged to end-of-life care. This is known at discharge and happens after inclusion (admission with heart failure).
- **Why exclude planned readmissions?** Because a scheduled return (e.g., for a cardiac procedure) is not an unplanned readmission. This is known at discharge and would contaminate the outcome if counted.

> **Note:** The timeline diagram is the single most important element of a cohort study design. If you cannot draw a clear timeline showing when each patient enters the study, when the exposure is measured, and when the outcome is assessed, your design is not well-defined.

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Lecture — Case-Control and Cross-Sectional Designs (15 min)

While the retrospective cohort is the most common design for hospital data, two other designs are frequently encountered: case-control and cross-sectional.

**Case-Control Design**

In a case-control study, you start from the outcome and look backward at exposures.

**Structure:**

1. **Cases:** Patients who experienced the outcome (e.g., patients who were readmitted within 30 days)
2. **Controls:** Patients who did not experience the outcome (e.g., patients who were not readmitted within 30 days)
3. **Comparison:** Look back at prior exposures and characteristics to find what differs between cases and controls

**When to Use Case-Control with Hospital Data:**

- **Rare outcomes** — If your outcome occurs in fewer than 5% of patients, a cohort study requires a very large sample to detect an effect. A case-control design efficiently samples from the rare outcome group.
- **Example:** Hospital-acquired infections occur in approximately 2-5% of admissions. To study risk factors, select all patients with hospital-acquired infections (cases) and a sample of matched patients without infections (controls), then compare their exposures (e.g., catheter use, surgical procedures, ICU stay).

**Matching in Case-Control Studies:**

Controls are often matched to cases on key variables to reduce confounding:

| Matching Variable | Rationale |
|------------------|-----------|
| Age (within 5 years) | Age affects nearly all clinical outcomes |
| Sex | Biological differences in disease risk |
| Admission year | Controls for changes in clinical practice over time |
| Hospital (in multi-center studies) | Controls for institutional differences |

**Measure of Association:** Case-control studies produce **odds ratios**, not relative risks, because you cannot calculate incidence rates (you sampled on the outcome, not the exposure).

**Cross-Sectional Design**

A cross-sectional study examines data at a single point in time. There is no follow-up period.

**Structure:**

1. Define a population at a specific time point (e.g., all patients in the hospital on January 1, 2024)
2. Measure exposures and outcomes simultaneously
3. Calculate prevalence, not incidence

**When to Use Cross-Sectional with Hospital Data:**

- **Prevalence studies** — What proportion of patients currently in the ICU have a central line?
- **Resource utilization snapshots** — How many beds are occupied? What is the average nurse-to-patient ratio on a given day?
- **Surveys** — Patient satisfaction measured at one point during the hospital stay

**Limitations of Each Design with Hospital Data**

| Design | Key Limitation | Why It Matters |
|--------|---------------|---------------|
| **Case-Control** | Recall bias (less relevant in hospital data since records are documented, not recalled) but selection of controls can introduce bias | If controls are not representative of the source population, odds ratios will be biased |
| **Case-Control** | Cannot calculate incidence or absolute risk | You can say "the odds of catheter use are 3 times higher in patients with infections" but you cannot say "catheter use causes a 15% increase in infection rate" |
| **Cross-Sectional** | Cannot determine temporal sequence | You observe that ICU patients have higher creatinine, but you cannot tell if high creatinine led to ICU admission or ICU-related events caused creatinine to rise |
| **Cross-Sectional** | Prevalence bias (Neyman bias) | Rapidly fatal conditions are underrepresented in cross-sectional snapshots because those patients die before being captured |

**Choosing Between Designs**

| Scenario | Recommended Design |
|----------|-------------------|
| Common outcome, clear temporal sequence | Retrospective cohort |
| Rare outcome (< 5% incidence) | Case-control |
| Descriptive snapshot, no causal question | Cross-sectional |
| Medication effect on readmission | Retrospective cohort |
| Risk factors for hospital-acquired MRSA | Case-control |
| Current ICU bed utilization | Cross-sectional |

> **Note:** In practice, most hospital data analyses use the retrospective cohort design because hospital databases naturally record events in chronological order, making it straightforward to define cohort entry, exposures, and outcomes along a timeline.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Lecture — Defining Study Populations (20 min)

The study population is the group of patients included in your analysis. Defining it precisely is critical — vague or incorrect population definitions are the most common source of bias in hospital data studies.

**Inclusion Criteria Using ICD-10 Codes**

ICD-10 codes are the primary tool for identifying patients with specific conditions in hospital data.

**How ICD-10 Codes Work:**

- Hierarchical structure: Category (3 characters) > Etiology/Anatomy/Severity (4th-5th characters) > Extension (6th-7th characters)
- Example: **I50** = Heart failure
  - I50.1 = Left ventricular failure
  - I50.2 = Systolic (congestive) heart failure
  - I50.20 = Unspecified systolic heart failure
  - I50.21 = Acute systolic heart failure
  - I50.22 = Chronic systolic heart failure
  - I50.23 = Acute on chronic systolic heart failure
  - I50.9 = Heart failure, unspecified

- Using **I50.x** (the parent code with wildcard) captures all subtypes of heart failure
- Using **I50.22** captures only chronic systolic heart failure — a much narrower and more specific population

**Common Inclusion/Exclusion Criteria**

**Inclusion criteria** define who enters the cohort. They are conditions **known at the time of enrollment** (the index date).

**Exclusion criteria** remove patients **after enrollment**. They are conditions that happen after the index date but are known before the outcome occurs.

| Criterion | Type | Rationale |
|-----------|------|-----------|
| Age >= 18 years | Inclusion | Known at enrollment. Pediatric patients have different disease patterns |
| Primary diagnosis matches target condition | Inclusion | Known at enrollment. Defines the clinical population |
| Discharged alive (for post-discharge outcomes) | Inclusion | Known at index date. Patients who died never reach the index date |
| No prior use of study medication within washout period | Inclusion | Known at enrollment. Ensures new users |
| Length of stay >= 1 day | Inclusion | Known at index date. Removes observation stays or same-day procedures |
| Not transferred from/to another facility | Inclusion | Known at enrollment/index date. Defines the population with complete data |
| Primary diagnosis code is present | Inclusion | Known at enrollment. Cannot confirm condition without it |
| Discharged to hospice or palliative care | Exclusion | Determined during admission, known at discharge but not at admission. Readmission is not a meaningful outcome for end-of-life patients |
| Planned readmission scheduled at discharge | Exclusion | Determined during admission, known at discharge but not at admission. A scheduled return is not an unplanned event |
| Comfort care / DNR order placed after admission | Exclusion | Determined after enrollment. Expected deaths are not treatment failures |

**Index Date Definition**

The index date anchors every patient's timeline. It must be:

- **Unambiguous:** Every patient has exactly one index date
- **Clinically meaningful:** Corresponds to a relevant clinical event
- **Consistent:** Defined the same way for all patients

**Example index date pitfall:** If you define the index date as "date of heart failure diagnosis" but a patient has three admissions with I50.x codes, which one is the index date? Usually the **first** diagnosis date — but this must be explicitly stated.

**Washout Periods for Medication Studies**

A washout period is a window of time before the index date during which a patient must not have used the medication of interest. This ensures you are studying **new users**, not patients already on long-term treatment.

**Example:** To study the effect of metformin initiation on HbA1c:
- Index date: Date of first metformin prescription
- Washout period: 180 days (6 months) before the index date with no metformin prescriptions
- Purpose: Ensures the patient is truly starting metformin, not refilling an existing prescription

Without a washout period, your "new users" group is contaminated with prevalent users — patients who may have been on metformin for years and have already experienced its full effect.

**Example Inclusion/Exclusion Criteria for Three Clinical Questions**

**Question 1: Does statin use reduce 30-day readmission in patients with acute myocardial infarction (AMI)?**

Index date: Discharge date

| Criterion | Type | Rationale |
|-----------|------|-----------|
| Primary diagnosis I21.x (AMI) | Inclusion | Known at discharge. Identifies AMI patients |
| Age >= 18 | Inclusion | Known at discharge. Adult patients only |
| Discharged alive, not transferred out | Inclusion | Known at discharge. Must reach index date |
| No prior statin use within 90 days | Inclusion | Known at discharge. Ensures new statin users (washout) |
| Length of stay >= 1 day | Inclusion | Known at discharge. Removes rule-out admissions |
| Discharged to hospice | Exclusion | Determined during admission. Readmission not meaningful |
| Planned cardiac procedure readmission scheduled | Exclusion | Determined during admission. Not an unplanned readmission |

**Question 2: Is ICU admission associated with in-hospital mortality among patients with community-acquired pneumonia (CAP)?**

Index date: Admission date

| Criterion | Type | Rationale |
|-----------|------|-----------|
| Primary diagnosis J13-J18 (pneumonia) | Inclusion | Known at admission. Identifies pneumonia patients |
| Age >= 18 | Inclusion | Known at admission. Adult patients only |
| Not transferred from another hospital ICU | Inclusion | Known at admission. Ensures comparable baseline severity |
| Pneumonia diagnosed > 48h after admission (hospital-acquired) | Exclusion | NOT known at admission. Determined after workup. Different etiology and prognosis |
| Comfort care / DNR order placed after admission | Exclusion | NOT known at admission. Determined during the stay. Expected deaths, not treatment failures |

**Question 3: Does early physical therapy reduce length of stay after hip fracture surgery?**

Index date: Surgery date

| Criterion | Type | Rationale |
|-----------|------|-----------|
| Primary diagnosis S72.0x (femoral neck fracture) or S72.1x (trochanteric fracture) | Inclusion | Known at surgery. Identifies hip fracture patients |
| Procedure code for hip fracture surgery (ICD-10-PCS 0SRx or 0QSx) | Inclusion | Known at surgery. Surgical patients only |
| Age >= 65 | Inclusion | Known at surgery. Geriatric hip fractures (different from young trauma) |
| Not pathological fracture (M84.4x) | Inclusion | Known at surgery. Different etiology (cancer, metabolic bone disease) |
| Not polytrauma (ISS > 15) | Inclusion | Known at surgery. LOS would be driven by other injuries |
| No prior hip surgery within 1 year | Inclusion | Known at surgery. Revision surgery has different recovery trajectory |
| Surgical complication requiring return to OR | Exclusion | NOT known at surgery. Determined post-operatively. LOS driven by complication, not PT timing |
| Transfer to another facility before PT could begin | Exclusion | NOT known at surgery. Determined post-operatively. Cannot assess PT effect |

> **Note:** Defining your study population is the most consequential decision in any hospital data analysis. Spend more time on inclusion/exclusion criteria than on choosing the statistical method. A perfect model applied to the wrong population is worthless.

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Lecture — Common Biases in Hospital Data Studies (15 min)

Bias is a systematic error that causes study results to deviate from the truth. In observational hospital data studies, several biases arise specifically from how the data was generated and how researchers define their study.

**1. Prevalent User Bias (Prevalent-User Bias)**

- **What it is:** Including patients who were already on treatment when the study begins, rather than restricting to patients who newly initiated treatment.
- **Clinical example:** You want to study whether ACE inhibitors reduce readmission in heart failure patients. Your exposed group includes patients who have been on ACE inhibitors for 5 years as well as patients who just started. The long-term users are a biased sample — they survived long enough and tolerated the drug well enough to still be on it. Patients who started ACE inhibitors and experienced serious side effects (or died) are no longer taking the drug and are missing from your "exposed" group.
- **Result:** Prevalent user bias makes the treatment look more effective and safer than it actually is.
- **Fix:** Use a **new user design** — restrict the exposed group to patients who newly initiated the treatment (with a washout period to confirm they were not previously using it).

```
Wrong (prevalent users):
|---------- already on drug ----------|--- Study period ---|
                                      ^
                                      Cohort entry

Right (new users):
|-- No drug (washout) --|-- Start drug --|--- Study period ---|
                         ^
                         Cohort entry = drug initiation
```

**2. Immortal Time Bias**

- **What it is:** A period of follow-up during which the outcome cannot occur, misclassified as exposed person-time.
- **Clinical example:** You study whether patients who receive a cardiology consultation after heart failure admission have lower 30-day mortality. The exposed group is defined as "patients who received a cardiology consult during admission." But the consult happens on Day 3, on average. During Days 0-3, these patients are classified as "exposed" even though they had not yet received the exposure. During this time, they could not have died and still been in the exposed group (if they died on Day 2, they never received the consult and would be in the unexposed group). This guaranteed survival time (Days 0-3) is the "immortal time."
- **Result:** The exposed group appears to have lower mortality because they were guaranteed to survive until the exposure occurred.
- **Fix:** Define exposure at a fixed time point (e.g., at admission), or use time-varying exposure models (e.g., Cox regression with time-dependent covariates).

```
Immortal time bias:

Patient A:  Admission --- Consult Day 3 --- Alive Day 30  --> Exposed, survived
Patient B:  Admission --- Died Day 2 --------------------------> Unexposed (never got consult)
Patient C:  Admission --- Consult Day 5 --- Died Day 20   --> Exposed, died

The period from Admission to Consult is "immortal time" for exposed patients.
They MUST survive this period to receive the exposure.
```

**3. Time-Related Biases**

- **What it is:** Comparing groups that have different amounts of follow-up time without accounting for the difference.
- **Clinical example:** Patients admitted in January 2024 have 12 months of follow-up data. Patients admitted in November 2024 have only 2 months. If you compare readmission rates without accounting for follow-up time, patients admitted in January will appear to have higher readmission rates simply because they had more time to be readmitted.
- **Fix:** Use survival analysis methods (Kaplan-Meier, Cox regression) that account for varying follow-up times. Alternatively, use a fixed follow-up window (e.g., exactly 30 days for all patients, excluding patients with less than 30 days of available follow-up).

**4. Selection Bias from Single-Center Data**

- **What it is:** Results from one hospital may not be generalizable to other hospitals because of differences in patient populations, clinical practices, or referral patterns.
- **Clinical example:** A tertiary referral center receives the sickest, most complex patients from surrounding hospitals. A study showing that ICU admission is associated with mortality at this center may reflect referral patterns (sicker patients are referred there) rather than a true relationship between ICU care and death. A community hospital might find a completely different association.
- **Other single-center biases:**
  - Specialist availability (e.g., 24/7 interventional cardiology at the study hospital but not at others)
  - Patient demographics (e.g., urban academic center vs. rural community hospital)
  - Electronic health record system and documentation practices
  - Coding practices (some hospitals code more aggressively for higher reimbursement)
- **Fix:** Acknowledge single-center limitations explicitly. Use multi-center data when available. Validate findings in external datasets.

> **Note:** These biases are not theoretical concerns — they appear routinely in published clinical research and have led to incorrect conclusions that influenced clinical practice. Understanding these biases is as important as understanding the statistical methods used to analyze the data.

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Hands-on — Design a Clinical Study (30 min)

In this exercise, you will practice designing a clinical study using a hospital discharge dataset. For each clinical question, you will define the study population, exposure, outcome, inclusion/exclusion criteria, potential confounders, and draw a timeline diagram.

**Available Dataset Variables (Hospital Discharge Data)**

| Variable | Description |
|----------|-------------|
| patient_id | Unique patient identifier |
| age | Patient age in years |
| sex | M or F |
| admission_date | Date of hospital admission |
| discharge_date | Date of hospital discharge |
| admission_type | Emergency, Elective, or Urgent |
| primary_dx_icd | Primary ICD-10 diagnosis code |
| secondary_dx_icd | Up to 10 secondary ICD-10 diagnosis codes |
| procedure_codes | ICD-10-PCS procedure codes |
| charlson_score | Charlson Comorbidity Index (0-15) |
| length_of_stay | Hospital days |
| icu_stay | Whether the patient had an ICU stay (Yes/No) |
| icu_los | ICU length of stay (days) |
| discharge_medications | List of medications at discharge |
| discharge_disposition | Home, Skilled Nursing Facility, Rehab, Died, AMA, Transfer |
| readmission_30d | Readmitted within 30 days (Yes/No) |
| lab values | WBC, hemoglobin, creatinine, HbA1c, troponin, BNP |
| vital signs | Systolic BP, heart rate, temperature, O2 saturation |

---

**Clinical Question 1: Does early ICU admission (within 24 hours) improve survival in patients with severe sepsis?**

Complete the following study design worksheet:

**1. Study Population Definition**

| Element | Your Definition |
|---------|----------------|
| Target condition (ICD-10 codes) | |
| Age restriction | |
| Other inclusion criteria | |
| Exclusion criteria (list at least 3) | |

**2. Exposure and Outcome**

| Element | Your Definition |
|---------|----------------|
| Exposure | |
| How is exposure ascertained from the data? | |
| Outcome | |
| How is outcome ascertained from the data? | |
| Outcome ascertainment window | |

**3. Potential Confounders**

List at least 5 variables from the dataset that might confound the relationship between ICU admission and survival:

| Confounder | Why It Might Confound |
|-----------|----------------------|
| | |
| | |
| | |
| | |
| | |

**4. Timeline Diagram**

Draw a timeline showing cohort entry, exposure window, and outcome ascertainment:

```
[Your timeline here]
```

**5. Potential Biases**

Which biases from Session 5 are most relevant to this question? How would you address them?

---

**Clinical Question 2: Is polypharmacy (>= 10 discharge medications) associated with 30-day readmission in elderly patients with diabetes?**

Complete the same worksheet:

**1. Study Population Definition**

| Element | Your Definition |
|---------|----------------|
| Target condition (ICD-10 codes) | |
| Age restriction | |
| Other inclusion criteria | |
| Exclusion criteria (list at least 3) | |

**2. Exposure and Outcome**

| Element | Your Definition |
|---------|----------------|
| Exposure | |
| How is exposure ascertained from the data? | |
| Outcome | |
| How is outcome ascertained from the data? | |
| Outcome ascertainment window | |

**3. Potential Confounders**

| Confounder | Why It Might Confound |
|-----------|----------------------|
| | |
| | |
| | |
| | |
| | |

**4. Timeline Diagram**

```
[Your timeline here]
```

---

**Clinical Question 3: Does weekend admission increase in-hospital mortality for patients with acute myocardial infarction?**

Complete the same worksheet:

**1. Study Population Definition**

| Element | Your Definition |
|---------|----------------|
| Target condition (ICD-10 codes) | |
| Age restriction | |
| Other inclusion criteria | |
| Exclusion criteria (list at least 3) | |

**2. Exposure and Outcome**

| Element | Your Definition |
|---------|----------------|
| Exposure | |
| How is exposure ascertained from the data? | |
| Outcome | |
| How is outcome ascertained from the data? | |
| Outcome ascertainment window | |

**3. Potential Confounders**

| Confounder | Why It Might Confound |
|-----------|----------------------|
| | |
| | |
| | |
| | |
| | |

**4. Timeline Diagram**

```
[Your timeline here]
```

---

**Discussion Points (after completing the worksheets)**

After students complete their worksheets, discuss as a class:

1. **Which study design** (cohort, case-control, cross-sectional) is most appropriate for each question, and why?
2. **What data is missing** from the available dataset that would strengthen each study? (e.g., outpatient records, pharmacy refill data, patient-reported outcomes)
3. **Is immortal time bias** a concern for any of these questions? If so, which one and how would you address it?
4. **Could any of these questions** be answered with a cross-sectional design instead? What would you gain or lose?

**Instructor Reference: Key Points for Each Question**

**Question 1 (ICU and sepsis):**
- ICD-10 codes: R65.20 (severe sepsis without shock), R65.21 (severe sepsis with shock), A41.x (sepsis)
- Major bias concern: Immortal time bias (patients must survive to be admitted to ICU) and confounding by indication (sicker patients are more likely to be admitted to ICU AND more likely to die)
- Key confounders: Charlson score, age, admission vital signs (BP, O2 sat), lactate level, time from ED to diagnosis

**Question 2 (Polypharmacy and readmission):**
- ICD-10 codes: E11.x (type 2 diabetes), E10.x (type 1 diabetes)
- Age restriction: >= 65 years
- This is a cohort study with exposure defined at discharge (number of medications)
- Key confounder: Charlson score (patients with more comorbidities take more medications AND are more likely to be readmitted — polypharmacy may be a marker of disease burden, not a cause of readmission)

**Question 3 (Weekend admission and AMI mortality):**
- ICD-10 codes: I21.x (acute myocardial infarction)
- Exposure is inherently time-fixed (weekend vs. weekday is determined at admission)
- No immortal time bias concern
- Key confounders: Admission type (emergency vs. elective — elective admissions rarely happen on weekends), severity at presentation (troponin, hemodynamics), time to intervention (PCI availability may differ on weekends)

> **Note:** Study design is not a form-filling exercise. Each decision in the worksheet must be justified by clinical reasoning and awareness of potential biases. When you present your findings, be prepared to defend every inclusion criterion, every exclusion, and every confounder you chose to adjust for.

[Back to Table of Contents](#table-of-contents)
