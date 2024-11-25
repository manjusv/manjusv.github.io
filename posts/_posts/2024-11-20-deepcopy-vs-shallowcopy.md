---
title: "Shallow Copy vs Deep Copy in Python"
categories:
  - python
tags:
  - shallow copy
  - deep copy
---

In Python, shallow copy and deep copy are ways of copying objects, but they differ in how they handle nested objects like lists or dictionaries within the copied object.

## Shallow Copy
A shallow copy creates a new object but does not create copies of the objects that are contained within it. Instead, it references the original nested objects.

- **Behavior**:
    - The outer object is duplicated.
    - Inner objects (e.g., lists, dictionaries) are still references to the same objects in memory.

- **Use Case**:
    - Use a shallow copy when the inner objects don't need to be modified independently of the original.

- **How to Create**:
    - Using the `copy` module's `copy()` function.
    - Using the `.copy()` method for certain collections (like lists or dictionaries).
    - Using slicing for lists (`new_list = old_list[:]`).

**Example**:
```python
import copy

original_list = [[1, 2, 3], [4, 5, 6]]
shallow_copy = copy.copy(original_list)

# Modifying inner list in shallow copy
shallow_copy[0][0] = 99

print("Original List:", original_list)  # [[99, 2, 3], [4, 5, 6]]
print("Shallow Copy:", shallow_copy)    # [[99, 2, 3], [4, 5, 6]]
```
**Explanation**:

Modifying the inner list in shallow_copy affects original_list because both point to the same inner objects.

## Deep Copy
A deep copy creates a new object along with recursively copying all objects within it. The new object is entirely independent of the original.

- **Behavior**:

    - Both the outer object and all nested objects are duplicated.

- **Use Case**:
    - Use a deep copy when changes to the copied object (including its inner objects) should not affect the original object.

- **How to Create**:

    - Using the `copy` module's `deepcopy()` function.

**Example**:
```python
import copy

original_list = [[1, 2, 3], [4, 5, 6]]
deep_copy = copy.deepcopy(original_list)

# Modifying inner list in deep copy
deep_copy[0][0] = 99

print("Original List:", original_list)  # [[1, 2, 3], [4, 5, 6]]
print("Deep Copy:", deep_copy)          # [[99, 2, 3], [4, 5, 6]]
```
**Explanation**:

- Modifying the inner list in deep_copy does not affect original_list because all inner objects were copied.

## Comparison Table

<table border="1" style="border-collapse: collapse; width: 100%;">
    <thead>
        <tr style="background-color: #f2f2f2;">
            <th>Fature</th>
            <th>Shallow Copy</th>
            <th>Deep Copy</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Outer Object</td>
            <td>Copied</td>
            <td>Copied</td>
        </tr>
        <tr>
            <td>Inner Objects</td>		
            <td>References to original</td>
            <td>Completely new copies</td>
        </tr>
        <tr>
            <td>Impact of Changes</td>		
            <td>Changes in inner objects affect original</td>
            <td>Changes in inner objects do not affect original</td>
        </tr>
        <tr>
            <td>Performance</td>
            <td>Faster</td>
            <td>Slower</td>
        </tr>
    </tbody>
</table>

## Practical Use Case Examples
### Use Case for Shallow Copy
- **Scenario**: A list of employees with unique IDs but the same default settings.
- **Reason**: You don’t need to copy the inner object (settings), as it’s shared across all employees.

```python
default_settings = {"theme": "dark", "notifications": True}
employees = [{"id": 1, "settings": default_settings}, {"id": 2, "settings": default_settings}]

shallow_copied_employees = employees.copy()

shallow_copied_employees[0]["settings"]["theme"] = "light"
print(employees[0]["settings"]["theme"])  # Output: light (shared settings modified)
```
### Use Case for Deep Copy
- **Scenario**: A simulation where each user’s settings evolve independently over time.
- **Reason**: The inner objects (settings) should not be shared.

```python
import copy

default_settings = {"theme": "dark", "notifications": True}
employees = [{"id": 1, "settings": default_settings}, {"id": 2, "settings": default_settings}]

deep_copied_employees = copy.deepcopy(employees)

deep_copied_employees[0]["settings"]["theme"] = "light"
print(employees[0]["settings"]["theme"])  # Output: dark (original unaffected)
```

## Final Thoughts
- Use shallow copy when:
    - Performance is critical.
    - You want to share inner objects between copies.
- Use deep copy when:
    - Independence of all objects is essential.
    - Modifications in the copy should not affect the original.
