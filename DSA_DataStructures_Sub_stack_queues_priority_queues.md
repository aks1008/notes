# Stacks, Queues, and Priority Queues

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
