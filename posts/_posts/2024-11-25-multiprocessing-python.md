---
title: "Multiprocessing in Python"
categories:
  - python
tags:
  - multiprocessing
---

## What is Multiprocessing in Python?

Multiprocessing is a Python module that allows you to create multiple processes to execute tasks concurrently. Each process runs in its own memory space, allowing true parallel execution. This is especially useful for CPU-bound tasks where the Global Interpreter Lock (GIL) in Python's threading module would otherwise limit parallelism.

### Layman Explanation
Imagine your computer is like a kitchen, and you have several chefs (CPU cores). With multiprocessing, each chef (process) works on a completely separate task, using their own set of tools (memory), without interfering with others. This way, the chefs can cook (execute tasks) in parallel, making the work faster.

## Key Concepts of Multiprocessing
1. **Processes**: Separate execution units that don’t share memory with other processes.
2. **Parallelism**: Achieving true parallel execution across CPU cores.
3. **Inter-Process Communication (IPC)**: Mechanisms like pipes or queues to exchange data between processes.
4. **Shared Memory**: Variables shared across processes using `multiprocessing.Value` or `multiprocessing.Array`.

## Why Use Multiprocessing?
- **Overcomes GIL**: Allows true parallel execution for CPU-bound tasks.
- **Efficient for CPU-intensive Work**: Great for tasks like numerical computations, data processing, and machine learning model training.
- **Scales with CPU Cores**: Utilizes multiple cores for faster execution.

## Practical Examples
### 1. **Simple Example: Multiprocessing with** `Process`
Each process executes a separate function in parallel.

```python
from multiprocessing import Process
import time

def task(name):
    print(f"Starting task {name}")
    time.sleep(2)  # Simulate some work
    print(f"Task {name} completed")

if __name__ == "__main__":
    processes = []

    # Create multiple processes
    for i in range(3):
        p = Process(target=task, args=(f"Process-{i}",))
        processes.append(p)
        p.start()

    # Wait for all processes to finish
    for p in processes:
        p.join()

    print("All tasks completed!")
```

**Explanation**:

1. Each `Process` runs the `task` function independently.
2. `p.start()` starts the process, and `p.join()` waits for it to finish.
3. Tasks run in parallel, so the total runtime is approximately the duration of the longest task.

### 2. **Using** `Pool` **for Parallel Execution**
The `Pool` class simplifies managing multiple processes and distributing tasks.
```python
from multiprocessing import Pool

def square_number(n):
    return n * n

if __name__ == "__main__":
    numbers = [1, 2, 3, 4, 5]
    with Pool(processes=3) as pool:
        results = pool.map(square_number, numbers)

    print("Squared numbers:", results)
```
**Explanation**:

1. The `Pool` distributes the `square_number` function to multiple processes.
2. The `pool.map()` method applies the function to each item in the list.
3. Processes run concurrently, utilizing multiple cores.

**Output**:
```python
Squared numbers: [1, 4, 9, 16, 25]
```
### 3. **Sharing Data Between Processes**
Use shared memory for processes to communicate.
```python
from multiprocessing import Process, Value

def increment(shared_value):
    for _ in range(1000):
        with shared_value.get_lock():  # Ensure no race condition
            shared_value.value += 1

if __name__ == "__main__":
    shared_value = Value('i', 0)  # Shared integer
    processes = [Process(target=increment, args=(shared_value,)) for _ in range(5)]

    for p in processes:
        p.start()

    for p in processes:
        p.join()

    print("Final value:", shared_value.value)
```
**Explanation**:

1. `Value` creates a shared integer accessible by all processes.
2. The `get_lock()` method ensures that only one process modifies the value at a time, preventing race conditions.
3. Each process increments the value in parallel.

### 4. **CPU-Bound Task: Calculating Factorials**
```python
from multiprocessing import Pool
import math

def calculate_factorial(n):
    return math.factorial(n)

if __name__ == "__main__":
    numbers = [100_000, 200_000, 300_000]
    with Pool(processes=3) as pool:
        results = pool.map(calculate_factorial, numbers)

    print("Factorials calculated")
```
**Explanation**:

- Each number's factorial is computed by a separate process.
- Tasks execute in parallel, significantly reducing total computation time.

### 5. **Using Queues for Inter-Process Communication**
```python
from multiprocessing import Process, Queue

def producer(q):
    for i in range(5):
        q.put(i)
        print(f"Produced: {i}")

def consumer(q):
    while not q.empty():
        item = q.get()
        print(f"Consumed: {item}")

if __name__ == "__main__":
    queue = Queue()
    p1 = Process(target=producer, args=(queue,))
    p2 = Process(target=consumer, args=(queue,))

    p1.start()
    p1.join()  # Ensure producer finishes first

    p2.start()
    p2.join()
```
**Explanation**:

- The producer process puts items in the queue.
- The consumer process retrieves and processes items.
- The queue facilitates communication between processes.

## Advantages of Multiprocessing
1. **True Parallelism**: Utilizes multiple CPU cores effectively.
2. **Bypasses GIL**: Ideal for CPU-intensive tasks.
3. **Flexible Communication**: Supports shared memory, queues, and pipes.

## When Not to Use Multiprocessing
1. For I/O-bound tasks (use asynchronous programming instead).
2. When the overhead of process creation exceeds the task's computational cost.
3. If the task requires frequent communication between processes, which can be slower.