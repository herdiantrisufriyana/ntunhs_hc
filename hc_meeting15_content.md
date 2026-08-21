# Capstone Project: Clinical Data Analysis Report

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Overview](#1-overview)
2. [Report Structure](#2-report-structure)
3. [Presentation Format](#3-presentation-format)
4. [Grading Rubric](#4-grading-rubric)
5. [Connecting Study Plan to Capstone](#5-connecting-study-plan-to-capstone)
6. [Peer Feedback Guidelines](#6-peer-feedback-guidelines)
7. [Submission Instructions](#7-submission-instructions)

---

## 1. Overview

This is the **capstone assessment** for the Hospital Clinical Data Analysis course, worth **45% of the final grade**.

The capstone project extends your study plan from Meeting 09 (Week 9) into a completed clinical data analysis. Over Meetings 10-14, you learned to use Orange for data loading, cleaning, visualization, comparative analysis, and outcome modeling. The capstone is where you apply those skills to the analysis you designed in your study plan.

**What the capstone demonstrates:**

- You can carry out a clinical data analysis from question to interpretation
- You can use Orange to load, clean, visualize, and analyze clinical data
- You can present findings in a structured clinical research format
- You can interpret results with clinical context, not just statistical output

**Relationship to the study plan:** Your capstone report should follow the analysis design laid out in your study plan. If you discovered during Meetings 10-14 that parts of your plan needed adjustment (e.g., a variable was unavailable, a different analytical method was more appropriate), document what changed and why. Deviations from the study plan are acceptable if justified.

> **Note:** The capstone is both a written report and an oral presentation. You submit the report and your Orange workflow file, and you present your key findings to the class during Meeting 15.

[Back to Table of Contents](#table-of-contents)

---

## 2. Report Structure

Your written report must contain the following sections. Follow the structure of a clinical research paper.

### 2.1 Introduction

- **Clinical question:** State the clinical question from your study plan, refined based on what you learned during Meetings 10-14
- **Background:** Briefly describe why this question matters clinically (1-2 paragraphs). What is the clinical context? What is known from existing literature?
- **Significance:** What would the answer to this question mean for clinical practice, hospital operations, or patient outcomes?

### 2.2 Methods

- **Study population:** Define the inclusion and exclusion criteria you applied. Report the number of patients before and after applying each criterion (CONSORT-style flow). If criteria changed from the study plan, explain why.
- **Variable definitions:** Provide the variable definitions table from your study plan, updated to reflect any changes made during analysis. Include variable name, clinical definition, role (exposure/outcome/covariate), data type, source system, and codes/units.
- **Data quality assessment results:** Report what you found during data quality assessment:
  - Number and percentage of missing values per variable
  - Any implausible values detected and how they were handled
  - Imputation method used and its justification
  - Any duplicate records identified and resolved
- **Analytical approach:** Describe the methods used, in order:
  - Descriptive analysis (summary statistics, Table 1)
  - Comparative analysis (statistical tests or visual comparisons used)
  - Outcome modeling (model type, variables included, evaluation metrics)
  - Reference the specific Orange widgets used for each step

### 2.3 Results

- **Descriptive analysis (Table 1):** Present a summary table of the study population. For continuous variables, report mean (SD) or median (IQR). For categorical variables, report count (%). Split by the exposure or outcome variable where appropriate.
- **Comparative analysis or outcome modeling results:** Present the main findings:
  - For group comparisons: effect sizes (odds ratios, mean differences) with confidence intervals
  - For prediction models: AUC, sensitivity, specificity, confusion matrix
  - Report both unadjusted and adjusted results if applicable
- **Visualizations from Orange:** Include the key visualizations that support your findings:
  - Box plots comparing groups
  - Distribution plots showing outcome or exposure patterns
  - Scatter plots illustrating variable relationships
  - ROC curves for prediction models
  - Mosaic displays for categorical associations
  - Each visualization must have a figure number, a descriptive title, axis labels, and a brief caption explaining what the figure shows

> **Note:** Export visualizations from Orange by right-clicking the plot area and selecting "Save Image" or using the screenshot tool. Ensure figures are legible at the size they will appear in the report.

### 2.4 Discussion

- **Interpretation with clinical context:** What do the results mean clinically? Do they align with existing clinical knowledge? If the odds ratio for your exposure is 2.3, what does that mean for patient care?
- **Comparison with existing literature:** How do your findings compare with published studies on the same topic? Are the effect sizes similar? Are there discrepancies, and if so, what might explain them?
- **Biases and limitations:** Revisit the biases and limitations you identified in your study plan. Did any of them materialize during the analysis? Were there additional limitations you discovered during execution?
- **Implications for clinical practice:** If your findings were confirmed in a larger study, what would the practical implications be? Would it change screening, treatment, discharge planning, or resource allocation?

[Back to Table of Contents](#table-of-contents)

---

## 3. Presentation Format

Each student presents their capstone project during Meeting 15.

**Time allocation:**

- **Presentation:** 15-20 minutes per student
- **Q&A:** 5-10 minutes following each presentation

**Presentation structure:**

Present your key findings, not every detail of the report. Focus on the following flow:

1. **Clinical question** (1-2 minutes) — State the question and why it matters. The audience should understand the clinical context immediately.
2. **Methods overview** (3-4 minutes) — Briefly describe the study population, key variables, and analytical approach. Show the Orange workflow if helpful. Do not read the variable definitions table; summarize the design verbally.
3. **Key results** (7-10 minutes) — Present the most important findings with supporting visualizations. Walk the audience through the figures. Highlight the main effect size (e.g., odds ratio, AUC) and what it means.
4. **Clinical interpretation** (3-4 minutes) — What do the results mean for patients and clinicians? Discuss limitations honestly. End with implications or a recommendation.

**Presentation tips:**

- Use slides (PowerPoint, PDF, or equivalent) with large fonts and clear figures
- Limit text on slides; let the visualizations carry the message
- Practice the timing; 15 minutes goes quickly
- Anticipate questions about your methods, data quality decisions, and clinical interpretation

> **Note:** The presentation is not a reading of the report. It is a distilled version that communicates the core message. Think of it as explaining your findings to a clinical colleague who has 20 minutes.

[Back to Table of Contents](#table-of-contents)

---

## 4. Grading Rubric

The capstone is graded on a five-level rubric (A through E). Each higher grade builds on all criteria of the grade below it.

| Grade | Criteria |
|-------|----------|
| **E** | Not submitted or not presented, or content is unrelated to the course. |
| **D** | Major flaws in the analysis (incorrect methods, substantial errors in data handling). Minimal effort evident in the report or presentation. Incomplete analysis (missing key sections). Substantial mismatch between the study plan and the capstone (different question, population, or methods without explanation). |
| **C** | Implements the analysis correctly following the study plan. Report contains all required sections (Introduction, Methods, Results, Discussion). Presentation is structured and covers the clinical question, methods, results, and interpretation. Orange workflow produces valid output. |
| **B** | All C criteria, plus: addresses data quality issues found during analysis (documents missing values, implausible values, and how they were handled). Visualizations are clear, well-labeled, and appropriately chosen for the variable types. Interprets results with clinical domain knowledge (not just statistical output). |
| **A** | All B criteria, plus: discusses biases and limitations with specificity (names the bias, explains its direction, estimates its impact). Draws implications for clinical practice supported by evidence-based reasoning. Demonstrates critical thinking about what the results mean and what they do not mean. |

> **Note:** A grade of C requires a technically correct, complete analysis. A grade of B additionally requires evidence of careful data quality handling and clinically informed interpretation. A grade of A further requires the kind of critical reasoning expected in clinical research: awareness of what could go wrong and what the findings mean in practice.

[Back to Table of Contents](#table-of-contents)

---

## 5. Connecting Study Plan to Capstone

The following table shows how each section of the study plan (Meeting 09) maps to the capstone report, and which Orange meeting (Meetings 10-14) supports the analysis for each section.

| Study Plan Section (Meeting 09) | Capstone Report Section | Supporting Orange Meeting(s) |
|---------------------------------|-------------------------|------------------------------|
| Clinical Question | Introduction: Clinical question and background | - |
| Data Source Description | Methods: Study population (data source context) | Meeting 10: Loading datasets, setting variable types and roles |
| Study Population (inclusion/exclusion) | Methods: Study population (criteria and patient flow) | Meeting 11: Filtering study population with Select Rows |
| Variable Definitions | Methods: Variable definitions (updated table) | Meeting 10: Variable roles and types; Meeting 11: Derived clinical flags with Formula |
| Data Quality Assessment Strategy | Methods: Data quality assessment results | Meeting 10: Column Statistics for plausibility checks; Meeting 11: Missing value handling with Impute |
| Analytical Approach (descriptive) | Results: Table 1 (descriptive summary) | Meeting 10: Data Table and Column Statistics; Meeting 12: Distributions and Box Plots |
| Analytical Approach (comparative) | Results: Comparative analysis results | Meeting 12: Box Plot, Mosaic Display, Scatter Plot; Meeting 13: Group comparisons, logistic regression for association testing |
| Analytical Approach (modeling) | Results: Outcome modeling results (AUC, confusion matrix) | Meeting 14: Train models, Test and Score, ROC Analysis, validation |
| Ethical Considerations | Discussion: Mentioned as part of study context | - |
| Biases and Limitations | Discussion: Biases and limitations (revisited with findings) | Meeting 13: Stratified analysis, Simpson's paradox, confounding |

> **Note:** The study plan is the design; the capstone is the execution. Every section of the study plan has a corresponding section in the capstone report. The Orange meetings provide the tools to carry out each step. If a step in your study plan proved infeasible during execution, document the deviation in the Methods section of your report.

[Back to Table of Contents](#table-of-contents)

---

## 6. Peer Feedback Guidelines

During each presentation, the audience provides structured feedback. Focus on the following areas.

**Clarity of clinical question:**

- Is the clinical question clearly stated and clinically relevant?
- Can you understand the PICO components (population, intervention/exposure, comparison, outcome) from the presentation?
- Is the significance of the question evident?

**Appropriate methods:**

- Are the inclusion/exclusion criteria appropriate for the clinical question?
- Is the analytical approach suitable for the variable types and study design?
- Were data quality issues handled appropriately?
- Does the Orange workflow follow a logical pipeline?

**Quality of visualizations:**

- Are figures well-labeled (axes, titles, legends)?
- Are the chart types appropriate for the variable types being compared?
- Do the visualizations effectively communicate the findings?
- Are there any misleading aspects (truncated axes, missing labels, inappropriate scales)?

**Soundness of interpretation:**

- Do the conclusions follow from the results presented?
- Does the presenter distinguish between statistical significance and clinical significance?
- Are limitations acknowledged honestly?
- Is the clinical interpretation reasonable, or does it overstate the findings?

**How to give feedback:**

- Be specific: "The box plot comparing hemoglobin by readmission status clearly shows the group difference" is more useful than "Good figures"
- Be constructive: "The exclusion criteria could be strengthened by specifying ICD-10 codes" is more useful than "Exclusion criteria are weak"
- Ask questions when something is unclear; the presenter may have a good reason for a decision that was not explained in the time available

[Back to Table of Contents](#table-of-contents)

---

## 7. Submission Instructions

Submit the following files via iCLASS by the end of Meeting 15 (Week 15).

**Required submissions:**

| File | Format | Description |
|------|--------|-------------|
| Capstone report | PDF or DOCX | The written report containing all sections described in Section 2 |
| Orange workflow | .ows | The Orange workflow file used to perform the analysis |

**File naming convention:**

- Report: `StudentID_Capstone.pdf` or `StudentID_Capstone.docx`
- Workflow: `StudentID_Capstone.ows`

**Report formatting:**

- No strict page limit, but a complete report typically requires 5-10 pages including figures
- Include all visualizations as embedded figures within the report (do not submit figures as separate files)
- Number all figures and tables sequentially (Figure 1, Figure 2, Table 1, etc.)
- Use a readable font size (11-12 pt) and standard margins

**Presentation slides:**

- Slides are used during the oral presentation but do not need to be submitted unless the instructor requests them
- If submitted, use the naming convention: `StudentID_Capstone_Slides.pdf`

> **Note:** The Orange workflow file (.ows) is required because it demonstrates that you built the analysis pipeline yourself and allows the instructor to verify your work. Make sure the workflow is saved with the final state of your analysis, and that the data file path is accessible (or include the data CSV alongside the .ows file).

[Back to Table of Contents](#table-of-contents)