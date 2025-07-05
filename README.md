# KULTRA MEGA STORES INVENTORY ANALYSIS
## PROJECT OVERVIEW
Kultra Mega Stores (KMS), headquartered in Lagos, specializes in office supplies and furniture, serving a diverse customer base of individual consumers, small retail businesses, and large corporate clients across Lagos, Nigeria. As a Business Intelligence Analyst, I was engaged to support the Abuja division of KMS by analyzing historical order data covering the period from 2009 to 2012.
The primary goal of this project was to uncover actionable insights that could help the Abuja division make better data-driven decisions and improve business performance.
## TOOLS AND SKILLS APPLIED
1. Excel: For initial data exploration and cleaning.
2. SQL (Structured Query Language): To query, transform, and analyze the data.
3. Data Analysis Techniques: Including grouping, aggregation, and filtering.
## DATASET DESCRIPTION 
The dataset given by the store include:
-  Order Details: Row ID,	Order ID,	Order Date,	Order Priority,	Order Quantity,	Sales	Discount	
-  Shipping Details: Ship Mode,	Profit,	Unit Price,	Shipping Cost,	Customer Name,	Province,	Region,	Customer Segment,	Ship Date
-  Product Details: Product Category,	Product Sub-Category,	Product Name,	Product Container,	Product Base Margin
## ANALYSIS TASKS
CASE SCENARIO 1
1. Which product category had the highest sales? 
2. What are the Top 3 and Bottom 3 regions in terms of sales? 
3. What were the total sales of appliances in Ontario? 
4. Advise the management of KMS on what to do to increase the revenue from the bottom 10 customers 
5. KMS incurred the most shipping cost using which shipping method?
 
 CASE SCENARIO 2

6. Who are the most valuable customers, and what products or services do they typically purchase? 
7. Which small business customer had the highest sales? 
8. Which Corporate Customer placed the most number of orders in 2009 – 2012? 
9. Which consumer customer was the most profitable one? 
10. Which customer returned items, and what segment do they belong to? 
11. If the delivery truck is the most economical but the slowest shipping method and Express Air is the fastest but the most expensive one, do you think the company appropriately spent shipping costs based on the Order Priority? Explain your answer
## DATA ANALYSIS PROCESSES
There was inconsistency in the raw data number of rows in Excel and in MYSQL. It was found out that a particular column has many blank values. 
1. Data Cleaning: To improve the data integrity, the column with blank values was deleted using excel and this is because the column wasn't needed in the main analysis.
2. Heading Changing: The data given has heading that are not suitable or easily analysed in MYSQL, hence some headings was changed such that space " " was substituted with underscore "_". This was done in Excel.
3. Calculated Column: Required column needed for analysis that was not primarity from the table was derived; such as Revenue (unit price * order quantity), Order_year
4. Data Query and Analysis: MYSQL was used to query data and perform the required data analysis.
## ANSWERS TO TASK
## Question 1: Which product category had the highest sales?
```
SELECT product_category, SUM(sales) AS Total_sales
FROM kms_table
GROUP BY product_category
ORDER BY total_sales DESC; -- Answer: Technology product category has the highest sales
```
RESULT
```
product_category | total_sales
-----------------+-------------
Technology       | 5984248.181999991
```
## Question 2: What are the Top 3 and Bottom 3 regions in terms of sales?
Top 3
```
-- Top 3
SELECT region, SUM(sales) AS Total_sales
FROM kms_table
GROUP BY Region
ORDER BY total_sales DESC
LIMIT 3; -- Answer: Top 3 regions in terms of sales are the West, Ontario and Prarie region
```
RESULT
```
region             | total_sales
-------------------+--------------
West               | 3597549.2754999986
Ontario            | 3063212.4794999994
Prarie             | 2837304.6014999985
```
Bottom 3
```
-- Bottom 3
SELECT region, SUM(sales) AS Total_sales
FROM kms_table
GROUP BY Region
ORDER BY total_sales ASC
LIMIT 3; -- Answer: Bottom 3 regions in terms of sales are Nunavut, Northwest Territories, and Yukon
```
RESULT
```
region                  | total_sales
------------------------+--------------
Nunavut                 |116376.4835
Northwest Territories   | 800847.3295000001
Yukon                   | 975867.370999999
```
## Question 3: What were the total sales of appliances in Ontario?
```
SELECT product_sub_category, SUM(sales) AS Total_sales
FROM kms_table
WHERE region = 'Ontario' AND product_sub_category = 'appliances'; -- Answer: 202346.84000000003
```
RESULT
```
product_sub_category | total_sales
---------------------+-------------
appliances           | 202346.84000000003
```
## Question 4: Advise the management of KMS on what to do to increase the revenue from the bottom 10 customers
There is no revenue (unit price * order quantity) so let's add it to the table i.e calculated column
```
    -- To make the table
ALTER TABLE kms_table
ADD COLUMN Revenue DECIMAL(10,2);
    -- Insert values
UPDATE kms_table
SET Revenue = (Unit_price * order_quantity);
    -- Let's see
SELECT unit_price, order_quantity, revenue
FROM kms_table;
    -- Done
```
To see the bottom 10 customers according to revenue
```
SELECT customer_name, SUM(revenue) AS Total_revenue
FROM kms_table
GROUP BY customer_name
ORDER BY Total_revenue ASC
LIMIT 10; -- Jeremy Farry, Natalie DeCherney, Nicole Fjeld'Katrina Edelman, Dorothy Dickinson, Christine Kargatis, Eric Murdock, Chris McAfee, Anne McFarland, and Rick Huthwaite are the customers with lowest revenue.
```
RESULT
```
customer_name       | Total_revenue
--------------------+-------------         
"Jeremy Farry"      | 85.72
"Natalie DeCherney" | 125.90
"Nicole Fjeld"      | 153.03
"Katrina Edelman"   | 180.76
"Dorothy Dickinson" | 201.16
"Christine Kargatis"| 293.46
"Eric Murdock"      | 333.14
"Chris McAfee"      | 355.50
"Anne McFarland"    | 421.37
"Rick Huthwaite"    | 426.00
```
INSIGHT: 
1. An increase in the discount for these specific 10, which will increase the quantity of goods ordered
2. Reduction in the shipping cost 
3. Make only Express Air ship mode available as it is averagely the cheapest
   
