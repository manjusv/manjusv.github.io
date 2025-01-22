---
title: "Parsed, Analyzed, and Optimized Logical Plans in Spark"
categories:
  - spark
tags:
  - cache
  - persist
  - spark
  - data engineering
---

## What are Parsed, Analyzed, and Optimized Logical Plans in Spark?
Apache Spark employs a sophisticated query optimization mechanism involving several logical and physical plans. Let’s explore the three key types of logical plans: Parsed, Analyzed, and Optimized.

### 1.1 Parsed Logical Plan
- **Definition**: The parsed logical plan is the initial representation of a query. It’s derived by parsing the user’s SQL query or DataFrame operations into a tree structure.

- **Purpose**: Acts as a syntax check; ensures that the query is syntactically valid but does not validate the schema or table references.

**Example**:
```python
df = spark.read.csv("data.csv")
result = df.filter("age > 30").select("name", "age")
```

For the query:
```sql
SELECT name, age FROM data WHERE age > 30;
```
The parsed logical plan captures operations like:
- A filter operation on the **age** column.

- A projection selecting **name** and **age**.

### 1.2 Analyzed Logical Plan
- **Definition**: The analyzed logical plan is the parsed plan enriched with metadata. Spark resolves table names, column names, and verifies that the operations are valid against the dataset's schema.

- **Purpose**: Validates the query by checking column existence, data types, and other semantic rules.

**How It Works**:

- Resolves column references (e.g., **age** and **name** are valid columns in **data**).

- Validates the schema and metadata of the underlying data source.

### 1.3 Optimized Logical Plan
- **Definition**: The optimized logical plan is an improved version of the analyzed logical plan. It applies various optimization rules (e.g., predicate pushdown, projection pruning, filter pushdown) to minimize the cost of the query execution.

- **Purpose**: Enhances query performance by reordering or simplifying operations.

**Optimization Examples**:
- **Predicate Pushdown**: Moves filters closer to the data source to minimize data read.

- **Projection Pruning**: Eliminates unnecessary columns early in the query plan.

## Major Differences Between Parsed, Analyzed, and Optimized Logical Plans
<table border="1" style="border-collapse: collapse; width: 100%;">
    <thead>
        <tr style="background-color: #f2f2f2;">
            <th>Aspect</th>
            <th>Parsed Logical Plan</th>
            <th>Analyzed Logical Plan</th>
            <th>Optimized Logical Plan</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Stage</td>
            <td>Initial parsing of the query.</td>
            <td>Schema and metadata validation.</td>
            <td>Query optimization.</td>
        </tr>
        <tr>			
            <td>Purpose</td>
            <td>Syntax validation.</td>
            <td>Semantic validation.</td>
            <td>Improve query performance.</td>
        </tr>
        <tr>			
            <td>Content</td>
            <td>Syntax tree.</td>
            <td>Fully resolved operations.</td>
            <td>Optimized tree of operations.</td>
        </tr>
        <tr>			
            <td>Schema/Metadata</td>
            <td>Not validated.</td>
            <td>Validated and resolved.</td>
            <td>Same as analyzed, but optimized.</td>
        </tr>
        <tr>			
            <td>Transformations</td>
            <td>User-defined.</td>
            <td>Logical verification.</td>
            <td>Optimized using Spark rules.</td>
        </tr>
    </tbody>
</table>


## When Should We Look at Each of Them Over Others?
- Parsed Logical Plan:
    - Use when debugging syntax errors.

    - Helpful for understanding the high-level query structure.

- Analyzed Logical Plan:
    - Use when debugging semantic issues like missing columns or invalid operations.

    - Check whether the schema and references are correctly resolved.

- Optimized Logical Plan:
    - Use when analyzing performance bottlenecks.

    - Understand how Spark has optimized the query, such as filter or projection pushdowns.

**How to View Plans**:
```python
# Sample DataFrame and Query
df = spark.read.csv("data.csv", header=True, inferSchema=True)
result = df.filter("age > 30").select("name", "age")

# Parsed Logical Plan
print(result.queryExecution.logical)

# Analyzed Logical Plan
print(result.queryExecution.analyzed)

# Optimized Logical Plan
print(result.queryExecution.optimizedPlan)
```

## Key Takeaways
1. Spark logical plans help understand and debug query execution at different stages.

2. The **Parsed Logical Plan** focuses on syntax validation, the **Analyzed Logical Plan** ensures semantic correctness, and the **Optimized Logical Plan** improves query performance.

3. Always use the optimized plan to analyze performance optimizations applied by Spark.

4. Logical plans are essential for understanding the internals of Spark SQL and DataFrame operations.

## Examples of Real-World Use Cases
**Use Case 1: Debugging Syntax Errors**
- **Scenario**: A developer writes a complex SQL query.

- **Solution**: Use the parsed logical plan to debug syntax issues.

**Use Case 2: Verifying Column Resolution**
- **Scenario**: A DataFrame operation fails because a column name is misspelled.

- **Solution**: Use the analyzed logical plan to identify unresolved column references.

**Use Case 3: Performance Tuning**
- **Scenario**: A query is running slower than expected due to reading unnecessary columns or rows.

- **Solution**: Check the optimized logical plan to see if projection pruning or predicate pushdown is applied.

```python
# Example of Performance Optimization
df = spark.read.parquet("large_data.parquet")
result = df.filter("age > 30").select("name")

# Check if predicate pushdown is applied
print(result.queryExecution.optimizedPlan)
```
