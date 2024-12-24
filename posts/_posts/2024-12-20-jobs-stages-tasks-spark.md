---
title: "Jobs, Stages and Tasks in Spark"
categories:
  - spark
tags:
  - jobs
  - stage
  - task
  - spark
  - data engineering
---
## What is a Job?
- A **Job** is a complete Spark operation triggered by an action like `collect()`, `count()`, or `save()`.

- Every action creates a new Job.

**Example**:
If you say: "Count all even numbers in a dataset."
- Spark creates 1 Job to do that.

## What is a Stage?
- A **Stage** is a group of operations that Spark can do **without moving data between machines (nodes)**.

- If Spark needs to **shuffle data** across nodes (due to wide transformations like `reduceByKey()`), it creates a new **Stage**.

**Example**:
If you say: "Double all numbers and then add numbers with the same key."
- Stage 1: Double the numbers (done independently).

- Stage 2: Add numbers with the same key (needs data from multiple nodes).

## What is a Task?
- A Task is a single piece of work that runs on one part of the data (partition).

- Each Stage runs one Task per partition.

**Example**:
If you have a dataset split into **4 partitions**, each stage will run **4 Tasks** (one task per partition).

## Layman Example
1. Job: Deliver pizzas to all customers (entire task).
2. Stages:
    - Stage 1: Make pizzas in the kitchen.
    - Stage 2: Deliver pizzas to customers.
3. Tasks:
    - For each delivery driver (partition), assign a delivery task.

## **Code Example**:
```python
from pyspark.sql import SparkSession

# Start Spark
spark = SparkSession.builder.appName("SimpleExample").getOrCreate()

# Create RDD with 4 partitions
rdd = spark.sparkContext.parallelize([1, 2, 3, 4, 5, 6, 7, 8], 4)

# Transformation (Stage 1 - Narrow)
mapped_rdd = rdd.map(lambda x: x * 2)

# Action (Stage 2 - Wide, triggers Job)
result = mapped_rdd.reduce(lambda x, y: x + y)

print(f"Final Result: {result}")
```

### How Spark Works:
1. Job: The action reduce() triggers a job.
2. Stages:
    - Stage 1: Multiply numbers by 2 (map()).
    - Stage 2: Add numbers together (reduce()).
3. Tasks: Since there are 4 partitions, Spark runs 4 Tasks in each stage.

## Number of Jobs = Number of Actions in DAG
A **Job** in Spark is triggered by an **Action** like `collect()`, `count()`, or `save()`. Each action creates a **DAG (Directed Acyclic Graph)**, which Spark breaks down into **stages** and **tasks**.

**Example**:
```python
from pyspark.sql import SparkSession

# Initialize Spark session
spark = SparkSession.builder.appName("JobsStagesTasks").getOrCreate()

# Create an RDD
rdd = spark.sparkContext.parallelize([1, 2, 3, 4, 5, 6], 3)

# Action 1 (Job 1)
result1 = rdd.filter(lambda x: x % 2 == 0).collect()

# Action 2 (Job 2)
result2 = rdd.count()
```
**Explanation**:

- **Actions Trigger Jobs**:

    `collect()` → Job 1

    `count()` → Job 2

**Total Jobs**: 2 (One per action)

## Number of Stages = Number of Wide Transformations + 1
A **Stage** is a group of tasks that can be executed without requiring data from other partitions. **Wide Transformations** like `reduceByKey()`, `groupByKey()`, and `join()` cause data **shuffling**, splitting the DAG into multiple stages.

**Example**:
```python
# Create an RDD of key-value pairs
rdd2 = spark.sparkContext.parallelize([("a", 1), ("b", 2), ("a", 3), ("b", 4)], 2)

# Transformation 1: map (Narrow Transformation)
mapped_rdd = rdd2.mapValues(lambda x: x * 2)

# Transformation 2: reduceByKey (Wide Transformation)
reduced_rdd = mapped_rdd.reduceByKey(lambda x, y: x + y)

# Action: collect()
result = reduced_rdd.collect()
```
**Explanation**:

1. `mapValues()` → **Narrow Transformation** (Same stage)
2. `reduceByKey()` → **Wide Transformation** (New stage due to shuffling)

**Total Stages**: 2 (1 for `mapValues()` + 1 for `reduceByKey()`)

## Number of Tasks = Number of Partitions per Stage
A **Task** is the smallest unit of work in Spark, assigned to each partition of a stage.

**How It Works**:
- Each stage runs **one task per partition**.
- If you have **4 partitions**, each stage will have **4 tasks**.

## Full Example: Jobs, Stages, and Tasks
```python
# Create RDD with 4 partitions
rdd3 = spark.sparkContext.parallelize([1, 2, 3, 4, 5, 6, 7, 8], 4)

# Transformation 1: map (Narrow)
mapped_rdd = rdd3.map(lambda x: x * 2)

# Transformation 2: reduceByKey (Wide)
paired_rdd = mapped_rdd.map(lambda x: (x % 2, x))
result_rdd = paired_rdd.reduceByKey(lambda x, y: x + y)

# Action: collect()
final_result = result_rdd.collect()
print(final_result)
```
### Breakdown:
1. Jobs:
    - Action: `collect()` → 1 Job
2. Stages:
    - Stage 1: `map()` (Narrow)
    - Stage 2: `reduceByKey()` (Wide, shuffling needed)
    **Total Stages**: 2
3. Tasks:
    - Stage 1: **4 Tasks** (since there are 4 partitions)
    - Stage 2: **4 Tasks** (again, due to 4 partitions after shuffle)

## Key Takeaways
1. **Number of Jobs = Number of Actions** ✅

2. **Number of Stages = Wide Transformations + 1** ✅

3. **Number of Tasks = Number of Partitions per Stage** ✅
