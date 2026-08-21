# Clinical Coding and Terminology Standards

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Session 1: Lecture — Why Clinical Coding Matters (10 min)](#session-1-lecture--why-clinical-coding-matters-10-min)
3. [Session 2: Lecture — ICD-10-CM Diagnosis Codes (30 min)](#session-2-lecture--icd-10-cm-diagnosis-codes-30-min)
4. [Session 3: Lecture — ICD-10-PCS Procedure Codes (10 min)](#session-3-lecture--icd-10-pcs-procedure-codes-10-min)
5. [Session 4: Lecture — ATC Drug Classification (15 min)](#session-4-lecture--atc-drug-classification-15-min)
6. [Session 5: Lecture — LOINC for Laboratory Data (20 min)](#session-5-lecture--loinc-for-laboratory-data-20-min)
7. [Session 6: Hands-on — Code Lookup Exercise (25 min)](#session-6-hands-on--code-lookup-exercise-25-min)

---

## Subtopics

- Why standardized clinical coding is essential for analysis, billing, and research
- ICD-10-CM diagnosis code structure and common hospital codes
- ICD-10-PCS procedure code structure (overview)
- ATC drug classification system and common hospital medications
- LOINC coding for laboratory test results
- Hands-on practice looking up codes using WHO and LOINC browsers

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — Why Clinical Coding Matters (10 min)

Every day, hospitals generate enormous amounts of clinical information. A physician writes "the patient has uncontrolled Type 2 diabetes with early kidney involvement" in a progress note. A nurse documents "IV ceftriaxone 1g given at 08:00." A lab technician reports "WBC 15.2." These are all useful for individual patient care, but they cannot be analyzed at scale in their raw form.

**Clinical coding solves this problem.**

Clinical coding is the process of translating clinical descriptions into standardized alphanumeric codes. Instead of free-text descriptions that vary by language, provider, and institution, codes provide a universal, machine-readable representation of clinical concepts.

**Four essential functions of clinical coding:**

**1. Billing and reimbursement**

- Hospitals are paid based on diagnosis and procedure codes
- Insurance claims require valid ICD-10 codes to justify the services provided
- Incorrect or missing codes lead to claim denials or underpayment
- In Taiwan's National Health Insurance (NHI) system, diagnosis-related group (DRG) payments are tied directly to ICD-10 codes

**2. Quality reporting**

- Hospital quality metrics (readmission rates, mortality rates, complication rates) are calculated from coded data
- Regulatory agencies compare hospitals using these standardized measures
- Without consistent coding, quality comparison across hospitals is meaningless

**3. Research and population health**

- Epidemiological studies use diagnosis codes to identify disease cohorts
- Drug safety surveillance uses medication codes to track adverse events across populations
- Multi-site research requires standardized codes to combine data from different hospitals

**4. Clinical decision support**

- Automated alerts (drug-disease interactions, duplicate orders) rely on coded diagnoses and medications
- Clinical prediction models use coded variables as inputs
- Public health surveillance (disease outbreak detection) depends on real-time coded data

**What happens without coding:**

Without standardized codes, clinical data is free text. Analyzing free text requires Natural Language Processing (NLP), which is complex, imperfect, and language-dependent. Codes bypass this entirely — they provide structured, language-independent, computationally efficient representations of clinical concepts.

> **Note:** In the hospital discharge dataset we are using in this course, the primary_dx_icd column contains ICD-10 diagnosis codes. The lab values (wbc, hemoglobin, creatinine, hba1c) correspond to specific LOINC codes. The medications counted in n_medications would each have an ATC code. Understanding these coding systems helps you interpret and validate the data you analyze.

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Lecture — ICD-10-CM Diagnosis Codes (30 min)

ICD-10-CM (International Classification of Diseases, 10th Revision, Clinical Modification) is the standard coding system for diagnoses in the United States and many other countries. Taiwan uses ICD-10-CM/PCS as well.

**Structure of an ICD-10-CM code:**

ICD-10-CM codes have a hierarchical structure with 3 to 7 characters:

```
E11.65
│││ ││
│││ │└─ 5th character: specific manifestation (hyperglycemia)
│││ └── 4th character: specific type (with complications)
││└──── 3rd character: category detail (Type 2)
│└───── 2nd character: block within chapter
└────── 1st character: chapter (E = Endocrine, nutritional, metabolic)
```

The structure follows a hierarchy from broad to specific:

1. **Chapter** (1st character) — the broadest category, indicated by a letter
2. **Block** (1st-3rd characters) — a group of related conditions within a chapter
3. **Category** (3rd character) — the specific condition type
4. **Subcategory** (4th-5th characters after the decimal) — clinical details, laterality, severity
5. **Extension** (6th-7th characters) — additional specificity

**Common ICD-10-CM chapters in hospital data:**

| Chapter | Code Range | Description | Example |
|---------|-----------|-------------|---------|
| A-B | A00-B99 | Infectious diseases | A41.9 Sepsis, unspecified |
| C-D | C00-D49 | Neoplasms | C34.90 Lung cancer |
| E | E00-E89 | Endocrine, nutritional, metabolic | E11.65 Type 2 diabetes with hyperglycemia |
| I | I00-I99 | Circulatory system | I50.9 Heart failure |
| J | J00-J99 | Respiratory system | J18.9 Pneumonia |
| K | K00-K95 | Digestive system | K70.30 Alcoholic cirrhosis |
| N | N00-N99 | Genitourinary system | N18.6 End-stage renal disease |
| S-T | S00-T88 | Injury, poisoning | S72.001A Hip fracture |

**10 common hospital ICD-10-CM codes:**

| Code | Description | Clinical Context |
|------|-------------|-----------------|
| I50.9 | Heart failure, unspecified | One of the most common causes of hospitalization. Patients often present with dyspnea, edema, and fatigue. High readmission rate. |
| J18.9 | Pneumonia, unspecified organism | Lung infection requiring hospitalization when severe. Common in elderly and immunocompromised patients. |
| E11.65 | Type 2 diabetes with hyperglycemia | Uncontrolled blood sugar requiring inpatient management. Often a secondary diagnosis complicating other conditions. |
| I21.9 | Acute myocardial infarction, unspecified | Heart attack. A medical emergency requiring immediate intervention (catheterization, stenting, or thrombolysis). |
| N18.6 | End-stage renal disease | Complete kidney failure requiring dialysis or transplant. Generates frequent hospital encounters. |
| A41.9 | Sepsis, unspecified organism | Life-threatening response to infection. High mortality, often requires ICU admission. |
| J44.1 | COPD with acute exacerbation | Worsening of chronic lung disease. Common cause of ED visits and hospital admissions. |
| I63.9 | Cerebral infarction, unspecified | Ischemic stroke. A neurological emergency with lasting disability risk. |
| K70.30 | Alcoholic cirrhosis of liver without ascites | End-stage liver disease from chronic alcohol use. Complex management with multiple complications. |
| S72.001A | Fracture of unspecified part of neck of right femur, initial encounter | Hip fracture. Very common in elderly patients after falls. Requires surgical repair. |

**Primary vs secondary diagnosis:**

- **Primary diagnosis** — the condition established after evaluation to be chiefly responsible for the admission. Each admission has one primary diagnosis.
- **Secondary diagnoses** — other conditions that affect treatment or length of stay. A patient admitted for pneumonia (primary: J18.9) may also have diabetes (secondary: E11.65), hypertension (secondary: I10), and chronic kidney disease (secondary: N18.3).
- In our dataset, **primary_dx_icd** contains the primary diagnosis code only.

**Present-on-admission (POA) indicator:**

- Indicates whether a diagnosis was present when the patient arrived at the hospital or developed during the stay
- **Y** = Yes, present on admission
- **N** = No, developed after admission (hospital-acquired)
- **U** = Unknown
- **W** = Clinically unable to determine
- This distinction is critical for quality measurement — hospital-acquired conditions (e.g., pressure ulcers, falls) are tracked as safety indicators, and hospitals may not receive additional payment for treating conditions they caused

> **Note:** ICD-10-CM codes are not just labels — they carry clinical meaning at every level of specificity. The difference between I50.9 (heart failure, unspecified) and I50.22 (chronic systolic heart failure) matters for billing, quality reporting, and research. More specific codes enable more precise analysis.

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Lecture — ICD-10-PCS Procedure Codes (10 min)

While ICD-10-CM codes describe what is wrong with the patient (diagnoses), ICD-10-PCS (Procedure Coding System) codes describe what was done to the patient (procedures).

**Structure of an ICD-10-PCS code:**

ICD-10-PCS codes are exactly 7 characters long. Each character position has a specific meaning:

| Position | Axis | Description | Example for Coronary Bypass |
|----------|------|-------------|-----------------------------|
| 1 | Section | Broad category of procedure | 0 = Medical and Surgical |
| 2 | Body System | Anatomical region | 2 = Heart and Great Vessels |
| 3 | Root Operation | What was done | 1 = Bypass |
| 4 | Body Part | Specific structure | 0 = Coronary Artery, One Artery |
| 5 | Approach | How the site was reached | 0 = Open |
| 6 | Device | What was left in the body | 9 = Autologous Venous Tissue |
| 7 | Qualifier | Additional detail | 3 = Coronary Artery |

The full code **0210093** describes an open coronary artery bypass using autologous venous tissue (the patient's own vein graft).

**Key root operations (Position 3) you will encounter:**

| Value | Root Operation | Meaning | Example |
|-------|---------------|---------|---------|
| 0 | Alteration | Modifying a body part without affecting function | Cosmetic rhinoplasty |
| 1 | Bypass | Rerouting contents of a tubular body part | Coronary artery bypass graft |
| 5 | Destruction | Physical eradication of a body part | Cauterization of nasal polyps |
| 9 | Drainage | Taking or letting out fluids | Thoracentesis (draining pleural fluid) |
| B | Excision | Cutting out a portion of a body part | Partial colectomy |
| H | Insertion | Putting in a nonbiological device | Pacemaker insertion |
| J | Inspection | Visual and/or manual exploration | Diagnostic arthroscopy |
| T | Resection | Cutting out all of a body part | Total hysterectomy |
| U | Supplement | Putting in a biological/synthetic material | Hernia repair with mesh |

**How ICD-10-PCS differs from ICD-10-CM:**

| Aspect | ICD-10-CM (Diagnoses) | ICD-10-PCS (Procedures) |
|--------|----------------------|------------------------|
| Length | 3-7 characters | Always 7 characters |
| Structure | Hierarchical (chapter → subcategory) | Multiaxial (each position independent) |
| Number of codes | ~72,000 | ~78,000 |
| Used for | Describing conditions | Describing interventions |

> **Note:** In this course, our focus is on diagnosis codes (ICD-10-CM) because the hospital discharge dataset uses diagnosis codes as the primary clinical descriptor. However, knowing that procedure codes exist and follow a different structure helps you understand the full picture of coded clinical data. If you encounter a dataset with procedure information, you will recognize the coding system.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Lecture — ATC Drug Classification (15 min)

The Anatomical Therapeutic Chemical (ATC) classification system is the international standard for classifying medications. It is maintained by the WHO Collaborating Centre for Drug Statistics Methodology.

**Structure of an ATC code:**

The ATC system has 5 levels, from broad anatomical group to specific chemical substance:

```
A10BA02
│ ││ ││
│ ││ │└─ Level 5: Chemical substance (02 = metformin)
│ ││ └── Level 4: Chemical subgroup (BA = biguanides)
│ │└──── Level 3: Pharmacological subgroup (B = blood glucose lowering drugs, excl. insulins)
│ └───── Level 2: Therapeutic subgroup (10 = drugs used in diabetes)
└─────── Level 1: Anatomical main group (A = alimentary tract and metabolism)
```

**The 5 levels explained:**

| Level | Characters | What It Represents | Example |
|-------|-----------|-------------------|---------|
| 1 | 1st letter | Anatomical main group (organ/system) | A = Alimentary tract and metabolism |
| 2 | 2nd-3rd characters | Therapeutic subgroup | A10 = Drugs used in diabetes |
| 3 | 4th letter | Pharmacological subgroup | A10B = Blood glucose lowering drugs, excl. insulins |
| 4 | 5th letter | Chemical subgroup | A10BA = Biguanides |
| 5 | 6th-7th characters | Chemical substance | A10BA02 = Metformin |

**Level 1 anatomical groups:**

| Code | Anatomical Group |
|------|-----------------|
| A | Alimentary tract and metabolism |
| B | Blood and blood forming organs |
| C | Cardiovascular system |
| D | Dermatologicals |
| G | Genito-urinary system and sex hormones |
| H | Systemic hormonal preparations |
| J | Anti-infectives for systemic use |
| L | Antineoplastic and immunomodulating agents |
| M | Musculo-skeletal system |
| N | Nervous system |
| P | Antiparasitic products |
| R | Respiratory system |
| S | Sensory organs |
| V | Various |

**8 common hospital medications with ATC codes:**

| ATC Code | Drug Name | Level 1 Group | Clinical Use | Common In |
|----------|-----------|---------------|-------------|-----------|
| A10BA02 | Metformin | A (Alimentary) | First-line oral drug for Type 2 diabetes. Lowers blood glucose by reducing hepatic glucose production. | Diabetic patients (very common) |
| B01AA03 | Warfarin | B (Blood) | Oral anticoagulant for preventing blood clots. Used in atrial fibrillation, deep vein thrombosis, mechanical heart valves. | Cardiac and vascular patients |
| C03CA01 | Furosemide | C (Cardiovascular) | Loop diuretic. Removes excess fluid in heart failure, kidney disease, and edema. | Heart failure, kidney disease |
| C07AB02 | Metoprolol | C (Cardiovascular) | Beta-blocker. Slows heart rate, lowers blood pressure. Used in heart failure, hypertension, post-heart attack. | Cardiac patients |
| C09AA02 | Enalapril | C (Cardiovascular) | ACE inhibitor. Lowers blood pressure and protects the heart and kidneys. Used in hypertension, heart failure, diabetic nephropathy. | Hypertension, heart failure |
| J01DD04 | Ceftriaxone | J (Anti-infectives) | Third-generation cephalosporin antibiotic. Broad-spectrum IV antibiotic for pneumonia, urinary tract infections, sepsis. | Infectious disease admissions |
| N02BE01 | Acetaminophen (Paracetamol) | N (Nervous system) | Analgesic and antipyretic. First-line treatment for pain and fever. | Nearly all hospitalized patients |
| N05BA06 | Lorazepam | N (Nervous system) | Benzodiazepine. Used for anxiety, insomnia, seizures, and procedural sedation in the hospital setting. | Psychiatric and surgical patients |

**Why ATC matters for clinical data analysis:**

- Standardized drug identification enables analysis across hospitals and countries
- ATC levels allow analysis at different granularities (e.g., all cardiovascular drugs at Level 1, or specifically beta-blockers at Level 4)
- The n_medications variable in our dataset counts the total number of distinct ATC-coded drugs at discharge
- Drug-outcome studies rely on ATC codes to define exposure groups

> **Note:** A single drug can appear under different ATC codes if it has multiple indications. Aspirin, for example, is classified as B01AC06 when used as an antiplatelet agent (blood group) and as N02BA01 when used as an analgesic (nervous system group). The ATC code reflects the intended use, not just the chemical identity.

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Lecture — LOINC for Laboratory Data (20 min)

LOINC (Logical Observation Identifiers Names and Codes) is the international standard for identifying laboratory tests and clinical observations. While ICD-10 codes what is wrong and ATC codes what drug is given, LOINC codes what was measured.

**Why LOINC exists:**

Without LOINC, the same lab test can be named differently across hospitals:

- "Glucose, serum" vs "Blood sugar" vs "GLU" vs "Glucose (S)" vs "BS fasting"

These all refer to the same test, but a computer cannot recognize them as equivalent without a standard code. LOINC assigns a unique code to each distinct test definition.

**Structure of a LOINC code:**

Each LOINC code is defined by 6 axes:

| Axis | What It Specifies | Example |
|------|-------------------|---------|
| Component | What is being measured | Glucose |
| Property | Type of measurement (mass concentration, enzyme activity, etc.) | Mass concentration (MCnc) |
| Time | Point in time vs over a duration | Point in time (Pt) |
| System | The specimen type | Serum/Plasma (Ser/Plas) |
| Scale | Quantitative, ordinal, nominal, or narrative | Quantitative (Qn) |
| Method | How the measurement was performed (optional) | — |

**Example: LOINC 2345-7 (Glucose)**

```
Component:  Glucose
Property:   Mass concentration
Time:       Point in time
System:     Serum/Plasma
Scale:      Quantitative
Method:     (not specified)
```

This fully specifies: "a quantitative measurement of glucose mass concentration in serum or plasma at a single point in time."

**8 common lab tests with LOINC codes:**

| LOINC Code | Test Name | What It Measures | Reference Range | Clinical Significance |
|-----------|-----------|-----------------|----------------|----------------------|
| 6690-2 | WBC (Leukocytes) | White blood cell count in blood | 4.5-11.0 (10^3/uL) | Elevated: infection, inflammation. Low: immunosuppression. |
| 718-7 | Hemoglobin | Hemoglobin concentration in blood | M: 13.5-17.5, F: 12.0-15.5 g/dL | Low: anemia (common in hospitalized patients). High: polycythemia. |
| 2160-0 | Creatinine | Serum creatinine concentration | 0.6-1.2 mg/dL | Elevated: impaired kidney function. Used to calculate eGFR. |
| 4548-4 | HbA1c | Glycated hemoglobin in blood | Below 5.7% (normal) | 5.7-6.4%: prediabetes. Above 6.5%: diabetes. Reflects 2-3 month glucose control. |
| 2345-7 | Glucose | Serum glucose concentration | 70-100 mg/dL (fasting) | Low: hypoglycemia. High: hyperglycemia/diabetes. |
| 777-3 | Platelets | Platelet count in blood | 150-400 (10^3/uL) | Low: bleeding risk. High: clotting risk. |
| 2951-2 | Sodium | Serum sodium concentration | 136-145 mmol/L | Low: hyponatremia (common in heart failure). High: hypernatremia (dehydration). |
| 2823-3 | Potassium | Serum potassium concentration | 3.5-5.0 mmol/L | Low: arrhythmia risk. High: cardiac arrest risk. Critical to monitor. |

**LOINC in relation to our dataset:**

The lab variables in the hospital discharge dataset correspond to specific LOINC codes:

| Dataset Variable | LOINC Code | Full LOINC Name |
|-----------------|-----------|-----------------|
| wbc | 6690-2 | Leukocytes [#/volume] in Blood by Automated count |
| hemoglobin | 718-7 | Hemoglobin [Mass/volume] in Blood |
| creatinine | 2160-0 | Creatinine [Mass/volume] in Serum or Plasma |
| hba1c | 4548-4 | Hemoglobin A1c/Hemoglobin.total in Blood |

**Reference ranges are not universal:**

Reference ranges can vary by:

- **Laboratory method** — different analyzers may produce slightly different values
- **Patient population** — pediatric, adult, and geriatric ranges differ
- **Sex** — hemoglobin has different normal ranges for males and females
- **Institution** — each lab validates its own reference ranges

This is why LOINC separates the test identity (the code) from the reference range (provided by the local lab). The same LOINC code at two hospitals may have slightly different reference ranges.

> **Note:** When analyzing lab data, always check which reference ranges apply. A hemoglobin of 13.0 g/dL is normal for a male but could be slightly low for an athlete, and is above average for many hospitalized elderly patients. Context matters more than absolute numbers.

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Hands-on — Code Lookup Exercise (25 min)

In this session, you will practice using online tools to look up clinical codes. This simulates what medical coders, researchers, and clinical informaticists do routinely when working with hospital data.

**Tools you will use:**

| Tool | URL | What It Does |
|------|-----|-------------|
| WHO ICD-10 Browser | https://icd.who.int/browse/ | Look up ICD-10 diagnosis codes by searching for a disease or browsing by chapter |
| WHO ATC/DDD Index | https://atcddd.fhi.no/atc_ddd_index/ | Look up ATC drug codes by searching for a drug name or browsing by anatomical group |
| LOINC Search | https://loinc.org/search/ | Look up LOINC codes for laboratory tests by searching for a test name |

**Exercise 1: ICD-10 Code Lookup (clinical description to code)**

Given the following clinical descriptions, find the correct ICD-10 code using the WHO ICD-10 Browser:

| Clinical Description | Find the ICD-10 Code |
|---------------------|---------------------|
| Type 2 diabetes mellitus with diabetic chronic kidney disease | Search "diabetes kidney" or browse E11 subcategories |
| Acute ST-elevation myocardial infarction of the anterior wall | Search "myocardial infarction anterior" |
| Community-acquired pneumonia due to Streptococcus pneumoniae | Search "pneumococcal pneumonia" |
| Chronic obstructive pulmonary disease with acute lower respiratory infection | Search "COPD infection" or browse J44 |
| Deep vein thrombosis of the left lower extremity | Search "deep vein thrombosis" |

**Exercise 2: ICD-10 Code Interpretation (code to clinical description)**

Given the following ICD-10 codes, identify what they mean using the WHO ICD-10 Browser:

| ICD-10 Code | What Does This Code Mean? |
|------------|--------------------------|
| I48.91 | Look up in the browser |
| N17.9 | Look up in the browser |
| K85.9 | Look up in the browser |
| J96.00 | Look up in the browser |
| E87.1 | Look up in the browser |

**Exercise 3: ATC Drug Code Lookup**

Using the WHO ATC/DDD Index, find the ATC codes for the following medications:

| Drug Name | Find the ATC Code |
|-----------|-------------------|
| Amlodipine | Search by drug name |
| Omeprazole | Search by drug name |
| Insulin glargine | Search by drug name |
| Amoxicillin | Search by drug name |

**Exercise 4: ATC Code Interpretation**

Given the following ATC codes, identify the drug and its therapeutic category:

| ATC Code | What Drug and Category? |
|----------|------------------------|
| C10AA05 | Look up in the index |
| B01AF01 | Look up in the index |
| R03AC02 | Look up in the index |
| N06AB06 | Look up in the index |

**Exercise 5: LOINC Code Lookup**

Using LOINC Search, find the LOINC codes for the following lab tests:

| Lab Test Description | Find the LOINC Code |
|---------------------|---------------------|
| Serum albumin concentration | Search "albumin serum" |
| Blood urea nitrogen | Search "urea nitrogen serum" |
| Total bilirubin in serum | Search "bilirubin total serum" |
| Prothrombin time (PT) | Search "prothrombin time" |

**Tips for using the lookup tools:**

- **ICD-10 Browser:** Use the search box at the top. If a search returns too many results, try adding more specific terms. You can also browse by chapter using the left navigation tree.
- **ATC Index:** Search by drug name (generic name, not brand name). The result shows the full ATC hierarchy from Level 1 to Level 5.
- **LOINC Search:** Search by test name. Results may show multiple entries for the same test (different methods, scales, or specimen types). Look for the one matching the description most closely. Filter by "Laboratory" class to narrow results.

> **Note:** Code lookup is a practical skill, not a memorization exercise. In real clinical data work, you will constantly look up codes to verify that a dataset's coded variables match their clinical descriptions. Even experienced medical coders use reference tools daily. The goal of this exercise is to become comfortable navigating these reference systems, not to memorize specific codes.

[Back to Table of Contents](#table-of-contents)
