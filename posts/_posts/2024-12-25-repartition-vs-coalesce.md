---
title: "Repartition vs Coalesce in Spark"
categories:
  - spark
tags:
  - repartition
  - coalesce
  - spark
  - data engineering
---

## What are Repartition and Coalesce in Spark?
### Repartition
- `repartition()` increases or decreases the number of partitions in an RDD or DataFrame.

- It **shuffles data** across the cluster to create the desired number of partitions, ensuring an even distribution of data.

- Suitable for both increasing and decreasing the number of partitions.

**Example**:
```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("RepartitionExample").getOrCreate()

data = [1, 2, 3, 4, 5]
rdd = spark.sparkContext.parallelize(data, 2)  # 2 partitions
print("Initial partitions:", rdd.getNumPartitions())  # Output: 2

# Increase to 4 partitions
repartitioned_rdd = rdd.repartition(4)
print("Repartitioned partitions:", repartitioned_rdd.getNumPartitions())  # Output: 4
```

### Coalesce
- `coalesce()` reduces the number of partitions without a full shuffle.

- It moves data from some partitions into others, often resulting in **fewer partitions than before**.

- Best suited for **reducing partitions** efficiently when the data distribution is already balanced.

**Example**:
```python
# Reduce to 1 partition
coalesced_rdd = rdd.coalesce(1)
print("Coalesced partitions:", coalesced_rdd.getNumPartitions())  # Output: 1
```
## Layman Explanation Along with Technical One
### Layman Explanation
- **Repartition**: Imagine you have a pile of books split into 2 bags. If you need to distribute them into 4 bags (or merge into 1 bag), you shuffle the books and evenly distribute them.

- **Coalesce**: If you’re reducing from 2 bags to 1, you simply dump one bag into the other without much effort, assuming it fits.

### Technical Explanation
- **Repartition**: Shuffles data to redistribute it evenly across all partitions. It works well when increasing partitions or when data is skewed across partitions.

- **Coalesce**: Merges data from multiple partitions into fewer partitions without shuffling, suitable for reducing partitions when data is already balanced.

## Advantages and Disadvantages
### Repartition
- **Advantages**:
    - Ensures even data distribution across partitions.

    - Suitable for both increasing and decreasing partitions.

- **Disadvantages**:
    - Involves a full shuffle, which is expensive in terms of time and resources.
### Coalesce
- **Advantages**:
    - Avoids a full shuffle, making it faster and more resource-efficient.

    - Ideal for reducing partitions efficiently.
- **Disadvantages**:
    - Does not rebalance data distribution, potentially causing skew.

## When to Use Repartition and Coalesce?
### When to Use Repartition:
- When increasing partitions to utilize more parallelism.

- When existing partitions are unevenly distributed, causing data skew.

- Before saving large datasets to distributed storage systems like HDFS for optimal performance.

### When to Use Coalesce:
- When reducing partitions for optimized execution (e.g., after a large shuffle or filter).

- When you know the data is evenly distributed across existing partitions.

- To prepare data for saving as a single file (e.g., coalescing into 1 partition).

### When to Avoid Repartition and Coalesce:
- Avoid **repartition** when reducing partitions, as `coalesce` is more efficient.

- Avoid **coalesce** if reducing partitions might cause imbalance or data skew.

## Key Takeaways
### Repartition:

- For both increasing and decreasing partitions.
- Always involves a shuffle.
- Ensures even distribution of data.

### Coalesce:

- For reducing partitions only.
- Avoids shuffle for efficiency.
- Works best when the existing data distribution is already balanced.

## Examples of Real-World Use Cases
### 1. Optimizing Parallelism for Large Data Processing
- **Scenario**: A dataset has 10 partitions but is processed on a cluster with 100 executors.

- **Solution**: Use `repartition(100)` to improve parallelism and speed up processing.

### 2. Reducing Partitions for Single Output File
- **Scenario**: You need to write a small filtered dataset as a single file.

- **Solution**: Use `coalesce(1)` to reduce partitions before writing.
```python
# Save a single file
filtered_df.coalesce(1).write.csv("output.csv")
```

### 3. Handling Skewed Data
- Scenario: A join operation results in uneven partition sizes.

- Solution: Use `repartition()` to redistribute the data evenly.

### 4. Post-Filter Optimization
- **Scenario**: After filtering a large dataset, many partitions are empty or have minimal data.

- **Solution**: Use `coalesce()` to reduce partitions and optimize storage and processing.
```python
filtered_rdd = large_rdd.filter(lambda x: x > 100)
optimized_rdd = filtered_rdd.coalesce(5)
```