---
title: "Decorators in Python"
categories:
  - Decorators
tags:
  - decorators
  - python
---

## What are Decorators in Python?
In Python, a decorator is a function that takes another function (or method) as input, extends or modifies its behavior, and then returns the modified function. They are a powerful feature in Python that allows us to add functionality to an existing function without changing its structure or writing the extra logic directly into it.

In code, decorators are often seen with the @ symbol above the function we want to decorate.

## Why are Decorators Needed in Python and What Problem Do They Solve?
Decorators are needed because they allow separation of concerns and reusability. Often, we may want to add functionality to multiple functions, like logging, checking permissions, or timing the function’s execution. Instead of writing this code repeatedly in every function, decorators let us wrap this logic around any function, allowing for cleaner and more readable code.

For example, if you have 10 functions in an application that need to log every time they run, instead of adding logging code inside each function, you can use a decorator to handle logging around each function call.

## Advantages and Disadvantages of Decorators
**Advantages**:

- **Code Reusability**: Helps avoid repeating common functionality.
- **Separation of Concerns**: Keeps main logic separate from additional functionality (like logging).
- **Readability**: Makes code cleaner by removing boilerplate code from functions.

**Disadvantages**:

- **Complexity**: Can make code harder to understand for beginners.
- **Debugging**: Since decorators modify functions, it can be harder to debug and trace issues.
- **Performance**: If misused, decorators could potentially slow down function execution.

## Layman Explanation of Decorators with Real-Time Examples
Imagine you’re a coffee shop owner. Customers can order different types of coffee, like a latte or cappuccino. Sometimes, they may also want add-ons like whipped cream or extra espresso shots.

Instead of creating separate recipes for each combination, you make one recipe for each coffee and then apply decorators for the extras. When someone orders a latte with whipped cream, you make the latte and decorate it with whipped cream before serving it. This way, you keep the base recipe intact and add decorations (features) as needed.

Similarly, decorators in Python add extra functionality to functions without changing the original function code.

## Examples with Code Explanations
**Basic Decorator Example**

Here’s how a simple decorator works in Python:
```python
def my_decorator(func):
    def wrapper():
        print("Something is happening before the function is called.")
        func()
        print("Something is happening after the function is called.")
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

say_hello()
```
**Explanation**:

1. my_decorator is a decorator function that takes func (another function) as an argument.
2. Inside my_decorator, we define an inner function called wrapper, which wraps additional code around func.
3. The @my_decorator syntax applies the decorator to the say_hello function.
4. When say_hello() is called, my_decorator executes extra code both before and after the original function (say_hello).


**Real-World Use: Logging with Decorators**

Let’s say you have multiple functions and want to log each time they run.
```python
def log_decorator(func):
    def wrapper(*args, **kwargs):
        print(f"Calling function '{func.__name__}' with arguments {args} and {kwargs}")
        result = func(*args, **kwargs)
        print(f"Function '{func.__name__}' returned {result}")
        return result
    return wrapper

@log_decorator
def add(a, b):
    return a + b

@log_decorator
def subtract(a, b):
    return a - b

add(10, 5)
subtract(20, 8)
```
**Explanation**:

- The log_decorator adds logging functionality to add and subtract functions, printing their names and arguments before calling them.
- This makes it easier to track function calls and their results without modifying the actual add and subtract code.

**Measuring Execution Time with a Decorator**

A common need is to measure how long a function takes to execute. Here’s a decorator that does this:
```python
import time

def timer_decorator(func):
    def wrapper(*args, **kwargs):
        start_time = time.time()
        result = func(*args, **kwargs)
        end_time = time.time()
        print(f"Function '{func.__name__}' executed in {end_time - start_time:.4f} seconds")
        return result
    return wrapper

@timer_decorator
def compute_squares(n):
    return [i ** 2 for i in range(n)]

compute_squares(10000)
```
**Explanation:**

- timer_decorator measures the time before and after calling compute_squares.
- It prints the execution time, which can be useful for optimizing functions.


**Decorators with Arguments**

If we need a decorator to accept arguments, we add another layer of functions:
```python
def repeat(num_times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(num_times):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(3)
def greet():
    print("Hello!")

greet()
```
**Explanation:**

- repeat is a decorator factory that accepts num_times as an argument.
- It returns a decorator function, which itself returns the wrapper function that repeats the decorated function num_times times.


**Advantages and Disadvantages in Practice**
**Advantages:**

- Reduces repetition of common logic, making code cleaner.
- Allows functionality to be added or removed without modifying the main function logic.

**Disadvantages:**

- Can make code less transparent, especially for developers who aren’t familiar with decorators.
- May make debugging harder because errors might appear in the wrapper rather than the main function.

## Summary
Decorators provide a flexible way to add functionality to functions in Python, similar to adding features to a standard recipe without changing the base. They help in separating concerns, improving readability, and reusing code effectively.
