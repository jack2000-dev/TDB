# Case Study

<!-- TODO: diagram -->

## Schema drift

This is schema drift because the source file structure changed by adding a new column: discount.

A data engineer should:

1. Detect the schema change.
2. Decide whether the new column is valid.
3. Update the staging table/schema if the column is expected.
4. Add validation tests.
5. Update downstream models if reporting needs the new discount column.
6. Alert or fail the pipeline if unexpected columns appear.

## Airflow DAG failed at `load_to_bigquery`

1. Airflow task logs and error message.
2. BigQuery credentials/service account permissions.
3. Target dataset/table existence and schema compatibility.
4. Input file/path availability in GCS or local storage.
5. Data type issues, nulls, or bad records.
6. BigQuery quota, network, or API errors.

## Data Quality Checks

1. order_id is not null.
2. order_id is unique.
3. amount is not negative.
4. order_date is not in the future.
5. customer_id exists in the customer dimension.
6. Required columns exist with the correct data types.
7. Row count is within an expected ~~range~~.

## References

- [IBM Data Management Guide](https://www.ibm.com/think/topics/data-management#7281535)
