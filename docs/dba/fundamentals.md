# Fundamentals

## Database Indexes

Indexes are database objects that enhance the speed of data retrieval operations. They function by creating a quick lookup mechanism for data based on one or more columns in a table, much like an index in a book helps you find information quickly. Namely, indexes reduce the amount of disk I/O needed to access data, thereby boosting overall database performance.

| Index type              | Description                                             | Use case                                                              |
| ----------------------- | ------------------------------------------------------- | --------------------------------------------------------------------- |
| **Clustered index**     | Determines the physical order of the data in the table. | Primary key columns where sorted data access is essential.            |
| **Non-clustered index** | Creates a separate structure with pointers to the data. | Frequently queried columns like email or date_of_birth.               |
| **Unique index**        | Ensures that all values in the index are unique.        | Ensuring uniqueness in fields like email or username.                 |
| **Composite index**     | Indexes multiple columns in combination.                | Queries filtering on multiple columns, like first_name and last_name. |
| **Full-text index**     | Facilitates fast text searches in large text fields.    | Searching through large text fields like description or comments.     |

## Database Partitioning

### What is database partitioning and when would you use it?

- db partitioning is dividing a large table into smaller, more manageable pieces called **partitions**.
- Each one stored separately and can be queried individually, thus, significantly improve performance and manageability, especially for very large datasets
- If data large -> use partitioning (Start using it when the query performance start to drop)
- For instance, in a table storing historical transaction data, I might partition the data by month or year. This allows queries that target specific time periods to access only the relevant partition instead of scanning the entire table, thus improving performance.
- Additionally, partitioning can make maintenance tasks, like archiving or purging old data, more efficient since these operations can be performed on individual partitions rather than the whole table.

| Partitioning type          | Description                                                          | Example use case                                                      |
| -------------------------- | -------------------------------------------------------------------- | --------------------------------------------------------------------- |
| **Range partitioning**     | Divides data into partitions based on a range of values in a column. | Partition a sales table by order_date (e.g., one partition per year). |
| **List partitioning**      | Partitions data based on a specific list of values.                  | Partition a customers table by country or region.                     |
| **Hash partitioning**      | Distributes data across partitions using a hash function.            | Distribute rows evenly for load balancing across multiple partitions. |
| **Composite partitioning** | Combines two or more partitioning strategies (e.g., range + list).   | Partition by order_date (range) and then by region (list).            |

## Database Replication

Database replication is basically clone your database. Copy and maintain database objects across multiple servers to make sure data are redundancy (fat data) and high availability (db status green 24/7). It can be synchronous or asynchronous.

- **Synchronous:** replication ensures that changes are reflected in real time across servers.
- **Asynchronous:** replication updates replicas with a slight delay.
- **More:** Replication is particularly useful in scenarios where uptime is critical, such as for e-commerce platforms, where users expect the database to always be available, even during maintenance or hardware failures.

## Types of Database Replication

| Feature               | **Master-slave replication**                                              | **Master-master replication**                              |
| --------------------- | ------------------------------------------------------------------------- | ---------------------------------------------------------- |
| **Write operations**  | Writes occur only on the master node.                                     | Writes can occur on both masters.                          |
| **Read operations**   | Reads can be offloaded to slave nodes.                                    | Reads can occur on any master node.                        |
| **Use case**          | Used when reads outnumber writes, and eventual consistency is acceptable. | Used in distributed systems with multiple write locations. |
| **Conflict handling** | No conflicts (since only one node writes).                                | Requires conflict resolution mechanisms.                   |
| **Example**           | MySQL Master-Slave Replication                                            | MongoDB or Cassandra Master-Master                         |

## Database Views

- Database view is a **virtual table**
- It based on a query's result. NO store data, only displays data retrieved from one or more underlying tables
- **More:** Views simplify complex queries by allowing users to select from a single view rather than writing a complicated SQL query. Views also enhance security by restricting user access to specific data fields without giving them access to the underlying tables. For example, a view might only expose certain columns of sensitive data, such as a customer's name and email, but not their financial information.

