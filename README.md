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
