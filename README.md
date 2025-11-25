# super-store-sales-analysis
End-to-end analysis of Superstore Sales data (Sheets → SQL Server → Power BI) to uncover insights on sales, profitability, discount efficiency, and product strategy.

## Dashboard Preview
<img src="Screenshot 2025-11-19 165217.png" width=600 height=250>
<img src="Screenshot 2025-11-19 181601.png" width=600 height=250>

## Dataset 
* **Source** : Kaggle, “Superstore Sales (2011-2014)” (by Ishan Shrivastava).
[Super-Store-Sales](https://www.kaggle.com/datasets/ishanshrivastava28/superstore-sales)
* **Rows** : 9994
* **Columns** : 21
* **Key Fields** : Order Date, Ship Date, Sales, Profit, Quantity, Discount, Region, and Segments.

## Tools & Technologies
* **Data Cleaning** : **Google Sheets**
* **Data Transformation** : **Google Sheets + SQL**
* **Data Analysis** : **Google Sheets + SQL Server (SSMS)**
* **Data Visualization** : **Power BI**
* **Documentation** : **PDF + folders**

## Key Analysis Steps
### Google Sheets
* Cleaned inconsistent fields
* Standardized numeric and percentage columns
* **Derived metrics** : Profit Margin , Shipping Period
* Exploratory charts and pivot tables

### SQL Server Analysis
* **Performed validated analysis using optimized SQL Queries:**
* Annual sales & profit trends
* Discount impact analysis
* Category-level profitability
* Top products (per-unit & total profit)
* Distribution analysis (Quartiles)
* Shipping period analysis
* Regional performance
* High-loss vs. high-profit orders
* Outlier detection & validation

### Power BI
**Two-page dashboard:**

##### Page 1 — Executive Summary
* KPIs
* **Slicers** : Category, Subcategory, State, City, Segments 
* Sales & profit trends
* Most profitable Subcategories
* Most profitable Segements
* Regional performance map

##### Page 2 — Detailed Analysis
* Product, segment, and category breakdowns
* Shipping period patterns
* Discount impact
* Conclusions narrative
* Recommended actions narrative

### Technical Challenges
* After finishing the analysis, I realized that the 1,781 rows were not actually 
errors. The raw data used the "Day-First" format (DD-MM-YYYY), but my tool read them as 
"Month-First" (MM-DD-YYYY). For example, it read February 10th as October 2nd, which messed 
up the timeline calculation.
* **Impact** : The financial numbers (Sales and Profit) are still 100% correct because the date format 
does not change the total values.

## Key Insights
#### What drives profit?
* Technology products generate the highest margins (15.65%).
* Moderate discounts → High profitability.
* Efficient shipping (2–6 days) strongly correlates with higher profit.
#### What destroys profit?
* Discounts above **20%** results in rapid profit decline.
* Above **30%** Most sales become unprofitable.
* The **furniture** category **suffers** the most from over-discounting.
* Some regions show **high sales** but **negative profits** (e.g., Texas, Pennsylvania).
#### Product Insights
* Top profit-per-unit: Canon copiers, HP printers, shredders.
* Top total profit: Same family (copiers & printers).
* Profit distribution is right-skewed → A few items drive most profits.
#### Customer Segments
* Consumer → Highest sales, moderate profitability
* Corporate → Balanced
* Home Office → Best profit efficiency
#### Time-Based Trends
* Sales & profit steadily increased from 2011 to 2014
* Strong seasonal peaks: October–December
* Early 2011 had high sales but weak/negative profit due to discount inefficiencies


## Recommendations
#### Pricing & Discounts
* Limit discounts to **≤ 20%**
* Avoid discounting large furniture items
* Apply category-specific discount policies
#### Product Strategy
* Prioritize high-margin items (copiers, accessories, printers)
* Reassess Tables & Machines (high sales, low profit)
#### Operational Improvements
* Investigate delays > 60 days
* Reduce shipping time inconsistencies
* Add shipping cost column to track true profit
#### Data Quality Improvements
* Standardize date formats
* Ensure correct numeric types
* Add validation checks during data entry

## SQL Queries Examples
* Discount Impact (0–10%, 10–20%, 20–30%, 30–50%, 50%+)
```sql
WITH b AS ( 
  SELECT *, 
    CASE  
      WHEN Discount BETWEEN 0 AND 0.10 THEN '0-10%' 
      WHEN Discount > 0.10 AND Discount <= 0.20 THEN '10-20%' 
      WHEN Discount > 0.20 AND Discount <= 0.30 THEN '20-30%' 
      WHEN Discount > 0.30 AND Discount <= 0.50 THEN '30-50%' 
      ELSE '>50%' END AS disc_bucket 
  FROM Superstore 
) 
SELECT disc_bucket, SUM(Sales) sales, SUM(Profit) profit, AVG(Profit_Margin) avg_margin, 
COUNT(*) orders 
FROM b 
GROUP BY disc_bucket 
ORDER BY disc_bucket;
```

* Profit Distribution (Q1, Median, Q3)
```sql
SELECT DISTINCT 
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY Profit) OVER () AS median_profit, 
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY Profit) OVER () AS q1, 
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY Profit) OVER () AS q3 
FROM Superstore; 
```

## Learning Objectives Achieved
* Data cleaning & transformation
* SQL analytical proficiency
* BI dashboard design
* Business interpretation of data
* Documentation and communication

## Final Deliverables 
* Clean dataset (multiple versions)
* SQL documentation
* Full analytical PDF (29 pages)
* 2-page Power BI dashboard
* Product profitability datasets
* This README 

## Conclusion
This project demonstrates the complete lifecycle of a retail analytics workflow — from messy data → clean dataset → SQL analysis → BI visualization → actionable insights.