## Question 5: KMS incurred the most shipping cost using which shipping method?
```
SELECT ship_mode, SUM(shipping_cost) AS Sum_of_shipping_cost
FROM kms_table
GROUP BY ship_mode
ORDER BY Sum_of_shipping_cost DESC
LIMIT 1; -- Using the delivery truck mode
```
RESULT 
```
ship_mode       | Sum_of_shipping_cost
----------------+------------------------------
Delivery Truck  | 51971.939999999784
```

## Question 6: Who are the most valuable customers, and what products or services do they typically purchase? 
```
SELECT customer_name 
FROM kms_table
GROUP BY customer_name
ORDER BY SUM(revenue) DESC 
LIMIT 5; -- The top 5 most valuable customers are Emily Phan, Deborah Brumfield, Sylvia Foulston, Roy Skaria, Alejandro Grove
```
RESULT
```
customer_name         | Total_revenue
----------------------+--------------
Emily Phan            |    118906.33
Deborah Brumfield     |    100784.20
Sylvia Foulston       |    95458.40
Roy Skaria,           |    90955.86
Alejandro Grove       |    86448.33

```
For products or services they purchase
```
SELECT DISTINCT(product_category)
FROM kms_table
WHERE customer_name IN ('Emily Phan', 'Deborah Brumfield', 'Sylvia Foulston', 'Roy Skaria', 'Alejandro Grove')
GROUP BY product_category;
```
RESULT
```
product_category
---------------------
Technology
Office Supplies
Furniture

```
## Question 7: Which small business customer had the highest sales?
```
SELECT customer_name, SUM(sales) AS Total_sales
FROM kms_table
WHERE customer_segment = 'Small Business'
GROUP BY customer_name
ORDER BY Total_sales DESC
LIMIT 1; -- Answer: Dennis Kane
```
RESULT
```
customer_name | Total_sales
--------------+---------------------
Dennis Kane   | 75967.5905
```
## Question 8: Which Corporate Customer placed the most number of orders in 2009 – 2012? 
There is no column for order year so one was created
```
SELECT *
FROM kms_table;
DESCRIBE kms_table; -- check datatype of table columns
ALTER TABLE kms_table
ADD Order_year VARCHAR(4); -- create new column
UPDATE kms_table
SET order_year = RIGHT(order_date, 4); -- insert order year to the new column
SELECT DISTINCT (Order_year)
FROM kms_table; -- to confirm what has been done
-- Let's go 
```
Now to the number of orders:
```
SELECT customer_name, SUM(Order_Quantity) AS Number_of_orders
FROM kms_table
WHERE ORDER_YEAR IN ('2009', '2010', '2011', '2012') AND customer_segment = 'Corporate'
GROUP BY customer_name
ORDER BY Number_of_orders DESC
LIMIT 1; -- Answer: Roy Skaria
```
```
customer_name   | Number_of_orders
----------------+------------------
'Roy Skaria'    | '773'

----------------
Roy Skaria   
```
## Question 9: Which consumer customer was the most profitable one? 
```
SELECT   customer_name, SUM(profit) AS total_customer_profit
FROM kms_table
GROUP BY customer_name
ORDER BY total_customer_profit DESC
LIMIT 1; -- Answer: Emily Phan
```
RESULT
```
customer_name | total_customer_profit
--------------+----------------------
Emily Phan    | 34005.44
```
## Question 10: Which customer returned items, and what segment do they belong to? 
Answer: Not Enough Data For This Analysis
## Question 11: If the delivery truck is the most economical but the slowest shipping method and Express Air is the fastest but the most expensive one, do you think the company appropriately spent shipping costs based on the Order Priority? Explain your answer
```
SELECT order_priority, ship_mode, SUM(shipping_cost) AS total_shipping_cost_for_priority, COUNT(order_id) AS number_of_orders_for_priority_ship_mode
FROM kms_table
GROUP BY order_priority, ship_mode
ORDER BYorder_priority ASC, total_shipping_cost_for_priority DESC;
```
RESULT
```
order_priority | ship_mode      | total_shipping_cost_for_priority | number_of_orders_for_priority_ship_mode
---------------+----------------+----------------------------------+-----------------------------------------
Critical       | Delivery Truck | 10783.82                         | 228
Critical       | Regular Air    | 8586.76                          | 1180
Critical       | Express Air    | 1742.10                          | 200
High           | Delivery Truck | 11206.88                         | 248
High           | Regular Air    | 10005.01                         | 1308
High           | Express Air    | 1453.53                          | 212
Low            | Delivery Truck | 11131.61                         | 250
Low            | Regular Air    | 10263.62                         | 1280
Low            | Express Air    | 1551.63                          | 190
Medium         | Delivery Truck | 9461.62                          | 205
Medium         | Regular Air    | 9418.72                          | 1225
Medium         | Express Air    | 1633.59                          | 201
Not Specified  | Regular Air    | 9734.08                          | 1277
Not Specified  | Delivery Truck | 9388.01                          | 215
Not Specified  | Express Air    | 1470.06                          | 180
```
Answer: Based on the analysis, it appears that KMS is NOT optimally or appropriately spending shipping costs based on Order Priority, particularly for high-priority orders.

