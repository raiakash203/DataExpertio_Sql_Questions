# DataExpertio_Sql_Questions

# Questions

## 1. Transform workers_info Table to Exclude NULL Values

SELECT
    id,
    'name' AS column_name,
    name AS value
FROM
    playground.workers_info
WHERE
    name IS NOT NULL

UNION ALL

SELECT
    id,
    'date_of_birth' AS column_name,
    cast(date_of_birth as varchar) AS value
FROM
    playground.workers_info
WHERE
    date_of_birth IS NOT NULL

UNION ALL

SELECT
    id,
    'salary' AS column_name,
    cast(salary as varchar)AS value
FROM
    playground.workers_info
WHERE
    salary IS NOT NULL

ORDER BY
    id ASC,
    column_name ASC


## 2. Calculate Monthly Average Product Rating

SELECT month(review_date) as month,product_id,avg(review_score) as avg_review FROM playground.product_reviews 
group by month(review_date),product_id order by month(review_date),product_id

## 3. Most Popular Fuel System for Each Fuel Type

select fuel_type,fuel_system, total_cars from (
select *, row_number() over (partition by fuel_type order by total_cars desc) as rw from (

SELECT fuel_type,fuel_system,count(*) as total_cars FROM playground.automobile
group by 1,2)
) where rw=1 order by fuel_type asc
