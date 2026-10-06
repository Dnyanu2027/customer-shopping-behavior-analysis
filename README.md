# Customer Shopping Behavior Analysis
End-to-end analysis of customer shopping behavior: data cleaning in **Python**, business analysis in **SQL (MySQL)**, and an interactive **Power BI** dashboard.
<img width="1311" height="735" alt="Screenshot 2026-10-05 114920" src="https://github.com/user-attachments/assets/fc5cf2df-fa80-45fd-a9bc-684f1dd0ea79" />


## Project Overview

This project analyzes **3,900 purchase records** across four product categories to understand spending patterns, customer segments, product preferences and subscription behavior, and to turn those findings into business recommendations.

## Dataset

| Item | Detail |
|---|---|
| Rows | 3,900 |
| Columns | 18 (19 after feature engineering, 1 dropped) |
| Missing data | 37 values in `Review Rating` |
| Duplicates | 0 |

**Feature groups**

- **Customer demographics:** Age, Gender, Location, Subscription Status
- **Purchase details:** Item Purchased, Category, Purchase Amount (USD), Season, Size, Color
- **Shopping behavior:** Discount Applied, Promo Code Used, Previous Purchases, Frequency of Purchases, Review Rating, Shipping Type, Payment Method

## Tech Stack

- **Python:** pandas, SQLAlchemy, PyMySQL (Jupyter Notebook)
- **Database:** MySQL
- **BI:** Power BI

## Workflow

### 1. Data preparation (Python)

- Loaded the CSV with pandas and profiled it with `df.info()` and `df.describe()`
- Filled 37 missing `Review Rating` values with the **median rating of each product category**
- Renamed all columns to `snake_case`
- Engineered two features:
  - `age_group`: quartile bins (Young Adult, Adult, Middle-aged, Senior)
  - `purchase_frequency_days`: purchase frequency text mapped to days (Weekly = 7, Fortnightly = 14, Monthly = 30, Quarterly = 90, Annually = 365)
- Dropped `promo_code_used` (redundant with `discount_applied`)
- Loaded the cleaned DataFrame into MySQL (database `customer_behivor`, table `customer`)

### 2. SQL analysis

Ten business questions answered with SQL (joins-free aggregations, CTEs, window functions, subqueries):

| # | Business question |
|---|---|
| 1 | Total revenue by gender |
| 2 | Discount users who still spent above the average purchase |
| 3 | Top 5 products by average review rating |
| 4 | Average purchase: Standard vs Express shipping |
| 5 | Subscribers vs non-subscribers: spend and revenue |
| 6 | Top 5 products by percentage of discounted purchases |
| 7 | Customer segments: New, Returning, Loyal |
| 8 | Top 3 products within each category |
| 9 | Are repeat buyers (more than 5 purchases) more likely to subscribe? |
| 10 | Revenue contribution by age group |

### 3. Power BI dashboard

KPI cards (average purchase amount, average review rating, number of customers), slicers (subscription status, gender, category, shipping type), and charts for sales and revenue by category, sales and revenue by age group, and subscription split.

## Key Findings

- **Revenue is $233,081 in total.** Males generate 67.7% ($157,890) and females 32.3% ($75,191), but average spend per customer is almost identical (about $59.54 vs $60.25). The gap comes from customer count, not spending.
- **Subscribers do not spend more.** Average spend is $59.49 for subscribers vs $59.87 for non-subscribers. Only 27% of customers (1,053) subscribe.
- **Express shipping users spend slightly more:** $60.48 vs $58.46 for Standard (+3.5%).
- **Customer base is highly loyal:** 79.9% Loyal (3,116), 18.0% Returning (701), 2.1% New (83), with segments defined as New = 1, Returning = 2 to 10, Loyal = more than 10 previous purchases.
- **Repeat buyers subscribe a bit more:** about 27.6% of customers with more than 5 purchases subscribe, vs about 22.4% of the rest.
- **Young Adults are the top revenue group** ($62,143, 26.7%), followed by Middle-aged ($59,197), Adult ($55,978) and Senior ($55,763).
- **Discounts are widely used:** 43% of purchases carry a discount, and the most discount-dependent products (Hat, Sneakers, Coat, Sweater, Pants) sit at 47% to 50%.
- **Ratings are tightly clustered:** the top 5 products (Gloves, Sandals, Boots, Hat, Skirt) range from 3.78 to 3.86.
- **Clothing and Accessories** lead in both sales and revenue.

## Business Recommendations

1. **Boost subscriptions:** design benefits that raise basket size or frequency, since subscribers currently spend no more per purchase.
2. **Loyalty programs:** reward repeat buyers and move the 701 Returning customers into Loyal.
3. **Review discount policy:** many discounted customers still spend above average, so test targeted discounts to protect margin.
4. **Product positioning:** feature top-rated and best-selling products (for example Gloves, Sandals, Jewelry, Blouse) in campaigns.
5. **Targeted marketing:** focus on Young Adults and Express-shipping users, and grow the female customer base, which spends as much per customer but is only about a third of customers.

## Repository Structure

```
.
├── Customer_shopping_behavior.ipynb   # data cleaning, feature engineering, load to MySQL
├── customer_shopping_behavior.sql     # the 10 SQL business queries
├── customer_shopping_behavior.png     # Power BI dashboard screenshot
├── Customer_Shopping_Behavior_Analysis_Report.pdf
└── README.md
```

> Add the source CSV (`customer_shopping_behavior.csv`) to the repository if the license of the dataset allows it.

## How to Run

1. Install dependencies:
   ```bash
   pip install pandas sqlalchemy pymysql jupyter
   ```
2. Create the database in MySQL:
   ```sql
   CREATE DATABASE customer_behivor;
   ```
3. Open `Customer_shopping_behavior.ipynb`, set your own MySQL credentials in the connection string (do not commit passwords), and run all cells to clean the data and load the `customer` table.
4. Run the queries in `customer_shopping_behavior.sql` against the `customer_behivor` database.
5. Connect Power BI to the `customer` table to rebuild the dashboard.

## Notes

- Q8 uses `ROW_NUMBER()`, which breaks ties arbitrarily (for example Blouse and Pants both have 171 orders). Use `DENSE_RANK()` if tied products should all be shown.
- The customer segment thresholds in Q7 put about 80% of customers in Loyal; consider quartile-based thresholds for a more balanced split.
