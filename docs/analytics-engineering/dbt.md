# What is dbt

- dbt (data build tool) transform raw data into analytics-ready models
- SQL-based data modeling and transformation
- Version control and CI/CD integration
- Testing and documentation built into workflows

## Why dbt with ELT?
- Moves transformation into the warehouse
- Promotes modular, reusable models
- Enables quality, lineage, and governance

## Ecosystem & Integration

- Supports both dbt Core (opensource) and dbt Cloud (managed platform)
- Works with Snowflake, BigQuery, Redshift, Databricks
- Integrates with tools like Airflow, Fivetran, and Looker

## Overview of dbt Projects

- **dbt projects define how data is modeled and manged**
  - Consist of models, seeds, sources, and configurations
  - Organized for modular development and collaboration

- **Profile in dbt**
  - Profile is a connection to a data warehouse
  - Define target environments (dev, staging, prod)
  - Stored in a `profiles.yml` file for authentication and setup
  - Contain connection details for data warehouse

### Models in dbt
- **Models are the core of dbt**
  - Core SQL files that transform data
  - Stored in `/models` directory
  - Complied into executable SQL for the warehouse

### Seeds and Sources

#### Seeds
- CSV files located in the `/seeds` directory
- Loaded into the warehouse as tables
- Used for static data like mappings, codes, or configurations
- Great for reference or static datasets
- **Common Use Cases**
    - Country code mappings
    - Product category mappings
    - Status code mappings
    - Currency conversion rates
- Note: Join seeds with models for enrichment or consistency
- Manageing and Updating Seeds
    - Use Git (Version control CSVs for tracability)
    - Keep naming clear and descriptive
    - Rebuild when data updates are needed

#### Sources
- Represent upstream tables in the warehouse
- Defined in `.yml` files under the `/sources` directory
- Used to track and manage external tables

## Building and Testing dbt Models

- dbt Sources and References
  - Sources define raw data inputs
  - Refs link models together for dependency tracking
  - Enables modular, maintainable transformation workflows

### Understanding DAG

**DAG or directed acyclic graph**

- **Model Dependencies**: DAG visualizes the model dependencies
- **Build Order**: Ensures correct build order of transformations
- **Debugging and Management**: Simplifies debugging and pipeline management

### Best Practices

- Define clear source and paths
- Use `ref()` for model lineage
- Validate dependencies before deployment

### Folder Structure and Naming

- **Folder Structure**
  - Use separate folders for Bronze (staging), Silver (intermediate), Gold (marts)
  - Maintain logical grouping for clarity
- **Naming Conventions**
  - Follow consistent naming conventions (e.g., stg_, int_, fct_ as for staging, intermediate, and fact)
  - Reflect transformation stage and business logic
- Align naming with team and data standards
- Document structure in `README.md`
- Keep folder depth minimal

## Materialization

- Defines how dbt builds and stores models
- Common types: table, view, incremental
- Balances performance and data freshness

### Table vs. View

|Table|View|
|---|---|
|Stores result physically for faster reads|View queries source data dynamically|
|Improve performance but increase storage cost|Save space and reflect real-time changes|
|Tables suit stable, large datasets|Views fit frequently updated or lightweight data|

*Note: Choose based on performance, freshness, and storage needs*

### Incremental Models

- Incremental models process only new or updated data instead of rebuilding everything
- They boost efficiency and cut compute time for large datasets
- Ideal for continuously growing data like event logs or transactions

## Core Philosophy of dbt
### 1. SQL First Approach

- dbt builds everything around SQL, making transformations easy to write and easy to understand. 

- Analysts can contribute without learning unfamiliar programming languages. 

- SQL becomes the center of the transformation process, not just a small part of it.

### 2. Software Engineering Best Practices for Analytics

- dbt encourages version control, testing, documentation and modular design. 

- Data models are structured in clear folders and reusable blocks. 

- This brings reliability, traceability and quality to analytics work.

### 3. Separation of Extraction Loading and Transformation

- dbt focuses only on the T in ELT. 

- Data is already in the warehouse, and dbt transforms it into final usable structures. 

- This simplifies the pipeline and reduces operational complexity.
Why dbt Became Popular?

### 4. Accessible for Analysts and Powerful for Engineers

- Analysts use SQL to build models. 

- Engineers use dbt features such as macros, tests and automation to scale the system. 

- The same tool supports simple tasks and complex pipelines.

### 5. Built for Modern Cloud Warehouses

- dbt works natively with platforms such as Snowflake, BigQuery and Redshift. 

- These warehouses handle computation, while dbt organizes logic and workflow. 

- This makes scaling simple because performance grows with the warehouse.

### 6. Clear and Automatic Documentation

- dbt generates documentation pages showing model definitions, lineage and relationships. 

- Teams understand how data flows from raw to final outputs. 

- Documentation updates automatically as models change.

### 7. Strong Community and Open Source Ecosystem

- dbt has an active community that shares best practices and packages. 

- The open source model accelerates learning and adoption. 

- Companies benefit from shared patterns and proven solutions.
How SQL Becomes Scalable through dbt?

### 8. Modular SQL Modeling

- dbt forces a layered design with staging, core models and business models. 

- Each model is small and reusable. 

- This prevents large, unmanageable SQL scripts.

### 9. Automatic Model Dependencies and Orchestration

- dbt understands which models depend on others. 

- It builds them in the correct order. 

- This eliminates manual job scheduling and dependency errors.

### 10. Built in Testing Framework

- dbt allows easy creation of tests for uniqueness, null checks and referential integrity. 

- These tests run automatically during deployments. 

- This ensures high quality data at every step.

### 11. Use of Templates and Macros

- dbt macros allow reusable logic. 

- Large transformations become cleaner and simple to manage. 

- Repetition is reduced, which improves maintainability.

### 12. Continuous Integration and Continuous Deployment

- dbt projects work well with modern automation tools. 

- Every change can be tested and validated before going live. 

- This brings software development discipline to the analytics world.

### Conclusion

dbt transforms simple SQL into a structured, scalable and production ready workflow. Its SQL first design makes analytics development accessible, while its engineering features create reliability and consistency across teams. By combining modular modeling, automated testing, clear documentation and strong warehouse integration, dbt builds a transformation layer that supports growth, accuracy and long term maintainability. This powerful blend of simplicity and discipline is what makes dbt one of the most popular tools in modern data engineering.