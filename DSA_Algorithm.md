|| Shree Ganeshay Namh ||

# Table of Contents

- [Linked List](#linked-list)
  - [Basic Operations](#basic-operations)
  - [Reverse Linked List](#reverse-linked-list)
- [Python Data Types](#python-data-types)
  - [Data Structure Ordering](#data-structure-ordering)
- [Essential Python Data Structures](#essential-python-data-structures)
- [Data Structure Complexity Comparison](#data-structure-complexity-comparison)
- [Arrays and Lists](#arrays-and-lists)
- [Dictionaries](#dictionaries)
- [Sets](#sets)
- [Stacks, Queues, and Priority Queues](#stacks-queues-and-priority-queues)
- [Linked List Details](#linked-list-details)
  - [Types of Linked Lists](#types-of-linked-lists)
  - [Essential Interview Patterns](#essential-interview-patterns)
  - [Linked List vs. Python List](#linked-list-vs-python-list)

# Linked List

**Linear data structure**
- Not store in contiguous location
- Contains nodes
- Node consists of data value and pointer to the address of next node
- Dynamic Linear Data structure, memory size allocation and deallocation at run time
- We can use linked list for implementation of stack, queue, graph, has maps, etc
- **Head node** – first node, **Tail node** – Last node {next pointer is null}
- Can store different data types

## Basic Operations

- **Insertion**
- **Deletion** – at the beginning or last
- **Display / Print**
- **Search**
- **Delete** – at any position, by providing key

Reference: https://www.tutorialspoint.com/data_structures_algorithms/linked_list_algorithms.htm

## Reverse Linked List

```java
Node previous = null;
Node current = this.head;
Node next = null;

while(current != null){
	next = current.next;
	current.next = previous;
	previous = current;
	current = next;
}
```












# Python Data Types

| Category | Type Name | Example | When to Use it |
|----------|-----------|---------|----------------|
| **Numeric** | int | age = 25 | Whole numbers (counts, indices, years). |
| | float | price = 19.99 | Decimals (measurements, weights, averages). |
| | complex | z = 1 + 2j | Advanced math and engineering. |
| **Text** | str | name = "Dev" | Any text data (words, sentences, IDs). |
| **Boolean** | bool | is_valid = True | Logic gates, toggles, and "if" conditions. |
| **Sequence** | list | [1, 2, 2] | Mutable (changeable) ordered collections. |
| | tuple | (10, 20) | Immutable (fixed) ordered collections. |
| **Mapping** | dict | {"id": 1} | Key-Value pairs for fast lookups (user profiles). |
| **Set** | set | {1, 2, 3} | Unique items only; removes duplicates. |
| **None** | NoneType | x = None | To represent "no value" or a placeholder. |



## Data Structure Ordering

| Data Type | Is it Ordered? | Python Version Change |
|-----------|---------------|----------------------|
| List | ✅ Yes | Always has been. |
| Tuple | ✅ Yes | Always has been. |
| Dictionary | ✅ Yes | Changed (Ordered since 3.7+). |
| Set | ❌ No | Remains Unordered. |





# Essential Python Data Structures

## 1. The "Big Three" Built-ins (Must Master)
- **Lists**
- **Dictionaries**
- **Sets**

## 2. Specialized Collections (The "Interview Toolkit")
- **Queues/Stacks**
- **Priority Queues**

## 3. User-Defined Structures (The "Implementation" Category)
- **Linked Lists**
- **Trees**
- **Graphs**
- **Tries (Prefix Trees)**



## Data Structure Complexity Comparison

| Topic | Key Python Tool | Complexity (Average) |
|-------|----------------|---------------------|
| Dynamic Array | list | Access: $O(1)$, Insert: $O(n)$ |
| Hash Map | dict | Search/Insert: $O(1)$ |
| Queue | collections.deque | Enqueue/Dequeue: $O(1)$ |
| Heap / Priority Queue | heapq | Push/Pop: $O(\log n)$ |
| Balanced BST | N/A (Manual) | Search/Insert: $O(\log n)$ |




# Arrays and Lists

## 1. Array + List:
A List is a built-in, highly flexible tool, while a true Array usually refers to the array module or NumPy arrays.

```python
# A Python List
my_list = [10, "Hello", 3.14, True] 
my_list.append(100) # Easy to resize

import array
# 'i' stands for signed integer
my_array = array.array('i', [1, 2, 3, 4])
```

**Note on NumPy**: If you are doing Data Science or heavy Math, you shouldn't use list or array. You should use NumPy Arrays (np.array), which are the industry standard for performance and vector operations.

| Feature | Python List | Python Array (array module) |
|---------|-------------|----------------------------|
| Data Types | Can be mixed (int, str, etc.) | Must be the same type. |
| Flexibility | High (very easy to use) | Low (restricted to basic types) |
| Memory Efficiency | Lower (due to pointer overhead) | Higher (stores raw bytes) |
| Import Required? | No (Built-in) | Yes (import array) |
| Mathematical Ops | Not built-in | Basic (but usually use NumPy) |





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
```

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


# Stacks, Queues, and Priority Queues

In Python, these three data structures are essential for managing the order of processing. While they might seem similar, they differ entirely in which element gets "served" next.

## 1. Stacks (LIFO)
A Stack follows the Last-In, First-Out principle. Think of a stack of physical plates: the last one you put on top is the first one you take off.
- **Logic**: $O(1)$ push and pop from the top.
- **Python Implementation**: Use a standard list.
- **When to use**: 
  - Undo/Redo functionality.
  - Reversing data.
  - Depth-First Search (DFS) in graphs.

```python
stack = []
stack.append('A')  # Push
stack.append('B')
top_item = stack.pop() # Removes 'B' (Last in)
```

## 2. Queues (FIFO)
A Queue follows the First-In, First-Out principle. Think of a line at a grocery store: the first person to join the line is the first one served.
- **Logic**: $O(1)$ addition at the end, $O(1)$ removal from the front.
- **Python Implementation**: Use collections.deque (not a list, as list.pop(0) is $O(n)$).
- **When to use**: 
  - Printer task scheduling.
  - Handling web requests.
  - Breadth-First Search (BFS) in graphs.

```python
from collections import deque
queue = deque(["Alice", "Bob"])
queue.append("Charlie") # Enqueue
first_person = queue.popleft() # Removes "Alice" (First in)
```

## 3. Priority Queues (Best-In, First-Out)
A Priority Queue doesn't care about the order of arrival. Instead, every element has a priority, and the element with the highest priority (or lowest value) is served first.
- **Logic**: $O(\log n)$ for insertion and removal.
- **Python Implementation**: Use the heapq module (implements a Min-Heap).
- **When to use**: 
  - Emergency room triage (most urgent case first).
  - Dijkstra's Shortest Path algorithm.
  - Finding the "Top K" frequent items.

```python
import heapq
pq = []
heapq.heappush(pq, 10) # Priority 10
heapq.heappush(pq, 1)  # Priority 1
heapq.heappush(pq, 5)  # Priority 5

# Always pops the SMALLEST value first
print(heapq.heappop(pq)) # Output: 1
```

### ⚖️ Comparison at a Glance
| Structure | Principle | Python Tool | Main Benefit |
|-----------|-----------|-------------|--------------|
| Stack | LIFO | list | Access to the most recent item. |
| Queue | FIFO | collections.deque | Fairness; processes in arrival order. |
| Priority Queue | Highest Priority | heapq | Focuses on the "most important" item. |

### Interview Tip: The "Hidden" Stack/Queue
- Recursion uses a hidden Stack (the Call Stack). If you are asked to turn a recursive solution into an iterative one, you will almost always need to manually use a list as a stack.
- Level-order Traversal of a tree is just a fancy name for using a Queue to visit nodes

**Learn internal implementation of Priority Queue**

# Linked List Details

## Types of Linked Lists

### Singly Linked List
Each node points only to the next node. You can only move forward.

```
HEAD -> [Data|Next] -> [Data|Next] -> [Data|Next] -> NULL
```

**Visual Representation:**
```
+------+    +------+    +------+    +------+
| HEAD | -> | 10   | -> | 20   | -> | 30   | -> NULL
+------+    +------+    +------+    +------+
            | Next |    | Next |    | Next |
            +------+    +------+    +------+
```

### Doubly Linked List
Each node has a next and a prev pointer. You can move in both directions.

```
NULL <- [Prev|Data|Next] <-> [Prev|Data|Next] <-> [Prev|Data|Next] -> NULL
```

**Visual Representation:**
```
NULL <+-> +------+    +------+    +------+ <+-> NULL
       | 10   | <-> | 20   | <-> | 30   |
       +------+    +------+    +------+
       | Prev |    | Prev |    | Prev |
       | Next |    | Next |    | Next |
       +------+    +------+    +------+
```

### Circular Linked List
The last node points back to the head (or the first node)

```
HEAD -> [Data|Next] -> [Data|Next] -> [Data|Next] -> (points back to HEAD)
```

**Visual Representation:**
```
    +-----------------------------+
    |                             |
    v                             |
+------+    +------+    +------+    
| HEAD | -> | 10   | -> | 20   | -> |
+------+    +------+    +------+    |
            | Next |    | Next |    |
            +------+    +------+    |
                                   |
                                   |
+-----------------------------------+
```

```python
class ListNode:
    def __init__(self, value=0, next=None):
        self.val = value    # The data
        self.next = next    # Pointer to the next node
```

| Operation | Array / Python List | Linked List |
|-----------|---------------------|-------------|
| Access (at index $i$) | $O(1)$ | $O(n)$ (must traverse from head) |
| Insert at Beginning | $O(n)$ (must shift all items) | $O(1)$ |
| Delete at Beginning | $O(n)$ | $O(1)$ |
| Search | $O(n)$ | $O(n)$ |


## 4. Essential Interview Patterns
If you get a Linked List question, it usually falls into one of these four categories:

### A. The "Dummy Head" Technique
When creating a new list or merging two lists, start with a "dummy" node. It prevents you from having to write an if statement for the very first node.

```python
dummy = ListNode(0)
curr = dummy
# ... logic to add nodes ...
return dummy.next # Returns the actual head
```

### B. Fast and Slow Pointers (Tortoise and Hare)
Used to find the middle of a list or to detect a cycle.
- **Middle**: Move slow by 1 step and fast by 2 steps. When fast hits the end, slow is at the middle.
- **Cycle Detection**: If fast eventually catches up to slow, there is a loop.

### C. Reversing a List (The #1 Interview Question)
You must be able to do this in your sleep. You need three pointers: prev, curr, and next_node.

```python
def reverseList(head):
    prev = None
    curr = head
    while curr:
        next_node = curr.next # Save next
        curr.next = prev      # Reverse pointer
        prev = curr           # Move prev forward
        curr = next_node      # Move curr forward
    return prev
```


## 5. Linked List vs. Python List
In a real Python interview, if you are asked to implement a Stack or Queue, the interviewer might ask why you'd use a Linked List instead of a Python list.
- **Python List**: When it runs out of allocated space, it has to "resize," which takes $O(n)$ time occasionally.
- **Linked List**: Never needs to resize; it just grabs a new spot in memory for the next node, ensuring $O(1)$ for every single insertion.

### 🛡️ Interview Cheat Sheet
- **Always check for None**: Before accessing node.next, ensure node is not None.
- **Space Complexity**: Most Linked List problems expect $O(1)$ extra space (modifying pointers in place) rather than creating a new list.
- **Recursion**: Many LL problems can be solved recursively, but be careful of "Stack Overflow" for very long lists.

