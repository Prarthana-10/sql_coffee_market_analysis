--Q6.Top Selling Products by City
--What are the top 3 selling products in each city based on sales volume?

SELECT *
FROM
(SELECT 
ct.city_name,
p.product_name,
count(s.sale_id) as toto,
DENSE_RANK () OVER(PARTITION BY ct.city_name ORDER BY COUNT(s.sale_id) DESC) as rank
FROM sales as s
JOIN products as p
ON s.product_id = p.product_id
JOIN customers as c
ON c.customer_id = s.customer_id
JOIN city as ct
ON ct.city_id = c.city_id
GROUP BY 1,2
ORDER BY 1,3 DESC) 
as t1
WHERE rank<=3





--Q7.Customer Segmentation by City
--How many unique customers are there in each city who have purchased coffee products?


SELECT
ct.city_name,
COUNT(DISTINCT c.customer_id) as unc
FROM city as ct
LEFT JOIN
customers as c
ON c.city_id = ct.city_id
JOIN sales as s
ON s.customer_id = c.customer_id
WHERE s.product_id IN (1,2,3,4,5,6,7,8,9,10,11,12,13,14)
GROUP BY 1

--Q8.Average Sale vs Rent
--Find each city and their average sale per customer and avg rent per customer

WITH city_table
AS
(
SELECT ct.city_name,
SUM(s.total) as total_rev,
COUNT(DISTINCT s.customer_id) as totcx,
ROUND(
SUM(s.total)::numeric/
COUNT(DISTINCT s.customer_id)::numeric,2) as avgsale

FROM sales as s
JOIN customers as c
ON s.customer_id = c.customer_id
JOIN city as ct
ON ct.city_id = c.city_id
GROUP BY 1
	ORDER BY 2 DESC
),
city_rent 
AS
(SELECT 
city_name,
estimated_rent
FROM city
)
SELECT 
cr.city_name,
cr.estimated_rent,
ct1.totcx,
ct1.avgsale,
ROUND(
cr.estimated_rent::numeric/ct1.totcx::numeric,2) as avgrentp
FROM city_rent as cr
JOIN city_table as ct1
ON cr.city_name = ct1.city_name
ORDER BY 4 DESC


--Q9.Monthly Sales Growth
--Sales growth rate: Calculate the percentage growth (or decline) in sales over different time periods (monthly) each city.

WITH 
monthly_sales
AS
(
SELECT
ct.city_name,
	EXTRACT(MONTH FROM sale_date) as month,
	EXTRACT(YEAR FROM sale_date) as year,
	SUM(s.total) as total_sale
	FROM sales as s
	JOIN customers as c
	ON c.customer_id = s.customer_id
	JOIN city as ct
	ON ct.city_id = c.city_id
	GROUP BY 1,2,3
	ORDER BY 1,3,2
	),
	growth_ratio
	AS
	(
	SELECT
	city_name,
	month,
	year,
	total_sale as cr_month_sale,
	LAG(total_sale,1) OVER(PARTITION BY city_name ORDER BY year,month) as last_month_sale
	FROM monthly_sales
    )

SELECT 
city_name,
month,
year,
cr_month_sale,
last_month_sale,
ROUND((cr_month_sale - last_month_sale)::numeric/last_month_sale::numeric * 100,2) as growth_ratio
FROM  growth_ratio
WHERE
last_month_sale IS NOT NULL


--Q10.Market Potential Analysis
--Identify top 3 city based on highest sales, return city name, total sale, total rent, total customers, estimated coffee consumer

WITH city_table
AS
(
SELECT ct.city_name,
SUM(s.total) as total_rev,
COUNT(DISTINCT s.customer_id) as totcx,
ROUND(
SUM(s.total)::numeric/
COUNT(DISTINCT s.customer_id)::numeric,2) as avgsale

FROM sales as s
JOIN customers as c
ON s.customer_id = c.customer_id
JOIN city as ct
ON ct.city_id = c.city_id
GROUP BY 1
	ORDER BY 2 DESC
),
city_rent 
AS
(SELECT 
city_name,
estimated_rent,
 ROUND((population * 0.25)/1000000,3) as esti_cof_con
FROM city
)
SELECT 
cr.city_name,
total_rev,
cr.estimated_rent as total_rent,
esti_cof_con,
ct1.totcx,
ct1.avgsale,
ROUND(
cr.estimated_rent::numeric/ct1.totcx::numeric,2) as avgrentp
FROM city_rent as cr
JOIN city_table as ct1
ON cr.city_name = ct1.city_name
ORDER BY 2 DESC
