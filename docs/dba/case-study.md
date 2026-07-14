# Case Study

## Production database performance drop

Production database experienced severe performance degradation, impacting our customer-facing application…

**Fix:**

1. **Notify** the stakeholders (anyone who is involved in the project or relevant people) and **set up a bridge call** to keep communication open
2. **Access** the databases and use tools like **SQL Server Profiler** to identify long-running queries and resource-intensive processes
3. After being **identified**, it turns out that the query was causing a deadlock due to a missing index. → **Fix** by adding the appropriate index
4. **Review** the query execution plan and restructure the SQL queries to optimize performance further. Schedule a maintenance window to thoroughly analyze and optimize the database without impacting users
5. **Document** the issue, solution steps, and the lessons learned to improve our incident response process for future scenarios. (E.g., the importance of having a systematic approach to troubleshooting and the need for proactive performance monitoring)

## Prioritizing multiple database projects

- Clearly understand the priorities and **deadlines** for each project
- **Collaborate** with stakeholders to identify critical tasks and use PM tools like Linear, Jira
- **Prioritize** based on their impact on business, potential risks, and dependencies. For example, task involving security patches would take precedence over routine maintenance. I also allocate dedicated time slots for each project to ensure steady progress without context switching.
- **Regular communication:** keep stakeholders informed of the progress and any potential delays. I also prepare for unforeseen issues by building buffer time into my schedule. If a high-priority issue arises, such as a database outage, I can quickly pivot to address it while keeping other projects on track.

## Staying updated with the latest database tech

- SQLServerCentral
- DatabaseJournal
- StackOverflow
- [SQL Server for Database Administrators](https://www.datacamp.com/tracks/sql-server-for-database-administrators)
- Learn from experts and exchange knowledge with peers
- Most importantly, learn from doing, build a real project, trial and error.

## Slow-running query

How to improve?

1. Analyze the query execution plan to spot any bottlenecks causing delays
2. Look for things like **full table scans**, **missing indexes**, **inefficient joins**

- If full table scans then add appropriate indexes to the columns used in the `WHERE` clause or `JOIN` operations can significantly improve performance.
- For example, if the query use a lot of filters on a column, then use index for that column to reduce the data retrieval time.
- Also consider rewriting the query to simplify or break it down using subqueries or CTE (Common Table Expression) aka temporary table.

## Database deadlocks

- Identify the root cause by reviewing the database logs and deadlock graphs (It will provide detailed information about the involved transactions and the resources they are fighting for.)

Once identified, here are some strategies you can try to resolve and prevent deadlocks

1. Ensure that all transaction access resources are in a consistent order, which reduces the chance of circular wait conditions. Additionally, keeping transactions short and reducing the amount of time locks are held can minimize the likelihood of deadlocks
2. Use appropriate isolation level for transactions; for example, using `READ COMMITTED` instead of `SERIALIZABLE` when full isolation isn't necessary can reduce the lock contention.
3. If deadlocks are frequent → Implementing a deadlock retry mechanism in the application logic. (This would catch the deadlock exception and automatically retry the transaction after a short delay).

## References

- [Top 30 Database Administrator Interview Questions for 2026](https://www.datacamp.com/blog/database-administrator-interview-questions)
- [DBA to Data Engineer](https://www.sqlservercentral.com/editorials/dba-to-data-engineer)
