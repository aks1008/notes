# Hash Tables (Map/Sets)

## Table of Contents

- [1. The Core Mechanism: The Hash Table](#1-the-core-mechanism-the-hash-table)
  - [How Hashing Works](#how-hashing-works)
  - [Storage and Retrieval Process](#storage-and-retrieval-process)
  - [Why Use Hash Tables? (O(1) Complexity)](#why-use-them)

- [2. Hash Maps vs. Hash Sets](#2-hash-maps-vs-hash-sets)
  - [Comparison Table](#comparison-table)
  - [Core Differences](#core-differences)
  - [How They Work Under the Hood](#how-they-work-under-the-hood)
    - [HashMap: The Dictionary Approach](#1-hashmap-the-dictionary-approach)
    - [HashSet: The Unique List](#2-hashset-the-unique-list)
  - [When to Use Which?](#when-to-use-which)

- [3. The "Gotcha": Collisions](#3-the-gotcha-collisions)
  - [What are Collisions?](#what-are-collisions)
  - [Collision Handling Methods](#collision-handling-methods)
    - [Chaining](#1-chaining)
    - [Open Addressing](#2-open-addressing)

- [4. Python Implementation](#4-python-implementation)
  - [Dictionaries (The Hash Map)](#1-dictionaries-the-hash-map)
  - [Sets (The Hash Set)](#2-sets-the-hash-set)
  - [The "Hashable" Rule](#3-the-hashable-rule)
  - [How it Works Under the Hood](#how-it-works-under-the-hood)

- [5. Removing Duplicates Efficiently](#5-removing-duplicates-efficiently)
  - [The Problem: List Approach](#the-problem-the-list-approach)
  - [The Solution: Set Approach](#the-solution-the-set-approach)
  - [Practical Example: Big Data Cleaner](#practical-example-the-big-data-cleaner)
  - [Pro-Tip: Preserving Order with dict.fromkeys()](#pro-tip-preserving-order)

- [6. Advantages & Disadvantages](#6-advantages--disadvantages)
  - [Advantages](#advantages)
    - [Speed (The "O(1)" Factor)](#1-speed-the-o1-factor)
    - [Flexibility](#2-flexibility)
    - [Efficiency in De-duplication](#3-efficiency-in-de-duplication)
  - [Disadvantages](#disadvantages)
    - [The "Collision" Penalty](#1-the-collision-penalty)
    - [Memory Overhead](#2-memory-overhead)
    - [Lack of Ordering](#3-lack-of-ordering)
    - [Not Great for "Range" Queries](#4-not-great-for-range-queries)
  - [Summary Comparison](#summary-comparison)

- [7. Building a Hash Table from Scratch](#7-building-a-hash-table-from-scratch)
  - [Implementation Overview](#1-the-implementation)
  - [Key Components Explained](#2-key-components-explained)
    - [The Hash Function](#the-hash-function-_hash_function)
    - [Handling Collisions (Chaining)](#handling-collisions-chaining)
  - [What's Missing from the "Toy" Version](#3-whats-missing-from-this-toy-version)

---

Think of a **Hash Table** as the high-speed filing cabinet of the computer science world. While a standard list makes you look through every folder until you find what you need, a hash table tells you exactly which drawer to open instantly.

Here is the breakdown of how they work and how they form the backbone of **Maps** and **Sets**.

---

## 1. The Core Mechanism: The Hash Table

A hash table stores data in an array format, but it uses a **Hash Function** to decide where that data goes.

* **Hashing:** You provide a "Key" (like a username). The hash function performs a mathematical calculation on that string to turn it into a specific number (an index).
* **Storage:** The data is stored at that index.
* **Retrieval:** When you want the data back, you provide the key again, the function generates the same index, and you jump straight to the data.

### Why use them?

In a perfect world, hash tables offer **O(1) time complexity**. This means whether you have 10 items or 10 million, finding an item takes the same amount of time—nearly instantaneous.

---

## 2. Hash Maps vs. Hash Sets

Most modern programming languages use hash tables to implement two specific data structures: **Maps** and **Sets**.

| Feature | Hash Map (or Dictionary) | Hash Set |
| --- | --- | --- |
| **Data Stored** | **Key-Value Pairs** (e.g., "User ID" : "Profile Data") | **Unique Keys only** (e.g., "User ID") |
| **Purpose** | To look up information associated with a specific label. | To check if something exists and ensure no duplicates. |
| **Example** | A phone book: Name (Key) -> Number (Value). | A guest list: Just a list of unique names. |

While both **HashMap** and **HashSet** are fundamental data structures used for high-performance data retrieval, they serve different purposes and handle data in distinct ways. 

The simplest way to remember the difference: A **HashMap** is for relationships (this belongs to that), while a **HashSet** is for unique collections (is this in the group?).

---

### Core Differences

| Feature | HashMap | HashSet |
| :--- | :--- | :--- |
| **Data Structure** | Implements the **Map** interface. | Implements the **Set** interface. |
| **Storage Style** | Stores data as **Key-Value pairs**. | Stores data as **Objects/Elements**. |
| **Duplicates** | Does not allow duplicate **keys**, but allows duplicate **values**. | Does not allow **duplicate elements** at all. |
| **Internal Mechanism** | Uses hashing to locate a key's bucket. | Internally uses a **HashMap** to store its elements. |
| **Adding Data** | Use the `put(key, value)` method. | Use the `add(element)` method. |

---

### How They Work Under the Hood

#### 1. HashMap: The Dictionary Approach
A HashMap stores entries where every unique key points to a specific value. Think of it like a coat check: the **ticket** is the key, and the **coat** is the value. You can have two different people with identical coats (duplicate values), but they must have unique tickets (unique keys).



#### 2. HashSet: The Unique List
A HashSet is used when you only care whether an item exists or not, and you want to ensure there are no repeats. Interestingly, in languages like Java, a HashSet is actually just a **HashMap** where your data is stored as the "Key," and a dummy object is used as a placeholder for the "Value."

> **Note:** Both structures offer an average time complexity of $O(1)$ for basic operations like adding or searching, making them incredibly efficient for large datasets.

---

#### When to Use Which?

* **Use HashMap when:** You need to look up information based on an identifier. 
    * *Example:* Storing a student's ID (key) and their Grade (value).
* **Use HashSet when:** You need to maintain a list of unique items and don't care about the order. 
    * *Example:* Storing a list of unique IP addresses that have visited a website.

---

## 3. The "Gotcha": Collisions

Sometimes, two different keys might result in the same index after going through the hash function. This is called a **Collision**.

To handle this, hash tables usually use:

1. **Chaining:** The index points to a small linked list of all items that hashed to that spot.
2. **Open Addressing:** The table looks for the next available empty slot nearby.

---

## Summary

* **Hash Table:** The underlying engine that uses math (hashing) to map keys to indices.
* **Map:** A structure that uses a hash table to link a unique key to a specific value.
* **Set:** A structure that uses a hash table to store a collection of unique items with no duplicates.

In Python, hash tables are built directly into the language's core. You don’t have to build the "cabinet" yourself; you just use **Dictionaries** and **Sets**.

Here is how they look in action:

---

## 1. Dictionaries (The Hash Map)

A Python `dict` stores **key-value pairs**. It uses a hash function on the key to find the value's location in memory instantly.

```python
# Creating a Hash Map (Dictionary)
user_scores = {
    "Alice": 95,
    "Bob": 88,
    "Charlie": 92
}

# Retrieval is O(1) - Instant
print(user_scores["Alice"])  # Output: 95

# Adding a new pair
user_scores["Diana"] = 99

```

---

## 2. Sets (The Hash Set)

A Python `set` is essentially a dictionary where there are **no values**, only unique keys. It is incredibly fast for "membership testing" (checking if an item exists).

```python
# Creating a Hash Set
invited_guests = {"Alice", "Bob", "Charlie"}

# Checking existence is O(1)
if "Alice" in invited_guests:
    print("Alice is on the list!")

# Sets automatically handle duplicates
invited_guests.add("Alice") 
print(invited_guests)  # Still only one "Alice"

```

---

## 3. The "Hashable" Rule

In Python, for something to be a **Key** in a dictionary or an element in a set, it must be **hashable**. This generally means it must be **immutable** (unchangeable).

* **Hashable:** Strings, Integers, Tuples.
* **Not Hashable:** Lists, Dictionaries (because they can change, which would break the hash).

| Data Type | Can be a Key? | Why? |
| --- | --- | --- |
| `str` | **Yes** | Immutable; the hash never changes. |
| `int` | **Yes** | Immutable. |
| `list` | **No** | Mutable; changing the list would change the hash, losing the data. |

---

## How it works under the hood

When you run `hash("Alice")` in Python, it returns a unique integer. Python uses that integer (and some bitwise math) to decide which slot in an internal array to store the data. If you try to use a list as a key, Python will throw a `TypeError: unhashable type: 'list'`.

Using a **Set** is the "gold standard" for removing duplicates in Python because it is both incredibly fast and requires very little code.

### The Problem: The "List" Approach

If you try to remove duplicates using a standard list, you might write a loop that checks `if item not in unique_list`. For a list of 100,000 items, this is disastrously slow.

* **List Check:** Every time you check `if item not in list`, Python has to scan the *entire* list from the beginning.
* **Time Complexity:** This is , which means if the list size doubles, the time it takes quadruples.

---

### The Solution: The "Set" Approach

A Set uses a hash table. When you ask "Is this item in the set?", Python jumps straight to the calculated "address" (index) for that item. It doesn't matter if the set has 10 items or 10 million; the check takes the same amount of time.

* **Set Check:** Instant lookup.
* **Time Complexity:** , which is linear. It scales perfectly with your data.

---

### Practical Example: The "Big Data" Cleaner

Imagine you have a log file with 1 million IP addresses, and many are repeats. You want a list of only the unique visitors.

```python
import time

# Let's simulate a "massive" list with 1 million items (lots of duplicates)
massive_list = ["192.168.1.1", "10.0.0.1", "172.16.0.1"] * 333334

# --- THE FAST WAY (Set) ---
start_time = time.time()

# 1. Convert to set (removes duplicates instantly)
# 2. Convert back to list (if you need it as a list)
unique_ips = list(set(massive_list))

end_time = time.time()

print(f"Unique IPs found: {len(unique_ips)}")
print(f"Time taken with Set: {end_time - start_time:.5f} seconds")

```

**Result:** On a modern machine, the Set approach will likely finish in **~0.02 seconds**. The "list-loop" approach for the same data could take **several minutes**.

---

### Pro-Tip: Preserving Order

The only downside to `list(set(my_list))` is that it **scrambles the order** of your items because hash tables don't care about sequence.

If you need to remove duplicates **but keep the original order**, use `dict.fromkeys()`:

```python
# This keeps the order of the first appearance of each item
ordered_unique = list(dict.fromkeys(massive_list))

```

#### In Python, `dict.fromkeys(massive_list)` is a highly efficient way to create a new dictionary where the elements of your list become the **keys**.

By default, all the values associated with those keys are set to `None`. 

---

#### What Happens Under the Hood
When you run `dict.fromkeys(massive_list)`, Python iterates through the list and performs two main actions:
1.  **Ensures Uniqueness:** Since dictionary keys must be unique, any duplicate items in your `massive_list` will effectively be merged into a single key.
2.  **Assigns a Value:** It assigns the same value (defaulting to `None`) to every single key created.

### Syntax
```python
# Default behavior
new_dict = dict.fromkeys(massive_list)

# Assigning a specific default value
new_dict = dict.fromkeys(massive_list, 0) 
```

Hash tables are the "speed demons" of the data world, but that performance comes with specific trade-offs. Here is a balanced look at why you would (and wouldn't) use them.

---

## Advantages

### 1. Speed (The "O(1)" Factor)

The primary reason to use a hash table is search, insertion, and deletion speed. On average, these operations take **constant time** .

* **Real-world feel:** Whether your database has 100 users or 100 million, finding "User_42" takes roughly the same amount of time.

### 2. Flexibility

Hash tables allow you to use almost anything as a "Key"—strings, integers, or even complex objects (as long as they are immutable). This makes them much more versatile than arrays, which only allow integer indices ().

### 3. Efficiency in De-duplication

As seen in the Python example, hash tables (Sets) are the most efficient way to ensure data uniqueness. Checking if an item already exists is nearly instantaneous.

---

## Disadvantages

### 1. The "Collision" Penalty

When two keys hash to the same index, the performance drops.

* If a hash function is poor and causes many collisions, the  speed can degrade to , making it no faster than a simple list.

### 2. Memory Overhead

Hash tables are "space-hungry."

* To keep collisions low, a hash table usually needs to stay **partially empty** (often 30-50% empty).
* If you fill it up too much, performance tanks, and the computer has to "resize" the table, which is a heavy operation involving re-hashing every single item.

### 3. Lack of Ordering

Hash tables do not store data in the order it was entered.

* If you need to get the "top 10" items or "the next item in alphabetical order," a hash table is the wrong tool. You would need a **Tree** or a **Sorted Array** instead.

### 4. Not Great for "Range" Queries

You cannot easily ask a hash table for "all users with ages between 20 and 30." You would have to check every single entry one by one.

---

## Summary Comparison

| Feature | Hash Table | Array / List |
| --- | --- | --- |
| **Search Speed** |  (Instant) |  (Scans everything) |
| **Ordering** | None (Unordered) | Ordered by index |
| **Memory** | High (Needs empty space) | Low (Compact) |
| **Best Use Case** | Quick lookups by ID/Name | Keeping items in a specific sequence |

Building a hash table from scratch is a fantastic way to understand how Python's `dict` works under the hood. To do this, we need three things:

1. **A Fixed-size Array:** A "bucket" list to store our data.
2. **A Hash Function:** To turn a key (string) into an index (number).
3. **Collision Handling:** A way to store multiple items if they hash to the same index (we'll use **Chaining** with a simple list).

---

## 1. The Implementation

Here is a simplified version of a Hash Table (a "Mini-Dict").

```python
class MyHashTable:
    def __init__(self, size=10):
        self.size = size
        # Create a list of empty lists (buckets) to handle collisions
        self.table = [[] for _ in range(self.size)]

    def _hash_function(self, key):
        """Standard Python hash() modded by table size."""
        return hash(key) % self.size

    def set(self, key, value):
        """Insert or update a key-value pair."""
        index = self._hash_function(key)
        bucket = self.table[index]
        
        # Check if key already exists to update it
        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value)
                return
        
        # If not found, append a new tuple to the bucket
        bucket.append((key, value))

    def get(self, key):
        """Retrieve a value by key."""
        index = self._hash_function(key)
        bucket = self.table[index]
        
        for k, v in bucket:
            if k == key:
                return v
        return None  # Key not found

    def __repr__(self):
        return str(self.table)

# --- Testing it out ---
ht = MyHashTable(size=5)
ht.set("apple", 10)
ht.set("banana", 20)
ht.set("cherry", 30) # Likely to cause a collision with size=5!

print(f"Value for 'apple': {ht.get('apple')}")
print(f"Internal Table Structure: {ht}")

```

---

## 2. Key Components Explained

### The Hash Function (`_hash_function`)

We use Python's built-in `hash()` to get a massive integer, then use the **Modulo Operator** (`%`) with the size of our table.

> **Example:** If `hash("apple")` is `12345` and our table size is `10`, the index is `12345 % 10 = 5`.

### Handling Collisions (Chaining)

In the code above, each "slot" in `self.table` is actually a **list** (a bucket).

* If "apple" and "cherry" both hash to index `3`, the bucket at index `3` will look like: `[("apple", 10), ("cherry", 30)]`.
* When searching, we go to index `3` and then do a tiny linear search inside that bucket.

---

## 3. What’s missing from this "Toy" version?

While the code above works, a real-world hash table (like Python's actual `dict`) includes:

1. **Dynamic Resizing:** If the table gets too full (e.g., 70% capacity), it automatically doubles in size and "re-hashes" everything to keep the buckets short.
2. **Deletion:** We would need a `delete(key)` method to remove items from the buckets.
3. **Better Hash Functions:** To ensure items are spread out as evenly as possible to avoid "clumping" at certain indices.
