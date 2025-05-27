# Sales
-- 1. How many customer in the dataset?
155
-- 2. Number of category? 
3


-- Data cleaning
use zero_analyst;
select  * from sale
where 
	tid   is null or
    sale_date is null or 
    time is null or 
    cust_id is null or
	age is null or
    category is null or;
    
select * from sale where
    quantity is null; 
    or
    price is null or
    cogs is null or
    total_sale is null;
-- Data Exploration 
select count(*) from sale;
-- 1. How many customer in the dataset?
select * from sale;
select count(distinct(cust_id)) from sale;
select count(distinct(cust_id)) from sale;
-- 2. Number of category?   
 select count(distinct(category)) from sale;
 
 -- Data analysis/ Business problem
 -- Q 1. Query to retrive sales made on '2022-11-05'?
 select * from sale
 where sale_date = '2022-11-5';
 -- Q 2. Query to retrieve the data where category is clothing and quantity => 4 and transaction made in
  -- month of nov-2022.
 WITH cte as(
 select * from sale
 where category='clothing' and month(sale_date)=11 and year(sale_date)=2022 and quantity >=4
)
select tid,sale_date, cust_id,gendre, category, quantity from cte;
-- OR
select tid,sale_date, cust_id,gendre, category, quantity from sale
 where category='clothing' and month(sale_date)=11 and year(sale_date)=2022 and quantity >=4;
/* Q 3. Write a query to calculate the total sale for each category?*/ 
select 
	category ,
    sum(total_sale) as total_sale,
    count(*) as total_order
from sale
group by 1;
/* Q 4. Write a query to find the average age of customer who have purchased from the 'Beauty' category. */
select round(avg(age),2) as avg_age
from sale where category='beauty';

/* Q5. Write query to find all transaction where total sale is >1000. */
select * from sale where total_sale > 1000;
/*Q 6. Write query to find the total number of transaction(transaction_id) made by each gendre in category*/
 
SELECT 
    category, gendre, COUNT(*) count
FROM
    sale
GROUP BY 1 , 2
ORDER BY 1; 

/* Q 7. Write a query to calculate the sale(total_sale) for each month. find the best selling in each year.*/
with best as(
select  year(sale_date) years,
        month(sale_date) months,
	   round(avg(total_sale),2) avg_sale,
       rank() over(partition by year(sale_date) order by round(avg(total_sale),2) desc  ) RANKS
from sale
group by 1,2
)
select * from best
where ranks = 1;

-- OR
select * from 
(
	select  year(sale_date) years,
        month(sale_date) months,
	   round(avg(total_sale),2) avg_sale,
       rank() over(partition by year(sale_date) order by round(avg(total_sale),2) desc  ) RANKS
	from sale
	group by 1,2
)as t
where ranks = 1;

/* Q 8. Write the query to find the top 5 customer based on the highest total sale. */
 
select cust_id,
	   sum(total_sale) total_sale
from sale
group by 1 
order by 2 desc
limit 5;

/* Q 9. write a query to find the number of unique customer who purchased item from each category.*/

select category ,
	count(distinct(cust_id)) No_of_customer
 from sale
 group by 1;
 
 /* Q 10. Write a query to create shift and number of orders(Morning<=12, Afternoor between 12 and 17
 evening <7). */
--  select hour(time) from sale
select shift,count(tid) as No_of_orders from  -- Derived Table
(
select *,
	CASE
		WHEN hour(time) < 12 THEN 'Morning'
        WHEN  hour(time) between 12  and 17  THEN 'Afternoon'
	else 'Evening'
end as shift
From sale
) t
group by shift;

-- OR
WITH hourly_sale -- CTE
as
(
select *,
	CASE
		WHEN hour(time) < 12 THEN 'Morning'
        WHEN  hour(time) between 12  and 17  THEN 'Afternoon'
	else 'Evening'
end as shift
From sale
) 
select shift,count(tid) as No_of_orders from hourly_sale
group by 1;



 
 




