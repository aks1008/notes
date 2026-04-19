# Stacks, Queues, and Priority Queues

## Table of Contents

- [1. Stacks (LIFO - Last-In, First-Out)](#1-stacks-lifo)
  - [Core Principle and Logic](#core-principle-and-logic)
  - [Python Implementation](#python-implementation)
  - [Common Use Cases](#when-to-use)
    - [Undo/Redo functionality](#undo-redo-functionality)
    - [Reversing data](#reversing-data)
    - [Depth-First Search (DFS)](#depth-first-search-dfs-in-graphs)
  - [Code Examples](#code-examples)

- [2. Queues (FIFO - First-In, First-Out)](#2-queues-fifo)
  - [Core Principle and Logic](#core-principle-and-logic-1)
  - [Python Implementation (collections.deque)](#python-implementation-collectionsdeque)
  - [Common Use Cases](#when-to-use-1)
    - [Printer task scheduling](#printer-task-scheduling)
    - [Handling web requests](#handling-web-requests)
    - [Breadth-First Search (BFS)](#breadth-first-search-bfs-in-graphs)
  - [Code Examples](#code-examples-1)

- [3. Priority Queues (Best-In, First-Out)](#3-priority-queues-best-in-first-out)
  - [Core Principle and Logic](#core-principle-and-logic-2)
  - [Python Implementation (heapq module)](#python-implementation-heapq-module)
  - [Common Use Cases](#when-to-use-2)
    - [Emergency room triage](#emergency-room-triage)
    - [Dijkstra's Shortest Path](#dijkstras-shortest-path-algorithm)
    - [Finding Top K frequent items](#finding-the-top-k-frequent-items)
  - [Code Examples](#code-examples-2)

- [4. Comparison Summary](#-comparison-at-a-glance)
  - [At-a-Glance Comparison Table](#-comparison-at-a-glance)
  - [Performance Characteristics](#performance-characteristics)
  - [Python Tools Overview](#python-tools-overview)

- [5. Interview Tips and Advanced Concepts](#-interview-tip-the-hidden-stackqueue)
  - [Hidden Stack/Queue Concepts](#-interview-tip-the-hidden-stackqueue)
    - [Recursion and Call Stack](#recursion-uses-a-hidden-stack-the-call-stack)
    - [Tree Level-order Traversal](#level-order-traversal-of-a-tree-is-just-a-fancy-name-for-using-a-queue-to-visit-nodes)
  - [Implementation Considerations](#implementation-considerations)

---

In Python, these three data structures are essential for managing the **order of processing**. While they might seem similar, they differ entirely in which element gets "served" next.

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
### Internal implementaton of collection.deque

Internal Implementation: Doubly Linked List of Blocks Unlike a standard Python List (which is a dynamic array), a deque is implemented as a doubly linked list of fixed-size blocks.The Blocks: Instead of each node being a single element, each node is a "block" (typically 64 elements).The 
Pointers: Each block has a pointer to the next and previous block.Memory Efficiency: This hybrid approach minimizes the overhead of a standard linked list while avoiding the expensive "shifting" cost of a dynamic array.

Performance:
Append/Pop (Both ends): $O(1)$
Random Access (Middle): $O(n)$ 

— Since it must traverse blocks, it is slower than a list for my_deque[500].

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

In Python, there are three primary ways to implement this.

---

### 1. Using `heapq` (The Recommended Way)
The `heapq` module is the most efficient way to handle priority queues. It implements a **min-heap**, meaning the smallest element is always kept at the root.

* **Efficiency:** Insertion and deletion both take $O(\log n)$ time.
* **Behavior:** It always pops the **smallest** value.

```python
import heapq

# Initialize a list as a heap
pq = []

# Push elements with (priority, task_name)
heapq.heappush(pq, (3, "Write code"))
heapq.heappush(pq, (1, "Fix critical bug"))
heapq.heappush(pq, (2, "Attend meeting"))

# Pop the highest priority (lowest number)
while pq:
    priority, task = heapq.heappop(pq)
    print(f"Processing: {task} (Priority: {priority})")
```



---

### 2. Using `queue.PriorityQueue`
This is a thread-safe class wrapper around `heapq`. Use this if you are working with **multithreading** or want a more object-oriented API.

* **Pros:** Thread-safe (built-in locking).
* **Cons:** Slightly slower than `heapq` due to the overhead of locking.

```python
from queue import PriorityQueue

pq = PriorityQueue()

pq.put((10, "Low priority task"))
pq.put((1, "Top priority task"))

# Get the highest priority element
print(pq.get()) # Output: (1, 'Top priority task')
```

---

### 3. Handling Max-Heaps
By default, Python’s tools are **min-heaps** (lower number = higher priority). If you need a **max-heap** (higher number = higher priority), the standard trick is to multiply your priority values by -1.

| Goal | Logic |
| :--- | :--- |
| **Standard Min-Heap** | Use values as they are (1, 2, 3...). |
| **Max-Heap** | Store values as (-10, -5, -1...). |

---

### Summary Comparison

| Feature | `heapq` | `queue.PriorityQueue` |
| :--- | :--- | :--- |
| **Type** | Module (functions on a list) | Class (object) |
| **Thread-Safe?** | No | Yes |
| **Performance** | Faster | Slower |
| **Best For** | General algorithms, competitive programming | Multi-threaded applications |

### ⚖️ Comparison at a Glance
| Structure | Principle | Python Tool | Main Benefit |
|-----------|-----------|-------------|--------------|
| Stack | LIFO | list | Access to the most recent item. |
| Queue | FIFO | collections.deque | Fairness; processes in arrival order. |
| Priority Queue | Highest Priority | heapq | Focuses on the "most important" item. |


### Interview Tip: The "Hidden" Stack/Queue
- Recursion uses a hidden Stack (the Call Stack). If you are asked to turn a recursive solution into an iterative one, you will almost always need to manually use a list as a stack.
- Level-order Traversal of a tree is just a fancy name for using a Queue to visit nodes

### Use Cases for Priority Queues

Priority queues are the "heavy lifters" for any scenario where you need to make decisions based on **importance** rather than just arrival time. Since they maintain a semi-sorted state efficiently, they are used whenever you have a dynamic set of tasks that changes frequently.

Here are the most common real-world and algorithmic scenarios:

---

#### 1. Dijkstra’s Shortest Path Algorithm
This is perhaps the most famous use case. When GPS apps (like Google Maps) calculate the fastest route from point A to point B, they use a priority queue.
* **The Logic:** The algorithm constantly "explores" the next closest intersection. 
* **The Priority:** The total distance from the start. The "shortest" distance is popped first.



#### 2. Operating System (OS) Scheduling
Your computer runs hundreds of processes at once, but they aren't all equal. 
* **The Scenario:** A system-critical task (like handling mouse movement or a hardware interrupt) must happen before a background task (like checking for software updates).
* **The Priority:** The OS assigns a priority level to each process; the priority queue ensures the CPU handles high-priority tasks immediately.

#### 3. Data Compression (Huffman Coding)
When you zip a file or transmit a JPEG, you are likely using Huffman Coding. 
* **The Scenario:** You want to assign the shortest binary codes to the most frequent characters.
* **The Priority:** The frequency of the characters. By using a priority queue to combine the two least-frequent characters repeatedly, you build an optimal encoding tree.



#### 4. Prim’s Algorithm (Minimum Spanning Tree)
Similar to Dijkstra’s, Prim’s algorithm uses a priority queue to connect nodes in a graph with the least possible total edge weight. This is used in **network design** (e.g., laying fiber optic cables between cities with minimum cost).

#### 5. Event-Driven Simulations
Imagine simulating a busy airport or a bank. 
* **The Scenario:** Events (plane landing, customer arriving, teller becoming free) happen at specific times.
* **The Priority:** The **timestamp** of the event. The simulation pops the "earliest" event from the priority queue to move the "virtual clock" forward.

---

#### Summary Table: When to reach for a Priority Queue

| If you need to... | Use a Priority Queue! |
| :--- | :--- |
| **Find the "Best" next step** | In AI or pathfinding (A* search). |
| **Manage shared resources** | In load balancers or printer queues. |
| **Merge sorted streams** | Merging multiple sorted log files into one. |
| **Keep track of "Top K"** | Finding the top 10 trending hashtags in real-time. |
