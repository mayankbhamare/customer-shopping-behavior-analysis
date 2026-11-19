# Customer Shopping Behavior Analysis

An end-to-end data analytics project investigating customer purchasing patterns, demographic spending drivers, promotional sensitivity, and subscription behavior across 3,900 retail transactions ($233,081 in total revenue).

The project implements a complete analytics pipeline: raw data cleaning and feature engineering in **Python (Pandas)**, structured data modeling and cohort analysis in **PostgreSQL**, and executive reporting through an interactive **Power BI** dashboard and presentation deck.

---

## Project Structure

```text
Customer-Shopping-Behavior-Analysis/
├── data/
│   └── customer_shopping_behavior.csv         # Raw transactional dataset (3,900 records)
├── notebooks/
│   └── sales_analysis.ipynb                  # Data cleaning, feature engineering & DB load
├── sql/
│   └── customer_analysis.sql                 # Schema DDL and 10 business analysis queries
├── dashboard/
│   └── customer_behavior_dashboard.pbix      # Interactive Power BI dashboard
├── docs/
│   ├── Business_Problem_Document.pdf         # Business requirements and objectives
│   ├── Customer_Shopping_Behavior_Analysis.pdf # Detailed project report with SQL outputs
│   └── Customer_Shopping_Behavior_Presentation.pdf # 10-slide executive presentation
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Business Objectives

Retail leadership sought to leverage customer transaction data to answer one overarching strategic question:

> **"How can the company leverage consumer shopping data to identify trends, improve customer engagement, and optimize marketing and product strategies?"**

Key analytical focus areas:
1. **Demographic Revenue Drivers:** Revenue contribution by gender and age cohorts.
2. **Subscription & Loyalty Dynamics:** Basket size comparison between subscribers and non-subscribers, and retention potential across buyer tiers.
3. **Discount Sensitivity:** Identifying SKUs heavily dependent on promotional pricing.
4. **Fulfillment Impact:** Average order value variations across shipping methods.
5. **Product & Assortment Performance:** Leading categories by order volume and customer satisfaction ratings.

---

## Tech Stack

- **Python (Pandas, SQLAlchemy, Psycopg2):** Data ingestion, missing value imputation, column standardization, feature binning, and database loading.
- **PostgreSQL:** Relational data modeling, CTEs, window functions (`ROW_NUMBER`, `SUM() OVER()`), and business transaction querying.
- **Power BI:** Interactive data visualization, KPI cards, category breakdown, and customer segment slicing.

---

## Dataset Overview

- **Source:** [Consumer Behavior / Shopping Trends Dataset](https://www.kaggle.com/datasets/iamsouravbanerjee/customer-shopping-trends-dataset) (Kaggle)
- **Scale:** 3,900 rows, 18 features
- **Key Attributes:**
  - *Demographics:* Age, Gender, Location, Subscription Status
  - *Purchase Details:* Item Purchased, Category, Purchase Amount (USD), Season, Size, Color
  - *Behavioral Metrics:* Discount Applied, Promo Code Used, Previous Purchases, Frequency of Purchases, Review Rating, Shipping Type, Payment Method

---

## Data Pipeline & Methodology

### 1. Preprocessing & Feature Engineering (Python)
- **Missing Data Imputation:** The `Review Rating` column contained 37 missing values, which were imputed using the median review rating of each respective product category.
- **Schema Normalization:** Standardized all column names to `snake_case` (e.g., `Purchase Amount (USD)` -> `purchase_amount`).
- **Feature Engineering:**
  - `age_group`: Segmented customer ages into 4 quartile cohorts (`Young Adult` [18–31], `Adult` [32–44], `Middle-Aged` [45–57], `Senior` [58–70]).
  - `purchase_frequency_days`: Mapped qualitative frequency intervals into numeric cadence (e.g., Weekly = 7, Fortnightly = 14, Quarterly = 90, Annually = 365).
- **Consistency Verification:** Confirmed 100% redundancy between `discount_applied` and `promo_code_used` (all 3,900 rows matched) and dropped `promo_code_used`.
- **Database Loading:** Streamed the cleaned DataFrame directly into a PostgreSQL relational table (`customer`) via SQLAlchemy.

### 2. Relational Analytics (PostgreSQL)
Ten targeted SQL queries were authored in `sql/customer_analysis.sql` to answer specific business questions:
- Revenue segmentation by gender and age cohort.
- Identification of high-spending discount users ($>Avg Spend of $59.76).
- Product ranking by average review rating.
- Fulfillment tier comparison (Standard vs. Express shipping).
- Subscriber vs. non-subscriber basket size and total revenue.
- Promotional dependency rates by SKU.
- Customer segmentation by purchase history (New: 1, Returning: 2–10, Loyal: >10).
- Category top-3 rank analysis via window functions.
- Subscription propensity among repeat buyers (>5 past purchases).

---

## Key Insights

| Focus Area | Key Finding | Strategic Implication |
| :--- | :--- | :--- |
| **Loyalty Base** | **79.9%** of customers (3,116) are in the Loyal tier (>10 orders); New buyers represent only **2.1%** (83). | Retention is strong; highest revenue upside lies in graduating the **701 Returning buyers** (18.0%) into the Loyal tier. |
| **Subscription Dynamics** | Average spend is virtually identical: Subscribers (**$59.49**) vs. Non-subscribers (**$59.87**). | Subscriptions do not lift single basket size. Subscription programs must focus on purchase cadence/frequency rather than cart value. |
| **Repeat Buyer Subscription Rate** | Repeat buyers (>5 past purchases) subscribe at **27.56%**, nearly identical to the overall customer rate (**27.00%**). | Repeat buying behavior does not automatically trigger organic subscription; explicit incentives are needed. |
| **Demographic Contribution** | Male shoppers generate **67.7%** ($157,890) of revenue; Female shoppers generate **32.3%** ($75,191). Young Adults lead age groups ($62,143). | Tailor digital acquisition to Young Adults while expanding assortment and engagement strategies for female shoppers. |
| **Promotional Reliance** | Hats (**50.0%**), Sneakers (**49.7%**), and Coats (**49.1%**) sell approximately half their total volume on discount. | Protect gross margins by scaling back blanket discounts on highly rated items (e.g., Hats hold a 3.80 rating). |
| **Fulfillment & Shipping** | Express shipping yields slightly higher average order value (**$60.48**) compared to Standard shipping (**$58.46**). | Test bundling free express shipping into the paid subscription tier as a primary conversion lever. |

---

## Interactive Dashboard (Power BI)

The companion Power BI dashboard (`dashboard/customer_behavior_dashboard.pbix`) provides interactive exploration:
- **Executive KPI Cards:** Average purchase amount ($59.76), Average Review Rating (3.75), and Total Customer Count (3.9K).
- **Subscription Breakdown:** Visual split of Subscriber vs. Non-Subscriber base (27% vs. 73%).
- **Category Performance:** Revenue and order volume by Category (Clothing, Accessories, Footwear, Outerwear).
- **Cohort Visuals:** Revenue and order volume sliced by Age Group.
- **Dynamic Slicers:** Interactive filtering by Subscription Status, Gender, Category, and Shipping Type.

---

## How to Reproduce

### 1. Clone the Repository
```bash
git clone https://github.com/<your-username>/customer-shopping-behavior-analysis.git
cd customer-shopping-behavior-analysis
```

### 2. Set Up Python Environment
```bash
python -m venv venv
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

pip install -r requirements.txt
```

### 3. Run Data Pipeline & Populate PostgreSQL
Ensure PostgreSQL is running locally and a database named `customer_behavior` exists. Set your environment variables (or provide credentials when prompted):
```bash
export DB_USER=postgres
export DB_PASSWORD=your_password
export DB_HOST=localhost
export DB_PORT=5432
export DB_NAME=customer_behavior

# Run the notebook in Jupyter or execute as a script
jupyter notebook notebooks/sales_analysis.ipynb
```

### 4. Execute SQL Queries
Open `sql/customer_analysis.sql` in pgAdmin, DBeaver, or psql:
```bash
psql -U postgres -d customer_behavior -f sql/customer_analysis.sql
```

### 5. Open the Power BI Dashboard
Open `dashboard/customer_behavior_dashboard.pbix` in **Power BI Desktop**.
- If prompted to refresh data credentials, point the PostgreSQL data source to your local instance (`localhost:5432`, database `customer_behavior`).
