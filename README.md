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

## 4. Calculate Average Lead Time for Each Shipping Option

SELECT ship_mode,count(*) as total_shipping_service,round(avg(day(ship_date-order_date)),2) as avg_lead_time FROM playground.superstore 
group by 1 order by ship_mode desc


## 5. Finding Actors by Favorite Movie Genre
with cte_genre as (
SELECT genre FROM playground.movies
group by 1
order by count(*) desc limit 1
)
select d.actor,c.age from playground.actor_ages c inner join 
(
select a.actor from playground.starring_actors a 
inner join playground.movies b on a.movie_name = b.movie
where b.genre in (select * from cte_genre) ) d on 
c.actor=d.actor
order by c.age desc,d.actor asc


## 6. Analyze Engine Efficiency by Fuel Type

SELECT fuel_type,
round(avg(city_mileage),2) as avg_city_mileage,
round(avg(highway_mileage),2) as avg_highway_mileage,
round(avg(highway_mileage)/avg(city_mileage),2) as efficiency_ratio
 FROM playground.automobile group by 1
 order by round(avg(highway_mileage)/avg(city_mileage),2) desc

## 7. Filtering Dance Contest Scores

WITH extreme_scores AS (
    SELECT 
        arbiter_id,
        COUNT(CASE WHEN first_criterion = (SELECT MIN(first_criterion) FROM playground.dance_scores)
                    OR first_criterion = (SELECT MAX(first_criterion) FROM playground.dance_scores) THEN 1 END) +
        COUNT(CASE WHEN second_criterion = (SELECT MIN(second_criterion) FROM playground.dance_scores)
                    OR second_criterion = (SELECT MAX(second_criterion) FROM playground.dance_scores) THEN 1 END) +
        COUNT(CASE WHEN third_criterion = (SELECT MIN(third_criterion) FROM playground.dance_scores)
                    OR third_criterion = (SELECT MAX(third_criterion) FROM playground.dance_scores) THEN 1 END) AS extreme_count
    FROM playground.dance_scores
    GROUP BY arbiter_id
)
SELECT ds.*
FROM playground.dance_scores ds
JOIN extreme_scores es ON ds.arbiter_id = es.arbiter_id
WHERE es.extreme_count < 2
ORDER BY ds.arbiter_id

## 8.  Identify Products Sold Exclusively in January 2024

select a.product_id,b.product_name from
(SELECT product_id FROM playground.product_sales where sale_date >=to_date('2024-01-01','yyyy-mm-dd') and sale_date<=to_date('2024-01-31','yyyy-mm-dd') )a
join
playground.products b on a.product_id=b.product_id


## 9. Calculating Median Searches per User

SELECT AVG(searches) AS "median"
FROM
(
   SELECT searches,
      ROW_NUMBER() OVER (ORDER BY searches ASC) AS RowAsc,
      ROW_NUMBER() OVER (ORDER BY searches DESC) AS RowDesc
   FROM playground.search_freq
) data
WHERE
   RowAsc IN (RowDesc, RowDesc - 1, RowDesc + 1)

https://subhralina.medium.com/5-ways-to-calculate-median-in-sql-cffba38aa945



https://www.kdnuggets.com/2020/11/5-tricky-sql-queries-solved.html

