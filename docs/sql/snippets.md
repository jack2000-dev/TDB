# SQL Snippets

## COUNT(*)

The 5 situations where you use `COUNT(*)`.

### 1. Row count -- the baseline health check

```sql
-- Production use: daily data pipeline check
-- "Did today's load actually land any rows?"

SELECT COUNT(*) AS row_count
FROM raw.shopify_orders
WHERE DATE(created_at) = CURRENT_DATE;

-- If this returns 0 at 9am, your ingestion pipeline is broken.
```

### 2. Count by group -- the most common analytics pattern

```sql
-- How many orders did each customer place?
-- Real use: customer segmentation, loyalty tiers

SELECT
    customer_id,
    COUNT(*) AS order_count
FROM orders
GROUP BY customer_id
ORDER BY order_count DESC;

-- Result:
-- customer_id | order_count
-- 42          | 14
-- 99          | 9
-- 17          | 2
```

### 3. Filtering groups with HAVING

`WHERE` filters rows before grouping. `HAVING` filters after grouping -- it works on the result of `COUNT(*)`.

```sql
-- Which customers placed MORE than 5 orders this month?
-- Real use: VIP identification, churn risk flagging

SELECT
    customer_id,
    COUNT(*) AS order_count
FROM orders
WHERE DATE_TRUNC('month', created_at) = DATE_TRUNC('month', CURRENT_DATE)
GROUP BY customer_id
HAVING COUNT(*) > 5
ORDER BY order_count DESC;
```

### 4. Data quality check -- finding duplicates

```sql
-- Are there duplicate order_ids? There should never be.
-- Real use: dbt test alternative, pipeline audit

SELECT
    order_id,
    COUNT(*) AS occurrences
FROM orders
GROUP BY order_id
HAVING COUNT(*) > 1;

-- If this returns any rows → you have duplicates → data integrity problem.
```

### 5. EXISTS check via COUNT

```sql
-- Did this customer place any order in the last 30 days?
-- Real use: re-engagement email logic, support tooling

SELECT
    COUNT(*) AS recent_orders
FROM orders
WHERE customer_id = 42
  AND created_at >= CURRENT_DATE - INTERVAL '30 days';

-- If 0 → send re-engagement email.
-- If > 0 → customer is active, don't spam them.
```

### Production dbt model using COUNT(*)

A realistic dbt mart model -- the kind that powers an executive dashboard.

- Grain: one row per day per country
- Powers: ops dashboard, SLA monitoring, finance reporting

```sql
-- models/mart/fct_daily_order_summary.sql

SELECT
    DATE_TRUNC('day', o.created_at)      AS order_date,
    c.country                             AS country,

    COUNT(*)                              AS total_orders,
    COUNT(DISTINCT o.customer_id)         AS unique_customers,
    COUNT(o.promo_code)                   AS orders_with_promo,  -- skips NULLs
    COUNT(*) - COUNT(o.promo_code)        AS orders_without_promo,

    SUM(o.amount)                         AS gross_revenue,
    ROUND(SUM(o.amount) / COUNT(*), 2)    AS avg_order_value

FROM {{ ref('stg_orders') }}       AS o
JOIN {{ ref('stg_customers') }}    AS c
    ON o.customer_id = c.customer_id

WHERE o.status != 'cancelled'

GROUP BY 1, 2
ORDER BY 1 DESC, 3 DESC
```

This single model answers: how many orders per day per country, how many used a promo, and what was the average value? `COUNT(*)` is the backbone of every metric.

## Cohort retention

```sql
WITH signups AS (
  SELECT user_id, DATE_TRUNC('month', created_at) AS cohort
  FROM users
),
activity AS (
  SELECT user_id, DATE_TRUNC('month', event_at) AS active_month
  FROM events
)
SELECT
  s.cohort,
  (EXTRACT(YEAR FROM AGE(a.active_month, s.cohort)) * 12
   + EXTRACT(MONTH FROM AGE(a.active_month, s.cohort)))::int AS month_index,
  COUNT(DISTINCT s.user_id) AS users
FROM signups s
JOIN activity a USING (user_id)
GROUP BY 1, 2
ORDER BY 1, 2;
```

## Funnel

```sql
WITH events_by_user AS (
  SELECT user_id,
         MAX(CASE WHEN event = 'visit'    THEN 1 ELSE 0 END) AS v,
         MAX(CASE WHEN event = 'signup'   THEN 1 ELSE 0 END) AS s,
         MAX(CASE WHEN event = 'purchase' THEN 1 ELSE 0 END) AS p
  FROM events GROUP BY user_id
)
SELECT
  SUM(v) AS visited,
  SUM(s) AS signed_up,
  SUM(p) AS purchased,
  ROUND(100.0 * SUM(s) / NULLIF(SUM(v),0), 2) AS visit_to_signup,
  ROUND(100.0 * SUM(p) / NULLIF(SUM(s),0), 2) AS signup_to_purchase
FROM events_by_user;
```

## Top N per group

```sql
SELECT *
FROM (
  SELECT
    *,
    ROW_NUMBER() OVER (PARTITION BY country ORDER BY revenue DESC) AS rn
  FROM customers
) t
WHERE rn <= 5;
```

## Sessionize events (gap-based)

```sql
WITH numbered AS (
  SELECT
    user_id, event_at,
    LAG(event_at) OVER (PARTITION BY user_id ORDER BY event_at) AS prev
  FROM events
),
flagged AS (
  SELECT *,
    CASE WHEN prev IS NULL OR event_at - prev > INTERVAL '30 minutes'
         THEN 1 ELSE 0 END AS new_session
  FROM numbered
)
SELECT *,
  SUM(new_session) OVER (PARTITION BY user_id ORDER BY event_at) AS session_id
FROM flagged;
```

## Date spine (fill missing dates)

```sql
WITH days AS (
  SELECT generate_series(DATE '2026-01-01', DATE '2026-12-31', INTERVAL '1 day')::date AS d
)
SELECT d, COALESCE(SUM(amount), 0) AS revenue
FROM days
LEFT JOIN orders ON orders.created_at::date = days.d
GROUP BY d ORDER BY d;
```

## Median per group

```sql
SELECT country,
       PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) AS median_salary
FROM employees
GROUP BY country;
```

## MoM growth

```sql
WITH monthly AS (
  SELECT DATE_TRUNC('month', created_at) AS m, SUM(total) AS rev
  FROM orders GROUP BY 1
)
SELECT m, rev,
       LAG(rev) OVER (ORDER BY m) AS prev,
       ROUND(100.0 * (rev - LAG(rev) OVER (ORDER BY m))
                   / NULLIF(LAG(rev) OVER (ORDER BY m), 0), 2) AS mom_pct
FROM monthly ORDER BY m;
```

## Detect duplicates

```sql
SELECT email, COUNT(*) AS dupes
FROM users GROUP BY email HAVING COUNT(*) > 1;
```

## Pivot (CASE method)

```sql
SELECT
  user_id,
  SUM(CASE WHEN month='2026-01' THEN amount ELSE 0 END) AS jan,
  SUM(CASE WHEN month='2026-02' THEN amount ELSE 0 END) AS feb,
  SUM(CASE WHEN month='2026-03' THEN amount ELSE 0 END) AS mar
FROM monthly_revenue GROUP BY user_id;
```

## References

- [Modern SQL — examples](https://modern-sql.com/)
- [PostgreSQL — generate_series](https://www.postgresql.org/docs/current/functions-srf.html)
- [Mode — SQL analytics tutorial](https://mode.com/sql-tutorial/sql-business-analytics-training/)
