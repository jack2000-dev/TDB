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
- **Load:** The raw, untransformed data is loaded directly into the target system — typically a data warehouse or lake — before any cleaning or structuring happens.
- **Transform:** Transformation (cleaning, formatting, structuring) happens in place, using the target system's own compute (e.g., a SQL warehouse engine or dbt).

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
- [dlt](https://dlthub.com/) - Data Load Tools
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
| **Infrastructure**   | Embedded in your app; relies on Kafka brokers               | Local mode on a single machine for development; a compute cluster (YARN, Kubernetes, Databricks) for production scale | Requires a compute cluster (Kubernetes, YARN, standalone)                                 |
| **Best For**         | Lightweight microservices, event-driven apps, and basic ETL | Large-scale data lake ingestion, machine learning, and mixed batch/stream pipelines | Complex event processing (CEP), ultra-low latency dashboards, and heavy stateful tracking |

## dbt

### Incremental Loading Design

Use `updated_at` as the incremental watermark.

1. Store the latest `updated_at` value from the previous successful load.
2. On the next run, extract rows where `updated_at >= saved watermark` (inclusive) — a strict `>` silently drops any row sharing the exact watermark timestamp, and re-processing the boundary rows is safe because the `unique_key` merge dedupes them.
3. Load the new or changed rows into a staging table.
4. Merge/upsert into the final table using transaction_id as the unique key.
5. After a successful run, update the stored watermark.

If the target table exists but is empty, `MAX(updated_at)` returns `NULL` and the filter would match nothing — `COALESCE` the watermark to a sentinel so an empty table still gets backfilled.

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

## Python: Dunder Methods

To understand why Data Engineers use so many dunders (double underscores), it helps to see how Python works under the hood.

### The Analogy: The Universal Adapter

Imagine Python is a house, and it comes with standard electrical outlets. Python's built-in features — like the `+` sign, `for` loops, and `with` statements — are appliances that plug perfectly into those outlets.

A standard number or string plugs right in. But in Data Engineering, we are building weird, massive industrial machinery — like a **"Salesforce API Connector"** or a **"10-Gigabyte CSV Streamer."** Python's house doesn't have a native outlet for a "Salesforce Connector." **Dunder methods are the universal adapters.** By adding a dunder method to your custom machinery, you are telling Python: *"Hey, when I use your standard outlets, here is how my custom machine should react."*

### 1. The WHAT: what actually is a dunder?

Dunders are Python's **secret handshakes**. They are special methods that you don't usually call directly. Instead, Python calls them automatically behind the scenes when you use normal, everyday syntax.

* You write: `a + b`
* Python secretly runs: `a.__add__(b)`
* You write: `for row in dataset:`
* Python secretly runs: `it = dataset.__iter__()`, then repeatedly calls `it.__next__()`. The object returned by `__iter__()` is the one `__next__()` is called on — for some types that's `dataset` itself, but not in general.

You know you are looking at a dunder because it starts and ends with two underscores, like `__init__` or `__exit__`.

### 2. The WHY: why do Data Engineers specifically love them?

Data Engineering is entirely about **automation, safety, and scale**. Data engineers write code that runs automatically at 3:00 AM while they are asleep. They use dunders for three main reasons:

#### Reason A: preventing disasters (the "safety net")

If a data pipeline crashes while moving millions of bank transactions, it can leave database connections hanging open.

* **The solution:** Data engineers use the `with` statement. By using the `__enter__` and `__exit__` dunders, they write code that says: *"Run the `__exit__` method when this block ends — including when it ends because of an exception."* What `__exit__` actually does (closing a connection, releasing a lock, flushing a buffer) depends entirely on how the class implements it; the dunder guarantees the call happens, not any specific cleanup behavior.

#### Reason B: keeping memory usage bounded

If you try to load a 50GB database table into a standard Python list, your server can run out of memory and crash.

* **The solution:** By using the `__iter__` and `__next__` dunders, engineers can turn a data source into a "stream," processing one item at a time instead of materializing the whole thing in a list. Whether this actually keeps memory near zero depends on the iterator's own implementation and the underlying source client — the dunders enable incremental processing, they don't guarantee a specific memory ceiling on their own.

#### Reason C: making code readable

Data pipelines can get incredibly messy. Dunders allow engineers to hide complex logic behind simple symbols. Instead of writing a messy line like `pipeline.set_downstream_task(clean_data)`, frameworks like Airflow use dunders so engineers can just write `pipeline >> clean_data`.

### 3. The HOW: a practical comparison

Look at how a Data Engineer would build a database tool **without** dunders versus **with** dunders.

#### Without dunders (clunky and risky)

```python
# You have to remember to manually open and close everything
loader = PostgresLoader(db_credentials)
loader.connect_to_database()

# If the code crashes right here, the database stays open and breaks!
loader.upload_data("sales_table.csv")

loader.close_database_connection()
```

#### With dunders (clean and safe)

By defining `__enter__` and `__exit__` inside the `PostgresLoader` class, the engineer can now write this:

```python
# Python automatically calls __enter__ at the start,
# and runs __exit__ when the `with` block exits — including via an exception.
with PostgresLoader(db_credentials) as loader:
    loader.upload_data("sales_table.csv")
```

When you see a project flooded with dunders, it means the creators are building **infrastructure** — teaching Python how to handle complex data tools safely, cleanly, and efficiently using Python's own native shortcuts.

## References

- [IBM Data Management Guide](https://www.ibm.com/think/topics/data-management#7281535)
