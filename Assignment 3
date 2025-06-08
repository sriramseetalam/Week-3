-- QUERY 1
WITH ProjectGroups AS (
    SELECT
        *,
        ROW_NUMBER() OVER (ORDER BY Start_Date) -
        ROW_NUMBER() OVER (ORDER BY End_Date) AS grp
    FROM Projects
)
SELECT
    MIN(Start_Date) AS Start_Date,
    MAX(End_Date) AS End_Date
FROM ProjectGroups
GROUP BY grp
ORDER BY
    DATEDIFF(DAY ,MAX(End_Date), MIN(Start_Date)) ASC,
    MIN(Start_Date) ASC;


-- QUERY 2
CREATE PROCEDURE
GETSTUDENTWITHRICHERBESTFRIENDS
AS
BEGIN
     SELECT S.NAME
	 FROM STUDENTS S
	 INNER JOIN FRIENDS F ON S.ID = F.ID
	 INNER JOIN PACKAGES P1 ON S.ID = P1.ID
	 INNER JOIN PACKAGES P2 ON F.FRIEND_ID = P2.ID
	 WHERE P2.SALARY > P1.SALARY
	 ORDER BY P2.SALARY
END

EXEC  GETSTUDENTWITHRICHERBESTFRIENDS 

-- QUERY 3
CREATE PROCEDURE GetSymmetricPairs
AS
BEGIN
    WITH CTE AS (
        SELECT X, Y, ROW_NUMBER() OVER(ORDER BY X) AS rn
        FROM Functions
    )
    SELECT DISTINCT f1.X, f1.Y
    FROM CTE f1
    JOIN CTE f2 ON f1.X = f2.Y AND f1.Y = f2.X
    WHERE f1.X <= f1.Y
      AND f1.rn != f2.rn
    ORDER BY f1.X;
END

EXEC GetSymmetricPairs

--QUERY 4
SELECT
    c.contest_id,
    c.hacker_id,
    c.name,
    SUM(ss.total_submissions) AS total_submissions,
    SUM(ss.total_accepted_submissions) AS total_accepted_submissions,
    SUM(vs.total_views) AS total_views,
    SUM(vs.total_unique_views) AS total_unique_views
FROM
    Contests c
LEFT JOIN Colleges col ON c.contest_id = col.contest_id
LEFT JOIN Challenges ch ON col.college_id = ch.college_id
LEFT JOIN Submission_Stats ss ON ch.challenge_id = ss.challenge_id
LEFT JOIN View_Stats vs ON ch.challenge_id = vs.challenge_id
GROUP BY
    c.contest_id, c.hacker_id, c.name
HAVING
    NOT (
        SUM(ss.total_submissions) IS NULL
        AND SUM(ss.total_accepted_submissions) IS NULL
        AND SUM(vs.total_views) IS NULL
        AND SUM(vs.total_unique_views) IS NULL
    )
    AND (
        COALESCE(SUM(ss.total_submissions), 0) > 0
        OR COALESCE(SUM(ss.total_accepted_submissions), 0) > 0
        OR COALESCE(SUM(vs.total_views), 0) > 0
        OR COALESCE(SUM(vs.total_unique_views), 0) > 0
    )
ORDER BY
    c.contest_id;


-- QUERY 5
SELECT
    s.submission_date,
    COUNT(DISTINCT s.hacker_id) AS unique_hackers,
    max_hacker.hacker_id,
    h.name
FROM
    Submissions s
JOIN (
    SELECT
        submission_date,
        hacker_id
    FROM (
        SELECT
            submission_date,
            hacker_id,
            COUNT(*) AS submission_count,
            RANK() OVER (
                PARTITION BY submission_date
                ORDER BY COUNT(*) DESC, hacker_id ASC
            ) AS rnk
        FROM
            Submissions
        GROUP BY
            submission_date,
            hacker_id
    ) ranked
    WHERE rnk = 1
) max_hacker
    ON s.submission_date = max_hacker.submission_date
JOIN Hackers h
    ON max_hacker.hacker_id = h.hacker_id
GROUP BY
    s.submission_date,
    max_hacker.hacker_id,
    h.name
ORDER BY
    s.submission_date;

--QUERY 6
SELECT
  ROUND(
    ABS(MIN(LAT_N) - MAX(LAT_N)) +
    ABS(MIN(LONG_W) - MAX(LONG_W)),
    4
  ) AS Manhattan_Distance
FROM
  STATION;

--QUERY 7
WITH RECURSIVE numbers(n) AS (
    SELECT 2
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < 1000
),
primes AS (
    SELECT n FROM numbers
    WHERE n = 2 OR NOT EXISTS (
        SELECT 1 FROM numbers AS factors
        WHERE factors.n < n AND factors.n > 1 AND n % factors.n = 0
    )
)
SELECT GROUP_CONCAT(n SEPARATOR '&') AS prime_numbers
FROM primes;


