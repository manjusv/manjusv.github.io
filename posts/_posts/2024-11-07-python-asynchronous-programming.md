---
title: "Asynchronous Programming: AsyncIO, blocking/non-blocking execution"
categories:
  - Asynchronous Programming
tags:
  - asyncIO
---
## Python Asynchronous Programming: An Overview
Asynchronous programming in Python, primarily achieved through the `asyncio` library, allows code to handle multiple tasks concurrently without traditional multi-threading or multi-processing. This is especially beneficial for tasks that involve waiting for external resources (like network responses) and where it’s unnecessary for code to wait idly until the task is complete.

## Understanding AsyncIO, Blocking, and Non-Blocking Execution
- **AsyncIO**: asyncio is Python’s built-in library for asynchronous programming. It allows code to perform tasks while "waiting" without holding up the entire program. With `asyncio`, functions are written using `async` and `await` keywords, enabling **non-blocking execution**.

- **Blocking vs. Non-Blocking Execution**:

    - **Blocking**: In a blocking operation, code stops and waits for a task to complete before moving to the next one.
    - **Non-blocking**: Here, the code moves on to other tasks instead of waiting, switching back to complete the pending task when it’s ready.


## Layman Explanation of Asynchronous Programming
Think of an asynchronous program as a restaurant with a single chef (like a single-threaded program). If the chef prepares only one dish at a time (blocking execution), each customer has to wait until their dish is ready before the chef starts the next. However, if the chef uses asynchronous programming, they can start preparing a dish and, while waiting for it to bake in the oven, begin another dish. This way, the chef can manage multiple orders efficiently without additional chefs or assistants.

## Key Components of AsyncIO in Python
1. `async` **and** `await` **keywords**: The `async` keyword marks a function as asynchronous, meaning it can be paused and resumed. The `await` keyword pauses the function's execution until a non-blocking task completes.
2. **Event Loop**: The core of `asyncio`, the event loop, runs and schedules asynchronous tasks, switching between them as they await external resources.
3. **Tasks and Coroutines**: In `asyncio`, asynchronous functions are **coroutines** that the event loop can run concurrently. Tasks are created from coroutines to run in the background, facilitating non-blocking execution.

## When to Use Asynchronous Programming
Asynchronous programming is ideal for tasks that involve waiting, such as:

- **Network I/O**: Making API requests, downloading files, or any tasks that rely on network responses.
- **File I/O**: Reading or writing files can benefit from asynchronous handling if they are large or involve multiple files.
- **Database queries**: Many databases support asynchronous queries, which can improve performance when handling multiple database interactions.

## When Not to Use Asynchronous Programming
- **CPU-bound tasks**: Asynchronous programming doesn’t parallelize CPU-bound tasks well; they still run in a single thread. For heavy calculations or data processing, **multi-threading** or **multi-processing** is better.
- **Simple, quick tasks**: If a program is straightforward with minimal I/O, async can add unnecessary complexity.

## Advantages and Disadvantages of Asynchronous Programming
### Advantages:

- **Improved efficiency for I/O-bound tasks**: Asynchronous programs don't block the event loop, maximizing efficiency.
- **Resource-friendly**: It achieves concurrency without multiple threads or processes, which can be resource-intensive.

### Disadvantages:

- **Complexity**: Async programming can make code harder to understand and debug.
- **Not ideal for CPU-bound tasks**: Since it still runs on a single thread, CPU-heavy tasks don't benefit from async.

## Code Examples
### Basic Example: Asynchronous Functions and Event Loop
```python
import asyncio

# Define an async function
async def say_hello(name):
    print(f"Hello, {name}")
    await asyncio.sleep(2)  # Simulate a wait time
    print(f"Goodbye, {name}")

# Run async function using asyncio.run()
asyncio.run(say_hello("Alice"))
```
In this example:

- `say_hello` is an asynchronous function that simulates a delay using `await asyncio.sleep()`.
- `asyncio.run()` starts the event loop to execute the coroutine.

### Running Multiple Tasks Concurrently
```python
import asyncio

async def download_file(file):
    print(f"Starting download for {file}")
    await asyncio.sleep(2)  # Simulate download time
    print(f"Finished downloading {file}")

async def main():
    files = ["file1.pdf", "file2.pdf", "file3.pdf"]
    
    # Run tasks concurrently
    tasks = [download_file(file) for file in files]
    await asyncio.gather(*tasks)

asyncio.run(main())
```
Here:

- `download_file` simulates file downloading.
- `asyncio.gather(*tasks)` runs all download tasks concurrently.

### Using await with Asynchronous API Requests
```python
import asyncio
import aiohttp

async def fetch_data(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.json()

async def main():
    urls = [
        "https://jsonplaceholder.typicode.com/todos/1",
        "https://jsonplaceholder.typicode.com/todos/2"
    ]
    
    tasks = [fetch_data(url) for url in urls]
    results = await asyncio.gather(*tasks)
    
    for result in results:
        print(result)

asyncio.run(main())
```
In this example:

- `aiohttp` is used for making asynchronous HTTP requests.
- `fetch_data` performs non-blocking HTTP GET requests, gathering results concurrently.

## Best Practices for Asynchronous Programming
1. **Avoid Blocking Calls**: Ensure that any function inside an async function does not block. Use `await` for I/O operations.
2. **Use** `asyncio.gather` **for Concurrency**: For running multiple coroutines concurrently, `asyncio.gather` is preferable as it allows easy task management.
3. **Handle Exceptions**: Use `try/except` within async functions to catch and handle exceptions since they won’t necessarily propagate to the main code flow.
4. **Optimize Coroutine Creation**: Avoid creating unnecessary coroutines, as it can lead to performance overhead.
5. **Use Asynchronous Libraries**: For optimal async performance, use libraries like `aiohttp` for HTTP requests, `aiomysql` for MySQL databases, etc., instead of synchronous libraries.

## Example of Error Handling with AsyncIO
```python
import asyncio
import aiohttp

async def fetch_data(url):
    try:
        async with aiohttp.ClientSession() as session:
            async with session.get(url) as response:
                return await response.json()
    except Exception as e:
        print(f"An error occurred: {e}")
        return None

async def main():
    url = "https://jsonplaceholder.typicode.com/todos/1"
    result = await fetch_data(url)
    if result:
        print(result)

asyncio.run(main())
```
In this code:

- `fetch_data` includes error handling to catch exceptions, which can prevent the entire application from failing if one request has an issue.

## Conclusion
Asynchronous programming in Python is highly efficient for I/O-bound tasks, enabling faster and more resource-efficient programs. By using `asyncio` and understanding how to use asynchronous code for concurrent operations, you can build applications that respond quickly without unnecessary delays. Just keep in mind the nature of your tasks: for tasks that involve heavy processing, you may still need traditional multi-threading or multi-processing techniques.