## Database Scalability

1. **Vertical scaling**
   This involves adding more resources, such as CPU, memory, or storage, to the existing database server. While it's the simplest approach, it has its limits since hardware can only be upgraded to a certain extent. I would use vertical scaling as a short-term solution or in scenarios where the database isn't extremely large or doesn't require frequent scaling.
2. **Horizontal scaling (sharding)**
   It basically distributing the database across servers or nodes. Great for larger databases or when dealing with massive dataset
3. **Replication**
   Replication involves copying data to multiple database servers to distribute the read workload. I would set up master-slave or master-master replication to allow multiple servers to handle read requests, improving read scalability. This method also adds redundancy, which enhances data availability and fault tolerance.
4. **Database indexing and query optimization**
   By analyzing and optimizing slow queries, adding appropriate indexes, and avoiding expensive operations like full table scans, I can reduce the load on the database, which indirectly contributes to scalability.
5. **Caching**
   Implementing a caching layer, like **Redis** or **Memcached**, helps offload frequently accessed data from the database. By storing and retrieving common queries from the cache, I can reduce the load on the database, resulting in faster response times and improved scalability.
6. **Partitioning**
   Database partitioning involves splitting a large table into smaller, more manageable pieces, improving query performance and making data management more efficient. For example, I might partition a large transactions table by date, so queries that target specific time ranges only scan the relevant partitions, reducing I/O and speeding up response times.

## OLTP vs. OLAP vs. LTAP

| Feature              | OLTP                          | OLAP                               | LTAP                                              |
| :------------------- | :---------------------------- | :--------------------------------- | :------------------------------------------------ |
| **Focus**            | Transactional processing      | Analytical processing              | Unified transactional & analytical processing     |
| **Query type**       | Simple, frequent transactions | Complex, long-running queries      | Both (separated compute on shared storage)        |
| **Data size**        | Small transactions            | Large data sets, often historical  | Single shared copy for active and historical data |
| **Schema design**    | Highly normalized             | Often denormalized                 | Open table formats (e.g., Delta, Iceberg)         |
| **Typical use case** | E-commerce, banking systems   | Data warehouses, reporting systems | AI agents, real-time operational analytics        |
| **Examples**         | MySQL, PostgreSQL             | Redshift, Snowflake                | Databricks (Lakebase + Lakehouse)                 |

- Acronym:
    - OnLine Transaction Processing (OLTP)
    - OnLine Analytics Processing (OLAP)
    - Lake Transactional/Analytical Processing (LTAP) it basically OLTP + OLAP

## Joins: INNER, LEFT, RIGHT

- `INNER JOIN`: returns only the rows with a match between the two tables based on the join condition.
- `LEFT JOIN`: returns all the rows from the left table and the matched rows from the right table; if there is no match, `NULL` values are returned for the columns from the right table.
- `RIGHT JOIN`: is similar to a `LEFT JOIN`, but it returns all the rows from the right table and the matched rows from the left table, filling in `NULL` where there is no match.

## Clustered vs. Non-Clustered Index

| Feature                    | **Clustered index**                                                                     | **Non-clustered index**                                                                                           |
| -------------------------- | --------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **Definition**             | Determines the physical order of the data in the table.                                 | Creates a separate structure with pointers to the physical data.                                                  |
| **Number of indexes**      | Only one clustered index per table (since it defines the physical order).               | Multiple non-clustered indexes can exist on a single table.                                                       |
| **Effect on data storage** | Directly impacts how the data is stored on disk (sorted).                               | Does not affect the physical storage of data.                                                                     |
| **Use case**               | Typically applied to the primary key or a column frequently queried for sorted results. | Used for columns frequently queried but not necessarily in sorted order (e.g., search operations on email, date). |
| **Data access**            | Faster when querying by the indexed column since the data is physically ordered.        | Requires additional lookups (via pointers) to retrieve the actual data.                                           |
| **Storage structure**      | Stores both the data and the index together in the same structure.                      | Stores only the index separately, with pointers to the actual data rows.                                          |
| **Example**                | Clustered index on `CustomerID` to sort data by customer.                               | Non-clustered index on `Email` or `OrderDate` to speed up specific searches.                                      |

