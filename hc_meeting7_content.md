# Comparative Clinical Analysis and Confounding

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Session 1: Lecture — Why Comparison is Central to Clinical Research (10 min)](#session-1-lecture--why-comparison-is-central-to-clinical-research-10-min)
3. [Session 2: Lecture — Confounding in Hospital Data (25 min)](#session-2-lecture--confounding-in-hospital-data-25-min)
4. [Session 3: Lecture — Selection Bias in Hospital Populations (15 min)](#session-3-lecture--selection-bias-in-hospital-populations-15-min)
5. [Session 4: Lecture — Information Bias (15 min)](#session-4-lecture--information-bias-15-min)
6. [Session 5: Lecture — Strategies for Handling Confounding (15 min)](#session-5-lecture--strategies-for-handling-confounding-15-min)
7. [Session 6: Hands-on — Identify Confounders in Clinical Scenarios (30 min)](#session-6-hands-on--identify-confounders-in-clinical-scenarios-30-min)

---

## Subtopics

- Why every clinical question is fundamentally a comparison
- Confounding variables and their role in distorting exposure-outcome relationships
- Directed acyclic graphs (DAGs) for distinguishing confounders, mediators, and colliders
- Selection bias in hospital populations: Berkson's bias, single-center bias, survivor bias
- Information bias: differential documentation, coding, and measurement error
- Strategies for handling confounding: restriction, stratification, regression, propensity scores
- Practical exercises identifying confounders in clinical scenarios using the hospital discharge dataset

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — Why Comparison is Central to Clinical Research (10 min)

Every clinical question is, at its core, a comparison.

**Examples of clinical comparisons:**

| Clinical Question | Comparison Groups |
|-------------------|-------------------|
| Does drug A reduce mortality? | Treated vs. untreated patients |
| Does smoking cause lung cancer? | Exposed vs. unexposed individuals |
| Does ICU care improve outcomes? | ICU patients vs. ward patients |
| Do diabetic patients stay longer? | Diabetic vs. non-diabetic admissions |
| Does emergency admission predict readmission? | Emergency vs. elective admissions |

Without a comparison group, a clinical finding has no meaning. Saying "30% of ICU patients died" is descriptive. Saying "30% of ICU patients died vs. 5% of ward patients" is comparative and starts to suggest something about ICU care or patient severity.

**Randomized trials vs. observational data:**

In a randomized controlled trial (RCT), patients are randomly assigned to groups. Randomization ensures that the groups are balanced on all factors — measured and unmeasured — so any difference in outcome can be attributed to the treatment.

Hospital data is observational. Patients are not randomly assigned to treatments, wards, or procedures. A doctor decides which patient goes to the ICU, which patient receives surgery, which patient gets a particular medication. These decisions are based on clinical judgment — meaning the groups being compared are already different before the outcome occurs.

**The central problem:**

When groups differ in ways beyond the exposure of interest, any observed difference in outcome could be due to:

1. The exposure itself (the true effect)
2. Pre-existing differences between the groups (confounding)
3. How patients ended up in the study (selection bias)
4. How the data was collected (information bias)

The rest of this meeting addresses each of these threats and what can be done about them.

> **Note:** Observational data from hospitals is not inferior to RCTs — it answers different questions. RCTs test efficacy under controlled conditions. Hospital data reveals effectiveness in real-world clinical practice. Both are necessary, but observational data requires careful handling of bias and confounding.

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Lecture — Confounding in Hospital Data (25 min)

**Definition:** A confounder is a third variable that distorts the apparent relationship between an exposure and an outcome because it is associated with both.

**The classic example:**

Suppose you analyze hospital data and find that patients who received more aggressive treatment (e.g., high-dose vasopressors) had higher mortality. Does aggressive treatment cause death?

No. Sicker patients receive more aggressive treatment AND sicker patients are more likely to die. Disease severity is a confounder: it causes both the exposure (aggressive treatment) and the outcome (death). Without accounting for severity, the treatment appears harmful when it may actually be beneficial or neutral.

This specific pattern is called **confounding by indication** — the indication for treatment (disease severity) is itself associated with the outcome. It is the most important type of confounding in hospital data.

**Directed Acyclic Graphs (DAGs):**

DAGs are diagrams that show causal relationships between variables. They help distinguish three types of third variables:

**1. Confounder:**

```
    Confounder
     /      \
    v        v
Exposure → Outcome
```

The confounder causes both exposure and outcome. It creates a backdoor path from exposure to outcome. To remove confounding, you must block this backdoor path by adjusting for the confounder.

Example: Age is a confounder of medication count and mortality. Older patients take more medications AND older patients have higher mortality.

**2. Mediator:**

```
Exposure → Mediator → Outcome
```

The mediator lies on the causal path between exposure and outcome. It is part of the mechanism by which the exposure affects the outcome. You should NOT adjust for a mediator if you want to estimate the total effect of the exposure.

Example: Smoking causes lung inflammation, which causes lung cancer. Inflammation is a mediator. Adjusting for inflammation would block part of the true effect of smoking.

**3. Collider:**

```
Exposure → Collider ← Outcome
```

The collider is caused by both exposure and outcome. Adjusting for a collider creates a spurious association between exposure and outcome where none exists. This is called collider bias.

Example: Hospitalization is a collider if both the exposure (e.g., obesity) and the outcome (e.g., respiratory failure) independently cause hospitalization. Among hospitalized patients, obesity and respiratory failure may appear negatively associated even if they are positively associated in the general population.

**Five clinical examples of confounders in hospital studies:**

| Study Question | Exposure | Outcome | Confounder | Why It Confounds |
|----------------|----------|---------|------------|------------------|
| Does polypharmacy increase readmission? | Number of medications | 30-day readmission | Charlson comorbidity score | Sicker patients take more medications AND are more likely to be readmitted |
| Does ICU stay increase mortality? | ICU admission | In-hospital death | Disease severity (e.g., APACHE score) | Sicker patients are admitted to ICU AND are more likely to die |
| Does weekend admission increase mortality? | Day of admission | In-hospital death | Admission type (emergency vs. elective) | Emergency admissions are more common on weekends AND have higher mortality |
| Does diabetes worsen surgical outcomes? | Diabetes diagnosis | Post-surgical complications | Age | Older patients are more likely to have diabetes AND more likely to have complications |
| Do more lab tests improve outcomes? | Number of lab tests ordered | Length of stay | Acuity of illness | Sicker patients get more lab tests AND stay longer in hospital |

**How to identify a confounder:**

A variable C is a confounder of the exposure-outcome relationship if ALL three conditions are met:

1. C is associated with the exposure
2. C is associated with the outcome (independent of the exposure)
3. C is NOT on the causal pathway between exposure and outcome (i.e., not a mediator)

> **Note:** Confounding is not a property of a variable in isolation. The same variable can be a confounder in one study, a mediator in another, and a collider in a third. It depends entirely on the causal question being asked. Always draw a DAG before deciding what to adjust for.

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Lecture — Selection Bias in Hospital Populations (15 min)

Selection bias occurs when the study population is not representative of the target population, and this non-representativeness is related to both the exposure and the outcome.

**Berkson's bias (admission rate bias):**

Hospital patients are not a random sample of the general population. People are hospitalized because they are sick. This means that conditions which independently cause hospitalization will appear associated within a hospital sample, even if they are unrelated in the general population.

**Example:** Suppose diabetes and fractures are unrelated in the general population. Both conditions independently increase the probability of hospitalization. Among hospitalized patients, a patient without diabetes is more likely to be there because of a fracture (and vice versa). This creates a spurious negative association between diabetes and fractures in the hospital sample.

**Why it matters for hospital data analysis:** Any study using only hospitalized patients may find associations between diseases that do not exist in the general population.

**Single-center bias:**

Results from one hospital may not generalize to other hospitals. Reasons include:

- Different patient demographics (teaching hospital vs. community hospital)
- Different clinical protocols and practice patterns
- Different coding and documentation standards
- Different payer mixes and insurance coverage
- Referral patterns that concentrate certain conditions

**Example:** A study at a tertiary referral center finds that a rare cancer has a 90% five-year survival rate. This does not mean the cancer has a good prognosis — it means the referral center receives patients who are well enough to be transferred and who have access to specialized care.

**Survivor bias (survivorship bias):**

Analyzing only patients who survived long enough to be captured in the dataset systematically excludes those who died before data collection.

**Example 1:** A study examines lab values at 48 hours after ICU admission and relates them to outcomes. Patients who died within the first 48 hours are excluded. These patients were likely the sickest. The study then underestimates the mortality rate and overestimates the predictive value of 48-hour lab values.

**Example 2:** Analyzing medication adherence among discharged patients. Patients who died during hospitalization are excluded. If the medication of interest contributed to their death, the analysis misses this entirely.

**Practical implications for the hospital discharge dataset:**

- The dataset contains only discharged patients — patients who died during hospitalization may or may not be included (check the outcome variable)
- Patients from a single hospital may have characteristics not generalizable to other settings
- Variables like readmission_30d can only be measured for patients who survived to discharge

> **Note:** Selection bias cannot be fixed by statistical adjustment after the fact. It is a design problem. The only solutions are to carefully define the study population upfront, use multi-center data when possible, and clearly state the limitations of single-center hospital analyses.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Lecture — Information Bias (15 min)

Information bias occurs when the data collected about exposure, outcome, or covariates is systematically inaccurate or incomplete. In hospital data, this takes several forms.

**1. Differential documentation:**

Different clinical settings generate different amounts and types of data.

| Setting | Vital Sign Frequency | Documentation Detail |
|---------|---------------------|---------------------|
| ICU | Every 1-2 hours | Extensive: ventilator settings, drip rates, hourly I/O |
| Step-down unit | Every 2-4 hours | Moderate |
| General ward | Every 4-8 hours | Basic: BP, HR, temp, SpO2 |
| Observation unit | Every 4-8 hours | Minimal |

**Why this matters:** An ICU patient with 12 blood pressure readings in 24 hours will have a higher recorded maximum BP and a lower recorded minimum BP than a ward patient with 3 readings — simply because more measurements capture more variability. More data points do not mean the patient is sicker. They mean the patient was monitored more closely.

**Example:** If you define "hypertensive crisis" as any systolic BP above 180 mmHg, ICU patients will appear to have more crises than ward patients, partly because they have more measurements. This is measurement artifact, not clinical reality.

**2. Differential coding:**

Not all diagnoses and procedures are coded with equal accuracy.

- **Billable procedures** (e.g., surgeries, invasive procedures) are coded carefully because they generate revenue. Coding accuracy is high.
- **Secondary diagnoses** (e.g., malnutrition, delirium, chronic pain) are often under-coded because they do not directly affect reimbursement.
- **Hospital-acquired conditions** (e.g., pressure injuries, catheter infections) may be under-reported because they affect quality metrics and hospital ratings.

**Result:** Studies using ICD-10 codes to identify conditions will underestimate the prevalence of under-coded conditions and overestimate the prevalence of heavily-coded conditions.

**3. Recall bias in chart review:**

When researchers manually review medical records, their knowledge of the patient's outcome can influence what they find.

**Example:** A researcher reviewing charts to identify pre-operative risk factors may document more risk factors for patients who had complications (because they search more thoroughly) than for patients who did well. This creates a spurious association between risk factors and complications.

**4. Measurement error in clinical data:**

Clinical measurements have inherent variability:

| Measurement | Sources of Error |
|-------------|-----------------|
| Blood pressure | Cuff size, patient position, white coat effect, time of day |
| Heart rate | Activity level, anxiety, medication timing |
| Lab values | Specimen handling, different assay methods, hemolyzed samples |
| Weight | Clothing, time of day, fluid status |
| Temperature | Oral vs. axillary vs. tympanic vs. rectal |

**Non-differential vs. differential measurement error:**

- **Non-differential:** The error is equally distributed across comparison groups. This usually biases results toward the null (makes it harder to find a true association).
- **Differential:** The error is systematically different between groups. This can bias results in either direction and is more dangerous.

> **Note:** Information bias is often invisible in the dataset. You cannot see it by looking at summary statistics. It requires clinical knowledge to recognize. Always ask: "Was this variable measured the same way in all patients?" If not, the comparison may be unfair.

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Lecture — Strategies for Handling Confounding (15 min)

Once a confounder is identified, several strategies exist to reduce or eliminate its influence. Each has strengths and limitations.

**1. Restriction:**

Limit the study population to a single level of the confounder.

- **How:** If age confounds the medication-outcome relationship, restrict the analysis to patients aged 65-75 only.
- **Advantage:** Completely eliminates confounding by that variable.
- **Disadvantage:** Reduces sample size and limits generalizability — results apply only to the restricted group.
- **Clinical example:** To study whether ICU stay affects readmission without confounding by surgical status, restrict to non-surgical patients only.

**2. Stratification:**

Analyze the exposure-outcome relationship separately within subgroups defined by the confounder.

- **How:** If sex confounds the exposure-outcome relationship, compute the association separately for males and females, then combine using a weighted average (Mantel-Haenszel method).
- **Advantage:** Allows you to see whether the effect differs across subgroups (effect modification).
- **Disadvantage:** Cannot handle many confounders simultaneously — with 5 confounders and 2-3 levels each, you get hundreds of strata with few patients in each.
- **Clinical example:** Stratify by Charlson score categories (0, 1-2, 3+) when studying whether emergency admission increases readmission risk.

**3. Regression adjustment (multivariable regression):**

Include confounders as covariates in a statistical model.

- **How:** Fit a regression model with the exposure and all identified confounders as independent variables. The coefficient for the exposure represents its effect after adjusting for the confounders.
- **Advantage:** Can adjust for many confounders simultaneously. Most commonly used approach.
- **Disadvantage:** Assumes the model is correctly specified (correct functional form, no important interactions missed). Requires sufficient data per confounder (rule of thumb: at least 10 events per covariate for logistic regression).
- **Clinical example:** Logistic regression for 30-day readmission including ICU stay (exposure), age, sex, Charlson score, and admission type as covariates.

**4. Propensity score methods (concept only):**

Create a single score summarizing the probability of receiving the exposure, given the observed covariates, then match or weight patients by this score.

- **How:** First, build a model predicting exposure (e.g., probability of ICU admission) from all observed confounders. This probability is the propensity score. Then, compare outcomes between exposed and unexposed patients with similar propensity scores.
- **Advantage:** Reduces many confounders to a single number, making matching feasible. Visually intuitive — you can check whether the propensity score distributions overlap between groups.
- **Disadvantage:** Only adjusts for measured confounders. Does not help with unmeasured confounding.
- **Clinical example:** Match ICU patients to ward patients who had the same probability of ICU admission based on age, comorbidities, and admission severity.

**When to use which strategy:**

| Strategy | Best When | Avoid When |
|----------|-----------|------------|
| Restriction | One strong confounder, large sample | Small sample, need generalizable results |
| Stratification | Few confounders (1-2), want to check for effect modification | Many confounders |
| Regression | Multiple confounders, sufficient sample size | Very small sample, highly collinear confounders |
| Propensity scores | Many confounders, want to mimic RCT design | Few confounders (overkill), small sample |

**The fundamental limitation: unmeasured confounders.**

All four strategies only work for confounders you can measure and include. If a confounder is not in your dataset, you cannot adjust for it.

**Example:** Suppose you study whether a medication reduces readmission. You adjust for age, sex, comorbidities, and admission severity. But you cannot measure medication adherence after discharge, social support at home, or health literacy — all of which affect readmission. These unmeasured confounders remain sources of bias.

> **Note:** In observational studies using hospital data, you can never prove that all confounders have been accounted for. This is why observational studies conclude with "association" rather than "causation." Always discuss potential unmeasured confounders as a limitation.

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Hands-on — Identify Confounders in Clinical Scenarios (30 min)

In this exercise, you will work through three clinical questions using the hospital discharge dataset. For each question, you will draw a DAG, identify potential confounders from available variables, propose an adjustment strategy, and discuss what confounders might be unmeasured.

**Scenario 1: "Does ICU stay increase readmission risk?"**

- **Exposure:** icu_stay (Yes/No)
- **Outcome:** readmission_30d (Yes/No)

Tasks:

1. Draw a DAG showing the relationship between ICU stay and 30-day readmission. Place potential confounders above the exposure-outcome arrow.
2. From the available variables, which could be confounders?
   - Consider: age, sex, admission_type, charlson_score, length_of_stay, lab values (wbc, hemoglobin, creatinine, hba1c), systolic_bp, heart_rate, n_medications
   - For each candidate, check: (a) is it associated with ICU admission? (b) is it associated with readmission? (c) is it NOT a mediator?
3. Is length_of_stay a confounder or a mediator? (Hint: ICU patients stay longer because of the ICU stay itself — this makes length_of_stay a mediator, not a confounder. Adjusting for it would block part of the true effect.)
4. Propose an adjustment strategy. Which variables would you include in a regression model?
5. What confounders are missing from the dataset? (Consider: reason for ICU admission, disease severity scores like APACHE, functional status at discharge, discharge disposition, social support.)

---

**Scenario 2: "Do more medications lead to longer hospital stays?"**

- **Exposure:** n_medications
- **Outcome:** length_of_stay

Tasks:

1. Draw a DAG. What variables cause both high medication counts and long hospital stays?
2. Identify confounders from the dataset:
   - charlson_score: sicker patients take more medications AND stay longer
   - age: older patients take more medications AND stay longer
   - admission_type: emergency admissions may involve more medications AND longer stays
   - icu_stay: is this a confounder or a mediator? (If ICU stay causes more medications to be added and also causes longer stay, it may be a mediator. But if patients already on many medications are more likely to be admitted to ICU, it could be a confounder. The direction matters.)
3. Is the exposure clearly defined? n_medications is measured at discharge — a patient who stayed longer had more days to accumulate medications. This creates reverse causation: length_of_stay may cause n_medications rather than the other way around. Discuss how this affects interpretation.
4. Propose an adjustment strategy given the reverse causation problem.
5. What confounders are unmeasured? (Consider: number of active diagnoses, surgical vs. medical admission, complications during stay, insurance type.)

---

**Scenario 3: "Is emergency admission associated with higher mortality?"**

- **Exposure:** admission_type (Emergency vs. Elective)
- **Outcome:** In-hospital mortality (derived from available variables, or use readmission_30d as a proxy outcome)

Tasks:

1. Draw a DAG. Why might emergency patients have worse outcomes independent of the emergency setting?
2. Identify confounders:
   - age: older patients may present more often as emergencies AND have higher mortality
   - charlson_score: patients with more comorbidities may present as emergencies AND have higher mortality
   - primary_dx_icd: certain diagnoses (e.g., MI, stroke) present emergently AND carry high mortality — but this variable is in the dataset as meta
3. Discuss the challenge of using primary_dx_icd as a confounder:
   - It is a text variable with many unique values
   - It would need to be grouped into categories (e.g., cardiovascular, respiratory, infectious) before use as a covariate
   - How would you group ICD-10 codes for adjustment?
4. Propose an adjustment strategy.
5. What confounders are unmeasured? (Consider: time of day, day of week, presenting vital signs severity, time from symptom onset to arrival, insurance status.)

---

**Discussion after all three scenarios:**

- Which scenario had the most clearly identifiable confounders?
- In which scenario was the direction of causation most ambiguous?
- How does the limitation of the dataset (16 variables) constrain your ability to address confounding?
- What additional variables would you most want to have in the dataset to improve these analyses?

> **Note:** There is no single correct answer for any of these scenarios. The goal is to practice the thinking process: (1) state the question clearly, (2) draw the DAG, (3) identify what could confound the relationship, (4) decide what can and cannot be adjusted for, and (5) honestly state the limitations. This structured thinking is the foundation of all clinical data analysis.

[Back to Table of Contents](#table-of-contents)
