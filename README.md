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



### Project Overview

This Project is case study number one of an 8 week SQL challenge posed by Danny Ma as can be found in this [link:](https://8weeksqlchallenge.com/case-study-1/) It aims at helping Danny's Diner Management to understand their customers better in terms of the spending patterns, trends and associated factors. Furthermore, the analysis of the data is hoped to provide insights into what the each customer seems to like or don't like. This will help Diner's Management to improve the Business

### Project  Motivation

"Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen. Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business" (Danny Ma)

### Problem Statement

Danny seeks to understand his customers behaviour and to have deeper connection with them to serve them better personalised services. He plans to do this by using "data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite". 
The insights from the data analysis will help him decide whether he should expand the existing customer loyalty program. He also needs help to "generate some basic datasets so his team can easily inspect the data without needing to use SQL".

### Data Source, Dataset and Entity Relationship Diagram
Danny provided a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough to write fully functioning SQL queries to help him answer his questions! The data are called: sales, menu and members. He also provided the Data loading syntax and the Entity Relationship Diagram for the Data. They can all be found in the link provided previously

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
8. What is the total items and amount spent for each member before they became a member?
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

Bonus Task 1:Recreate the table output using the available data 

Bonus Task 2: Rank all things! (That is, Rank all the products putting NULL ranking values for non-member purchases)

### Data Analysis

### Question 1: What is the total amount each customer spent at the restaurant?

```sql
SELECT s.customer_id, SUM(m.price) AS total_spent
FROM sales s
JOIN menu m
ON s.product_id = m.product_id
GROUP BY 1;
```
####
