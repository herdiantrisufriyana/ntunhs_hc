# Hands-on with Orange: Clinical Data Visualization

**Herdiantri Sufriyana**
Graduate Institute of Artificial Intelligence and Big Data in Healthcare
National Taiwan University of Nursing and Health Sciences

---

## Table of Contents

1. [Subtopics](#subtopics)
2. [Prerequisites](#prerequisites)
3. [Session 1: Lecture — Choosing Visualizations for Clinical Data (10 min)](#session-1-lecture--choosing-visualizations-for-clinical-data-10-min)
4. [Session 2: Hands-on — Box Plot for Lab Value Comparisons (15 min)](#session-2-hands-on--box-plot-for-lab-value-comparisons-15-min)
5. [Session 3: Hands-on — Distributions for Clinical Variables (15 min)](#session-3-hands-on--distributions-for-clinical-variables-15-min)
6. [Session 4: Hands-on — Scatter Plot for Clinical Relationships (15 min)](#session-4-hands-on--scatter-plot-for-clinical-relationships-15-min)
7. [Session 5: Lecture — Visualizing Categorical Associations (10 min)](#session-5-lecture--visualizing-categorical-associations-10-min)
8. [Session 6: Hands-on — Mosaic Display for Clinical Associations (15 min)](#session-6-hands-on--mosaic-display-for-clinical-associations-15-min)
9. [Session 7: Hands-on — Heat Map for Lab Correlation Patterns (15 min)](#session-7-hands-on--heat-map-for-lab-correlation-patterns-15-min)

---

## Subtopics

- Matching chart type to variable type (continuous vs categorical)
- Comparing clinical measurements between patient groups
- Interpreting distribution shapes in hospital data
- Exploring relationships between clinical variables
- Visualizing categorical associations with mosaic displays
- Discovering lab value correlation patterns

[Back to Table of Contents](#table-of-contents)

---

## Prerequisites

This hands-on continues from **Meeting 11** (Hands-on with Orange: Clinical Data Cleaning and Feature Engineering). Open your Meeting 11 workflow — you will need:

- The **cleaned hospital discharge dataset** with derived clinical flags
- Variables available: age, length_of_stay, wbc, hemoglobin, creatinine, n_medications, charlson_score, admission_type, icu_stay, diabetes_flag, leukocytosis, anemia, hypertension, age_group, high_comorbidity
- **Target** variable: readmission_30d (whether the patient was readmitted within 30 days)

All visualizations in this session connect from the final cleaned Data Table output of your Meeting 11 workflow.

[Back to Table of Contents](#table-of-contents)

---

## Session 1: Lecture — Choosing Visualizations for Clinical Data (10 min)

- The type of variables you have determines which chart to use:
  - **Continuous vs continuous** — Scatter Plot (e.g., age vs length_of_stay)
  - **Continuous vs categorical** — Box Plot or Distributions (e.g., hemoglobin by readmission_30d)
  - **Categorical vs categorical** — Mosaic Display (e.g., icu_stay by readmission_30d)
- Clinical data visualization serves two purposes:
  - **Comparing groups** — Are lab values different between readmitted and non-readmitted patients?
  - **Exploring relationships** — Do older patients stay longer? Do patients with more comorbidities take more medications?
- What clinicians expect to see in hospital data:
  - **Distributions** — Is age right-skewed (elderly population)? Is length_of_stay heavy-tailed (a few very long stays)?
  - **Group comparisons** — Median lab values between readmitted vs not readmitted
  - **Correlation patterns** — Which lab values move together (e.g., creatinine and hemoglobin in kidney disease)?
- Good visualization makes patterns visible before any statistical test — if you cannot see a difference in the plot, the effect is likely small

*Can you guess why box plots are preferred over bar charts for comparing lab values between groups?*

[Back to Table of Contents](#table-of-contents)

---

## Session 2: Hands-on — Box Plot for Lab Value Comparisons (15 min)

**Widgets:** Box Plot

1. Connect **Cleaned data** (your final Data Table from Meeting 11) → **Box Plot** via **Selected Data → Data**
2. Set **Variable** to **wbc**
3. Set **Subgroups** to **readmission_30d**
4. Examine the plot:
   - Compare the **median** (line inside the box) between readmitted and non-readmitted groups
   - Compare the **IQR** (box height) — a wider box means more variability in that group
   - Look for **outliers** (dots beyond the whiskers) — these are patients with extreme WBC values
5. Change **Variable** to **hemoglobin** and keep **Subgroups** as **readmission_30d**
   - Is hemoglobin lower in readmitted patients? This could suggest anemia as a readmission risk factor
6. Change **Variable** to **creatinine** and keep **Subgroups** as **readmission_30d**
   - Is creatinine higher in readmitted patients? Elevated creatinine suggests kidney dysfunction
7. Now change **Subgroups** to **icu_stay** (keep creatinine as the variable)
   - Are creatinine levels higher in ICU patients? ICU patients often have worse kidney function
8. Try **Variable** = **length_of_stay** with **Subgroups** = **readmission_30d**
   - Do readmitted patients have shorter or longer initial stays?

**Check these:**
- Which lab value shows the largest difference between readmitted and non-readmitted groups?
- Are there many outliers? In clinical data, outliers often represent genuinely sick patients rather than data errors
- Does changing the subgroup variable reveal different patterns?

> **Note:** Box plots show the five-number summary: minimum, Q1 (25th percentile), median (50th percentile), Q3 (75th percentile), and maximum. The IQR (Q1 to Q3) contains the middle 50% of patients. Outliers are defined as values beyond 1.5 times the IQR from Q1 or Q3.

[Back to Table of Contents](#table-of-contents)

---

## Session 3: Hands-on — Distributions for Clinical Variables (15 min)

**Widgets:** Distributions

1. Connect **Cleaned data** → **Distributions** via **Selected Data → Data**
2. Select **age** from the variable dropdown
   - Hospital populations are often **right-skewed** (more elderly patients) — check if this is the case
   - The distribution is automatically split by the target variable (**readmission_30d**)
   - Look for age ranges where readmitted patients are more concentrated
3. Select **length_of_stay**
   - Expect a **heavy right tail** — most patients stay a few days, but some stay weeks or months
   - This skewness is typical of hospital data and has implications for statistical analysis (means are pulled by extreme values)
4. Select **wbc** (white blood cell count)
   - A normal WBC distribution should be roughly bell-shaped, centered around 5-10 x10^9/L
   - Is the distribution different for readmitted vs non-readmitted patients?
5. Select **hemoglobin**
   - Look for whether readmitted patients have a shifted distribution (lower hemoglobin)
6. Select **creatinine**
   - Creatinine is typically right-skewed because kidney dysfunction causes values to rise dramatically in some patients
7. Select **n_medications**
   - Does the number of medications differ between readmitted and non-readmitted groups?

**Check these:**
- Which variables are approximately normal (bell-shaped) and which are skewed?
- For skewed variables, does the skewness make clinical sense?
- When split by readmission_30d, which variables show the clearest separation between groups?

> **Note:** Understanding distribution shapes matters clinically. A right-skewed length_of_stay tells you most patients are discharged quickly but a few have prolonged stays — the mean is not representative, and the median is a better summary. Skewed lab values (like creatinine) may need log transformation before parametric statistical tests.

[Back to Table of Contents](#table-of-contents)

---

## Session 4: Hands-on — Scatter Plot for Clinical Relationships (15 min)

**Widgets:** Scatter Plot

1. Connect **Cleaned data** → **Scatter Plot** via **Selected Data → Data**
2. Set **x-axis** to **age**, **y-axis** to **length_of_stay**, **Color** to **readmission_30d**
   - Enable **Show regression line**
   - Is there a positive relationship? Do older patients tend to stay longer?
   - Do readmitted patients (one color) cluster in a particular region of the plot?
3. Change to **x-axis** = **creatinine**, **y-axis** = **hemoglobin**, **Color** = **readmission_30d**
   - Look for an **inverse relationship** — as creatinine increases, hemoglobin often decreases
   - This pattern reflects chronic kidney disease (CKD): damaged kidneys produce less erythropoietin, leading to anemia
   - Do readmitted patients cluster in the high-creatinine, low-hemoglobin corner?
4. Change to **x-axis** = **charlson_score**, **y-axis** = **n_medications**, **Color** = **icu_stay**
   - Patients with higher comorbidity scores tend to take more medications
   - Are ICU patients concentrated at higher comorbidity and medication counts?
5. Try **x-axis** = **age**, **y-axis** = **creatinine**, **Color** = **readmission_30d**
   - Does kidney function (creatinine) worsen with age?
   - Is the age-creatinine relationship different for readmitted patients?

**Check these:**
- Which variable pair shows the strongest visible relationship?
- Does the regression line slope match your clinical expectation?
- Are there clusters of readmitted patients in specific regions of the scatter plot?

> **Recall Meeting 11:** The derived flags (diabetes_flag, anemia, leukocytosis) were created by applying clinical thresholds to continuous lab values. The scatter plots let you see the continuous relationships that those binary flags simplify. For example, the creatinine-hemoglobin scatter plot shows the full spectrum of kidney-anemia interactions, not just the flagged extremes.

[Back to Table of Contents](#table-of-contents)

---

## Session 5: Lecture — Visualizing Categorical Associations (10 min)

- When **both variables are categorical**, bar charts and scatter plots do not work well
- Instead, use a **Mosaic Display** — a grid of rectangles where:
  - **Tile width** represents the proportion of one variable's categories
  - **Tile height** represents the proportion of the other variable's categories within each column
  - **Tile color** represents the **deviation from expected** — how much the observed count differs from what you would expect if the two variables were independent
- Reading a mosaic display:
  - **Blue tiles** = more observations than expected (positive association)
  - **Red tiles** = fewer observations than expected (negative association)
  - **Equal-sized tiles with no color** = no association (independence)
- Example: if ICU patients are readmitted more often than expected, the ICU-readmitted tile will be blue (larger than expected) and the ICU-not-readmitted tile will be red (smaller than expected)
- Mosaic displays are essentially visual contingency tables — they show the same information as a chi-squared test but in a form you can see instantly

*Can you guess what it means if all tiles are the same shade of gray (no blue or red)?*

[Back to Table of Contents](#table-of-contents)

---

## Session 6: Hands-on — Mosaic Display for Clinical Associations (15 min)

**Widgets:** Mosaic Display

1. Connect **Cleaned data** → **Mosaic Display** via **Selected Data → Data**
2. Set the variables to **admission_type** and **readmission_30d**
   - Look at the tile sizes: are emergency admissions readmitted at a different rate than elective admissions?
   - Check tile colors: blue means more than expected, red means fewer than expected
   - If the emergency-readmitted tile is blue, it suggests emergency patients are readmitted more often than expected by chance
3. Change the variables to **icu_stay** and **readmission_30d**
   - Is ICU stay associated with readmission?
   - A blue tile for icu_stay=Yes and readmission_30d=Yes would suggest ICU patients are readmitted more frequently
4. Change to **diabetes_flag** and **readmission_30d**
   - Is diabetes associated with higher readmission?
   - Diabetes is a well-known risk factor for hospital readmission — does the mosaic display confirm this?
5. Try **high_comorbidity** and **readmission_30d**
   - Patients with high comorbidity burden (Charlson score above the threshold) may have higher readmission rates
6. Try **anemia** and **icu_stay**
   - Is anemia more common among ICU patients?
   - This would make clinical sense: severely anemic patients are more likely to require intensive care

**Check these:**
- Which categorical pair shows the strongest color deviation (strongest association)?
- Do the associations match your clinical expectations?
- Are any associations surprising or unexpected?

> **Note:** The mosaic display visualizes the chi-squared test. Strong blue or red coloring means the observed frequency deviates substantially from the expected frequency under independence. This is equivalent to a large chi-squared statistic and a small p-value — but you can see the pattern directly instead of relying on a single number.

[Back to Table of Contents](#table-of-contents)

---

## Session 7: Hands-on — Heat Map for Lab Correlation Patterns (15 min)

**Widgets:** Select Columns, Correlations (or Heat Map)

This session examines how continuous lab values correlate with each other. Since correlation requires continuous variables, we first isolate only the numeric lab and clinical measurements.

1. Connect **Cleaned data** → **Select Columns** (label it "Select continuous variables") via **Selected Data → Data**
   - Move the following to **Features**: wbc, hemoglobin, creatinine, age, length_of_stay, n_medications, charlson_score
   - Move all categorical and flag variables to **Ignored** (admission_type, icu_stay, diabetes_flag, leukocytosis, anemia, hypertension, age_group, high_comorbidity, readmission_30d)

**Option A: Using the Correlations widget (if available)**

2. Connect **Select continuous variables** → **Correlations** via **Data**
3. The **Correlations** widget shows a matrix of pairwise Pearson correlation coefficients
4. Look for strong correlations (values close to +1 or -1):
   - **creatinine and hemoglobin** — expect a negative correlation (inverse relationship in CKD)
   - **charlson_score and n_medications** — expect a positive correlation (more comorbidities = more medications)
   - **age and charlson_score** — expect a positive correlation (older patients accumulate more comorbidities)
   - **age and creatinine** — expect a weak positive correlation (kidney function declines with age)

**Option B: Using the Heat Map widget**

2. Connect **Select continuous variables** → **Heat Map** via **Data**
3. The **Heat Map** widget displays a color-coded matrix of the data values
   - Enable **Clustering** on both rows and columns to group similar variables and similar patients together
   - Variables that cluster together on the column axis have correlated patterns across patients
4. Look for vertical bands of similar color — these indicate groups of patients with similar lab profiles

**Interpreting the correlation patterns:**

5. Identify variable pairs with strong correlations:
   - **Negative correlation** (e.g., creatinine-hemoglobin): as one increases, the other decreases. Clinically, this reflects the erythropoietin pathway — damaged kidneys (high creatinine) produce less erythropoietin, causing anemia (low hemoglobin)
   - **Positive correlation** (e.g., charlson_score-n_medications): as one increases, the other increases. Patients with more chronic conditions take more medications
   - **Weak or no correlation** (e.g., wbc-hemoglobin): these lab values measure different physiological systems and may be independent
6. Consider the clinical implications:
   - Strongly correlated variables carry redundant information — including both in a predictive model may cause multicollinearity
   - Unexpected correlations may reveal confounders or mediators for hypothesis testing

**Check these:**
- Which variable pair has the strongest positive correlation?
- Which variable pair has the strongest negative correlation?
- Are there any unexpected correlations that warrant further investigation?
- Do the correlation patterns match known clinical pathophysiology?

> **Note:** Correlation measures linear association between two continuous variables. A Pearson correlation of +1 means perfect positive linear relationship, -1 means perfect negative linear relationship, and 0 means no linear relationship. In clinical data, correlations above 0.7 or below -0.7 are considered strong and may indicate multicollinearity if both variables are used as predictors. The creatinine-hemoglobin inverse correlation is one of the most clinically important patterns in hospital data because it reflects the kidney-anemia axis common in chronic kidney disease and acute kidney injury.

[Back to Table of Contents](#table-of-contents)
