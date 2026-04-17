|| Shree Ganeshay Namh ||

## Table of Contents

- [Data Structure Ordering](#data-structure-ordering)
  - [Lists are Ordered](#1-lists-are-ordered)
  - [When you might want "Unordered" behavior](#2-when-you-might-want-unordered-behavior)
  - [The "Unordered" Confusion: Sets and Dictionaries](#3-the-unordered-confusion-sets-and-dictionaries)
- [Arrays, Strings and Lists](#arrays-strings-and-lists)
  - [Array + List](#1-array--list)
    - [How List is internally implemented in Python](#how-list-is-internally-implemented-in-python)
  - [Dictionaries](#2-dictionaries)
  - [Set](#3-set)
    - [Removing duplicates](#removing-duplicates)
    - [Membership Testing (The Speed Trap)](#membership-testing-the-speed-trap)
    - [Quick Comparison (List, Tuple, Set)](#quick-comparison-list-tuple-set)
- [CRUD Operations](#crud-operations)
- [Python & C# Comparison](#python--c-comparison)

---

## Data Structure Ordering

| Data Type | Is it Ordered? | Python Version Change |
|-----------|---------------|----------------------|
| List | ✅ Yes | Always has been. |
| Tuple | ✅ Yes | Always has been. |
| Dictionary | ✅ Yes | Changed (Ordered since 3.7+). |
| Set | ❌ No | Remains Unordered. |


In Python, a **list is strictly an ordered collection**. This means the items have a defined order that will not change unless you explicitly modify the list (by sorting, appending, or deleting).

However, the term "unordered" can be confusing because it is used differently depending on whether you are talking about **memory** or **logical behavior**.

### 1. Lists are Ordered
When you create a list, the elements are stored in a specific sequence, and that sequence is maintained by an **index**.
```python
my_list = ["Apple", "Banana", "Cherry"]
# "Apple" is always at index 0
# "Banana" is always at index 1
```
If you print this list 100 times, it will always appear in that exact order. This is what "ordered" means in Python.

---

### 2. When you might want "Unordered" behavior
If you are looking for a collection where the order doesn't matter (or where the computer decides the order for performance reasons), you should use a **Set**.

| Feature | List (Ordered) | Set (Unordered) |
| :--- | :--- | :--- |
| **Syntax** | `[1, 2, 3]` | `{1, 2, 3}` |
| **Duplicates** | Allowed | Not Allowed |
| **Indexing** | `list[0]` works | `set[0]` throws an error |
| **Lookup Speed** | $O(n)$ (Slow) | $O(1)$ (Very Fast) |



---

### 3. The "Unordered" Confusion: Sets and Dictionaries
* **Sets:** These are truly unordered. When you add items to a set, Python uses a hash table to decide where they go. You cannot rely on the order they appear when printed.
* **Dictionaries:** Historically, these were unordered. However, since **Python 3.7+**, dictionaries maintain **insertion order** (they remember which key was added first).



# Arrays, Strings and Lists

## 1. Array + List:
A List is a built-in, highly flexible tool, while a true Array usually refers to the array module or NumPy arrays.

```python
# A Python List
my_list = [10, "Hello", 3.14, True] 
my_list.append(100) # Easy to resize

import array
# 'i' stands for signed integer
my_array = array.array('i', [1, 2, 3, 4])

# 'u' is the type code for Unicode characters
char_array = array.array('u', 'Hello')

# Add a character
char_array.fromunicode(' World')

print(char_array) 
# Output: array('u', 'Hello World')

import numpy as np

# Creates an array specifically for strings
my_array = np.array(["Apple", "Banana", "Cherry"], dtype='U')
```

**Note on NumPy**: If you are doing Data Science or heavy Math, you shouldn't use list or array. You should use NumPy Arrays (np.array), which are the industry standard for performance and vector operations.

| Feature | Python List | Python Array (array module) |
|---------|-------------|----------------------------|
| Data Types | Can be mixed (int, str, etc.) | Must be the same type. |
| Flexibility | High (very easy to use) | Low (restricted to basic types) |
| Memory Efficiency | Lower (due to pointer overhead) | Higher (stores raw bytes) |
| Import Required? | No (Built-in) | Yes (import array) |
| Mathematical Ops | Not built-in | Basic (but usually use NumPy) |

### How List is internally implemented in Python

In Python (specifically CPython, the most common implementation), a **list** is not actually a linked list, despite the name. It is implemented as a **dynamic array**. 

Internally, it is a C structure that contains a pointer to an array of pointers, where each pointer in that array points to a Python object.

---

#### 1. The C Structure
Under the hood, a list is represented by the `PyListObject` struct. It tracks three main things:
* **Ob_item:** A pointer to the block of memory where the actual array of pointers is stored.
* **Allocated:** The total number of slots available in current memory.
* **Size:** The number of items currently in the list (the "length").

---

#### 2. Over-allocation (Dynamic Resizing)
To avoid resizing the array every time you call `.append()`, Python uses a strategy called **over-allocation**. 

When you add an item and the list is full, Python allocates a new, larger block of memory—usually more than it currently needs. The growth pattern follows a specific formula:
$$M = (N >> 3) + (N < 9 ? 3 : 6)$$
Where $N$ is the current size and $M$ is the extra slots allocated. This ensures that the time complexity for appending items remains **Amortized $O(1)$**.

---

#### 3. Pointer-Based Architecture
Unlike arrays in languages like C++ or Java where elements are stored contiguously in memory, a Python list stores **pointers** to objects.
* The array of pointers is contiguous.
* The actual objects (integers, strings, etc.) can be scattered anywhere in the heap.
This is why a single Python list can hold multiple data types (heterogeneous) simultaneously.

---

#### 4. Time Complexity of Operations
Understanding the dynamic array implementation explains why certain operations are faster than others:

| Operation | Complexity | Reason |
| :--- | :--- | :--- |
| **Get Item** | $O(1)$ | Direct access via index calculation. |
| **Append** | $O(1)$ | Amortized due to over-allocation. |
| **Insert/Delete** | $O(n)$ | Requires shifting all subsequent elements in memory. |
| **Pop (End)** | $O(1)$ | Just decreases the "Size" counter. |
| **Pop (Start)** | $O(n)$ | Requires shifting every single remaining item forward. |

#### Why does this matter for your work?
If you are building an enterprise system (like a high-volume inventory tracker), frequently inserting or deleting items from the *start* or *middle* of a list will cause performance bottlenecks as the list grows. In those cases, a `collections.deque` (double-ended queue) is often a better choice because it is a true linked list and offers $O(1)$ performance for operations at either end.

---

## 2. Dictionaries:
- stores data in key-value pairs.

### Core Characteristics
- **Key-Value Pairs**: Every entry has a unique key that maps to a specific value.
- **Ordered**: Since Python 3.7+, dictionaries maintain the order in which items are inserted.
- **Mutable**: You can add, remove, or change values after the dictionary is created.
- **Unique Keys**: Keys must be unique and "hashable" (immutable types like strings, numbers, or tuples). Values, however, can be anything.

```python
# Creating a dictionary
user = {
    "name": "Alice",
    "age": 25,
    "is_admin": False,
    "skills": ["Python", "SQL"]
}

# Accessing a value
print(user["name"])  # Output: Alice

# Adding a new pair
user["email"] = "alice@example.com"

# 1. Using del
del graph["Satara"]

# 2. Using pop (Safe way)
# Providing a second argument (None) prevents an error if the key doesn't exist
removed_value = graph.pop("Goa", None) 

# 3. Using popitem (LIFO order)
last_added = graph.popitem()

# Standard way
config = {"env": "prod", "version": "1.0", "region": "IN"}

for key in config:
    print(key) 

# Explicitly using .keys()
for key in config.keys():
    print(key)

for value in config.values():
    print(value)
# Output: prod, 1.0, IN

for key, value in config.items():
    print(f"Option: {key} | Value: {value}")

for key in sorted(config):
    print(f"{key}: {config[key]}")
```

Performance Tip
If you need to modify a dictionary (add or remove keys) while looping, do not loop over the dictionary itself. Python will raise a RuntimeError. Instead, loop over a copy of the keys:

```python
# Safe way to delete items during a loop
for key in list(config.keys()):
    if key == "env":
        del config[key]
```
This ensures you aren't changing the size of the collection while the iterator is moving through it. Are you building a script that needs to filter or transform large JSON-like dictionaries?

In Data Structures and Algorithms, the dictionary is Python's implementation of a Hash Map.

### The Speed Advantage
The most important thing to know for interviews is the Time Complexity:
- **Access/Search**: $O(1)$ — Average time.
- **Insertion**: $O(1)$ — Average time.
- **Deletion**: $O(1)$ — Average time

| Method | Description |
|--------|-------------|
| .get(key, default) | Returns the value for a key. If the key doesn't exist, it returns a default value instead of crashing. |
| .keys() | Returns a view of all keys in the dictionary. |
| .values() | Returns a view of all values in the dictionary. |
| .items() | Returns a view of all key-value tuples (useful for looping). |
| .pop(key) | Removes the key and returns its value. |




## 3. Set
**Uniqueness, Unordered, Mutable, Highly Efficient $O(1)$ time complexity)**

| Operation | Operator | Description |
|-----------|----------|-------------|
| Union | set1 | set2 | All unique items from both sets. |
| Intersection | set1 & set2 | Only items present in both sets. |
| Difference | set1 - set2 | Items in set1 that are not in set2. |
| Symmetric Difference | set1 ^ set2 | Items in either set, but not both. |

```python
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

# Union: All unique items
union_res = set1 | set2          # Method: set1.union(set2)
# Result: {1, 2, 3, 4, 5, 6}

# Intersection: Only common items
inter_res = set1 & set2          # Method: set1.intersection(set2)
# Result: {3, 4}

# Difference: In set1 but NOT in set2
diff_res = set1 - set2           # Method: set1.difference(set2)
# Result: {1, 2}

# Symmetric Difference: In either, but not both
symm_res = set1 ^ set2           # Method: set1.symmetric_difference(set2)
# Result: {1, 2, 5, 6}
```

``` python
# Creating a set
fruits = {"apple", "banana", "cherry"}
# Adding an item
fruits.add("orange")
# Adding a duplicate (will be ignored)
fruits.add("apple") 
# Removing an item
fruits.remove("banana")
print(fruits) # Output: {'apple', 'cherry', 'orange'} (order may vary)
```

| Feature | List | Set | Dictionary |
|---------|------|-----|------------|
| Duplicates | Allowed | Not Allowed | Keys must be unique |
| Ordered | Yes | No | Yes (since 3.7) |
| Lookup Speed | Slow ($O(n)$) | Fast ($O(1)$) | Fast ($O(1)$) |
| Access Method | Index (e.g., [0]) | Membership (in) | Key (e.g., ['id']) |

### Removing duplicates:
```python
numbers = [1, 2, 2, 3, 4, 4, 4]
unique_numbers = list(set(numbers)) # Result: [1, 2, 3, 4]
```

### Membership Testing (The Speed Trap)
If you need to check if a value exists inside a collection multiple times (e.g., inside a loop), never use a list.
- **List Search**: $O(n)$ — Python has to look at every item one by one.
- **Set Search**: $O(1)$ — Python uses a hash table to find the item instantly.

## Quick Comparison (List, Tuple, Set)

In Python, **List**, **Tuple**, and **Set** are the most common built-in data types used to store collections of data. While they might seem similar at first glance, they behave very differently regarding order, changeability, and how they handle duplicate values.

Here is a breakdown of their primary differences:

| Feature | List | Tuple | Set |
| :--- | :--- | :--- | :--- |
| **Syntax** | `[1, 2, 3]` | `(1, 2, 3)` | `{1, 2, 3}` |
| **Ordering** | Ordered | Ordered | Unordered |
| **Mutability** | Mutable (Can change) | Immutable (Cannot change) | Mutable |
| **Duplicates** | Allows duplicates | Allows duplicates | No duplicates allowed |
| **Indexing** | Supported | Supported | Not supported |

---

### 1. List `[]`
A list is an **ordered** collection that is **mutable**, meaning you can add, remove, or change items after the list has been created. Lists are the "workhorse" of Python collections because of their flexibility.

* **Best for:** Collections where the order matters and you expect to modify the data frequently.
* **Example:** A shopping list or a list of tasks where items can be added or checked off.

### 2. Tuple `()`
A tuple is an **ordered** collection that is **immutable**. Once a tuple is created, you cannot change its contents (no adding, removing, or replacing). Because they are fixed, tuples are generally faster than lists and are safer for data that should not be tampered with.

* **Best for:** Data that stays constant throughout a program, like the latitude and longitude of a city or the RGB values of a specific color.
* **Example:** `point = (10, 20)`

### 3. Set `{}`
A set is an **unordered** collection of **unique** items. This means a set will automatically filter out any duplicate values you try to put into it. Because they are unordered, sets do not support indexing or slicing.

* **Best for:** Membership testing (checking if an item exists in a group), removing duplicates from another collection, and performing mathematical operations like unions or intersections.
* **Example:** A list of unique user IDs or a collection of tags for a blog post.

---

### When to use which?

* Use a **List** if you need a sequence of items that might change.
* Use a **Tuple** when you have a sequence of items that will never change; it also acts as a "hint" to other developers that the data is constant.
* Use a **Set** when you need to ensure every item is unique and the specific order of those items doesn't matter.

## CRUD Operations
Here is the complete code implementation for **List**, **Tuple**, and **Set**, covering the full lifecycle of data manipulation in Python.

---

### 1. List (Mutable & Ordered)
Lists are ideal when you need an ordered sequence that you can change frequently.

```python
# --- CREATE ---
frameworks = ["ASP.NET Core", "Angular"]

# --- GET (READ) ---
item = frameworks[0]  # "ASP.NET Core"

# --- ADD ---
frameworks.append("React")      # Adds to the end
frameworks.insert(1, "Svelte")  # Inserts at index 1

# --- UPDATE ---
frameworks[2] = "Vue.js"        # Changes "Angular" to "Vue.js"

# --- REMOVE / DELETE ---
frameworks.remove("Svelte")     # Removes by value
deleted_item = frameworks.pop(0) # Removes by index and returns it
del frameworks[0]               # Direct deletion

# --- LOOPING ---
for tool in frameworks:
    print(f"Framework: {tool}")

# Loop with index
for i, tool in enumerate(frameworks):
    print(f"Index {i}: {tool}")
```

---

### 2. Tuple (Immutable & Ordered)
Tuples are best for fixed data. Because they cannot be changed, they are slightly faster and more memory-efficient.

```python
# --- CREATE ---
coordinates = (18.5204, 73.8567) # Pune Lat/Long

# --- GET (READ) ---
lat = coordinates[0]

# --- ADD / UPDATE / REMOVE ---
# Tuples are immutable! To "change" them, you must create a new one.
coordinates = coordinates + (560,) # Technically creates a new tuple

# --- LOOPING ---
for coord in coordinates:
    print(f"Coordinate value: {coord}")
```

---

### 3. Set (Mutable & Unordered)
Sets are unique. They are the go-to choice for removing duplicates or checking membership quickly.

```python
# --- CREATE ---
cloud_providers = {"AWS", "Azure", "GCP"}

# --- GET (READ) ---
# No indexing! You check for existence instead:
exists = "AWS" in cloud_providers # True

# --- ADD ---
cloud_providers.add("DigitalOcean")
cloud_providers.add("AWS") # Does nothing (already exists)

# --- UPDATE ---
# You can't update an index. You remove and then add.
cloud_providers.discard("GCP")
cloud_providers.add("Oracle Cloud")

# --- REMOVE / DELETE ---
cloud_providers.remove("Azure")   # Errors if not found
cloud_providers.discard("Azure")  # Safe, no error if missing

# --- LOOPING ---
# Order will be unpredictable!
for provider in cloud_providers:
    print(f"Provider: {provider}")
```

---

### Quick Summary of Methods

| Operation | List | Tuple | Set |
| :--- | :--- | :--- | :--- |
| **Get** | `list[i]` | `tuple[i]` | `val in set` |
| **Add** | `.append(x)` | `tup + (x,)` | `.add(x)` |
| **Update** | `list[i] = x` | N/A (Immutable) | `.discard()` then `.add()` |
| **Delete** | `.pop(i)`, `.remove(x)` | `del tup` (whole) | `.remove(x)`, `.discard(x)` |
| **Looping** | Ordered | Ordered | **Unordered** |

When building performance-heavy applications—like an enterprise inventory system or a microservices-based API—choosing a **Set** for lookups can be significantly more efficient than a **List**, as set lookups are generally $O(1)$ compared to $O(n)$ for lists.

---

Since you have a strong background in .NET and are now working with Python, comparing these data types is a great way to map your existing knowledge to the new ecosystem.

## Python & C# Comparison
The most significant shift is moving from C\#'s **static, strongly-typed** system to Python's **dynamic** system. Here is how they compare across the board:

### 1\. Simple Data Types

| Python Type | C\# Equivalent | Notes |
| :--- | :--- | :--- |
| `int` | `int`, `long`, `BigInteger` | Python `int` has arbitrary precision (it grows as needed). |
| `float` | `double` | Python floats are 64-bit double precision by default. |
| `bool` | `bool` | In Python, `True` and `False` are capitalized. |
| `str` | `string` | Python strings are Unicode and immutable, just like in C\#. |
| `None` | `null` | Used to represent the absence of a value. |

-----

### 2\. Collection Types (The "Big Four")

The way Python handles collections is very similar to C\#, but with a few key differences in naming and mutability.

| Python Type | C\# Equivalent | Key Difference |
| :--- | :--- | :--- |
| **`list`** | `List<T>` | Python lists are heterogeneous (can hold mixed types) and dynamic. |
| **`tuple`** | `(T1, T2)` / `ValueTuple` | Python tuples are **immutable**. C\# `ValueTuple` is mutable. |
| **`dict`** | `Dictionary<TKey, TValue>` | Python dicts are ordered by insertion (since 3.7+). |
| **`set`** | `HashSet<T>` | Both ensure unique items and offer $O(1)$ lookup time. |

-----

### 3\. Deep Dive: Implementation Differences

#### Lists vs. `List<T>`

  * **Python:** A list is an array of pointers to objects. This allows a single list to contain an `int`, a `str`, and a `custom_object` all at once.
  * **C\#:** `List<T>` is a contiguous block of memory for a specific type. It is generally more memory-efficient because it avoids the "pointer overhead" for value types like integers.

#### Tuples: Immutability vs. Performance

  * **Python:** Used heavily for returning multiple values from functions. Because they are immutable, they can be used as **keys in a dictionary**, which a list cannot.
  * **C\#:** Modern C\# `(var1, var2)` syntax creates a `ValueTuple`, which is a `struct` (value type) and is actually mutable. Python's tuple is closer in spirit to the older `System.Tuple` class but much more lightweight.

#### Dictionaries vs. `Dictionary<K, V>`

  * **Python:** Dictionaries are highly optimized and use a hash table. Since Python 3.6+, they also track the order in which items were added, similar to an `OrderedDictionary` in .NET but with the performance of a standard one.

-----

### 4\. Looping & LINQ vs. Comprehensions

As a .NET developer, you likely use **LINQ** for data transformation. In Python, you will use **List Comprehensions**, which are often faster and more readable for simple filters.

  * **C\# (LINQ):**
    ```csharp
    var results = myList.Where(x => x > 10).Select(x => x * 2).ToList();
    ```
  * **Python (Comprehension):**
    ```python
    results = [x * 2 for x in my_list if x > 10]
    ```

### Summary

When moving from C\# to Python, the biggest "gotcha" isn't the data types themselves, but the lack of compile-time type checking. While you'll miss the safety of `List<User>`, you'll likely enjoy the speed of prototyping that comes with Python's flexible collections.
