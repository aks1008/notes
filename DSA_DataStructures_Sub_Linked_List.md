# Linked List Details

## Table of Contents

- [1. Types of Linked Lists](#types-of-linked-lists)
  - [Singly Linked List](#singly-linked-list)
    - [Structure and Visual Representation](#visual-representation)
  - [Doubly Linked List](#doubly-linked-list)
    - [Structure and Visual Representation](#visual-representation-1)
  - [Circular Linked List](#circular-linked-list)
    - [Structure and Visual Representation](#visual-representation-2)

- [2. Core Implementation](#core-implementation)
  - [ListNode Class Definition](#listnode-class-definition)
  - [Operations Comparison Table](#operations-comparison-table)
    - [Array vs Linked List Performance](#array--python-list--linked-list)

- [3. Essential Interview Patterns](#4-essential-interview-patterns)
  - [The "Dummy Head" Technique](#a-the-dummy-head-technique)
  - [Fast and Slow Pointers (Tortoise and Hare)](#b-fast-and-slow-pointers-tortoise-and-hare)
    - [Finding the Middle](#middle)
    - [Cycle Detection](#cycle-detection)
  - [Reversing a List (The #1 Interview Question)](#c-reversing-a-list-the-1-interview-question)
    - [Three-Pointer Technique](#three-pointer-technique)

- [4. Linked List vs. Python List](#5-linked-list-vs-python-list)
  - [Performance Considerations](#performance-considerations)
  - [Memory Management Differences](#memory-management-differences)
  - [Interview Cheat Sheet](#-interview-cheat-sheet)
    - [Best Practices](#always-check-for-none)
    - [Space Complexity Guidelines](#space-complexity)
    - [Recursion Considerations](#recursion)

- [5. Fundamental Concepts](#linked-list)
  - [Linear Data Structure Properties](#linear-data-structure)
  - [Memory Allocation Characteristics](#dynamic-linear-data-structure)
  - [Key Terminology](#key-terminology)
    - [Head Node](#head-node)
    - [Tail Node](#tail-node)
  - [Use Cases and Applications](#we-can-use-linked-list-for-implementation-of-stack-queue-graph-has-maps-etc)

- [6. Basic Operations](#basic-operations)
  - [Insertion](#insertion)
  - [Deletion (Beginning, Last, Any Position)](#deletion)
  - [Display / Print](#display--print)
  - [Search](#search)

- [7. Advanced Operations](#reverse-linked-list)
  - [Reverse Linked List](#reverse-linked-list-1)
    - [Java Implementation](#java-implementation)
    - [Three-Pointer Algorithm](#three-pointer-algorithm)

---

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