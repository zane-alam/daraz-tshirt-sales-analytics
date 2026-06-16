## Daraz-tshirt-sales-analytics
Daraz T‑Shirt Sales Analytics — CSV (3,280 rows, 9 cols). Reproducible analyses using SQL and Python: data cleaning, EDA, SQL queries, and a short report extracting pricing, discount, sales‑volume, SKU and seller insights. Includes scripts and instructions to reproduce results.

***Handling Missing Values using python***

1.Import required python libraries

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```
2. Load dataset

```python
df = pd.read_csv('/content/daraz_raw_tshirt_sales_data.csv')
```

3.load the info of dataset

```python
df.info()
```
4. FInd the amount of Missing/Null values

```python
print(df.isnull().sum())
```

5. Finding the mean values[extracting numeric values from strings

```python
df['No. of Sells'] = pd.to_numeric(df['No. of Sells'].astype(str).str.extract(r'(\d+)', expand=False), errors='coerce')
print(df['No. of Sells'].mean())
df['No. of Review'] = pd.to_numeric(df['No. of Review'].astype(str).str.extract(r'(\d+)', expand=False), errors='coerce')
print(df['No. of Review'].mean())
df.columns = df.columns.str.strip()
df['Discount(%)'] = pd.to_numeric(df['Discount(%)'].astype(str).str.extract(r'(\d+)', expand=False), errors='coerce')
print(df['Discount(%)'].mean())
```

6.Updating Null values with mean value

```python
updated_df= df
updated_df['No. of Sells'] = updated_df['No. of Sells'].fillna(df['No. of Sells'].mean())
updated_df.info()
updated_df= df
updated_df['No. of Review'] = updated_df['No. of Review'].fillna(df['No. of Review'].mean())
updated_df.info()
updated_df= df
updated_df['Discount(%)'] = updated_df['Discount(%)'].fillna(df['Discount(%)'].mean())
updated_df.info()
```

7. Delete extra unwanted columns

```python
df = df.drop(columns=['Unnamed: 9', 'Unnamed: 10'], errors='ignore')
print("Remaining columns:", df.columns.tolist())
display(df.head())
```
8. Fixing the price column and extracting only numeric values

```python
df['Price'] = df['Price'].astype(str).str.replace('à§³', '', regex=False).str.strip()
df['Price'] = pd.to_numeric(df['Price'], errors='coerce')
print("Cleaned Price Head:")
print(df['Price'].head())
df.info()
```
9. Download the updated csv dataset

```python
from google.colab import files
# Save the dataframe to a csv file
df.to_csv('cleaned_daraz_data.csv', index=False)
# Trigger the download to your local machine
files.download('cleaned_daraz_data.csv')
```

# ***Analyzing dataset using MySQL***

creating and using database
```sql
create database daraz_data;
use daraz_data;
```
-- Create table for Daraz T-Shirt Sales Dataset
```sql
CREATE TABLE daraz_tshirts (
	Product_Name varchar(100),
    Price int,
    Discount int,
    No_of_Sells	int,
    No_of_Review int,
    Location varchar(100),
    Product_Link varchar(100),
    Product_ID	int primary key not null,
    SKU varchar(100)
);
```
-- Q1A. Retrieve all columns from the dataset.
```sql
select * from daraz_tshirts;
```
-- Q1B. Retrieve only Product Name, Price, and Location.
```sql
select 
	Product_Name,
    Price ,
    location 