## Migrating On-Premises Databases to the Cloud

1. **Assessment and planning**
   Start by assessing the existing database environment to understand the schema, data size, and application dependencies. Next, I'd select the appropriate cloud service and instance type based on the workload requirements – it's important to plan for network configuration, security, and compliance considerations.
2. **Data migration strategy**
   Choose an appropriate data migration strategy such as offline migration using tools like AWS Database Migration Service (DMS) or Azure Database Migration Service for minimal downtime. For large databases, using a phased approach or data pipeline solutions like AWS Snowball for initial bulk data transfer can be effective.
3. **Testing**
   Conduct thorough testing in a staging environment that mirrors the production setup. Test the data migration process, connectivity, performance, and failover scenarios to identify any issues before the actual migration.
4. **Minimal downtime cutover**
   Plan the final cutover during a low-usage period. Use database replication to keep the cloud database in sync with the on-premises database until the final cutover to ensure minimal downtime and data loss.
5. **Post-migration validation**
   After migration, validate data integrity, run performance tests, and monitor the cloud database to ensure everything operates as expected.

## Security in Cloud-Based Databases

1. **Data encryption**
   Enable encryption both at rest and in transit. For at-rest encryption, I use the cloud provider's encryption services like AWS KMS or Azure Key Vault to manage encryption keys. For data in transit, I use SSL/TLS to encrypt connections between the application and the database.
2. **Access control**
   Implement the principle of least privilege by granting only the necessary permissions to users and applications. Use [Identity and Access Management](https://www.datacamp.com/tutorial/aws-identity-and-access-management-iam-guide) (IAM) roles and policies to control access to the database and its resources. Additionally, enable multi-factor authentication (MFA) for administrative access.
3. **Network security**
   Utilize Virtual Private Cloud (VPC) or Virtual Network (VNet) configurations to isolate databases within a secure network. Use security groups, firewalls, and network ACLs to restrict access to the database to trusted IP addresses or subnets.
4. **Monitoring and auditing**
   Enable database logging and monitoring features to track access and query execution. Use services like AWS CloudTrail, [Azure Monitor](https://www.datacamp.com/tutorial/getting-started-with-azure-monitor), or Google Cloud Audit Logs to maintain an audit trail of database activities.
5. **Compliance and regular security audits**
   Ensure the database complies with relevant regulations, like [GDPR](https://www.datacamp.com/courses/understanding-gdpr) or HIPAA or for Thailand, [PDPA](https://www.dct.or.th/upload/downloads/1612025563SummaryPDPA_DigitalCouncilofThailand.pdf); by configuring data protection settings and performing regular security audits and vulnerability assessments.

## On-Premises vs. Cloud-Based Database Management

- **On-premises:** It's basically that you own your server (hardware infrastructure). Meaning that you have to do backups, patching, and monitoring all by yourself at your own expense, time, manpower, and effort.
- **Cloud-based:** You pay for the cloud service, and the big company takes care of you. They offer scalability, built-in high availability, and automated backups. Cloud databases also provide options for scaling resources up or down as needed, without the need to invest in physical hardware. For example, in AWS RDS, you can easily scale compute power and storage with just a few clicks, and the system manages the hardware side of things for you.

## References

- [Databricks Launches LTAP: The First Lake Transactional/Analytical Processing Architecture](https://www.databricks.com/company/newsroom/press-releases/databricks-launches-ltap-first-lake-transactionalanalytical) (2026)
- [Top 30 Database Administrator Interview Questions for 2026](https://www.datacamp.com/blog/database-administrator-interview-questions)
- [DBA to Data Engineer](https://www.sqlservercentral.com/editorials/dba-to-data-engineer)
