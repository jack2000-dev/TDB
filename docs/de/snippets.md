# Practical Snippets

## Idempotent daily pipeline

Make the load idempotent by using the file date or order_id as a unique control.

For a daily batch:

1. Load orders_2026_05_28.csv into a staging table.
2. Delete existing records for order_date = '2026-05-28' from the final table.
3. Insert the cleaned staging records into the final table.

This way, if the pipeline runs twice, the final table still has only one correct version of the data.

```sql
BEGIN TRANSACTION;

DELETE FROM fact_orders
WHERE order_date = '2026-05-28';

INSERT INTO fact_orders
SELECT *
FROM staging_orders
WHERE order_date = '2026-05-28';

COMMIT;
```

Wrap both statements in one transaction — otherwise a failure between the `DELETE` and the `INSERT` leaves `fact_orders` missing that day's data, and a retry runs against a partially-replaced table.

Note: Another good method is `MERGE`/upsert using `order_id`.

## dbt incremental model

Watermark logic and pitfalls are covered in [Fundamentals → dbt](fundamentals.md#dbt).

```sql
{{ config(
    materialized='incremental',
    unique_key='transaction_id'
) }}

SELECT
    transaction_id,
    customer_id,
    amount,
    created_at,
    updated_at
FROM source.transactions

{% if is_incremental() %}
WHERE updated_at >= (
    SELECT COALESCE(MAX(updated_at), '1970-01-01') FROM {{ this }}
)
{% endif %}
```

## References

- [dbt](https://www.getdbt.com/) - The modern standard for data transformation
- [What is dbt?](https://www.datacamp.com/tutorial/what-is-dbt?utm_aid=157098107015&utm_loc=9074658-&utm_mtd=-c&utm_kw=&gad_source=1&gad_campaignid=19589720824)
