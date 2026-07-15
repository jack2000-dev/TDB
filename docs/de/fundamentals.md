# Fundamentals

<!-- TODO: diagram -->

## ETL process

<!-- TODO: diagram -->

- **Extract:** Data is pulled from various sources, often in unstructured or semi-structured formats.
- **Transform:** The data undergoes a transformation process, cleaning, formatting, and structuring it for analysis.
- **Load:** Once transformed, the data is loaded into a target system, typically a data warehouse, where it becomes available for querying and reporting.

## ELT process

<!-- TODO: diagram -->

- **Extract:** Data is pulled from various sources, often in unstructured or semi-structured formats.
- **Transform:** The data undergoes a transformation process, cleaning, formatting, and structuring it for analysis.
- **Load:** Once transformed, the data is loaded into a target system, typically a data warehouse, where it becomes available for querying and reporting.

## Data Ingestion

### Data Retrieval

Read more: [Understanding Push vs. Poll event-driven architecture](https://theburningmonk.com/2025/05/understanding-push-vs-poll-in-event-driven-architectures/#Poll_best_for_stream_processing)

|                    | Push-based                                                                                           | Pull-based                                                                                                                                                                                                  |
| ------------------ | ---------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **What**           | Server proactively sending data to clients when updates occur                                        | Clients periodically requesting updates from the server at predefined intervals. In this approach, clients send requests to the server at regular intervals, asking for any new data since the last request |
| **Use cases**      | **Chat application**, new messages are pushed to clients as soon as they are available on the server | **News application**, clients might poll the server every few minutes to check for new articles or updates.                                                                                                 |
| **Key advantages** | Minimize latency and provide a more responsive UX                                                    | Simpler to implement and can be more suitable for applications with lower data update frequencies or limited server resources                                                                               |

## Tools

### Extraction

- Apache Kafka

### Transformation

- [dbt](https://www.getdbt.com/) - The modern standard for data transformation ([Read more](https://www.datacamp.com/tutorial/what-is-dbt?utm_aid=157098107015&utm_loc=9074658-&utm_mtd=-c&utm_kw=&gad_source=1&gad_campaignid=19589720824))

### Load

### Orchestration

- [Apache Airflow](https://airflow.apache.org/) - A platform to programmatically author, schedule, and monitor workflows
- [Apache Hive](https://hive.apache.org/) - Data warehouse software
- [Apache Spark](https://spark.apache.org/) - A multi-language engine for executing data engineering, data science, and machine learning on single-node machines or clusters.
- [dlt]() - Data Load Tools
- [Label Studio](https://labelstud.io/) - Data labeling platform
- [Cosmos](https://github.com/astronomer/astronomer-cosmos) - Bridge dbt and Airflow
- [PgDog](https://pgdog.dev/) - Horizontal scaling for PostgreSQL.
- [Soda Core](https://github.com/sodadata/soda-core) - Data Contract Engine (Data Quality)
- [PySpark Tutorial](https://sparkbyexamples.com/pyspark-tutorial/)
- [PyTest](https://github.com/pytest-dev/pytest) - Testing

### Spark vs. Kafka vs. Flink

| **Feature**          | **Apache Kafka (with Streams)**                             | **Apache Spark (Structured Streaming)**                                             | **Apache Flink**                                                                          |
| -------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| **Primary Role**     | Message broker + Embedded client-side processing            | Cluster-scale data processing & unified analytics                                   | Native, real-time distributed stream processing                                           |
| **Processing Model** | Continuous (Event-at-a-time)                                | Micro-batch (by default)                                                            | Continuous (Event-at-a-time)                                                              |
| **Latency**          | Milliseconds                                                | Seconds to high milliseconds                                                        | Sub-second / Milliseconds                                                                 |
| **Infrastructure**   | Embedded in your app; relies on Kafka brokers               | Requires a compute cluster (YARN, Kubernetes, Databricks)                           | Requires a compute cluster (Kubernetes, YARN, standalone)                                 |
| **Best For**         | Lightweight microservices, event-driven apps, and basic ETL | Large-scale data lake ingestion, machine learning, and mixed batch/stream pipelines | Complex event processing (CEP), ultra-low latency dashboards, and heavy stateful tracking |

## dbt

### Incremental Loading Design

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

- [IBM Data Management Guide](https://www.ibm.com/think/topics/data-management#7281535)
