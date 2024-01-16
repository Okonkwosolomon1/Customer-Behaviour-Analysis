# Customer_Behaviour_Analysis using MySQL

## Table of Content

- [Project Overview](#project-overview)

- [Project Motivation](#project-motivation)

- [Problem Statement](#problem-statement)
- [Data Source, Dataset and Entity Relationship Diagram](#data-Source,-dataset-and-entity-relationship-diagram)
- [Skills Deployed](#skills-deployed)
- [Case Study Questions](#case-study-questions)
- [Question 1](#question-1)
- [Question 2](#question-2)
- [Question 3](#question-3)
- [Question 4](#question-4)
- [Question 5](#question-5)
- [Question 6](#question-6)
- [Question 7](#question-7)
- [Question 8](#question-8)
- [Question 9](#question-9)
- [Question 10](#question-10)
- [Question 11](#question-11)
- [Question 12](#question-12)

- [Insights from Data Analysis](#insights-from-data-analysis)

### Project Overview

This Project is case study number one of an 8 week SQL challenge posed by Danny Ma as can be found in this [link:](https://8weeksqlchallenge.com/case-study-1/) It aims at helping Danny's Diner Management to understand their customers better in terms of the spending patterns, trends and associated factors. Furthermore, the analysis of the data is hoped to provide insights into what the each customer seems to like or don't like. This will help Diner's Management to improve the Business

### Project  Motivation

"Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen. Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business" (Danny Ma)

### Problem Statement

Danny seeks to understand his customers behaviour and to have deeper connection with them to serve them better personalised services. He plans to do this by using "data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite". 
The insights from the data analysis will help him decide whether he should expand the existing customer loyalty program. He also needs help to "generate some basic datasets so his team can easily inspect the data without needing to use SQL".

### Data Source, Dataset and Entity Relationship Diagram
Danny provided a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough to write fully functioning SQL queries to help him answer his questions! The data are called: sales, menu and members. He also provided the Data loading syntax and the Entity Relationship Diagram for the Data. They can all be found in the link provided previously

![ERD](https://github.com/Okonkwosolomon1/Customer_Behaviour_Analysis/assets/155292549/fbbbb290-42e7-484f-a124-f48c0e210d98)


### Skills Deployed

Window Functions
Database Creation from raw data
Aggregations
JOINs
Common Table Expressions (CTE)
Subquery
Script writing

### Case Study Questions
1. What is the total amount each customer spent at the restaurant?
2. How many days has each customer visited the restaurant?
3. What was the first item from the menu purchased by each customer?
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
5. Which item was the most popular for each customer?
6. Which item was purchased first by the customer after they became a member?
7. Which item was purchased just before the customer became a member?
8. What is the total items and amount spent by each member before they became a member?
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

Bonus Task 1:Recreate the table output using the available data 

Bonus Task 2: Rank all things! (That is, Rank all the products putting NULL ranking values for non-member purchases)

### Data Analysis

### Question 1: What is the total amount each customer spent at the restaurant?
#### _Ans: Customers A,B and C spent $76, $74 and $36 respectively_

```sql
SELECT s.customer_id, SUM(m.price) AS total_spent
FROM sales s
JOIN menu m
ON s.product_id = m.product_id
GROUP BY 1;
```
### Question 2 How many days has each customer visited the restaurant?
#### _Ans: Customers A, B and C visited for 4, 6 and 2 days respectively_

```sql
SELECT customer_id, COUNT(DISTINCT order_date) AS num_of_visit_days
 FROM sales
 GROUP BY 1;
```
### Question 3 What was the first item from the menu purchased by each customer?
#### _Ans: The first items they purchased were sushi and curry for customer A, curry for customer B and ramen for customer C_

```sql
WITH customer_first_purchase AS
			(
				SELECT s.customer_id, MIN(s.order_date) AS first_purchase
				FROM sales s
				GROUP BY s.customer_id
	      		)
		      SELECT cfp. customer_id, cfp.first_purchase,m. product_name
		      FROM customer_first_purchase cfp
		      JOIN sales s
		      ON cfp. customer_id = s. customer_id
		      AND cfp. first_purchase = s.order_date
		      JOIN menu m 
		      ON m.product_id = s. product_id;
```
### Question 4 What is the most purchased item on the menu and how many times was it purchased by all customers?
#### _Ans: Ramen was the most purchased item. Customer A bought it 3 times, B 2 times and customer C, 3 times as well_

```sql
WITH most_purchashed_item AS 
		(
			SELECT product_id, COUNT(product_id) AS num_of_items_purchased
			FROM sales s 
			GROUP BY 1
			ORDER BY 2 DESC
        	)
		SELECT mp. product_id, m. product_name, mp. num_of_items_purchased
		FROM most_purchashed_item mp
		JOIN menu m
		ON mp.product_id = m. product_id;
```
### Question 5 Which item was the most popular for each customer?
#### _Ans: For Customers A and C it is ramen but Customer B liked all 3 items equally_

```sql
WITH ranked_purchase AS
			(
				SELECT s.customer_id, s.product_id, m.product_name, COUNT(s.product_id) AS purchase_count,
				DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY COUNT(s.product_id)DESC) AS purchase_rank 
				FROM sales s
				JOIN menu m 
				ON s.product_id = m.product_id
				GROUP BY 1,2,3
                	)
			SELECT rp. customer_id, rp.product_id, rp.product_name, rp.purchase_count
            		FROM ranked_purchase rp
           		WHERE purchase_rank = 1;
```
### Question 6 Which item was purchased first by the customer after they became a member?
#### _Ans: The first items A purchased was curry while B's first item was sushi. Customer C did not buy any items_

```sql
WITH ranked_order AS 
			(
				SELECT s.customer_id, s.product_id,
				DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS rank_num
				FROM sales s
				JOIN members me
				ON s.customer_id = me.customer_id
				AND s.order_date >= me.join_date
                
                )
		SELECT ro.customer_id, m.product_name
            	FROM ranked_order ro
            	JOIN menu m 
            	ON ro.product_id = m.product_id
            	AND ro.rank_num =1;
```
### Question 7 Which item was purchased just before the customer became a member?
#### Ans: Customer A bought sushi and curry while customer B bought sushi

```sql
WITH pre_membership_order AS 
			(
				SELECT s.customer_id, s.order_date, s.product_id,
				DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date DESC) AS rank_num
				FROM sales s
				JOIN members me
				ON s.customer_id = me.customer_id
				AND s.order_date < me.join_date
                
                	)
			SELECT pmo.customer_id, m.product_name
			FROM pre_membership_order pmo
		        JOIN menu m 
		        ON pmo.product_id = m.product_id
		        AND pmo.rank_num =1;
```
### Question 8 What is the total items and amount spent by each member before they became a member?
#### _Ans: Customer A bought a total of 25 items while customer B bought a total of 40 items_

```sql
WITH total_spent_and_quantity AS
			(
				SELECT s.customer_id, s.order_date,  COUNT(s.product_id) AS total_items, SUM(m.price) AS total_amount
				FROM sales s 
				JOIN menu m 
				ON s.product_id = m.product_id
	            		GROUP BY s.customer_id, s.order_date
            		)
			SELECT tsq.customer_id, tsq.total_items, tsq.total_amount
		        FROM total_spent_and_quantity tsq
		        JOIN members me
		        ON tsq.customer_id = me.customer_id
		        AND tsq.order_date < me.join_date;

```
###  Question 9 If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
#### _Ans: Customers A,B,C have 860,940 and 360 points respectively_

```sql
			SELECT DISTINCT sub.customer_id,
			SUM(sub.total_points) OVER (PARTITION BY sub.customer_id) AS total_points_for_each_customer
			FROM 
				(
				WITH sales_record AS 
					(
						SELECT s.customer_id, s.product_id, m.product_name, 
							CASE 					
							WHEN m.product_name = 'sushi' THEN 20
							WHEN m.product_name = 'curry' THEN 10
				                        WHEN m.product_name = 'ramen' THEN 10 #from the given database we know there are only 3 kinds of products
				                        ELSE 0
				                        END AS product_points
						FROM sales s
						JOIN menu m
						ON s.product_id = m. product_id
                    			)
					SELECT sr.customer_id, (m.price * sr.product_points) AS total_points
					FROM sales_record sr
				        JOIN menu m
				        ON sr. product_id = m. product_id
				) sub;
```
### Question 10  In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
#### _Ans: Customer A had 1370 points while customer B had 940 points_

```sql

   	SELECT 
		DISTINCT sub. customer_id, 
		SUM(sub.product_points) OVER (PARTITION BY sub. customer_id ORDER BY sub. customer_id) AS total_points_earned
	FROM
			(	
				SELECT s.customer_id, s.product_id, m.product_name, me.join_date, s.order_date,
					CASE 
						WHEN s.order_date BETWEEN me.join_date AND (me.join_date + INTERVAL 1 week) THEN (m.price*20)
				                WHEN m.product_name ='sushi' THEN (m.price*20)
				                ELSE (m.price*10)
				                END AS product_points
				FROM sales s 
				JOIN menu m
				ON s.product_id = m. product_id
		                JOIN members me
		                ON s.customer_id = me.customer_id
				WHERE s.customer_id IN ('A','B')
                		AND s.order_date <= '2021-01-31'
			) sub;
```
### Bonus Task 1 (Question 11): Recreate the table output using the available data
```sql
	SELECT s.customer_id, s.order_date, m. product_name, m.price,
		CASE
			WHEN s.order_date >= me.join_date THEN 'Y'
            		ELSE 'N' END AS members
	FROM sales s
	JOIN menu m
	ON s.product_id = m.product_id
	LEFT JOIN members me
	ON s.customer_id = me.customer_id
	ORDER BY s.customer_id, s.order_date;
```
### Bonus Task 2 (Question 12): Rank all the products putting NULL ranking values for non-member purchases
```sql
	WITH pre_ranking_table AS
		(
			SELECT s.customer_id, s.order_date, m. product_name, m.price,
				CASE
					WHEN s.order_date >= me.join_date THEN 'Y'
					ELSE 'N' END AS members
			FROM sales s
			JOIN menu m
			ON s.product_id = m.product_id
			LEFT JOIN members me
			ON s.customer_id = me.customer_id
			ORDER BY s.customer_id, s.order_date
            	) 
		SELECT prt.customer_id, prt.order_date, prt.product_name, prt.price, prt.members,
			CASE 
				WHEN members = 'N' THEN NULL
				ELSE RANK() OVER (PARTITION BY prt.customer_id,members ORDER BY prt.order_date)
                END AS ranking
	       FROM pre_ranking_table prt
	       ORDER BY prt.customer_id, prt.order_date;

```
![Question 12 pic](https://github.com/Okonkwosolomon1/Customer_Behaviour_Analysis/assets/155292549/d345eb6d-3b7f-4f6d-8f97-928cefbdce0d)

### Insights from Data Analysis
The most frequent visitor is Customer B. Customer B made 6 visits in January 2021. However, customer A and B earned the most loyalty points
Danny’s Diner’s most popular item is ramen, followed by curry and sushi.
Customer A loves ramen, Customer C loves only ramen whereas Customer B seems to enjoy sushi, curry and ramen equally.
The last item ordered by Customers A and B before they became members are sushi and curry. Does it mean both of these items are the deciding factor?
