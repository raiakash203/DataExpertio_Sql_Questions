# DataExpertio_Sql_Questions

#Questions

##1. Transform workers_info Table to Exclude NULL Values

Solution:
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

-- Repeat the above for all columns you need to unpivot

ORDER BY
    id ASC,
    column_name ASC
