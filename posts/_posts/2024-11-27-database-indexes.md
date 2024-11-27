---
title: "Database Indexes - UNIQUE, CLUSTERED, NONCLUSTERED"
categories:
  - sql
tags:
  - indexes
  - sql
---

## Types of Indexes
### 1. Single-Column Index
An index created on a single column. It is the simplest form of indexing.

**Advantages**:

- Speeds up searches and sorting based on the indexed column.
- Reduces query execution time for queries with a WHERE clause.

**Limitations**:
- Doesn’t improve performance for queries involving multiple columns.

**SQL Example**:
```sql
CREATE INDEX idx_user_id ON users(user_id);
```

### 2. Composite Index
An index on two or more columns, primarily used for queries that filter or sort by multiple columns.

**Advantages**:
- Useful for queries with multiple columns in the WHERE clause.
- Enhances performance for queries that sort or filter by the indexed columns.

**Limitations**:
- The order of columns in the index matters. For example, an index on (A, B) will not - efficiently handle a query filtering only on B.

**SQL Example**:
```sql
CREATE INDEX idx_city_age ON users(city, age);
```

### 3. Unique Index
A unique index enforces that all values in the indexed column(s) must be unique.

**Advantages**:
- Guarantees data integrity by preventing duplicate entries.
- Often used for columns like email, username, etc.

**Limitations**:
- Slightly slower inserts/updates due to uniqueness checks.

**SQL Example**:
```sql
CREATE UNIQUE INDEX idx_unique_email ON users(email);
```

### 4. Full-Text Index
A special type of index for searching text within columns.

**Advantages**:
- Optimized for text search.
- Ideal for applications like search engines or document retrieval systems.

**Limitations**:
- High storage overhead.
- Requires database-specific support (e.g., FTS in SQLite or FULLTEXT in MySQL).

**Example**:
```sql
CREATE FULLTEXT INDEX idx_fulltext_content ON articles(title, content);
```

### 5. Clustered Index
In a clustered index, the rows of the table are physically sorted to match the index. The table itself becomes the index. Each table can have only one clustered index.

**Advantages**:
- **Faster for Range Queries**: Queries like BETWEEN, ORDER BY, and GROUP BY are highly efficient because data is stored sequentially.
- **Efficient Retrieval of Full Rows**: Since the data is the index, fetching the entire row is faster.

**Limitations**:
- **Only One Per Table**: Since the table is sorted physically, only one clustered index can exist.
- **Slower Inserts/Updates**: Maintaining physical order means higher overhead for data modifications.

**When to Use Clustered Index**
- Tables with many range-based queries.
- Primary key columns or frequently sorted columns.
- Tables that are more read-heavy than write-heavy.

**SQL Example (MySQL)**:
Suppose you create a clustered index on a table's primary key:
```sql
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    name VARCHAR(50),
    age INT
);
```
Here, the primary key `emp_id` is a **clustered index by default**. The table's rows are stored in ascending order of `emp_id`.

- Querying `SELECT * FROM employees WHERE emp_id BETWEEN 10 AND 20;` is fast because the data is physically ordered.


### 6. Non-Clustered Index
In a non-clustered index, a separate structure is created to store index data and pointers to the actual rows in the table. The table's data is not physically sorted.

**Advantages**:
- **Multiple Indexes Allowed**: You can create multiple non-clustered indexes on a table, optimizing multiple query patterns.
- **Faster for Narrow Queries**: Ideal for queries that only need to access a few columns.

**Limitations**:
- **Additional Storage Required**: Indexes require extra space to store the index structure.
- **Extra Lookups**: Requires pointer chasing to fetch actual rows from the table, making it slower for queries needing the full row.

**When to Use Non-Clustered Index**
- Columns frequently used in WHERE, JOIN, or ORDER BY clauses but not the primary key.
- Tables where multiple query patterns exist.
- Write-heavy tables where physical reordering would be costly.

**SQL Example**: Suppose you add a non-clustered index on the name column:
```sql
CREATE NONCLUSTERED INDEX idx_name ON employees(name);
```
This creates a separate structure mapping each `name` to the corresponding `emp_id`.

- Querying `SELECT * FROM employees WHERE name = 'Alice';` uses the non-clustered index to quickly locate the row.

### 7. Bitmap Index
Uses a bitmap for each unique value of the indexed column. Ideal for low-cardinality columns (columns with few unique values).

**Advantages**:
- Efficient for low-cardinality columns (e.g., gender, status).
- Combines bitmaps for multiple conditions.

**Limitations**:
- High overhead for updates.
- Mostly used in read-heavy workloads.

### 8. Hash Index
Uses a hash table to store index data, making equality lookups (=) extremely fast.

**Advantages**:
- Optimized for exact-match queries.
- Simple and fast.

**Limitations**:
- Not suitable for range queries (>, <, BETWEEN).

**SQL Example (PostgreSQL)**:
```sql
CREATE INDEX idx_hash_user_id ON users USING HASH(user_id);
```

## Optimizing Queries in Python-Based Applications
1. **Use Indexes Appropriately**:

    - Ensure indexes exist on columns frequently used in WHERE, JOIN, ORDER BY, or GROUP BY clauses.
    - Avoid indexing columns with high write/update frequency unnecessarily.

2. **Avoid Full Table Scans**:
    - Structure queries to leverage indexed columns.
    - Use specific column names in SELECT instead of SELECT *.

3. **Database Connection Pooling**:
    - Use libraries like SQLAlchemy or psycopg2 with connection pooling to reduce overhead.
    - **Example**:
    ```python
    from psycopg2.pool import SimpleConnectionPool

    pool = SimpleConnectionPool(1, 10, database="test", user="postgres", password="secret")
    conn = pool.getconn()
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM users WHERE age > 25")
    pool.putconn(conn)
    ```

4. **Batch Queries**:
    - Process bulk data in batches instead of executing multiple queries sequentially.
    - **Example**:
    ```python
    from psycopg2.extras import execute_batch

    data = [(1, 'Alice', 25), (2, 'Bob', 30)]
    query = "INSERT INTO users (user_id, name, age) VALUES (%s, %s, %s)"
    execute_batch(cursor, query, data)
    ```
5. **Caching**:
    - Use caching mechanisms like Redis for frequently accessed data.
    - Cache query results in memory where applicable.
    - **Example**:
    ```python
    import redis

    cache = redis.StrictRedis(host="localhost", port=6379, decode_responses=True)
    cache.set("user_1", '{"user_id": 1, "name": "Alice"}')
    ```

6. **Use Query Optimizers**:
    - Analyze query plans using `EXPLAIN` or `EXPLAIN ANALYZE` to identify bottlenecks.
    - **Example**:
    ```sql
    EXPLAIN ANALYZE SELECT * FROM users WHERE age > 25;
    ```