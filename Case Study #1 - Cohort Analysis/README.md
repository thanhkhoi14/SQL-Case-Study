# Case Study #1 - Cohort Analysis
![image](https://github.com/user-attachments/assets/73ce3876-d521-4d69-8955-6e4bbf3f1fdb)




## Table of Contents
* [Context](#context)
* [Dataset](#dataset)
* [Questions and Answers](#questions-and-answers)
***
### Context
#### 1. What is Cohort Analysis?
A cohort is simply a group of people who have something in common.Therefore, a cohort analysis is simply an analysis of several different cohorts (i.e. groups of customers) to get a better understanding of behaviors, patterns, and trends.

![image](https://github.com/user-attachments/assets/87815e26-be00-4995-b5d4-06951658459f)

One of the most common types of cohort analyses looks at time-based cohorts which groups users/customers by specific time-frame

#### 2. Cohort Analysis example 

![image](https://github.com/user-attachments/assets/5550b705-1501-4506-be5c-5c43dd7b1367)

Retention table tell us:
 - What % of people are coming back to the app after 3 days, 5 days,...revealing the quality of onboarding experience
 - How often users return and how valuable that cohort is. This can be linked to quality of product, operations and customer support

***
### Dataset

The dataset used in the project is available in the "Data" folder. The data set includes information about customers' online purchases stored in the FactInternetSales table.
- 📅 FactInternetSales

***
### Questions and Answers
**📝Write a query that'll query Rention Cohort Analysis based on First time Customer Purchase in the period of Jan 2020 to Jan 2021 What is the total amount each customer spent at the restaurant?**

````sql
WITH ListOrder AS (
SELECT DISTINCT 
  CustomerKey, --customer list by order date
  OrderDate
FROM FactInternetSales
) 
, ListFirstPurchase AS (
SELECT 
  CustomerKey,  -- retrieve FirstPurchaseMonth
  MIN(OrderDate) as FirstPurchaseDate,
  FORMAT(MIN(OrderDate), 'yyyy-MM') as FirstPurchaseMonth
FROM ListOrder
GROUP BY CustomerKey
)
, CohortIndex AS (
SELECT DISTINCT 
  O.CustomerKey,
  FirstPurchaseMonth,
  DATEDIFF(MONTH, FirstPurchaseDate, OrderDate) as CohortIndex -- Number of months customers return to buy
FROM ListOrder as O 
LEFT JOIN ListFirstPurchase as FP 
ON O.CustomerKey = FP.CustomerKey
)
SELECT *  -- pivot
INTO #cohort_pivot
FROM CohortIndex
PIVOT (
COUNT(CustomerKey) 
For CohortIndex IN ([0],
[1],
[2],
[3],
[4],
[5],
[6],
[7],
[8],
[9],
[10],
[11],
[12]
) 
) as pvt 
WHERE FirstPurchaseMonth >= '2020-01' 
ORDER BY FirstPurchaseMonth  
-- Format into % 
SELECT 
 FirstPurchaseMonth,
 FORMAT(1.0* [0]/[0], 'p') as [0],
 FORMAT(1.0* [1]/[0], 'p') as [1],
 FORMAT(1.0* [2]/[0], 'p') as [2],
 FORMAT(1.0* [3]/[0], 'p') as [3],
 FORMAT(1.0* [4]/[0], 'p') as [4],
 FORMAT(1.0* [5]/[0], 'p') as [5],
 FORMAT(1.0* [6]/[0], 'p') as [6],
 FORMAT(1.0* [7]/[0], 'p') as [7],
 FORMAT(1.0* [8]/[0], 'p') as [8],
 FORMAT(1.0* [9]/[0], 'p') as [9],
 FORMAT(1.0* [10]/[0], 'p') as [10],
 FORMAT(1.0* [11]/[0], 'p') as [11],
 FORMAT(1.0* [12]/[0], 'p') as [12]
FROM #cohort_pivot
ORDER BY FirstPurchaseMonth
````

*Answer:*

| FirstPurchaseMonth | 0      | 1      | 2      | 3      | 4      | 5      | 6      | 7      | 8      | 9      | 10     | 11     | 12     |
|--------------------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|--------|
| 2020-01            | 100.00% | 5.23%  | 4.62%  | 5.85%  | 4.92%  | 6.77%  | 5.23%  | 5.85%  | 4.31%  | 5.85%  | 6.15%  | 4.62%  | 5.23%  |
| 2020-02            | 100.00% | 5.52%  | 5.15%  | 5.15%  | 4.51%  | 5.52%  | 4.42%  | 4.60%  | 5.61%  | 5.70%  | 5.34%  | 4.42%  | 0.00%  |
| 2020-03            | 100.00% | 3.78%  | 3.78%  | 3.09%  | 4.21%  | 3.61%  | 3.09%  | 3.52%  | 3.52%  | 3.44%  | 2.84%  | 0.00%  | 0.00%  |
| 2020-04            | 100.00% | 2.76%  | 3.49%  | 3.31%  | 3.03%  | 3.22%  | 3.40%  | 2.21%  | 2.76%  | 3.03%  | 0.00%  | 0.00%  | 0.00%  |
| 2020-05            | 100.00% | 3.51%  | 2.72%  | 3.51%  | 2.80%  | 2.10%  | 3.33%  | 2.45%  | 2.98%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  |
| 2020-06            | 100.00% | 1.99%  | 2.69%  | 3.21%  | 3.90%  | 2.77%  | 2.77%  | 1.91%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  |
| 2020-07            | 100.00% | 2.09%  | 2.76%  | 3.04%  | 2.66%  | 4.18%  | 2.38%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  |
| 2020-08            | 100.00% | 1.77%  | 3.26%  | 2.23%  | 3.07%  | 2.05%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  |
| 2020-09            | 100.00% | 2.24%  | 2.53%  | 3.11%  | 2.33%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  |
| 2020-10            | 100.00% | 2.12%  | 2.03%  | 2.47%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  |
| 2020-11            | 100.00% | 2.29%  | 2.38%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  |
| 2020-12            | 100.00% | 1.31%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  |
| 2021-01            | 100.00% | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  | 0.00%  |

***

















