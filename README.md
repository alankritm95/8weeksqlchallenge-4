# 8weeksqlchallenge-4

## Case Study #4: Data Bank

![image](https://github.com/alankritm95/8weeksqlchallenge-4/assets/129503746/b2889568-d108-4c91-b753-6e15e9368301)

View the case study [here](https://8weeksqlchallenge.com/case-study-4/)


### Introduction
There is a new innovation in the financial industry called Neo-Banks: new aged digital only banks without physical branches.

Danny thought that there should be some sort of intersection between these new age banks, cryptocurrency and the data world…so he decides to launch a new initiative - Data Bank!

Data Bank runs just like any other digital bank - but it isn’t only for banking activities, they also have the world’s most secure distributed data storage platform!

Customers are allocated cloud data storage limits which are directly linked to how much money they have in their accounts. There are a few interesting caveats that go with this business model, and this is where the Data Bank team need your help!

### Problem Statement
The management team at Data Bank want to increase their total customer base - but also need some help tracking just how much data storage their customers will need.

This case study is all about calculating metrics, growth and helping the business analyse their data in a smart way to better forecast and plan for their future developments!

### Entity Relationship Diagram

![image](https://github.com/alankritm95/8weeksqlchallenge-4/assets/129503746/0232dc35-15a3-47da-b994-c0ad6bf637fa)

## Case Study Questions

### How many unique nodes are there on the Data Bank system?

select count(distinct node_id) as unique_nodes from customer_nodes;

![image](https://github.com/alankritm95/8weeksqlchallenge-4/assets/129503746/93960d40-f208-4876-834d-963ce6e6fbdb)


### What is the number of nodes per region?

select region_id, count( node_id) as unique_nodes from customer_nodes
group by region_id order by region_id;

![image](https://github.com/alankritm95/8weeksqlchallenge-4/assets/129503746/726a58f8-bd43-44fd-bbd6-fbdee3242219)


### How many customers are allocated to each region?

select c.region_id, region_name, count(customer_id) as total_customers from customer_nodes c join regions r
on c.region_id = r.region_id
group by c.region_id, region_name order by c.region_id;

![image](https://github.com/alankritm95/8weeksqlchallenge-4/assets/129503746/84b8de8c-e759-4a57-9ee7-ef98904e89bd)


### How many days on average are customers reallocated to a different node?


SELECT round(avg(TIMESTAMPDIFF(DAY, start_date, end_date)),2) AS diff
FROM customer_nodes WHERE end_date!='9999-12-31';

![image](https://github.com/alankritm95/8weeksqlchallenge-4/assets/129503746/bcff4275-e945-4b33-b7ac-17264349f8b1)


### What is the median, 80th and 95th percentile for this same reallocation days metric for each region?


WITH cte1 AS
  (SELECT r.region_id,
       region_name,
          TIMESTAMPDIFF(DAY, start_date, end_date) AS reallocation_days
   FROM customer_nodes c
   INNER JOIN regions r on c.region_id = r.region_id
   WHERE end_date!='9999-12-31'),
     cte2 AS
  (SELECT *,
          percent_rank() over(PARTITION BY region_id
                              ORDER BY reallocation_days)*100 AS p
   FROM cte1)
SELECT region_id,
       region_name,
       avg(reallocation_days)
FROM cte2
WHERE p >95
group by region_id,
       region_name;

 ![image](https://github.com/alankritm95/8weeksqlchallenge-4/assets/129503746/a7f00a8e-09af-441c-91ed-43e2ee1c1bc6)



