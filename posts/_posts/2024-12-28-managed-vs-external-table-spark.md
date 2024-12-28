---
title: "Managed vs external table in Spark"
categories:
  - spark
tags:
  - managed table
  - external table
  - spark
  - data engineering
---
## What Are Managed Tables and External Tables in Spark?
### Managed Tables
- **Definition**: In a managed table, Spark manages both the metadata and the data itself. When you create a managed table, Spark automatically handles the location where the data is stored.

- **Data Management**: If you drop a managed table, Spark deletes both the metadata and the actual data from storage.

### External Tables
- **Definition**: In an external table, Spark manages only the metadata, while the data location is controlled and maintained by the user. Spark only reads the data from the specified path.

- **Data Management**: Dropping an external table removes only the metadata; the actual data remains intact.

**Example**
```sql
-- Managed Table
CREATE TABLE managed_table (
    id INT,
    name STRING
) 
USING PARQUET;

-- Insert data into the managed table
INSERT INTO managed_table VALUES (1, 'Alice'), (2, 'Bob');

-- External Table
CREATE TABLE external_table (
    id INT,
    name STRING
)
USING PARQUET
LOCATION '/path/to/external/data';

-- Insert data into the external table
INSERT INTO external_table VALUES (3, 'Charlie'), (4, 'Daisy');
```

## Layman Explanation Along with Technical One
### Layman Explanation
- **Managed Table**: Think of it like renting a fully-furnished apartment where the landlord takes care of everything, including cleaning up after you move out.
- **External Table**: It’s like owning your own furniture and moving it into an apartment. If you leave, you take your furniture with you.

### Technical Explanation
- A **managed table** stores its data in the Spark warehouse directory, and Spark controls all operations, including cleanup.
- An **external table** points to data stored outside the warehouse (e.g., on HDFS, S3, or local storage). Spark tracks only the metadata.

## Advantages and Disadvantages of Each
### Managed Tables
**Advantages**:
- **Simplicity**: Data and metadata management is handled by Spark.

- **Integration**: Ideal for workflows where Spark manages the entire data lifecycle.

**Disadvantages**:
- **Less Control**: Spark deletes the data when the table is dropped.

- **Portability**: Moving data to other systems is more complicated.

### External Tables
**Advantages**:
- **Data Preservation**: Dropping the table doesn’t delete the data.

- **Flexibility**: The same data can be used by multiple systems or applications.

- **Location Control**: Users can store data in specific storage systems or paths.

**Disadvantages**:
- **Manual Management**: Users are responsible for ensuring the integrity and availability of the data.

- **Complexity**: Requires careful setup of storage paths and permissions.

## When to Use and When to Avoid Managed and External Tables
### Managed Tables
**Use When**:
- The data lifecycle is entirely managed by Spark.

- You want simplicity in data and metadata management.

- The data is temporary or not shared across multiple systems.

**Avoid When**:
- You need control over data location.

- The data is shared between multiple applications or platforms.

- You want to ensure the data persists even if the table is dropped.

### External Tables
**Use When**:
- Data resides in a shared storage system like HDFS, S3, or a cloud data lake.

- Multiple applications or platforms need to access the same data.

- You want control over the storage location and format.

**Avoid When**:
- You want Spark to handle all data management tasks.

- Data integrity and access policies are complex to maintain manually.

## Key Takeaways
### 1. Lifecycle Management:
- Managed tables: Spark controls the entire lifecycle (metadata and data).

- External tables: Spark manages metadata; the user manages data.
### 2. Data Deletion:
- Managed tables: Data is deleted when the table is dropped.

- External tables: Data persists even if the table is dropped.
### 3. Flexibility:
- Managed tables: Simplified data management for Spark-only workflows.

- External tables: Enhanced flexibility for shared or multi-application environments.

## Examples of Real-World Use Cases
### Managed Tables
1. **Temporary Analytics Pipelines**:

    - An organization needs to run periodic analyses on data and doesn’t need the data after the process is completed.
    ```sql
    CREATE TABLE temp_analysis USING PARQUET AS
    SELECT * FROM transactions WHERE amount > 1000;
    ```
2. **Prototyping**:
    - During data pipeline development, managed tables simplify testing.

### External Tables
1. **Shared Data Lake**:
    - Data stored on S3 is shared between Spark, Hive, and other analytics tools. An external table ensures Spark does not delete the data accidentally.
    ```sql
    CREATE TABLE shared_data USING PARQUET LOCATION 's3://my-data-lake/sales/';
    ```

2. **Compliance Requirements**:
    - In industries like healthcare or finance, raw data must remain intact even if the metadata is removed.

3. **Integration with Other Tools**:
    - A BI tool like Tableau uses the same data stored in a cloud storage location.

