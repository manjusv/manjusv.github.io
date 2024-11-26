---
title: "Strong Consistency vs Eventual Consistency in Distributed Systems"
categories:
  - python
tags:
  - strong consistency
  - eventual consistency
  - distributed systems
---
Consistency refers to how a distributed system maintains a single, coherent view of data across multiple nodes. Two common consistency models are **strong consistency** and **eventual consistency**.

## Strong Consistency
In a strongly consistent system, after a write operation is completed, all subsequent read operations (from any node) will return the most recent value. This ensures that all users always see the latest data, regardless of the node they access.

### Example Use Cases:

- Banking Systems: Ensuring accurate account balances after transactions.
- Stock Trading Platforms: Guaranteeing up-to-date information about stock prices and orders.

### Advantages:

- Guarantees up-to-date data.
- Eliminates ambiguity for users.

### Limitations:

- High latency due to synchronization between nodes.
- Scalability issues, as maintaining strict consistency is resource-intensive.

**Python Code Example**:
```python
import threading

class StrongConsistency:
    def __init__(self):
        self.lock = threading.Lock()
        self.data = None

    def write(self, value):
        with self.lock:  # Ensures no concurrent writes or reads during update
            self.data = value
            print(f"Data written: {value}")

    def read(self):
        with self.lock:  # Ensures only the most recent data is read
            return self.data

# Example Usage
store = StrongConsistency()

def writer():
    store.write("Latest Value")

def reader():
    print("Read value:", store.read())

thread1 = threading.Thread(target=writer)
thread2 = threading.Thread(target=reader)

thread1.start()
thread1.join()
thread2.start()
thread2.join()
```

## Eventual Consistency
In an eventually consistent system, the system guarantees that all nodes will converge to the same state over time, but reads may return stale data in the short term.

### Example Use Cases:

- **Social Media**: Post likes and comments might be temporarily inconsistent but eventually converge.
- **DNS**: Domain name updates may take time to propagate across all servers.

### Advantages:
- High availability and scalability.
- Faster writes, as immediate synchronization isn't required.

### Limitations:
- Temporary inconsistencies might confuse users.
- Requires additional mechanisms (e.g., conflict resolution) for correctness.

**Python Code Example**:
```python
import time
from threading import Thread

class EventualConsistency:
    def __init__(self):
        self.nodes = {"node1": None, "node2": None}

    def write(self, value):
        print(f"Writing value: {value}")
        for node in self.nodes:
            # Simulate propagation delay
            Thread(target=self._propagate, args=(node, value)).start()

    def _propagate(self, node, value):
        time.sleep(2)  # Simulate delay
        self.nodes[node] = value
        print(f"{node} updated to {value}")

    def read(self, node):
        return self.nodes[node]

# Example Usage
store = EventualConsistency()

store.write("New Value")
time.sleep(1)
print("Read from node1:", store.read("node1"))
time.sleep(2.5)
print("Read from node2:", store.read("node2"))
```

## When to Use Each Model
### Strong Consistency:
- When correctness and accuracy are critical.
- Use cases:
  - Financial transactions (e.g., banking, stock trading).
  - Inventory management systems with low latency requirements.

### Eventual Consistency:
- When availability and performance are prioritized over immediate consistency.
- Use cases:
  - Social media (e.g., likes, comments, notifications).
  - Content delivery networks (CDNs).
  - Caching layers.

## Limitations
### Strong Consistency:
1. **High Latency**: Synchronous communication increases response time.
2. **Partition Tolerance**: Difficult to maintain during network partitions (violates CAP theorem).
3. **Scalability**: Synchronization overhead limits system scalability.
### Eventual Consistency:
1. **Stale Reads**: Users may see outdated data temporarily.
2. **Conflict Resolution**: Requires additional mechanisms for handling simultaneous updates.
3. **Complexity for Developers**: Developers must design for eventual convergence.

## Impact on System Design
- Database Choice:
  - Strong Consistency: Relational databases like PostgreSQL, MySQL.
  - Eventual Consistency: NoSQL databases like Cassandra, DynamoDB.

- Partition Handling:
  - Strong Consistency: Handle network partitions with failover or retries.
  - Eventual Consistency: Design for temporary discrepancies and eventual convergence.

- Performance vs. Accuracy:
  - Strong Consistency: Trade-off performance for accuracy.
  - Eventual Consistency: Trade-off accuracy for high availability and low latency.