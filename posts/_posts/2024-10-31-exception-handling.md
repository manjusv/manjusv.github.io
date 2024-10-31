---
title: "Exception Handling in Python"
categories:
  - exception handling
  - python
tags:
  - exception handling
---
## What is Exception Handling?
Exception handling in Python involves catching and managing errors that occur during program execution. Without handling exceptions, an error can cause a program to crash. By using exception handling, you can control how errors are managed and prevent them from interrupting your program flow.

## The try and except Block
The **try** block contains the code that may raise an exception. If an exception occurs, the code in the except block runs to handle the error.

**Example**:
```python
try:
    result = 10 / 0  # This will raise a ZeroDivisionError
except ZeroDivisionError:
    print("Cannot divide by zero!")
```
**Output**:
```python
Cannot divide by zero!
```
Here, if **10 / 0** raises a **ZeroDivisionError**, the code in the **except** block runs, and a message is printed instead of the program crashing.

## The else Block
The else block runs if the try block completes without raising any exceptions. It’s useful for code that should only execute if no exceptions were encountered.

**Example**:
```python
try:
    result = 10 / 2  # This will succeed
except ZeroDivisionError:
    print("Cannot divide by zero!")
else:
    print("Division successful! Result:", result)
```
**Output**:
```python
Division successful! Result: 5.0
```
In this case, since no exception was raised, the else block runs, indicating the successful execution of the division.

## The finally Block
The **finally** block always runs, regardless of whether an exception was raised. It’s often used for cleanup actions, such as closing files or releasing resources.

**Example**:
```python
try:
    file = open("example.txt", "r")
    content = file.read()
except FileNotFoundError:
    print("File not found.")
finally:
    file.close()  # Ensures the file is closed whether an error occurred or not
    print("File closed.")
```
**Output**:
```python
File not found.
File closed.
```
In this example, the **finally** block ensures that the file is closed whether the file was found or not.

## Custom Exceptions
You can define custom exceptions in Python by creating a new class that inherits from Python's built-in Exception class. This is useful when you want to define application-specific errors that can be caught and handled separately.

**Example of a Custom Exception**
```python
class NegativeNumberError(Exception):
    pass

def calculate_square_root(value):
    if value < 0:
        raise NegativeNumberError("Cannot calculate the square root of a negative number")
    return value ** 0.5

try:
    result = calculate_square_root(-9)
except NegativeNumberError as e:
    print(e)
```
**Output**:
```python
Cannot calculate the square root of a negative number
```
In this example, a custom **NegativeNumberError** is raised when trying to calculate the square root of a negative number. The error is caught and printed.

## Chaining Exceptions
Python allows chaining exceptions using the raise keyword to provide more context about an error in a multi-layered setup.

**Example of Exception Chaining**
```python
class DatabaseConnectionError(Exception):
    pass

def connect_to_database():
    raise DatabaseConnectionError("Could not connect to the database")

try:
    connect_to_database()
except DatabaseConnectionError as e:
    raise RuntimeError("Application could not proceed due to database issues") from e
```

## Advantages and Disadvantages of Exception Handling
### Advantages
- **Graceful Error Handling**: Prevents program crashes and gives meaningful feedback to users.
- **Error Propagation**: Allows errors to bubble up to higher levels of code where they can be handled.
- **Code Organization**: Separates normal logic from error-handling logic, making the code cleaner and easier to read.

### Disadvantages
- **Performance Overhead**: Exception handling can slow down code execution.
- **Complexity**: If overused, exception handling can make code more complex and harder to maintain.

## Complete Example Using try, except, else, finally, and Custom Exception
```python
class InsufficientFundsError(Exception):
    """Custom exception for insufficient funds in an account."""
    pass

def withdraw_money(balance, amount):
    try:
        if amount > balance:
            raise InsufficientFundsError("Insufficient funds for withdrawal.")
        balance -= amount
    except InsufficientFundsError as e:
        print("Error:", e)
    else:
        print("Withdrawal successful. Remaining balance:", balance)
    finally:
        print("Transaction completed.")

# Testing the function
withdraw_money(500, 600)
```
**Output**:
```python
Error: Insufficient funds for withdrawal.
Transaction completed.
```
In this example, **InsufficientFundsError** is used to handle insufficient balance in a bank account. The **else** block executes if the withdrawal is successful, and the **finally** block confirms transaction completion regardless of the outcome.

## Summary
Python's try, except, else, and finally blocks provide powerful tools for managing errors. Custom exceptions enable more specific error handling and maintain the clarity and robustness of your code.