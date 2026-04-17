# Data Structures and Algorithms Essentials

## Table of Contents

1. **Python Data Types Overview**
   - Numeric Types (int, float, complex)
   - Text Type (str)
   - Boolean Type (bool)
   - Sequence Types (list, tuple)
   - Mapping Type (dict)
   - Set Type (set)
   - None Type (NoneType)

2. **Essential Python Data Structures**
   - The "Big Four" Built-ins
     - Arrays & Strings
     - Lists
     - Dictionaries
     - Sets
     - Tuples
     - Hash Tables (Maps/Sets)
   - Specialized Collections
     - Queues/Stacks
     - Priority Queues
   - User-Defined Structures
     - Linked Lists
     - Trees
     - Graphs
     - Tries (Prefix Trees)
     - Disjoint Set Union (Union-Find)
     - Segment Trees / Fenwick Trees

3. **Data Structure Complexity Comparison**
   - Performance Analysis Table
   - Time and Space Complexities

4. **Essential Data Structures for Interviews**
   - The Essentials (Must-Have)
     - Arrays & Strings
     - Hash Tables (Maps/Sets)
     - Linked Lists
     - Stacks & Queues
   - Advanced Nonlinear Structures
     - Trees (Binary Trees, BST)
     - Graphs
     - Heaps (Priority Queues)
   - The "Difference Makers"
     - Tries (Prefix Trees)
     - Disjoint Set Union (Union-Find)
     - Segment Trees / Fenwick Trees

5. **Algorithm Categories by Problem Type**
   - Searching Algorithms
     - Linear Search
     - Binary Search
     - Hash-based Search
   - Sorting Algorithms
     - Simple Sorts (O(n²))
     - Efficient Sorts (O(n log n))
     - Specialized Sorts (O(n))
   - Graph Algorithms
     - Traversal Algorithms
     - Shortest Path Algorithms
     - Minimum Spanning Tree
     - Network Flow
   - Dynamic Programming Patterns
     - Memoization vs. Tabulation
     - Common DP Problems

6. **Interview Strategy**
   - Problem-Solving Framework
     - Clarify Requirements
     - Choose Right Data Structure
     - Write Clean Code
     - Optimize Solutions
     - Test Thoroughly
   - Common Pitfalls to Avoid
     - Indexing Errors
     - Recursion Issues
     - Memory Management
     - Big-O Analysis

7. **Implementation Tips**
   - Code Quality Standards
   - Testing Strategy
   - Best Practices

---

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

# Essential Python Data Structures

## 1. The "Big Four" Built-ins (Must Master)
- **Array & Strings**
- **Lists**
- **Dictionaries**
- **Sets**
- **Tuples**
- **Hash Tables (Maps/Sets)**

## 2. Specialized Collections (The "Interview Toolkit")
- **Queues/Stacks**
- **Priority Queues**

## 3. User-Defined Structures (The "Implementation" Category)
- **Linked Lists**
- **Trees**
- **Graphs**
- **Tries (Prefix Trees)**
- **Disjoint Set Union (Union-Find)**
- **Segment Trees / Fenwick Trees**

# Data Structure Complexity Comparison

| Topic | Key Python Tool | Complexity (Average) |
|-------|----------------|---------------------|
| Dynamic Array | list | Access: $O(1)$, Insert: $O(n)$ |
| Hash Map | dict | Search/Insert: $O(1)$ |
| Queue | collections.deque | Enqueue/Dequeue: $O(1)$ |
| Heap / Priority Queue | heapq | Push/Pop: $O(\log n)$ |
| Balanced BST | N/A (Manual) | Search/Insert: $O(\log n)$ |

## Table of Contents

