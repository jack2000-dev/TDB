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

## Dunder explained

To understand why Data Engineers use so many dunders (double underscores), we have to look at how Python works under the hood.

### The Analogy: The Universal Adapter

Imagine Python is a house, and it comes with standard electrical outlets. Python's built-in features—like the `+` sign, `for` loops, and `with` statements—are appliances that plug perfectly into those outlets.

A standard number or string plugs right in. But in Data Engineering, we are building weird, massive industrial machinery—like a **"Salesforce API Connector"** or a **"10-Gigabyte CSV Streamer."** Python's house doesn't have a native outlet for a "Salesforce Connector." **Dunder methods are the universal adapters.** By adding a dunder method to your custom machinery, you are telling Python: *"Hey, when I use your standard outlets, here is how my custom machine should react."*

---

### 1. The WHAT: What actually is a dunder?

Dunders are Python's **secret handshakes**. They are special methods that you don't usually call directly. Instead, Python calls them automatically behind the scenes when you use normal, everyday syntax.

* You write: `a + b`
* Python secretly runs: `a.__add__(b)`
* You write: `for row in dataset:`
* Python secretly runs: `dataset.__iter__()` and `dataset.__next__()`

You know you are looking at a dunder because it starts and ends with two underscores, like `__init__` or `__exit__`.

---

### 2. The WHY: Why do Data Engineers specifically love them?

Data Engineering is entirely about **automation, safety, and scale**. Data engineers write code that runs automatically at 3:00 AM while they are asleep. They use dunders for three main reasons:

#### Reason A: Preventing Disasters (The "Safety Net")

If a data pipeline crashes while moving millions of bank transactions, it can leave database connections hanging open, corrupting data.

* **The Solution:** Data engineers use the `with` statement. By using the `__enter__` and `__exit__` dunders, they write code that says: *"No matter what happens—even if the cloud crashes or the internet cuts out—run the `__exit__` method to safely lock the database and save our progress."*

#### Reason B: Not Exploding the Computer's RAM

If you try to load a 50GB database table into a standard Python list, your server will run out of memory and crash.

* **The Solution:** By using the `__iter__` and `__next__` dunders, engineers can turn a massive database into a "stream." Python will only look at one row at a time, process it, throw it away, and grab the next, keeping RAM usage near zero.

#### Reason C: Making Code Readable

Data pipelines can get incredibly messy. Dunders allow engineers to hide complex logic behind simple symbols. Instead of writing a messy line like `pipeline.set_downstream_task(clean_data)`, frameworks like Airflow use dunders so engineers can just write `pipeline >> clean_data`.

---

### 3. The HOW: A Practical Comparison

Look at how a Data Engineer would build a database tool **without** dunders versus **with** dunders.

#### Without Dunders (Clunky and Risky)

```python
# You have to remember to manually open and close everything
loader = PostgresLoader(db_credentials)
loader.connect_to_database()

# If the code crashes right here, the database stays open and breaks!
loader.upload_data("sales_table.csv") 

loader.close_database_connection()

```

#### With Dunders (Clean and Safe)

By defining `__enter__` and `__exit__` inside the `PostgresLoader` class, the engineer can now write this:

```python
# Python automatically calls __enter__ at the start 
# and GUARANTEES __exit__ runs at the end, even if it crashes.
with PostgresLoader(db_credentials) as loader:
    loader.upload_data("sales_table.csv")

```

### Summary

When you see a project flooded with dunders, it means the creators are building **infrastructure**. They are teaching Python how to handle complex data tools safely, cleanly, and efficiently using Python's own native shortcuts.

## References

- [IBM Data Management Guide](https://www.ibm.com/think/topics/data-management#7281535)
