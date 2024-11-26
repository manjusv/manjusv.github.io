---
title: "What is CAP theorem?"
categories:
  - python
tags:
  - python
  - distributed systems
  - CAP theorem
---
The CAP Theorem is a foundational concept in distributed systems, reminding us that no system can achieve Consistency (C), Availability (A), and Partition Tolerance (P) simultaneously.

## Layman's Example
Imagine you’re running a library system with multiple branches spread across the city. Each branch maintains its own copy of the entire database of all books. This is done so that branches can work independently without constantly querying a central server. 

**Now let's say each Branch Has a Copy of All 10 Books**
- Each branch (A, B, and C) knows about all 10 books in the library system.
- For example, if a user checks out "Book 1" at Branch A, the database at Branch A must be updated to reflect that "Book 1" is borrowed. Ideally, this update should also propagate to Branch B and Branch C to keep all branches in sync.

**Challenges**

- Users can borrow books from any branch.
- All branches should ideally have up-to-date information about borrowed books.
- Sometimes, communication between branches can break (like a network failure).

**Now let's connect the above example to CAP**

Here’s how this setup works under different CAP scenarios:

1. **Consistency (C)** - Consistency means every branch of the library should show the same, up-to-date information.
    - After "Book 1" is borrowed from Branch A, branches B and C should **immediately know** that "Book 1" is unavailable.
    - This requires communication between all branches to ensure their databases are in sync.
    - If the network connecting the branches fails, the system may delay responding to users at Branch A until it can confirm updates with Branch B and Branch C.
        - **Trade-off**: Users may experience delays, but the data will remain accurate across all branches.
2. **Availability (A)** - 
Availability means every branch can serve users and process their requests (e.g., borrowing a book) even if some branches don’t have the latest information.
    - Branch A allows the user to borrow "Book 1" even if it can’t communicate with Branch B or Branch C due to network issues.
    - However, Branch B and Branch C might still show "Book 1" as available because their databases haven’t been updated yet.
        - **Trade-off**: Users can borrow books without delay, but the information may temporarily be inconsistent.
3. **Partition Tolerance (P)** - Partition tolerance means the library system continues to function even if some branches can’t communicate with each other (e.g., due to a network failure).
    - The system tolerates network failures between branches. Each branch operates independently without shutting down.
    - Partition tolerance is **non-negotiable** in distributed systems because network failures can always happen. You must choose between prioritizing C (Consistency) or A (Availability) during these failures.

### Summary of Layman Example
- **Consistency (C)**: All branches always show the same, accurate information, but may delay responses during network failures.
- **Availability (A)**: Branches always respond to users, but the information may not be up-to-date during network failures.
- **Partition Tolerance (P)**: The system keeps running despite communication issues, but you must choose between Consistency or Availability.

## Scenarios in CAP Theorem
1. **CP (Consistency + Partition Tolerance)**
    - Focus on accurate data, sacrificing availability during network issues.
    - Example: Distributed databases like HBase or MongoDB in "strong consistency" mode.
    - Ideal for scenarios like banking and financial systems, where incorrect data can lead to severe consequences.

2. **AP (Availability + Partition Tolerance)**
    - Ensure the system responds, but the data might be outdated.
    - Example: DNS systems, eventual-consistency databases like Cassandra.
    - Ideal for systems like social media or online stores where temporary inconsistencies (e.g., showing an item as "in stock" when it’s not) are acceptable.

3. **CA (Consistency + Availability)**

    - Achieving this requires a system without partitions, meaning it can’t tolerate network failures.
    - Example: Single-node databases (not distributed).

## Practical Examples
### Example 1: Consistency vs. Availability
Suppose you’re building an inventory system using Python and a distributed database.

- **Consistency Example**:
Ensure that every update to the inventory reflects immediately. Use a distributed lock to guarantee consistency.
```python
import redis
from redis.exceptions import LockError

# Simulating a distributed lock for consistency
redis_client = redis.StrictRedis()

def update_inventory(item_id, quantity):
    lock = redis_client.lock(f"lock:{item_id}", timeout=5)
    try:
        if lock.acquire(blocking=True):
            current_quantity = int(redis_client.get(item_id) or 0)
            redis_client.set(item_id, current_quantity + quantity)
            print(f"Updated inventory for {item_id} to {current_quantity + quantity}")
    finally:
        lock.release()

update_inventory("item_101", 10)
```
- **Availability Example**:
Allow updates to proceed even if not all nodes are in sync.
This ensures no downtime, but the inventory might show stale data.
```python
# Simulating an eventual consistency model
def update_inventory_eventual(item_id, quantity):
    # Simulate sending updates to multiple nodes asynchronously
    print(f"Update for {item_id} sent to Node A, Node B, Node C (may sync later).")

update_inventory_eventual("item_101", 10)
```

### Example 2: Partition Tolerance
In distributed systems, partition tolerance is essential because networks can fail. Python frameworks like Celery with message brokers (e.g., RabbitMQ) are used to maintain system operation during partitions.

**Celery Task Queue Example**:
```python
from celery import Celery

app = Celery('tasks', broker='redis://localhost:6379/0')

@app.task
def update_inventory(item_id, quantity):
    # Process update task asynchronously
    print(f"Inventory update task for {item_id}: {quantity}")

# Simulate network partition: Even if one node is down, the task will be retried.
update_inventory.delay("item_101", 10)
```

## How CAP Theorem Influences Backend Design
1. Consistency vs. Availability Trade-offs:
    - If you prioritize consistency (e.g., financial transactions), the system may temporarily reject requests during partitions.
    - If you prioritize availability (e.g., social media), you accept eventual consistency but ensure the system is always responsive.

2. Application Scenarios:
    - CP Systems: Banking, inventory, or other critical systems needing consistent state.
    - AP Systems: Social media platforms, DNS, or systems handling user-generated content.

3. Database Choices:
    - CP Databases: MongoDB (with strong consistency), HBase.
    - AP Databases: Cassandra, DynamoDB.