### [1. Algorithm Categories by Problem Type](#4-algorithm-categories-by-problem-type)
- [Searching Algorithms](#searching-algorithms)
- [Sorting Algorithms](#sorting-algorithms)
- [Graph Algorithms](#graph-algorithms)
- [Dynamic Programming Patterns](#dynamic-programming-patterns)

### [2. Interview Strategy](#5-interview-strategy)
- [Problem-Solving Framework](#problem-solving-framework)
- [Common Pitfalls to Avoid](#common-pitfalls-to-avoid)

### [3. Implementation Tips](#6-implementation-tips)

## 1. The Essentials (Must-Have)
These appear in nearly every interview. You should be able to implement them from scratch and know their Big-O complexities for all operations.

### Arrays & Strings: The most common
- Master Two-Pointer technique, Sliding Window, and Prefix Sums

### Hash Tables (Maps/Sets): The "cheat code" for many problems
- Use them for $O(1)$ lookups
- Master collision handling (chaining vs. open addressing)
- [Hash Tables (Maps/Sets)](./DSA_DataStructure_Sub_HashTables.md)

### Linked Lists: Focus on Singly Linked Lists
- Master reversing a list, cycle detection (Floyd's Tortoise and Hare), and finding the middle element

### Stacks & Queues: Understand LIFO and FIFO
- Practice using them for expression parsing (stacks) and level-order traversal (queues)

## 2. Advanced Nonlinear Structures
FAANG loves these because they test your ability to think recursively and handle complex pointers.

### Trees: Focus heavily on Binary Trees and Binary Search Trees (BST)
- Master traversals (In-order, Pre-order, Post-order, and Level-order)
- [Trees](./DSA_DataStructure_Sub_Trees.md)

### [Graphs](./DSA_DataStructure_Sub_Graphs.md): Essential for "Social Network" or "Pathfinding" questions
- You must know how to represent them (Adjacency List vs. Matrix) and how to traverse them (BFS and DFS)

### Heaps (Priority Queues): Critical for "K-th largest element" or "Top K frequent" problems
- Understand how a Min-Heap and Max-Heap function

## 3. The "Difference Makers"
Mastering these will set you apart for Senior (L5+) or highly competitive roles.

### Tries (Prefix Trees):
- Frequently used for autocomplete or dictionary-based problems

### Disjoint Set Union (Union-Find): Vital for connectivity problems in graphs
- e.g., "Number of Islands" or "Redundant Connection"

### Segment Trees / Fenwick Trees: Rare, but occasionally show up in Google or Meta interviews
- For range query problems

## 4. Algorithm Categories by Problem Type

### Searching Algorithms
- Linear Search: $O(n)$
- Binary Search: $O(\log n)$ (requires sorted data)
- Hash-based Search: $O(1)$ average case

### Sorting Algorithms
- **Simple Sorts**: Bubble, Selection, Insertion - $O(n^2)$
- **Efficient Sorts**: Merge, Quick, Heap - $O(n \log n)$
- **Specialized Sorts**: Radix, Bucket, Counting - $O(n)$ for specific data types

### Graph Algorithms
- **Traversal**: BFS, DFS, Dijkstra's, A*
- **Shortest Path**: Floyd-Warshall, Bellman-Ford
- **Minimum Spanning Tree**: Kruskal's, Prim's
- **Network Flow**: Ford-Fulkerson, Edmonds-Karp

### Dynamic Programming Patterns
- Memoization vs. Tabulation
- Knapsack problems
- Longest Common Subsequence
- Coin Change problems
- Edit Distance

## 5. Interview Strategy

### Problem-Solving Framework
1. **Clarify Requirements**: Ask questions about constraints, edge cases, expected input/output
2. **Choose Right Data Structure**: Based on operation frequency and time complexity needs
3. **Write Clean Code**: Follow consistent naming, handle edge cases
4. **Optimize**: Look for $O(n \log n)$ solutions when $O(n^2)$ is obvious
5. **Test**: Consider edge cases, large inputs, and performance

### Common Pitfalls to Avoid
- Off-by-one errors in array indexing
- Infinite recursion without base cases
- Memory leaks in linked list operations
- Incorrect Big-O analysis
- Not considering input constraints

## 6. Implementation Tips

### Code Quality Standards
- Use meaningful variable names
- Add comments for complex logic
- Handle null/empty cases
- Follow consistent coding style
- Write modular, reusable functions

### Testing Strategy
- Test with small inputs
- Test with edge cases (empty, single element, sorted)
- Test with large inputs for performance
- Test with duplicate elements
- Verify time complexity matches expectations

---

**Remember**: It's better to know 5 algorithms deeply than 20 algorithms superficially. Focus on understanding the patterns and trade-offs rather than memorizing implementations.

---


