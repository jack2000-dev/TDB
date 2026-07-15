# Practical Snippets

## Idempotent daily pipeline

Make the load idempotent by using the file date or order_id as a unique control.

For a daily batch:

1. Load orders_2026_05_28.csv into a staging table.
2. Delete existing records for order_date = '2026-05-28' from the final table.
3. Insert the cleaned staging records into the final table.

This way, if the pipeline runs twice, the final table still has only one correct version of the data.

```sql
DELETE FROM fact_orders
WHERE order_date = '2026-05-28';

INSERT INTO fact_orders
SELECT *
FROM staging_orders
WHERE order_date = '2026-05-28';
```

Note: Another good method is `MERGE`/upsert using `order_id`.

## dbt incremental model

Use `updated_at` as the incremental watermark.

1. Store the latest updated_at value from the previous successful load.
2. On the next run, extract only rows where updated_at is greater than the saved watermark.
3. Load the new or changed rows into a staging table.
4. Merge/upsert into the final table using transaction_id as the unique key.
5. After a successful run, update the stored watermark.

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
WHERE updated_at > (
    SELECT MAX(updated_at)
    FROM {{ this }}
)
{% endif %}
```

## References

- [dbt](https://www.getdbt.com/) - The modern standard for data transformation
- [What is dbt?](https://www.datacamp.com/tutorial/what-is-dbt?utm_aid=157098107015&utm_loc=9074658-&utm_mtd=-c&utm_kw=&gad_source=1&gad_campaignid=19589720824)