-- QUERY 8
WITH ordered AS (
    SELECT
        Name,
        Occupation,
        ROW_NUMBER() OVER (PARTITION BY Occupation ORDER BY Name) AS rn
    FROM OCCUPATIONS
)
SELECT
    COALESCE(MAX(CASE WHEN Occupation = 'Doctor' THEN Name END), 'NULL') AS Doctor,
    COALESCE(MAX(CASE WHEN Occupation = 'Professor' THEN Name END), 'NULL') AS Professor,
    COALESCE(MAX(CASE WHEN Occupation = 'Singer' THEN Name END), 'NULL') AS Singer,
    COALESCE(MAX(CASE WHEN Occupation = 'Actor' THEN Name END), 'NULL') AS Actor
FROM ordered
GROUP BY rn
ORDER BY rn;


--QUERY 9
SELECT
    N,
    CASE
        WHEN P IS NULL THEN 'Root'
        WHEN N IN (SELECT P FROM DST) THEN 'Inner'
        ELSE 'Leaf'
    END AS NodeType
FROM DST
ORDER BY N;

--QUERY 10
SELECT
    c.company_code,
    c.founder,
    COUNT(DISTINCT l.lead_manager_code) AS lead_manager_count,
    COUNT(DISTINCT s.senior_manager_code) AS senior_manager_count,
    COUNT(DISTINCT m.manager_code) AS manager_count,
    COUNT(DISTINCT e.employee_code) AS employee_count
FROM
    company c
    LEFT JOIN lead_manager l ON c.company_code = l.company_code
    LEFT JOIN senior_manager s ON c.company_code = s.company_code
    LEFT JOIN manager m ON c.company_code = m.company_code
    LEFT JOIN employee e ON c.company_code = e.company_code
GROUP BY
    c.company_code, c.founder
ORDER BY
    c.company_code;

--QUERY 11
CREATE PROCEDURE
GETSTUDENTWITHRICHERBESTFRIENDS
AS
BEGIN
     SELECT S.NAME
	 FROM STUDENTS S
	 INNER JOIN FRIENDS F ON S.ID = F.ID
	 INNER JOIN PACKAGES P1 ON S.ID = P1.ID
	 INNER JOIN PACKAGES P2 ON F.FRIEND_ID = P2.ID
	 WHERE P2.SALARY > P1.SALARY
	 ORDER BY P2.SALARY
END

EXEC  GETSTUDENTWITHRICHERBESTFRIENDS 

--QUERY 12
SELECT
  job_family,
  region,
  ROUND(
    100.0 * cost / SUM(cost) OVER (PARTITION BY job_family),
    2
  ) AS cost_percentage
FROM
  job_family_costs
WHERE
  region IN ('India', 'International');


--QUERY 13
SELECT
  bu_id,
  TO_CHAR(date, 'YYYY-MM') AS month,
  SUM(cost) AS total_cost,
  SUM(revenue) AS total_revenue,
  ROUND(100.0 * SUM(cost) / NULLIF(SUM(revenue), 0), 2) AS cost_revenue_ratio_percent,
  ROUND(
    (100.0 * SUM(cost) / NULLIF(SUM(revenue), 0)) -
    LAG(100.0 * SUM(cost) / NULLIF(SUM(revenue), 0)) OVER (PARTITION BY bu_id ORDER BY date),
    2
  ) AS mom_change_percent
FROM
  bu_financials
GROUP BY
  bu_id, TO_CHAR(date, 'YYYY-MM')
ORDER BY
  bu_id, month;

--QUERY 14
SELECT
  sub_band,
  COUNT(*) AS headcount,
  ROUND(
    100.0 * COUNT(*) / SUM(COUNT(*)) OVER (),
    2
  ) AS percentage_of_headcount
FROM
  employees
GROUP BY
  sub_band;

-- QUERY 15
SELECT *
FROM employees e1
WHERE 5 > (
    SELECT COUNT(*) 
    FROM employees e2 
    WHERE e2.salary > e1.salary
)

--QUERY 16
UPDATE employees
SET 
    salary = salary + bonus,
    bonus = salary - bonus,
    salary = salary - bonus;

UPDATE employees
SET 
    salary = CONCAT(salary, '||', bonus),
    bonus = SUBSTRING(salary, 1, CHARINDEX('||', salary) - 1),
    salary = SUBSTRING(salary, CHARINDEX('||', salary) + 2, LEN(salary));



--QUERY 17
CREATE LOGIN hr_user WITH PASSWORD = 'secure123';

CREATE USER hr_user FOR LOGIN hr_user;

ALTER ROLE db_owner ADD MEMBER hr_user;



--QUERY 18
SELECT
  bu_id,
  month,
  ROUND(SUM(cost * weight) / NULLIF(SUM(weight), 0), 2) AS weighted_avg_cost
FROM
  employee_costs
GROUP BY
  bu_id,
  month
ORDER BY
  bu_id,
  month;

--QUERY 19
SELECT
  CEIL(
    ABS(
      AVG(salary) -
      AVG(CAST(REPLACE(salary, '0', '') AS INTEGER))
    )
  ) AS error_amount
FROM EMPLOYEES;


--QUERY 20
INSERT INTO table2 (col1, col2, col3)
SELECT col1, col2, col3 FROM table1;