from daraz_tshirts;
```
-- Q2A. Find all unique seller locations.
```sql
Select distinct location from daraz_tshirts;
```
-- Q2B. Display the 10 most expensive products.
```sql
select Product_Name,price
from daraz_tshirts
order by Price DESC
limit 10;
```
-- Q3A. Find products priced above 400.
```sql
select Product_Name,Price from daraz_tshirts 
where price >400;
```
-- Q3B. Find products with discounts greater than 50%.
```sql
select Product_ID,Discount from daraz_tshirts
where discount > 50 
Order By Discount DESC;
```
-- Q4A. Find product priced above 450 and having more than 100 sales.
```sql
select Product_Name, Price,No_of_Sells from Daraz_tshirts
where Price > 450 and No_of_Sells >100;
```
-- Q4B. Find products having either more than 400 sales or more than 100 reviews.
```sql
select Product_Name,No_of_Sells, No_of_Review from Daraz_tshirts
where No_of_Sells >400 OR No_of_Review > 100;
```
-- Q5A. Find products containing "T Shirt".
```sql
select Product_Name from daraz_tshirts
where Product_Name like "%T Shirt%";
```
-- Q5B. Find products containing "Combo".
```sql
select Product_Name from daraz_tshirts
where Product_Name like "%Combo%";
```
-- Q6A. Find total sales volume.
```sql
select sum(No_of_Sells) as Total_Sales
from daraz_tshirts;
```
-- Q6B. Find average product price.
```sql
Select round(avg(Price),2) as Avg_Price
from daraz_tshirts;
```
-- Q7A. Find the most expensive product price.
```sql
Select Max(Price) As Highest_Price from daraz_tshirts;
```
-- Q7B. Count total products.
```sql
Select Count(Product_Name) as Total_Products
from daraz_tshirts;
```
-- Q8A. Count products available in each location.
```sql
Select Distinct location , Count(Product_Name) as CP
from daraz_tshirts
group by location
Order By CP DESC;
```
-- Q8B. Calculate average price by location.
```sql
Select location, round(Avg(Price),2) from daraz_tshirts
group by location;
```
-- Q9A. Show locations having more than 50 products.
```sql
select location, count(Product_ID) as  Product_Count
from daraz_tshirts 
Group by location 
Having count(Product_ID) > 50;
```
-- Q9B. Show locations where average price exceeds 200.
```sql
select location , round(AVG(Price),2) as Avg_Price
from daraz_tshirts 
group by location 
having round(AVG(Price),2) > 200;
```
-- Q10A. Convert product names to uppercase.
```sql
select upper(Product_Name) as product_name
from daraz_tshirts;
```
-- Q10B. Find product name lengths.
```sql
select Product_Name, Length(Product_Name) as name_length
from daraz_tshirts;
```
-- Q11A. Categorize products by price[CASE Statements]
```sql
select Distinct Product_Name, Price,
Case 
    When Price < 300 Then 'Budget Product'
    When Price Between 300 and 400 Then 'Mid Range Product'
    Else 'Premium Product'
End as Price_Category
From daraz_tshirts;
```
-- Q11B. Categorize products by sales performance [CASE Statements]
```sql
Select Product_Name , No_of_Sells,
Case
	when No_of_Sells >= 500 then 'Best seller'
    when No_of_Sells >= 400 then 'Good Seller'
    Else 'Low Seller'
End as sales_category
from daraz_tshirts
Order By No_of_Sells Desc;
```
-- Q12A. Find products priced above the average price[Subqueries]
```sql
select Product_Name, Price from daraz_tshirts
where Price > 
( Select AVG(Price) from daraz_tshirts);
```
-- Q12B. Find products with sales greater than average sales[Subqueries]
```sql
select Product_Name , No_of_Sells from daraz_tshirts
where No_of_Sells>
(select avg(No_of_Sells) from daraz_tshirts);
```
-- Q13A. Find products whose price is higher than the average price of their location [Correlated Subqueries]
```sql
select Product_Name , Price,location from daraz_tshirts AS d1
where Price >
(select avg(Price) from daraz_tshirts as d2
where d1.location = d2.location);
```
-- Q13B. Find products whose reviews exceed the location average [Correlated Subqueries]
```sql
select Product_Name , No_of_Review,location from daraz_tshirts AS d1
where No_of_Review >
(select avg(No_of_Review) from daraz_tshirts as d2
where d1.location = d2.location);
```
-- Q14A. Rank products by sales [Window Functions (Ranking)]
```sql
select Product_Name, No_of_Sells, 
RANK() Over(Order by No_of_Sells Desc) as sales_rank
from daraz_tshirts;
```
-- Q14B. Find top 3 products in each location [Window Functions (Ranking)]
```sql
select * from
(Select Product_Name, No_of_Sells, Location,
	row_number() over(Partition by location Order by No_of_Sells DESC)as rn
from daraz_tshirts) as x
where rn<=3;
```
-- Q15A. Calculate running total of sales [Window Functions (Analytics)]
```sql
SELECT Distinct Product_Name, Product_ID, No_of_Sells, SUM(No_of_Sells)
       OVER( ORDER BY No_of_Sells desc) AS running_sales
FROM daraz_tshirts;
```
-- Q15B. Compare each product's sales with previous product  [Window Functions (Analytics)]
```sql
SELECT Product_Name,Product_ID, No_of_Sells,
       LAG(No_of_Sells) OVER(ORDER BY Product_ID) AS previous_sales
FROM daraz_tshirts;
```
-- Q16A. Find products with sales above overall average using CTE.
```sql
WITH avg_sales AS
(
    SELECT AVG(No_of_Sells) AS avg_sell
    FROM daraz_tshirts
)
SELECT d.* FROM daraz_tshirts d
JOIN avg_sales a
ON d.No_of_Sells > a.avg_sell;
```
-- Q16B. Find the best-selling product from each location.
```sql
with ranked_product as(
select Product_Name,Location, No_of_Sells,
ROW_Number() Over(Partition by location order by No_of_Sells DESC) as RN
From daraz_tshirts)
select * From ranked_product 
where RN= 1;
```
