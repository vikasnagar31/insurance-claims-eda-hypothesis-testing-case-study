#  Insurance Claims — Exploratory Data Analysis & Hypothesis Testing
      
> A comprehensive end-to-end data analytics case study on insurance claims data, covering data wrangling, feature engineering, exploratory visualisation, and statistical hypothesis testing using Python.

 ![Alt text](https://knnindia.co.in/uploads/newsfiles/INSURANCE-26-10-2024.jpg)

---

##  Repository Structure
```
├── Insurance_Claims_Case_Study_.ipynb   # Main solved notebook
├── claims.csv                           # Claims transactional data
├── cust_demographics.csv                # Customer demographic data
└── README.md
```

---

##  Problem Statement

The goal of this case study is to build a **360-degree analytical view** of insurance claims data by combining transactional claim records with customer demographics, and then to answer important business questions through exploratory data analysis (EDA) and formal statistical hypothesis testing.

---

##  Tech Stack

| Library | Purpose |
|---|---|
| `pandas` | Data manipulation & wrangling |
| `numpy` | Numerical operations |
| `matplotlib` | Core visualisations |
| `seaborn` | Statistical & faceted visualisations |
| `scipy.stats` | Hypothesis testing (t-test, chi-square, Spearman) |
| `statsmodels` | Z-test for two-sample mean comparison |

---

##  Dataset Overview

**claims.csv** — Contains transactional insurance claim records:
- `customer_id`, `claim_date`, `claim_amount`, `claim_type`
- `incident_cause`, `police_report`, `fraudulent`, `total_policy_claims`, `State`

**cust_demographics.csv** — Contains customer profile data:
- `CUST_ID`, `DateOfBirth`, `gender`, `Segment`

---

##  Case Study Walkthrough

### ✅ Q1 — Data Integration
- Loaded `claims.csv` and `cust_demographics.csv` into separate DataFrames.
- Merged both datasets on `customer_id` ↔ `CUST_ID` using an **inner join** to create a unified 360° view (`cust_claims`).

---

### ✅ Q2 — Data Audit (Datatype Mismatch)
- Ran `.info()` to inspect column types.
- Identified that `claim_date` and `DateOfBirth` were stored as `object` (string) instead of `datetime`.
- Converted both to `datetime` using `pd.to_datetime()` with `format='mixed'`.

---

### ✅ Q3 — Cleaning `claim_amount`
- Column arrived as a string with a leading `$` symbol.
- Stripped the `$` using `.str.lstrip('$')` and cast to `float64`.

---

### ✅ Q4 — Unreported Injury Claims Flag
- Created a binary alert flag column `injury_unreported_flag`.
- Flag is set to **1** when:
  - `claim_type` is `'Injury only'` or `'Material and injury'`
  - **AND** `police_report` is **not** `'Yes'`
- Used `np.where()` for vectorised conditional assignment.

---

### ✅ Q5 — Deduplication (Most Recent Claim per Customer)
- Sorted the dataset by `customer_id` and `claim_date` in ascending order.
- Applied `drop_duplicates(subset=['customer_id'], keep='last')` to retain only the **most recent** claim per customer.

---

### ✅ Q6 — Missing Value Imputation
- Identified missing values using `.isna().sum()`.
- **Continuous column** `claim_amount` → imputed with **mean**.
- **Categorical/discrete column** `total_policy_claims` → imputed with **mode** (`mode()[0]` to extract scalar value from Series).

---

### ✅ Q7 — Customer Age & Age Category
- Corrected anomalous `DateOfBirth` years > 2000 by subtracting 100 years (data entry errors like `2004` instead of `1904`).
- Calculated `Age` as the difference between `claim_date` year and `DateOfBirth` year.
- Segmented customers using `pd.cut()`:

| Category | Age Range |
|---|---|
| Children | < 18 |
| Youth | 18 – 30 |
| Adult | 30 – 60 |
| Senior | > 60 |

---

### ✅ Q8 — Average Claim Amount by Segment
- Used `groupby('Segment').claim_amount.mean()` to find the average claim per customer segment.

---

### ✅ Q9 — Total Claim Amount by Incident Cause (Within 20-Day Window)
- Filtered claims occurring within **20 days prior to 1st October 2018** using `pd.DateOffset`.
- Grouped filtered data by `incident_cause` and summed `claim_amount`.

---

### ✅ Q10 — Adults with Driver-Related Claims in TX, DE & AK
- Applied a compound filter:
  - `State` in `['TX', 'DE', 'AK']`
  - `cust_category == 'Adult'`
  - `incident_cause` contains the substring `'river'` (covers all driver-related causes)
- Used `.value_counts()` to count per state.

---

### ✅ Q11 — Pie Chart: Claim Amount by Gender × Segment
- Aggregated total `claim_amount` grouped by `Segment` and `gender`.
- Plotted a **pie chart** with `autopct='%.2f%%'` to show each slice's percentage contribution.

---

### ✅ Q12 — Gender vs. Driver-Related Claims (Bar Chart)
- Filtered all claims where `incident_cause` contains `'river'`.
- Counted occurrences by `gender` and visualised with a **bar chart**.
- **Insight:** Males had a higher count of driver-related claims than females.

---

### ✅ Q13 — Fraudulent Claims by Age Group (Bar Chart)
- Filtered records where `fraudulent == 'Yes'`.
- Counted fraudulent claims per `cust_category`.
- Visualised with a **bar chart** to identify the most fraud-prone age group.

---

### ✅ Q14 — Monthly Claim Trend (Line Chart)
- Grouped data by month using `dt.to_period('M')`.
- Converted period to string for clean axis labels.
- Plotted a **line chart** using `sns.lineplot` with months in **chronological order**.

---

### ✅ Q15 — Average Claim by Gender & Age Category (Faceted Bar Chart)
- Computed mean `claim_amount` grouped by `cust_category`, `gender`, and `fraudulent`.
- Built a **FacetGrid** with two facets — one for fraudulent claims, one for non-fraudulent.
- Each facet shows a grouped bar chart by `cust_category` and `gender`.

---

## 🧪 Hypothesis Testing (Q16–Q20)

All tests were conducted at a **95% confidence level** (α = 0.05).

---

### ✅ Q16 — Similarity in Claim Amounts: Male vs. Female

| Parameter | Value |
|---|---|
| Test Used | Two-sample Z-test / Independent T-test |
| H₀ | Mean claim amount of Males = Mean claim amount of Females |
| Hₐ | Mean claim amounts differ |
| Result | **p-value > 0.05 → Fail to reject H₀** |

**Business Conclusion:** There is no statistically significant difference in claim amounts between males and females. Insurers need not apply gender-based pricing differentiation for claim amounts.

---

### ✅ Q17 — Relationship Between Age Category and Segment

| Parameter | Value |
|---|---|
| Test Used | Chi-Square Test of Independence |
| H₀ | Age category and segment are independent |
| Hₐ | Age category and segment are related |
| Result | **p-value > 0.05 → Fail to reject H₀** |

**Business Conclusion:** Age category and customer segment are statistically independent. Customer segmentation is not driven by age group, suggesting other factors (income, policy type) may define segments.

---

### ✅ Q18 — Rise in 2018 Claim Amounts vs. 2016–17 Average of $10,000

| Parameter | Value |
|---|---|
| Test Used | One-sample T-test |
| H₀ | Mean claim amount for 2018 = $10,000 |
| Hₐ | Mean claim amount for 2018 > $10,000 |
| Result | **p-value < 0.05 → Reject H₀** |

**Business Conclusion:** The 2018 claim amounts are significantly higher than the 2016–17 fiscal average. This signals a rising trend in claims and warrants a re-evaluation of premiums and fraud controls.

---

### ✅ Q19 — Difference Between Age Groups and Insurance Claim Types

| Parameter | Value |
|---|---|
| Test Used | Chi-Square Test of Independence |
| H₀ | No significant difference in claim types across age groups |
| Hₐ | Claim type distribution differs across age groups |
| Result | **p-value < 0.05 → Reject H₀** |

**Business Conclusion:** Claim types are significantly associated with age groups. For example, younger customers may file more driver-related claims while seniors file more medical/injury claims. This supports **age-targeted insurance products**.

---

### ✅ Q20 — Relationship Between Number of Policy Claims and Claim Amount

| Parameter | Value |
|---|---|
| Test Used | Spearman's Rank Correlation |
| H₀ | No correlation between total policy claims and claim amount |
| Hₐ | Significant correlation exists |
| Result | **p-value > 0.05 → Fail to reject H₀** |

**Business Conclusion:** The total number of past policy claims does not significantly predict the amount claimed. Claim frequency and severity are independent metrics and should be modelled separately in risk assessments.

---

## 📈 Key Insights & Business Findings

###  Data Quality
-  `claim_amount` was stored as a **string with a `$` prefix** — a common pipeline issue that would silently break any aggregation without explicit cleaning.
-  `DateOfBirth` contained **anomalous future years** (e.g., `2004` instead of `1904`) due to 2-digit year parsing errors — corrected by subtracting 100 years for           values post-2000.
-  Both datasets required a **key mismatch resolution** (`customer_id` vs `CUST_ID`) before merging — a real-world data integration challenge.
-  Missing values were found in `claim_amount` (continuous → imputed with **mean**) and `total_policy_claims` (discrete → imputed with **mode**).

---

###  Fraud & Risk Signals
-  A subset of **injury-related claims** (`Injury only` and `Material and injury`) were **never reported to the police** — these were flagged with `injury_unreported_flag = 1` and represent a high-priority fraud investigation list.
-  **Adults (age 30–60)** had the **highest number of fraudulent policy claims**, making them the most at-risk segment for fraud — insurers should apply stricter verification for this group.
-  **2018 claim amounts were statistically significantly higher** than the 2016–17 fiscal average of $10,000 (One-sample T-test, p < 0.05) — suggesting either inflation in claims, increased fraud, or a shift in policyholder behaviour.

---

###  Customer Behaviour
-  Several customers had **multiple claims on record** — after deduplication, only the **most recent claim per customer** was retained, ensuring customer-level uniqueness for modelling.
-  **Males filed significantly more driver-related claims** than females — this could reflect higher exposure (more miles driven) or higher risk behaviour among male policyholders.
-  There is **no statistically significant difference in claim amounts between males and females** (Z-test, p > 0.05) — while males claim more frequently for driver issues, the *amounts* claimed are similar across genders.

---

###  Segment & Age Analysis
-  **Customer segment and age category are statistically independent** (Chi-square, p > 0.05) — segments are likely defined by policy type or income, not age. This is useful for avoiding redundant features in models.
-  **Claim types differ significantly across age groups** (Chi-square, p < 0.05) — younger customers tend to file different types of claims compared to seniors, supporting the case for **age-targeted insurance products and premiums**.
-  Among adults in **TX, DE, and AK**, driver-related claims were most concentrated — these states could benefit from targeted road safety campaigns or premium adjustments.

---

###  Trend Analysis
-  The **monthly claim trend** revealed seasonal/periodic spikes in total claim amounts — identifying high-claim months helps insurers plan **reserve allocation and staffing** more effectively.
-  Claims within the **20-day window before 1st October 2018** showed variation by incident cause — understanding short-term claim surges by cause can help with **event-driven risk modelling**.

---

###  Correlation & Predictive Signals
-  **Total number of past policy claims has no significant correlation with the claim amount** (Spearman, p > 0.05) — a customer who claims frequently does not necessarily claim larger amounts. This means **frequency models and severity models must be built separately** in actuarial pricing.
-  The **average claim amount varies by customer segment** — certain segments consistently claim higher amounts, which directly informs **risk-based premium stratification**.

---

###  Overall Business Takeaways
-  Fraudulent claims are **age and type dependent** — not random — enabling targeted fraud prevention rules.
-  Gender is **not a reliable predictor of claim severity**, though it may predict claim frequency for specific causes.
-  Statistical testing confirmed that **not all intuitive relationships hold up under scrutiny** — e.g., more claims ≠ higher amounts, and age ≠ segment.
-  The 2018 spike in claims is statistically confirmed and should trigger a **business review of underwriting policies**.

---

##  How to Run

1. Clone the repository:
```bash
   git clone https://github.com/vikasnagar31/insurance-claims-eda.git
   cd insurance-claims-eda
```

2. Install dependencies:
```bash
   pip install pandas numpy matplotlib seaborn scipy statsmodels
```

3. Place `claims.csv` and `cust_demographics.csv` in the project root.

4. Launch Jupyter Notebook:
```bash
   jupyter notebook Insurance_Claims_Case_Study_.ipynb
```

---

## 🙋 Author

**Vikas Nagar**  
[LinkedIn](https://www.linkedin.com/in/vikas31/) · 

---
