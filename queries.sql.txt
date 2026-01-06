-- Ecommerce SQL Analysis Queries - All in One
-- Author: Wu Peng
-- Date: 2026-01-06

-- 1. Total Orders
SELECT COUNT(*) AS total_orders FROM olist_orders_dataset;
-- Suggestion: If growth slow, promote to boost volume.

-- 2. State Sales Top (from your order... file? adjust)
SELECT c.customer_state, SUM(i.price + i.freight_value) AS state_sales
FROM olist_orders_dataset o
JOIN olist_customers_dataset c ON o.customer_id = c.customer_id
JOIN olist_order_items_dataset i ON o.order_id = i.order_id
GROUP BY c.customer_state
ORDER BY state_sales DESC LIMIT 5;
-- Suggestion: Target SP state with marketing.

3.EXPLAIN SELECT * FROM olist_orders_dataset WHERE order_status = 'delivered'; 
-- 业务建议：如果全表扫描，建议加索引优化查询速度，减少运营报表延迟，支持实时决策
 
4.SELECT * FROM olist_customers_dataset LIMIT 10 ;
SELECT *FROM olist_order_items_dataset LIMIT 10;
SELECT *FROM olist_order_payments_dataset LIMIT 10;
SELECT *FROM olist_orders_dataset LIMIT 10;
SELECT *FROM olist_products_dataset  LIMIT 10;
-- 业务建议：数据完整性确认后，如果行数少于预期，建议检查导入源，避免分析偏差影响销售预测

5.SELECT customer_id, COUNT(order_id) AS order_count
FROM olist_orders_dataset
GROUP BY customer_id
ORDER BY order_count DESC LIMIT 10;
-- 业务建议：高频用户贡献大，建议VIP忠诚计划（如积分/专属优惠），提升留存和复购率。

6.SELECT COUNT(*) AS total_orders FROM olist_orders_dataset;
-- 业务建议；如果订单总数增长过慢，建议分析用户流失原因，并推动促销活动提升订单量

7.SELECT SUM(i.price + i.freight_value) AS total_sales
FROM olist_order_items_dataset i;
-- 业务建议：总销售高但利润率低时，建议优化运费结构或针对高额订单降成本，提升整体盈利

8.SELECT p.product_category_name, SUM(i.price + i.freight_value) AS category_sales
FROM olist_order_items_dataset i
JOIN olist_products_dataset p ON i.product_id = p.product_id
GROUP BY p.product_category_name
ORDER BY category_sales DESC LIMIT 5;
-- 业务建议：电子品类Top，建议库存倾斜；低销品类分析用户反馈，优化产品线或降价促销。

9.SELECT c.customer_state,SUM(i.price + i.freight_value) AS state_sales
FROM olist_orders_dataset o
JOIN olist_customers_dataset c ON o.customer_id = c.customer_id
JOIN olist_order_items_dataset i ON o.order_id = i.order_id
GROUP BY c.customer_state
ORDER BY state_sales DESC LIMIT 5
-- 业务建议： SP州销售Top，建议增加本地库存/针对性营销，扩大市场份额

10.SELECT order_id,(price + freight_value) AS amount
FROM olist_order_items_dataset
WHERE (price + freight_value) > 3 * (SELECT AVG(price + freight_value) FROM olist_order_items_dataset)
ORDER BY amount DESC LIMIT 10;

11.SELECT 
    DATE(first_order) AS cohort_date,
    COUNT(DISTINCT customer_id) AS total_users,
    COUNT(DISTINCT CASE WHEN DATEDIFF(order_purchase_timestamp, first_order) = 1 THEN customer_id END) AS retained_day1
FROM (
    SELECT customer_id, order_purchase_timestamp,
           MIN(order_purchase_timestamp) OVER (PARTITION BY customer_id) AS first_order
    FROM olist_orders_dataset
) sub
GROUP BY cohort_date;
-- 业务建议：留存率低（假设<20%），建议日推送提醒/优惠卷，减少用户流失，提高LTV

12.SELECT
    (SELECT COUNT(DISTINCT o.order_id) 
FROM olist_orders_dataset o 
JOIN olist_order_payments_dataset p ON o.order_id = p.order_id
WHERE p.payment_value>0)/COUNT(DISTINCT order_id) * 100 AS payment_conversion_rate
FROM olist_orders_dataset;
-- 业务建议：转化率低（假设<80%），建议优化支付流程（如简化页面/加支付方式），减少弃单损失

-- Add all your other queries here...