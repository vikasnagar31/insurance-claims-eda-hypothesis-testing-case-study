# Insurance Claims Case Study 📊

This project performs Exploratory Data Analysis (EDA) and Hypothesis Testing on insurance claims data using Python. The aim is to identify patterns, detect anomalies, and derive actionable insights for decision-making.

## 📁 Files in the Repository

- `Insurance_Claims_Analysis.ipynb` – Combined EDA & Hypothesis Testing notebook
- `data/claims_data.csv` – Claims dataset
- `data/cust_data.csv` – Customer information dataset
- `requirements.txt` – Python dependencies

---

## 📌 Key Business Questions Addressed

### 🔍 **Data Cleaning & Feature Engineering**
1. Import and combine `claims_data.csv` and `cust_data.csv` to create a 360-degree customer view.
2. Audit data types and check for mismatches.
3. Convert `claim_amount` to numeric (remove `$`).
4. Create a police alert flag for unreported injury claims.
5. Remove duplicate `customer_id` entries, keeping the most recent one.
6. Impute missing values (mean for numeric, mode for categorical).
7. Calculate customer age and categorize into:
   - Children: < 18  
   - Youth: 18–30  
   - Adult: 30–60  
   - Senior: > 60

---

### 📊 **Exploratory Data Analysis**
8. Average claim amount by customer segment.
9. Total claim amount by incident cause (for incidents before Sept 11, 2018).
10. Adult claim counts from TX, DE, AK for driver-related issues.
11. Pie chart of claim amount % by gender and segment.
12. Bar chart: Gender comparison for driver-related claims.
13. Bar chart: Age group with most fraudulent claims.
14. Line chart: Monthly trend in total claimed amount.
15. Facetted bar chart: Avg claim amount by gender and age category, separated by fraud/non-fraud.

---

### 📐 **Hypothesis Testing**
16. Is there any similarity in claim amount by gender?
17. Is there a relationship between age category and segment?
18. Has the current year seen a rise in claim amounts vs 2016–17 average ($10,000)?
19. Is there any difference between age groups and insurance claims?
20. Is there any relationship between number of policy claims and claimed amount?

For each hypothesis:
- Null and alternative hypotheses are defined
- Statistical test is chosen based on variable types
- p-value is interpreted
- Business conclusions are drawn

---

 🧰 Technologies Used :
 
Python (Pandas, NumPy)

Seaborn & Matplotlib (Visualizations)

Scipy, Statsmodels (Hypothesis Testing)

Jupyter Notebook

