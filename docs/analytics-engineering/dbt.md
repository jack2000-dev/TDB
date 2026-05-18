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

### Core Practices for Materialization Strategy
1. Choosing the Right Materialization for the Job

- Views are ideal for lightweight transformations that do not require stored results. 

- Tables are best for models that are queried frequently or require improved performance. 

- Incremental models are the right choice for large datasets where only new or updated rows need to be processed. 

- Ephemeral models are used for logic that should be inlined and not stored as a physical object.
2. Considering Performance Requirements

- Use tables when the warehouse must return results quickly. 

- Use incremental models to reduce processing time for large fact style datasets. 

- Avoid heavy logic inside views because views are recomputed at query time. 

- Use ephemeral models for small transformations that are used many times.
3. Optimizing Incremental Models

- Define a clear unique key to identify new or updated rows. 

- Use filters that limit processing to only the required records. 

- Ensure that logic for incremental and full refresh scenarios is consistent. 

- Test incremental models regularly to confirm correct behavior.
Best Practices for Seeds
4. Using Seeds for Stable Reference Data

- Seeds are ideal for lookup values, small code lists and consistent reference tables. 

- They work best for datasets that change infrequently. 

- Seeds allow non technical users to update data in a simple csv file. 

- This keeps reference data version controlled and fully transparent.
5. Keeping Seed Files Clean and Organized

- Store seed files in the seeds directory to maintain a predictable structure. 

- Ensure values in the csv files follow clear naming patterns. 

- Avoid adding unnecessary columns or large volumes of data. 

- Clean and simple seeds improve readability and reduce errors.
6. Ensuring Data Quality for Seeds

- Validate that seed values match expected formats. 

- Check for duplicates in key fields before loading. 

- Apply tests in dbt to ensure seed tables remain accurate over time. 

- This protects downstream models that rely on seed data.
Operational Considerations
7. Version Control and Change Management

- Keep seed files and materialization settings in version control. 

- Review changes through pull requests to avoid unexpected updates. 

- Document the purpose and usage of each seed and model. 

- This ensures long term clarity and accountability.
8. Balancing Cost, Performance and Freshness

- High cost and high frequency processing should be reserved for tables or incrementals. 

- Low cost and low frequency transformation can be handled with views. 

- Seeds should remain small to avoid unnecessary storage usage. 

- Choosing the right balance improves both performance and budget efficiency.
9. Aligning Materializations with Data Modeling Layers

- Use views in the staging layer when transformations are simple. 

- Use tables or incrementals in the core layer to support reliable metrics. 

- Use materialized marts when performance is essential for analytics tools. 

- Consistent structure improves clarity across the entire project.
10. Testing Materialized Models

- Add tests for uniqueness and non null fields. 

- Add relationship tests to confirm correct joins. 

- Validate incremental logic with dedicated tests. 

- Testing ensures that materializations produce trusted and accurate data.

#### Conclusion

- dbt materializations and seeds provide flexible tools for building efficient transformation workflows. By choosing the right materialization, optimizing performance and maintaining clean seed files, teams can create pipelines that scale smoothly and remain easy to understand. These practices ensure that dbt projects stay organized, cost effective and dependable, supporting high quality analytics and long term maintainability.

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

# Testing and Documenting Models

## Why testing matters in dbt?
- Ensure data reliability and trust
- Detects data quality issue early
- Integrated directly into your pipepline

### Built-in test types
- `unique`: Ensures no duplicate values exist within a column, maintaining data integrity
- `not_null`: Validate that all rows contain non-empty values, preventing missing data

### Defining Tests in YAML
- **Setup**: Tets defined within model `.yml` files
- **Execution**: Automatically excecuted via `dbt test` command
- **Validation**: Supports schema and data validation

### Best Practices for dbt Testing
- Test key columns consistently
- Automate test runs in CI/CD
- Track failures in dashboards or alerts

## Custom SQL dbt 

- Write tailored tests for specific logic checks
- Enable flexible, business specific validation
- Define queries that return failing records

### Example Use Cases

- Validate data ranges or patterns
- Ensure numeric thresholds (e.g., revenue > 0)
- Enforce business rules (e.g., active customers only)

## Documentation in dbt

- Add descriptions to models, columns, and sources
- Generatge docs using `dbt docs generate` and `dbt docs serve`
- Provides a self-service data catalog

### Documenting Best Practices

- Keep descriptions concise and updated
- Use consistent naming and tagging
- Link tests and sources for transparency

## The Role of Testing in dbt Projects
1. Ensuring Accuracy of Key Fields

- dbt tests confirm that important columns follow expected patterns. 

- Tests can validate that primary keys are unique and not empty. 

- They help identify incorrect values before they affect calculations. 

- This ensures models represent real world behavior accurately.
2. Detecting Missing or Invalid Data

- Null checks identify fields that should always contain a value. 

- Accepted value tests verify that categories or status codes match approved lists. 

- Referential tests confirm that every foreign key points to a valid dimension record. 

- These tests protect downstream reports from incomplete or unusable data.
3. Protecting Model Relationships

- Relationship tests check whether linked tables remain consistent. 

- They ensure that fact tables connect properly to dimensions. 

- Failed relationship tests highlight broken joins or missing keys. 

- These validations support healthy data models and accurate reporting.
Types of dbt Tests
4. Generic Tests Provided by dbt

- Unique tests ensure that identifiers do not repeat. 

- Not null tests confirm that required fields always contain values. 

- Accepted values tests check for valid categories or codes. 

- Relationship tests ensure integrity between connected tables.
5. Custom Tests for Complex Requirements

- Teams can create custom logic to validate business rules. 

- Examples include checking financial rollups or enforcing data ranges.

- Custom tests capture logic that generic tests cannot express. 

- This provides flexibility while keeping quality high.
Operational Benefits of dbt Testing
6. Early Detection of Issues

- Tests run automatically during development and deployment. 

- Problems appear immediately, not after dashboards break. 

- Early detection saves time and reduces data related incidents. 

- Teams can resolve issues before they reach production.
7. Consistency Across All Models

- Shared tests promote uniform rules across the project. 

- Every model follows the same quality standards. 

- This creates predictable and stable data structures. 

- Consistency leads to greater trust in the warehouse.
8. Support for Continuous Integration

- Tests integrate naturally with version control and automated pipelines. 

- Every change is validated before becoming part of the main project. 

- This prevents accidental errors from moving forward. 

- Continuous testing keeps the entire transformation environment dependable.

