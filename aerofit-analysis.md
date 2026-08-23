**Tool Used: Microsoft SQL Server**

# Table of Contents
- [Data Preparation](#Data-Preparation)
- [Business Questions and Answers](#Business-Questions-and-Answers)

## Data Preparation

The aerofit_treadmill_data.csv dataset was imported into SQL as the `Customers` table.

To improve data organization and maintainability, I:
1. Added a `UserID` column to the `Customers` table to identify each customer record.
2. Created a `Products` table because product prices were not included in the original dataset.
3. Added a `ProductID` column to uniquely identify each product.
4. Replaced the product name in the `Customers` table with `ProductID` and established a foreign key relationship with the `Products` table.

### 1. Create a `UserID` column primary key in the `Customers` table
````sql
ALTER TABLE Customers
ADD UserID INT IDENTITY(1,1) PRIMARY KEY;
````
### 2. Create a new table for the products data
````sql
CREATE TABLE Products (

  ProductID int IDENTITY(1,1) PRIMARY KEY,
  Product nvarchar(50) NOT NULL,
  Price int NOT NULL

);

INSERT INTO Products (Product, Price)
VALUES ('KP281', 1500), ('KP481', 1750), ('KP781', 2500);
````
### 3. Replace the `Product` column with the `ProductID` in the `Customers` table
````sql
ALTER TABLE Customers
ADD ProductID INT;

UPDATE Customers
SET Customers.ProductID = Products.ProductID
FROM Customers
JOIN Products
  ON Customers.Product = Products.Product;

ALTER TABLE Customers
DROP COLUMN Product;

ALTER TABLE Customers
ADD CONSTRAINT FK_Customers_Products
FOREIGN KEY (ProductID)
REFERENCES Products(ProductID);
````
**Steps:**
- Add `ProductID` to the `Customers` table to replace the product name with a unique product identifier and improve the structure of the data.
- Update `ProductID` by matching the existing `Product` value in the `Customers` table using JOIN. This assigns the correct `ProductID` to each customer record.
- Remove the original `Product` column after all customer records have been re-assigned.
- Create a `FOREIGN KEY` constraint between `Customers.ProductID` and `Products.ProductID` to maintain the relationship between the two tables.

***

## Business Questions and Answers

To gain meaningful insights, I defined a set of questions. The main questions explored were:

1. What is the customer profile characteristics for each treadmill?
2. What percentage of customers in each income group purchase each treadmill?
3. Which KP281 customers have characteristics similar to KP481 and KP781 customers?
4. What are the total purchases and total sales of each product?
5. Which treadmill generates the highest sales, and what percentage of total sales does it represent?

### 1. What is the customer profile characteristics for each treadmill?

First, I used `CASE WHEN` to separate male and female customers, and `AVG` to calculate the average customer characteristics for each product.
To round the results, I used the `CAST` function. For those percentage results, I included `DECIMAL(10,2)` in the `CAST` function to get two decimals.
````sql
SELECT ProductID,

-- Male Customers

  CAST(AVG(CASE WHEN Gender = 'Male' THEN 1.0 ELSE 0 END) * 100 AS decimal(10,2)) AS Pct_MaleUsers,
  CAST(AVG(CASE WHEN Gender = 'Male' THEN Age END) AS decimal) AS Male_Age,
  CAST(AVG(CASE WHEN Gender = 'Male' THEN Education * 1.0 END) AS decimal) AS Male_Education,
  CAST(AVG(CASE WHEN Gender = 'Male' AND MaritalStatus = 'Single' THEN 1.0 ELSE 0 END) * 100 AS decimal(10,2)) AS Pct_SingleMen,
  CAST(AVG(CASE WHEN Gender = 'Male' AND MaritalStatus = 'Partnered' THEN 1.0 ELSE 0 END) * 100 AS decimal(10,2)) AS Pct_PartneredMen,
  CAST(AVG(CASE WHEN Gender = 'Male' THEN Usage * 1.0 END)  AS decimal) AS Male_Usage,
  CAST(AVG(CASE WHEN Gender = 'Male' THEN Miles * 1.0 END) AS decimal) AS Male_Miles,
  CAST(AVG(CASE WHEN Gender = 'Male' THEN Income * 1.0 END) AS decimal) AS Male_Income,
  CAST(AVG(CASE WHEN Gender = 'Male' THEN Fitness * 1.0 END)  AS decimal) AS Male_Fitness,

-- Female Customers

  CAST(AVG(CASE WHEN Gender = 'Female' THEN 1.0 ELSE 0 END) * 100 AS decimal(10,2)) AS Pct_FemaleUsers,
  CAST(AVG(CASE WHEN Gender = 'Female' THEN Age END) AS decimal) AS Female_Age,
  CAST(AVG(CASE WHEN Gender = 'Female' THEN Education * 1.0 END) AS decimal) AS Female_Education,
  CAST(AVG(CASE WHEN Gender = 'Female' AND MaritalStatus = 'Single' THEN 1.0 ELSE 0 END) * 100 AS decimal(10,2)) AS Pct_SingleWomen,
  CAST(AVG(CASE WHEN Gender = 'Female' AND MaritalStatus = 'Partnered' THEN 1.0 ELSE 0 END) * 100 AS decimal(10,2)) AS Pct_PartneredWomen,
  CAST(AVG(CASE WHEN Gender = 'Female' THEN Usage * 1.0 END) AS decimal) AS Female_Usage,
  CAST(AVG(CASE WHEN Gender = 'Female' THEN Miles * 1.0 END) AS decimal) AS Female_Miles,
  CAST(AVG(CASE WHEN Gender = 'Female' THEN Income * 1.0 END) AS decimal) AS Female_Income,
  CAST(AVG(CASE WHEN Gender = 'Female' THEN Fitness * 1.0 END) AS decimal) AS Female_Fitness
FROM Customers
GROUP BY ProductID;
````
#### Answers:
##### Male Customers:
<img width="833" height="74" alt="Screenshot 2026-08-23 164737" src="https://github.com/user-attachments/assets/4a28d802-ae46-4614-951a-ad15de716565" />

##### Female Customers:
<img width="847" height="74" alt="Screenshot 2026-08-23 164745" src="https://github.com/user-attachments/assets/9a96f11c-a1af-445d-b279-3acfb2be71c3" />


Now, I wanted to analyze the customer profile without separating customers by gender to obtain an overall view of each product’s customer base. This query follows the same approach as the above: it calculates the average customer characteristics and the percentage of customers by gender and marital status for each treadmill model.

````sql
SELECT 
	ProductID,
	CAST(AVG(CASE WHEN Gender = 'Male' THEN 1.0 ELSE 0 END) * 100 AS decimal(10,2)) AS Pct_MaleUsers,
	CAST(AVG(CASE WHEN Gender = 'Female' THEN 1.0 ELSE 0 END) AS decimal(10,2)) * 100 AS Pct_FemaleUsers,
	CAST(AVG(Age) AS decimal) AS Avg_Age,
	AVG(Education) AS Avg_Education,
	CAST(AVG(CASE WHEN MaritalStatus = 'Single' THEN 1.0 ELSE 0 END) * 100 AS decimal(10,2)) AS Pct_SinglePeople,
	CAST(AVG(CASE WHEN MaritalStatus = 'Partnered' THEN 1.0 ELSE 0 END) * 100 AS decimal(10,2)) AS Pct_PartneredPeople,
	AVG(Usage) AS Usage,
	AVG(Miles) AS Avg_Miles,
	AVG(Income) AS Avg_Income,
	AVG(Fitness) AS Avg_Fitness
FROM Customers
GROUP BY ProductID;
````
#### Output:

<img width="910" height="80" alt="Screenshot 2026-08-23 165641" src="https://github.com/user-attachments/assets/303044b9-aa35-45a5-ac30-77883207ad85" />

- The customer profiles for the KP281 and KP481 treadmills are similar.
- The KP781 treadmill shows a distinct customer profile, characterized by higher annual income, fitness levels, expected usage and miles.

***

### 2. What percentage of customers in each income group purchase each treadmill?

To analyze purchasing patterns, I created the `v_seg` view to segment customers by Income, Fitness, Usage, and Miles. The group boundaries were defined based on the distribution of the data.

````sql
CREATE VIEW v_seg AS
SELECT
	UserID,
	ProductID,
	CASE
		WHEN Income < 45000 THEN 'Low'
		WHEN Income BETWEEN 45000 AND 60000 THEN 'Middle'
		WHEN Income > 60000 THEN 'High' 
	END AS Income_Group,

	CASE
		WHEN Fitness < 3 THEN 'Low'
		WHEN Fitness = 3 THEN 'Medium'
		WHEN Fitness > 3 THEN 'High'
	END AS Fitness_Group,

	CASE
		WHEN Usage < 3 THEN 'Low'
		WHEN Usage = 3 THEN 'Medium'
		WHEN Usage > 3 THEN 'High'
	END AS Usage_Group,

	CASE 
		WHEN Miles < 80 THEN 'Low'
		WHEN Miles BETWEEN 80 AND 120 THEN 'Middle'
		WHEN Miles > 120 THEN 'High'
	END AS Miles_Group
FROM Customers
````
Now, the question can be answered using the  `Income_Group` column. To calculate the percentages, I used `COUNT` with `CASE WHEN` to count customers who purchased each product and divided this by the total number of customers within each income group. Then, I grouped the results by `Income_Group` to compare product purchases across the three income segments.

````sql
SELECT
	Income_Group,
	CAST(1.0 * COUNT(CASE WHEN ProductID = 3 THEN UserID END) / COUNT(UserID) * 100 AS decimal(10,2)) AS Pct_KP781,
	CAST(1.0 * COUNT(CASE WHEN ProductID = 2 THEN UserID END) / COUNT(UserID) * 100 AS decimal(10,2)) AS Pct_KP481,
	CAST(1.0 * COUNT(CASE WHEN ProductID = 1 THEN UserID END) / COUNT(UserID) * 100 AS decimal(10,2)) AS Pct_KP281
FROM v_seg
GROUP BY Income_Group; 
````
#### Answer:
<img width="329" height="76" alt="Screenshot 2026-08-23 172215" src="https://github.com/user-attachments/assets/309a1a64-834f-4e4b-b0cf-26b39eea9056" />

***

### 3. Which KP281 customers have characteristics similar to KP481 and KP781 customers?

For this query, I used a `CTE` named `patterns` to count the number of customers who purchased each product across the income, fitness and usage segments previously defined in the `v_seg` view.

In the main query, I combined KP481 and KP781 customers into a single `Highertier_UserCount` group to compare them with KP281 customers. Then, I used `WHERE` clause to remove those results where either there were no KP281 customers or no higher-tier customers, since those groups could not provide a meaningful comparison. Lastly, I limited the output to the `TOP 5` patterns, and ordered them by the KP281 customers in descending order.

````sql
WITH patterns AS (

SELECT
	Income_Group,
	Fitness_Group,
	Usage_group,
	COUNT(CASE WHEN ProductID = 1 THEN UserID END) KP281_Count,
	COUNT(CASE WHEN ProductID = 2 THEN UserID END) KP481_Count,
	COUNT(CASE WHEN ProductID = 3 THEN UserID END) KP781_Count
FROM v_seg
GROUP BY Income_Group, Fitness_Group, Usage_Group

)

SELECT TOP 5
	Income_Group,
	Fitness_Group,
	Usage_Group,
	KP281_Count,
	KP481_Count + KP781_Count AS Highertier_UserCount
FROM patterns
WHERE KP281_Count > 0 
	AND KP481_Count + KP781_Count > 0
ORDER BY KP281_Count DESC, Highertier_UserCount DESC;
````
#### Answer:
<img width="484" height="114" alt="Screenshot 2026-08-23 173216" src="https://github.com/user-attachments/assets/f30fa44c-26c6-459f-befd-c64a805d588c" />

- The **Middle Income – Medium Fitness – Medium Usage** and **Middle Income – Medium Fitness – High Usage** segments show the strongest customer counts across KP281 and the higher-tier products.

Now, it's important to evaluate the results without combining KP481 and KP781 customers into a single group, to determine whether the patterns change when each higher-tier product is analyzed separately.

````sql
WITH patterns AS (

SELECT
	Income_Group,
	Fitness_Group,
	Usage_group,
	Miles_group,
	COUNT(CASE WHEN ProductID = 1 THEN UserID END) KP281_Count,
	COUNT(CASE WHEN ProductID = 2 THEN UserID END) KP481_Count,
	COUNT(CASE WHEN ProductID = 3 THEN UserID END) KP781_Count
FROM v_seg
GROUP BY Income_Group, Fitness_Group, Usage_Group, Miles_group

)

SELECT
*
FROM patterns
WHERE KP281_Count > 0 AND KP781_Count > 0
ORDER BY KP281_Count DESC;
````
#### Output:
<img width="599" height="72" alt="Screenshot 2026-08-23 174919" src="https://github.com/user-attachments/assets/51e09db0-9332-44a4-a951-94ba9c2fd77e" />

With these results, the same **Middle Income – Medium Fitness – High Usage** pattern appears, but it's concentrated among KP481 customers.

However, the **Middle Income – Medium Fitness – Medium Usage** pattern doesn't exist in this output, so we can conclude it's only present among KP281 and KP481 customers (because I included the `WHERE` condition `KP781_Count > 0`).

It's important to note that adding the miles group didn't reveal any additional patterns.

***

### 4. What are the total purchases and total sales of each product?

I used a `JOIN` to retrieve the `Price` of each product and calculate the total sales value.
To calculate total purchases, I simply used `COUNT(UserID)`, since each customer record represents one treadmill purchase.

````sql
SELECT
	p.Product,
	COUNT(UserID) AS Total_Purchases,
	SUM(Price) AS Total_Sales
FROM Customers c
JOIN Products p
ON c.ProductID = p.ProductID
GROUP BY p.Product
````
#### Answer:

<img width="253" height="73" alt="Screenshot 2026-08-23 180840" src="https://github.com/user-attachments/assets/2ede4891-7690-4c2c-9863-f8adb8f019fc" />

***

### 5. Which treadmill generates the highest sales, and what percentage of total sales does it represent?

To answer this question, the query used above is necessary, so I made it a CTE named `totals`. In the main query, I calculated each product's share by the total sales by the overall sales, using the `OVER()` clause to obtain the total. I also included each product’s share of total purchases using the previously calculated purchase counts.
````sql
WITH totals AS (

SELECT
	p.Product,
	COUNT(UserID) AS Total_Purchases,
	SUM(Price) AS Total_Sales
FROM Customers c
JOIN Products p
ON c.ProductID = p.ProductID
GROUP BY p.Product

)

SELECT
	*,
	CAST(Total_Purchases / SUM(Total_Purchases * 1.0) OVER() * 100 AS decimal(10,2)) AS Pct_Units_Sold,
	CAST(Total_Sales / SUM(Total_Sales * 1.0) OVER() * 100 AS decimal(10,2)) AS Pct_Sales
FROM totals;
````
#### Answer:

<img width="405" height="73" alt="Screenshot 2026-08-23 182345" src="https://github.com/user-attachments/assets/ab8f2431-2462-4e4a-ae4f-494d3ce1dede" />

- The KP281 treadmill generated the highest sales, representing 36.92% of the total sales.

***
