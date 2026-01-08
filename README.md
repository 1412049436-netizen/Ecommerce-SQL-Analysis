# Ecommerce-SQL-Analysis
## Background
SQL-based analysis of e-commerce user behavior using Olist dataset (100k rows, 5 tables). Focus on metrics like DAU, retention, conversion, and anomalies.

## Data Source
Kaggle Olist Brazilian E-commerce: https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

## Steps
1. Imported CSV files into MySQL using Navicat.
2. Wrote 15+ SQL queries for aggregation, JOIN, window functions.
3. Exported results to Excel with business insights.

## Key Queries (Examples)
```sql
-- Total Orders
SELECT COUNT(*) AS total_orders FROM olist_orders_dataset;
-- Suggestion: If growth slow, promote to boost volume.

-- State Sales Top
SELECT c.customer_state, SUM(i.price + i.freight_value) AS state_sales
FROM olist_orders_dataset o
JOIN olist_customers_dataset c ON o.customer_id = c.customer_id
JOIN olist_order_items_dataset i ON o.order_id = i.order_id
GROUP BY c.customer_state
ORDER BY state_sales DESC LIMIT 5;
-- Suggestion: Target SP state with marketing.

-- More in queries.sql.txt

Results and Insights
Total orders: 99441; average order value ~150 BRL.
SP state tops sales (~40% share); retention low (~15%), suggest push notifications to improve LTV by 10%.
Conversion rate ~80%; recommend simplifying payment to reduce drop-offs.
High-value orders contribute 5%; monitor for fraud.
Files
queries.sql.txt: All SQL scripts.
results_and_suggestions.xlsx: Exported data and suggestions.
