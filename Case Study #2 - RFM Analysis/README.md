# Case Study #2 - RFM Analysis
![image](https://github.com/user-attachments/assets/66370439-2d09-4970-a029-f5d83821e0b8)

## Table of Contents
* [Context](#context)
* [Dataset](#dataset)
* [Questions and Answers](#questions-and-answers)
***
### Context
 **1. Customer segmentation**
 ![image](https://github.com/user-attachments/assets/a5cda3c3-6f75-4f7e-80e9-73051a206aaa)

 
 **2. RFM Analysis**
 ![image](https://github.com/user-attachments/assets/b5c7842f-7946-444c-b4f5-4490c7231941)

 **Customer segmentation based on transaction history, purchase frequency, and order value:**
- Days since last purchase: Identify active and dormant customers
- Purchase frequency: Categorize customers as loyal, occasional, or one-time buyers
- Order value: Segment customers as high-value, average-value, or low-value


 **3. Scoring R, F, M on a scale of 1-4**
 ![image](https://github.com/user-attachments/assets/8a390107-9573-4bb5-94ee-633f8a794c8d)

- Each criterion R, F, M is scored on a scale of 1-4. By combining these three scores, we obtain the RFM score (e.g., 441, 134).
- The closer the customer's most recent purchase (R), the higher the score. The higher the frequency of purchases (F), the higher the score. The higher the purchase value (M), the higher the score.

 **4. Customer Segmentation by RFM**

| Customer Group  | Characteristics | Marketing Strategies |
| ----------- | ---------- | ---------- |
| Loyal           | Recently purchased, high frequency, high order value | - Reward programs, New products     |
| Promising       | Recently purchased, high frequency, low order value | - Product recommendations   |
| Big spenders    | High order value, low frequency | - Cross/up-sells, Luxury products     |
| New Customers   |Recently purchased, low frequency | - Membership offers   |
| Potential churn | Purchased a while ago, no recent purchases | - Discount offers          |
| Lost            | Purchased a long time ago, churned |          |


***
### Dataset

The dataset used in the project is available in the "Data" folder. The data set includes information about customers' online purchases stored in the FactInternetSales table.
- 📅 FactInternetSales

***
### Questions and Answers
**Write a query that'll query to implement RFM analysis into serveral segmentation like:**
- Loyal
- Promising
- Big spenders
- New customers
- Potential churn
- Lost

````sql
WITH CustSales AS (
SELECT 
  CustomerKey,
  COUNT(DISTINCT SalesOrderNumber) as Frequency ,
  SUM(SalesAmount) as Monetary, 
  MAX(OrderDate) as MostRecentOrderDate, 
  DATEDIFF(DAY, MAX(OrderDate), (SELECT MAX(OrderDate) FROM dbo.FactInternetSales) ) as Recency 
FROM dbo.FactInternetSales 
GROUP BY CustomerKey) 

, RFM_scoring AS (
SELECT 
  CustomerKey,
  NTILE(4) OVER (ORDER BY Recency DESC) as rfm_recency,
  NTILE(4) OVER (ORDER BY Frequency) as rfm_frequency,
  NTILE(4) OVER (ORDER BY Monetary) as rfm_monetary 
FROM CustSales)

, RFM_score AS (
SELECT 
  CustomerKey,
  CONCAT(rfm_recency, rfm_frequency, rfm_monetary) as RFM_score
FROM RFM_scoring
) 

, RFM_Segmentation AS ( 
SELECT 
  CustomerKey,
  RFM_score,
  CASE 
WHEN RFM_score LIKE '1__' THEN 'Lost Customer' 
WHEN RFM_score LIKE '[3,4][3,4][1,2]' THEN 'Promising' 
WHEN RFM_score LIKE '[3,4][3,4][3,4]' THEN 'Loyal' 
WHEN RFM_score LIKE '_[1,2]4' THEN 'Big spenders' 
WHEN RFM_score LIKE '[3,4][1,2]_' THEN 'New customer' 
WHEN RFM_score LIKE '2__' THEN 'Potential churn' 
END AS CustomerSegmentation 
FROM RFM_score
) 

SELECT 
CustomerSegmentation, 
COUNT(CustomerKey) as NoCustomer 
FROM RFM_Segmentation
GROUP BY CustomerSegmentation
````

*Answer:*

| **Customer Segmentation** | **No.Customer** |
| --------------- | --------------- |
| Big spenders          | 26    |
| Lost Customer          | 4621    |
| Loyal           | 3545      |
| New customer    | 2830    |
|  Potential churn  | 4608      |
|    Promising      | 2854       |


***

