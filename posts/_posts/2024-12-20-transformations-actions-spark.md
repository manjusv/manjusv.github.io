---
title: "Transformations and Actions in Spark"
categories:
  - data engineering
tags:
  - spark
  - data engineering
  - apache spark
---
In Apache Spark, two key operations work together to process data: Transformations and Actions. Understanding these concepts helps us efficiently work with large datasets.

## Layman Explanation
- **Transformation**: You describe what you want to do but Spark waits. Like writing steps for a recipe without starting cooking.
- **Action**: You tell Spark, "Go cook!" Spark follows your recipe (transformations) and serves the dish (result).

## 1. Transformations (What to Do with Data)
A **Transformation** creates a new dataset from an existing one. It’s **lazy**, meaning Spark doesn’t process the data immediately; it builds a plan and waits until an **Action** is triggered.

**Common Transformations**:
- `map()`: Applies a function to each element.

- `filter()`: Keeps elements that meet a condition.

- `flatMap()`: Similar to map, but flattens nested results.

- `distinct()`: Removes duplicates.

**Example**:
Imagine you have a list of numbers [1, 2, 3, 4, 5, 6].
```python
from pyspark.sql import SparkSession

# Create Spark session
spark = SparkSession.builder.appName("TransformationsExample").getOrCreate()

# Create an RDD (Resilient Distributed Dataset)
data = spark.sparkContext.parallelize([1, 2, 3, 4, 5, 6])

# Apply a transformation (double each number)
transformed_data = data.map(lambda x: x * 2)

# No processing happens here yet (lazy execution)
```

## 2. Actions (Get Results from Data)
An **Action** triggers the computation of transformations and returns a result to the driver program or writes to storage.

**Common Actions**:
- `collect()`: Returns all elements as a list.

- `count()`: Counts elements in the dataset.

- `take(n)`: Retrieves the first n elements.

- `reduce()`: Aggregates using a function.

**Example**:
Continuing from the previous example:
```python
# Trigger an action to collect results
result = transformed_data.collect()

# Print the result
print(result)
```
**Output**:
```python
[2, 4, 6, 8, 10, 12]
```

## Final Thoughts:
1. **Transformations** are instructions (lazy).

2. **Actions** execute the instructions (eager).

3. Spark builds a plan, optimizes it, and only runs it when an **Action** occurs.