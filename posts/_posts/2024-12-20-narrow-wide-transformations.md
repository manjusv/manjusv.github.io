---
title: "Narrow and Wide Transformations in Spark"
categories:
  - data engineering
tags:
  - narrow transformation
  - data engineering
  - wide transformation
---
In Apache Spark, Transformations are divided into two types: **Narrow** and **Wide** Transformations. Understanding these helps optimize Spark jobs for performance and resource management.

## 1. Narrow Transformations
A **Narrow Transformation** occurs when **each input partition contributes to exactly one output partition**, meaning data dependencies are simple. This allows Spark to process partitions independently, enabling efficient and parallel computation.

Examples of Narrow Transformations:
- `map()`
- `filter()`
- `flatMap()`
- `mapPartitions()`

**Example**:
Consider an RDD of numbers `[1, 2, 3, 4, 5, 6]`.
```python
from pyspark.sql import SparkSession

# Initialize Spark session
spark = SparkSession.builder.appName("NarrowWideExample").getOrCreate()

# Create an RDD
rdd = spark.sparkContext.parallelize([1, 2, 3, 4, 5, 6], 2)

# Apply a narrow transformation
result_rdd = rdd.map(lambda x: x * 2)

# Collect and print the result
result = result_rdd.collect()
print(f"Narrow Transformation Result: {result}")
```
**Output**:
```python
Narrow Transformation Result: [2, 4, 6, 8, 10, 12]
```
### Why It's "Narrow"?
Each element in a source partition transforms to exactly one element in the corresponding output partition. No data shuffling happens between partitions.

## 2. Wide Transformations
A **Wide Transformation** occurs when data from **multiple input partitions** is needed for **one output partition**, causing **data shuffling** across the cluster. This process is more costly because it involves network I/O and coordination between nodes.

**Examples of Wide Transformations**:
- `groupByKey()`
- `reduceByKey()`
- `join()`
- `distinct()`
- `sortByKey()`

**Example**:
Let's perform a wide transformation using `reduceByKey()`:
```python
# Create an RDD of key-value pairs
pairs_rdd = spark.sparkContext.parallelize([("a", 1), ("b", 2), ("a", 3), ("b", 4)], 2)

# Apply a wide transformation
reduced_rdd = pairs_rdd.reduceByKey(lambda x, y: x + y)

# Collect and print the result
result = reduced_rdd.collect()
print(f"Wide Transformation Result: {result}")
```

**Output**:
```python
Wide Transformation Result: [('a', 4), ('b', 6)]
```
### Why It's "Wide"?
The keys `("a")` and `("b")` existed in multiple partitions, so Spark had to **shuffle data** between partitions to combine records with the same key.

## Final Thoughts
- Use **Narrow Transformations** whenever possible to avoid expensive shuffling.
- Be aware of **Wide Transformations**, as they require careful design to minimize data movement.
