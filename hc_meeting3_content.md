# Data Governance, Privacy, and Ethics in Clinical Data

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Session 1: Lecture — Why Data Governance Matters (10 min)](#session-1-lecture--why-data-governance-matters-10-min)
3. [Session 2: Lecture — Taiwan Personal Data Protection Act (20 min)](#session-2-lecture--taiwan-personal-data-protection-act-20-min)
4. [Session 3: Lecture — De-identification Methods (20 min)](#session-3-lecture--de-identification-methods-20-min)
5. [Session 4: Lecture — IRB Process for Secondary Data Use (15 min)](#session-4-lecture--irb-process-for-secondary-data-use-15-min)
6. [Session 5: Lecture — Ethical Pitfalls in Retrospective Clinical Studies (15 min)](#session-5-lecture--ethical-pitfalls-in-retrospective-clinical-studies-15-min)
7. [Session 6: Hands-on — De-identification Exercise (30 min)](#session-6-hands-on--de-identification-exercise-30-min)

---

## Subtopics

- Why clinical data governance matters and the consequences of failure
- Taiwan's Personal Data Protection Act (PDPA) and its application to hospital data research
- Comparison of PDPA with HIPAA for international context
- Safe Harbor and Expert Determination de-identification methods
- The Institutional Review Board (IRB) process for secondary data use
- Ethical pitfalls in retrospective clinical studies, including algorithmic bias
- Practical exercise in de-identifying a sample clinical record

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — Why Data Governance Matters (10 min)

Hospital clinical data is among the most sensitive information that exists. Every lab result, diagnosis, and treatment record is tied to a real person who trusted the healthcare system with their most private details. Data governance is the set of rules, policies, and practices that ensure this data is handled responsibly.

**Why it matters:**

- **Sensitive patient information** — Clinical records contain diagnoses (including stigmatized conditions like psychiatric illness, HIV, substance use), genetic information, reproductive history, and behavioral data. Unauthorized disclosure can cause real harm to patients.

- **Legal obligations** — Hospitals and researchers are bound by law to protect patient data. In Taiwan, the Personal Data Protection Act (PDPA) imposes penalties for violations. Internationally, regulations like HIPAA (US), GDPR (EU), and APPI (Japan) set similar standards.

- **Trust between patients and the healthcare system** — Patients share information with their doctors because they trust it will remain confidential. If that trust is broken — through data breaches, unauthorized research use, or careless data handling — patients may withhold information, avoid seeking care, or refuse to participate in future research.

- **Consequences of data breaches** — A single data breach can result in:
  - Legal penalties and fines for the institution
  - Loss of institutional reputation
  - Personal liability for the researchers involved
  - Harm to patients (discrimination, insurance denial, social stigma)
  - Loss of research privileges and funding

**Clinical data is not just data — it is a record of someone's most vulnerable moments.** Every person who handles clinical data, whether for patient care, quality improvement, or research, has an obligation to protect it.

> **Note:** Data governance is not just an IT problem. It is a responsibility shared by everyone who touches clinical data — clinicians, researchers, administrators, and students. Understanding governance is a prerequisite for any work with hospital datasets.

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Lecture — Taiwan Personal Data Protection Act (20 min)

Taiwan's Personal Data Protection Act (PDPA), formally the Personal Information Protection Act (PIPA), governs how personal data — including medical records — is collected, processed, and used. For anyone working with hospital data, three articles are especially important.

**Article 6: Special Categories of Personal Data**

Article 6 designates certain types of data as requiring heightened protection. These include:

- Medical records
- Healthcare information
- Genetic data
- Sexual life information
- Criminal records
- Results of health examinations

For these special categories, collection and use require **explicit consent** from the data subject, unless one of the following legal bases applies:

- Required by law
- Necessary for government agencies to perform statutory duties
- Necessary for statistics or academic research by a government agency or academic institution, where the data has been processed to prevent identification of specific individuals
- Necessary for public interest
- Data made public by the data subject themselves
- Necessary for the exercise or defense of rights in legal proceedings

**Implication for hospital data research:** You cannot simply use medical records for a research study because they happen to be available. You need either explicit patient consent or a qualifying legal basis (most commonly: IRB-approved research with de-identified data for academic purposes).

**Article 16: Purpose Limitation**

Data collected for one purpose should not be used for a different purpose without additional legal basis or consent. Hospital data collected for patient care cannot be automatically repurposed for research, commercial analysis, or teaching without proper authorization.

**Example:** A hospital collects lab values to guide treatment decisions. Using those same lab values in a research study on readmission prediction requires separate authorization — the original purpose (clinical care) does not cover the secondary purpose (research).

**Article 19: Conditions for Collection**

Non-government entities (including private hospitals and research institutions) may collect personal data only when:

- The data subject has given consent
- It is necessary for the performance of a contract
- It is necessary for the entity's legal obligations
- It is necessary to prevent harm to the data subject or others
- It is necessary for statistical or academic research (with de-identification)

**How PDPA Applies to Secondary Use of Hospital Data for Research**

Secondary use means using data collected for one purpose (clinical care) for another purpose (research). Under PDPA:

1. **With consent:** If patients gave broad research consent at admission, secondary use may be permitted within the scope of that consent.
2. **Without consent:** If no research consent was obtained, the data must be de-identified before use, and the research must be approved by an IRB. Even then, only the minimum necessary data should be accessed.
3. **Retrospective studies:** Most hospital data analysis courses and research projects use retrospective data — data that was already collected during routine care. This almost always requires IRB review and de-identification.

**Comparison with HIPAA (US) for Context**

| Aspect | Taiwan PDPA | US HIPAA |
|--------|-------------|----------|
| Scope | All personal data across all sectors | Protected health information (PHI) in healthcare entities |
| Special categories | Medical records, genetic data, sexual life, criminal records | All PHI (medical records, billing, insurance) |
| De-identification standard | Required for research without consent; no specific method mandated | Two methods defined: Safe Harbor (18 identifiers) and Expert Determination |
| Consent requirement | Explicit consent for special categories unless legal basis exists | Authorization required unless exception applies (research, public health, TPO) |
| Research exception | Academic research with de-identified data, IRB approval | IRB/Privacy Board waiver, limited data sets with data use agreements |
| Penalties | Criminal penalties possible (up to 5 years imprisonment, fines) | Civil fines (up to USD 1.9 million per violation category per year); criminal penalties for willful violations |

> **Note:** While HIPAA provides more specific technical guidance on de-identification (the Safe Harbor method lists exactly 18 identifier types), Taiwan's PDPA is broader in scope — it covers all personal data, not just health information. Researchers working with hospital data in Taiwan should follow both PDPA requirements and international best practices for de-identification.

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Lecture — De-identification Methods (20 min)

De-identification transforms data so that the identity of individuals cannot be determined. This is the primary mechanism that allows hospital data to be used for research without individual patient consent.

**Two Standard Methods (from HIPAA, widely adopted internationally)**

**1. Safe Harbor Method**

Remove or generalize 18 specific identifier types from the dataset:

| # | Identifier Type | Examples |
|---|----------------|----------|
| 1 | Names | Patient name, next of kin, guarantor |
| 2 | Geographic data smaller than state | Street address, city, ZIP code (first 3 digits may be kept if population > 20,000) |
| 3 | Dates more specific than year | Admission date, discharge date, birth date, death date (year may be kept; ages > 89 must be aggregated) |
| 4 | Phone numbers | Home, mobile, emergency contact |
| 5 | Fax numbers | Hospital fax |
| 6 | Email addresses | Patient or provider email |
| 7 | Social Security / National ID numbers | National ID number (in Taiwan: unified ID number) |
| 8 | Medical record numbers (MRN) | Hospital chart number |
| 9 | Health plan beneficiary numbers | Insurance card number (NHI card number in Taiwan) |
| 10 | Account numbers | Bank account, billing account |
| 11 | Certificate/license numbers | Professional license, driver's license |
| 12 | Vehicle identifiers | License plate number, VIN |
| 13 | Device identifiers and serial numbers | Pacemaker serial number, implant identifier |
| 14 | Web URLs | Patient portal URLs |
| 15 | IP addresses | Hospital network log data |
| 16 | Biometric identifiers | Fingerprints, voiceprints, retinal scans |
| 17 | Full-face photographs | Clinical photos showing the face |
| 18 | Any other unique identifying number | Any code that could be used to re-identify a person |

**2. Expert Determination Method**

A qualified statistical or scientific expert applies methods to determine that the risk of identifying any individual is "very small." The expert must document the methods and results. This approach is more flexible but requires specialized expertise.

**Practical Examples: What Can Be Kept vs. What Must Be Removed**

| Can Be Kept | Must Be Removed or Modified |
|-------------|----------------------------|
| Age (in years, if under 90) | Name |
| Diagnosis codes (ICD-10) | Exact admission date (shift to relative days or keep year only) |
| Lab values (WBC, hemoglobin, creatinine) | Medical record number |
| Medication names and doses | Doctor's name |
| Procedure codes | Hospital name (if small enough to narrow down patients) |
| Length of stay (days) | Ward number or room number |
| Sex (M/F) | Phone number |
| Vital signs | Home address |
| Charlson comorbidity score | National ID number |
| ICU stay (Yes/No) | NHI card number |

**De-identification Strategies for Common Fields**

- **Dates:** Replace exact dates with relative offsets (e.g., "Day 0" = admission, "Day 3" = third day of stay). Alternatively, shift all dates by a random offset (same offset per patient to preserve intervals).
- **Geographic data:** Generalize to region or city-level. Remove addresses entirely.
- **Age:** Keep exact age for patients under 90. For patients 90 and older, report as "90+" to prevent identification in small populations.
- **MRN/patient_id:** Replace with a random study ID. Maintain a crosswalk (linking original ID to study ID) only in a secure, access-restricted location.

**Re-identification Risks with Small Populations**

Even after removing the 18 identifier types, re-identification may still be possible if:

- The population is small (e.g., a rare disease at a small hospital — there may be only one 34-year-old male admitted with that condition in a given year)
- Multiple quasi-identifiers are combined (age + sex + diagnosis + admission year can narrow down to a single person)
- The dataset is linked to external data sources (e.g., public death records, news reports)

**Mitigation:** For small populations, apply additional suppression (remove records with unique combinations) or generalization (widen age ranges, group rare diagnoses into broader categories).

> **Note:** De-identification is not absolute. It reduces risk but does not eliminate it. Researchers must remain vigilant about re-identification possibilities, especially when working with rare conditions, small hospitals, or datasets that overlap with publicly available information.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Lecture — IRB Process for Secondary Data Use (15 min)

An Institutional Review Board (IRB) is an ethics committee that reviews research involving human subjects to ensure that the rights, safety, and welfare of participants are protected. In Taiwan, IRBs at hospitals and universities follow Ministry of Health and Welfare regulations.

**When Is IRB Review Needed for Hospital Data Research?**

IRB review is required whenever you use identifiable patient data for research purposes, even if you are only analyzing existing records. The key question is: **Does the activity meet the definition of "human subjects research"?**

- Using de-identified data that cannot be linked back to individuals may be exempt — but the IRB makes that determination, not the researcher.
- Using identifiable data (even indirectly identifiable) for any research purpose requires IRB approval.
- Quality improvement projects using hospital data may be exempt from IRB review if they are not designed to produce generalizable knowledge — but this boundary is often unclear, and many institutions require IRB review for QI projects as well.

**Three Levels of IRB Review**

| Review Level | When It Applies | Timeline |
|-------------|-----------------|----------|
| **Exempt** | Minimal risk research using existing, de-identified data with no possibility of re-identification | Days to 2 weeks |
| **Expedited** | Minimal risk research using identifiable data, or research involving chart review with a waiver of consent | 2-4 weeks |
| **Full Board** | Greater than minimal risk, or research involving vulnerable populations (children, prisoners, cognitively impaired) | 4-8 weeks or more |

**Most hospital data research falls under expedited review** — you are using existing medical records, the data is not fully de-identified at the time of collection, and you are requesting a waiver of informed consent.

**Data Use Agreements (DUA)**

When data is shared between institutions (e.g., a university researcher analyzing data from a partner hospital), a Data Use Agreement is required. A DUA specifies:

- What data will be shared and in what format
- Who will have access to the data
- How the data will be stored and secured
- What happens to the data when the research is complete (destruction or return)
- Prohibition on re-identification attempts
- Publication restrictions (e.g., no reporting of cell sizes smaller than 5)

**Minimum Necessary Principle**

Only request the minimum amount of data needed to answer your research question. If you are studying readmission risk factors, you do not need the full text of clinical notes, pathology reports, or imaging studies. Request only the variables relevant to your analysis.

**Waiver of Informed Consent for Retrospective Studies**

For retrospective studies using existing hospital data, it is often impractical or impossible to obtain consent from every patient (some may have died, moved, or cannot be located). An IRB can grant a waiver of informed consent if:

1. The research involves no more than minimal risk
2. The waiver will not adversely affect the rights and welfare of subjects
3. The research could not practicably be carried out without the waiver
4. When appropriate, subjects will be provided with additional pertinent information after participation

> **Note:** Even with a waiver of consent, the researcher must still protect patient privacy through de-identification, secure data handling, and adherence to the minimum necessary principle. A waiver of consent is not a waiver of responsibility.

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Lecture — Ethical Pitfalls in Retrospective Clinical Studies (15 min)

Working with existing hospital data introduces unique ethical challenges that go beyond privacy and consent. These pitfalls can compromise the integrity of research findings and, if the findings inform clinical practice, can ultimately harm patients.

**1. Cherry-Picking Outcomes**

- **What it is:** Running analyses on multiple outcome variables and only reporting the ones that show statistically significant results.
- **Example:** A researcher tests whether a medication reduces 30-day readmission, 60-day readmission, 90-day mortality, ICU transfer, and length of stay. Only 90-day mortality shows p < 0.05, so only that result is published — without disclosing that four other outcomes were tested and showed no effect.
- **Why it is harmful:** It inflates the false positive rate. If you test 20 outcomes, one will be significant by chance at the 0.05 level.
- **Prevention:** Pre-specify your primary outcome before analysis. Report all outcomes tested, not just the significant ones.

**2. P-hacking with Multiple Comparisons**

- **What it is:** Repeatedly testing different subgroups, variable transformations, or model specifications until a significant result appears.
- **Example:** The overall analysis shows no effect. But among females over 65 with diabetes and a Charlson score above 3 who were admitted through the emergency department... the effect is significant (p = 0.04).
- **Why it is harmful:** With enough subgroups, you can always find a "significant" result. This is not discovery — it is noise.
- **Prevention:** Adjust for multiple comparisons (Bonferroni, false discovery rate). Pre-specify subgroup analyses. Report the number of tests performed.

**3. Reporting Bias**

- **What it is:** Selectively reporting results that support a desired conclusion while omitting results that do not.
- **Example:** A table shows 15 variables, but the 3 variables that contradicted the hypothesis were quietly removed from the final manuscript.
- **Why it is harmful:** It creates a distorted view of the evidence. Other researchers and clinicians rely on published results to make decisions.
- **Prevention:** Report all variables included in the analysis. Use study registration (e.g., ClinicalTrials.gov) to pre-commit to your analysis plan.

**4. Responsible Use of AI/LLM in Clinical Data Analysis**

- **What it is:** Using large language models (ChatGPT, Claude, etc.) or AI tools to write code, interpret results, or generate clinical conclusions from hospital data.
- **Risks:**
  - LLMs can generate plausible but incorrect statistical interpretations
  - LLMs may hallucinate clinical knowledge (cite studies that do not exist, state incorrect drug interactions)
  - Uploading patient data to cloud-based AI services may violate PDPA and institutional data security policies
  - Over-reliance on AI-generated code without understanding the underlying methods
- **Responsible use:**
  - Never upload identifiable or potentially identifiable patient data to external AI services
  - Verify every AI-generated statistical claim against the actual data
  - Understand the code and methods — do not blindly trust AI-generated analysis pipelines
  - Disclose AI tool use in publications (AI Usage Statement)

**5. Algorithmic Bias from Training on Biased Clinical Data**

- **What it is:** When a prediction model trained on hospital data reflects and amplifies existing biases in healthcare delivery.
- **Example:** If a hospital historically undertreated pain in certain demographic groups, a model trained on that data will learn that those groups have lower pain scores — and may recommend less aggressive pain management for future patients from those groups.
- **Other examples:**
  - Models trained on data from a single hospital may not generalize to other settings
  - Disease prevalence differences across populations can cause models to underperform for minority groups
  - Missing data patterns may correlate with socioeconomic status (patients without insurance have fewer lab tests)
- **Prevention:** Evaluate model performance across demographic subgroups. Be transparent about the data source and its limitations. Do not deploy clinical prediction models without fairness assessment.

> **Note:** Ethical conduct in retrospective clinical research is not just about following rules. It is about maintaining the integrity of the evidence base that clinicians use to make treatment decisions. Every shortcut — every cherry-picked result, every unreported test — erodes that integrity.

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Hands-on — De-identification Exercise (30 min)

In this exercise, you will practice identifying and removing protected information from a sample clinical record using the Safe Harbor method.

**Sample Clinical Record**

Below is a fictional clinical record. Read it carefully and identify every element that must be removed or modified for de-identification.

---

**Discharge Summary**

**Patient Name:** Chen, Mei-Ling
**Medical Record Number:** A20230847
**Date of Birth:** 1958-03-14
**Age:** 68 years
**Sex:** Female
**National ID:** F234567890
**Phone:** 02-2822-7101
**Address:** No. 365, Mingde Road, Beitou District, Taipei City 112

**Attending Physician:** Dr. Lin, Chih-Wei
**Hospital:** Taipei Veterans General Hospital
**Ward:** 12A (Internal Medicine)
**Admission Date:** 2024-11-02
**Discharge Date:** 2024-11-09
**Length of Stay:** 7 days

**Diagnoses:**
- Primary: Type 2 diabetes mellitus with diabetic chronic kidney disease, stage 3 (E11.22)
- Secondary: Essential hypertension (I10)
- Secondary: Hyperlipidemia (E78.5)

**Lab Values at Admission:**
- WBC: 8.2 (10^3/uL)
- Hemoglobin: 11.8 g/dL
- Creatinine: 2.1 mg/dL
- HbA1c: 9.3%
- Fasting glucose: 186 mg/dL

**Medications at Discharge:**
- Metformin 500 mg twice daily
- Lisinopril 10 mg daily
- Atorvastatin 20 mg daily
- Insulin glargine 18 units at bedtime

**Clinical Notes:**
Patient was admitted for uncontrolled diabetes with rising creatinine. Nephrology consult recommended renal-dose adjustments. Patient educated on dietary modifications and insulin self-administration. Follow-up scheduled at Dr. Lin's outpatient clinic on 2024-11-16.

---

**Part 1: Identify Protected Fields (10 min)**

Review each field in the record above and mark it as one of:

- **REMOVE** — must be deleted entirely
- **MODIFY** — must be generalized or replaced
- **KEEP** — safe to retain as-is

Use this worksheet:

| Field | Decision | Rationale |
|-------|----------|-----------|
| Patient Name | ? | |
| Medical Record Number | ? | |
| Date of Birth | ? | |
| Age | ? | |
| Sex | ? | |
| National ID | ? | |
| Phone | ? | |
| Address | ? | |
| Attending Physician | ? | |
| Hospital | ? | |
| Ward | ? | |
| Admission Date | ? | |
| Discharge Date | ? | |
| Length of Stay | ? | |
| Diagnoses (ICD-10 codes) | ? | |
| Lab Values | ? | |
| Medications | ? | |
| Follow-up date and doctor name in notes | ? | |

**Part 2: Propose De-identification Strategies (10 min)**

For each field you marked as MODIFY, propose a specific de-identification strategy:

**Date shifting:**
- What is a reasonable date-shifting approach? (Hint: apply the same random offset to all dates for this patient so that intervals like length of stay are preserved.)
- If dates are shifted by +47 days, what would the admission date become? The discharge date? Is the length of stay preserved?

**Geographic generalization:**
- The address is in Beitou District, Taipei City. What level of geographic detail can be kept? (Hint: Taipei City has a population well over 20,000, so the city name may be acceptable. The district, street address, and ZIP code must be removed.)

**Other modifications:**
- How should the hospital name be handled? (Consider: if the dataset comes from a single hospital, including the hospital name effectively identifies the source institution and narrows the patient pool.)
- How should the ward number be handled?
- How should the attending physician's name be handled?

**Part 3: Post-De-identification Analysis (10 min)**

After de-identification, discuss which analysis questions are still answerable:

| Question | Answerable After De-identification? | Why or Why Not |
|----------|--------------------------------------|----------------|
| What is the average HbA1c of diabetic patients? | ? | |
| Which ward has the highest readmission rate? | ? | |
| Is creatinine level associated with length of stay? | ? | |
| Did Dr. Lin's patients have better outcomes than Dr. Wang's? | ? | |
| Are patients from Beitou District readmitted more often? | ? | |
| Does metformin use reduce 30-day readmission? | ? | |
| What is the age distribution of patients with CKD stage 3? | ? | |

**Expected Answers for the Worksheet (for instructor reference)**

| Field | Decision | Rationale |
|-------|----------|-----------|
| Patient Name | REMOVE | Safe Harbor identifier #1 |
| Medical Record Number | REMOVE | Safe Harbor identifier #8 |
| Date of Birth | MODIFY | Keep year only (1958), or replace with age. Safe Harbor identifier #3 |
| Age | KEEP | Age under 90 is safe to retain |
| Sex | KEEP | Not an identifier |
| National ID | REMOVE | Safe Harbor identifier #7 |
| Phone | REMOVE | Safe Harbor identifier #4 |
| Address | REMOVE | Safe Harbor identifier #2. City name (Taipei) may be kept; district and street must go |
| Attending Physician | REMOVE | Can function as an identifier (Safe Harbor #18) |
| Hospital | MODIFY | Remove or generalize (e.g., "teaching hospital in northern Taiwan") |
| Ward | REMOVE | Can narrow down patient pool (Safe Harbor #18) |
| Admission Date | MODIFY | Shift or keep year only. Safe Harbor identifier #3 |
| Discharge Date | MODIFY | Shift by same offset as admission date. Safe Harbor identifier #3 |
| Length of Stay | KEEP | Derived value, not an identifier |
| Diagnoses (ICD-10) | KEEP | Clinical codes, not identifiers |
| Lab Values | KEEP | Clinical measurements, not identifiers |
| Medications | KEEP | Treatment information, not identifiers |
| Follow-up date and doctor name | REMOVE/MODIFY | Date: shift or remove. Doctor name: remove |

> **Note:** In a real research setting, de-identification is not a one-time task. It must be verified by a second reviewer, documented in the study protocol, and maintained throughout the lifecycle of the dataset — including during analysis, storage, and eventual destruction or archival.

[Back to Table of Contents](#table-of-contents)
