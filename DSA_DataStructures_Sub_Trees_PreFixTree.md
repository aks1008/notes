# Tree

If a Hash Table is a high-speed filing cabinet, a **Tree** is a high-speed **organizational chart**.

In a Hash Table, the data is scattered randomly based on a math formula. In a Tree, the data is stored in a **hierarchy**, which allows you to maintain relationships and order.

## Table of Contents

### [1. What is a Tree?](#what-is-a-tree)
### [2. Binary Search Trees (BST)](#binary-search-trees-bst)
- [Why use a BST instead of a Hash Table?](#why-use-a-bst-instead-of-a-hash-table)
- [Practical Example: Finding a Value](#practical-example-finding-a-value)
- [Coding a Simple Node in Python](#coding-a-simple-node-in-python)
- [When to use which?](#when-to-use-which)
- [Tree Types Comparison Table](#tree-types-comparison-table)
- [Priority Table](#priority-table)

### [3. Mastery Plan: Tree Data Structures](#mastery-plan-tree-data-structures-for-faang)
- [Binary Search Tree (BST)](#the-foundation-binary-search-tree-bst)
- [General Binary Trees](#the-heavy-hitter-general-binary-trees)
- [Trie (Prefix Tree)](#the-specialist-trie-prefix-tree)
- [Heap (Priority Queue)](#the-utility-heap-priority-queue)

### [4. BST Core Concepts](#bst-core-concepts)
- [The Core Rule](#the-core-rule)
- [Why use a BST?](#why-use-a-bst-the-divide-and-conquer-logic)
- [Basic Operations](#basic-operations)
- [The "Magic" Traversal](#the-magic-traversal)
- [The "Skewed" Problem](#the-skewed-problem-warning)
- [Implementation Template](#implementation-template-python)

### [5. Tree Traversals](#tree-traversals)
- [The Big Three Traversals](#the-big-three-traversals)
- [How Deletion Works](#how-deletion-works-with-examples)
- [Visual Summary Table](#visual-summary-table)
- [Practice Exercise](#practice-exercise)

### [6. Tree Balance](#tree-balance)
- [Unbalanced BST](#unbalanced-standard-bst)
- [Perfect Binary Trees](#perfect-binary-trees)
- [Balanced BSTs](#balanced-bsts-the-goal)
- [Pro-Tip for FAANG Interviews](#pro-tip-for-faang-interviews)

---
## 1. What is a Tree?

A tree is a collection of "nodes" connected by "edges." It always starts at a single point called the **Root**.

* **Root:** The top node (the "boss").
* **Parent/Child:** A node directly above another is its parent; the one below is the child.
* **Leaf:** A node with no children (the "ends" of the branches).
* **Subtree:** Any node and all its descendants.

---

## 2. Binary Search Trees (BST)

The most common type of tree used in programming is the **Binary Search Tree**. It follows one strict rule that makes searching incredibly fast:

1. **Left Child** is always **smaller** than the parent.
2. **Right Child** is always **greater** than the parent.

### Why use a BST instead of a Hash Table?

| Feature | Hash Table | Binary Search Tree (Balanced) |
| :--- | :--- | :--- |
| **Search Speed** | **$O(1)$** (Constant Average) | **$O(\log n)$** (Logarithmic) |
| **Insertion/Deletion** | $O(1)$ Average | $O(\log n)$ |
| **Space Complexity** | $O(n)$ (Often uses extra "buffer" space) | $O(n)$ |
| **Data Ordering** | **Unordered** (Randomized) | **Ordered** (Sorted) |
| **Range Queries** | Inefficient (Must scan everything) | **Efficient** (e.g., "Find all values between 10 and 50") |
| **Worst Case** | $O(n)$ (If many collisions occur) | $O(n)$ (Only if the tree is unbalanced/skewed) |
---

## 3. Practical Example: Finding a Value

Imagine searching for the number **7** in a tree of 1 million sorted numbers.

1. You start at the **Root**. Is 7 bigger or smaller?
2. If smaller, you ignore the entire right half of the tree instantly.
3. You repeat this. Each "step" cuts your search area in half.

This is called **Logarithmic Time** . To find one item out of 1 million, it only takes about **20 steps**.

---

## 4. Coding a Simple Node in Python

In Python, we don't have a built-in "Tree" type like we do for dicts, so we build them using classes:

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.left = None
        self.right = None

# Building a tiny tree
root = Node(10)
root.left = Node(5)
root.right = Node(15)

```

---

## 5. When to use which?

* Use a **Hash Table** if you just need to find a specific item by name as fast as possible and don't care about the order (e.g., User Sessions, Caching).
* Use a **Tree** if you need to keep your data sorted, find "ranges" of data, or represent hierarchical structures (e.g., Folder systems on your computer, HTML DOM, Game AI decision paths).

Answer: No, they aren't the same, but they are definitely "cousins."

Think of a Linked List as a straight path and a Tree as a map of branching roads. While they both use the same basic building blocks (nodes and pointers), how those blocks connect changes everything.

The Core Differences
1. Structure and Connectivity

Linked List: Each node is a "loner" that only knows about the person directly behind them (and sometimes the person in front). It is a linear data structure.

Tree: Each node can have multiple "children." It is a hierarchical data structure.

2. Implementation Details
Feature	Linked List	Tree (e.g., Binary Tree)
Pointers	Usually has one (next) or two (prev, next).	Usually has two or more (left, right).
Entry Point	You start at the Head.	You start at the Root.
Relationship	Sequential (1:1).	Parent-Child (1:N).
Ending	Ends at a null pointer.	Ends at Leaf nodes (nodes with no children).
Why they feel similar
You’re right to notice a connection! In code, both look very similar.

If you look at a Singly Linked List node:

```C
struct Node {
    int data;
    struct Node* next;
};
```

And compare it to a Binary Tree node:

```C
struct Node {
    int data;
    struct Node* left;
    struct Node* right;
};
```

When to use which?

Use a Linked List if you just need a simple list where you plan to add/remove items frequently from the ends.

Use a Tree if you need to represent a hierarchy (like a file system) or if you want to search through millions of items very quickly (using a Binary Search Tree).


| Tree Name | Core Use Case | Real-World Example | How it Behaves / Implementation |
|----------|--------------|-------------------|--------------------------------|
| Binary Search Tree | Simple data organization. | Basic lookup tables. | Left < Parent < Right. No balancing; can become a "line" if data is sorted. |
| AVL Tree | Strict, fast lookups. | Memory-intensive apps. | Strictly balanced. Each node tracks its "height"; performs rotations if height difference > 1. |
| Red-Black Tree | Balanced searching. | C++ STL, Java TreeMap. | Rule-based balancing. Uses "color" bits (Red/Black) to ensure the longest path is ≤ 2x the shortest. |
| B-Tree / B+ Tree | Massive disk storage. | SQL Databases, NTFS. | Multi-way nodes. Each node holds many keys/pointers to reduce "disk reads" by staying very short and wide. |
| Trie (Prefix Tree) | String searching. | Auto-complete, IP routing. | Character-by-character. Each node is a character; the path from root to leaf forms a full word. |
| Heap | Priority management. | OS Task Schedulers. | Complete Binary Tree. Usually implemented as an Array; Parent is always ≥ (or ≤) its children. |
| Segment Tree | Fast range queries. | Stock market analysis. | Divide and Conquer. Each node stores an aggregate value (like a sum) of a specific range of an array. |
| N-ary Tree | Hierarchical data. | HTML DOM, File folders. | Flexible children. Each node contains a list or vector of pointers to an unlimited number of child nodes. |

### Priority Table

| Priority | Tree Type | Difficulty | Interview Frequency |
|----------|-----------|------------|---------------------|
| High     | BST / Binary Tree | Medium | ⭐⭐⭐⭐⭐ |
| High     | Heap | Medium | ⭐⭐⭐⭐ |
| Medium   | Trie | Hard | ⭐⭐⭐ |
| Low      | N-ary Tree | Easy | ⭐⭐ |
| Theory Only | Red-Black / AVL | Hard | ⭐ |

This is a great way to organize your study notes. Converting this into a structured **Table of Contents (ToC)** for your Markdown files will help you track your progress as you master each concept.

Here is the plan formatted as a clean, professional Markdown index.

---

## 🌳 Mastery Plan: Tree Data Structures for FAANG

### Table of Contents

1. The Foundation: Binary Search Tree BST
    - Properties of a BST (Left < Parent < Right)
    - Insertion & Search Algorithms ($O(\log n)$ vs $O(n)$)
    - Node Deletion (The three cases: Leaf, One Child, Two Children)
    - The "Big Three" Traversals:
      - **In-order:** Recursive & Iterative (The "Sorted List" Trick)
      - **Pre-order:** Recursive & Iterative
      - **Post-order:** Recursive & Iterative

2. The Heavy Hitter: General Binary Trees
    - Structural Algorithms:
      - Calculating Tree Height & Depth
      - Finding the Diameter (Longest path between any two nodes)
      - Symmetry & Identity (Checking if two trees are identical)
    - Advanced Traversal:
      - **Level-order Traversal:** Breadth-First Search (BFS) using a Queue
      - **Zig-Zag / Spiral Traversal:** 
    - Mastering Recursion
      - Bottom-up approach (Post-order logic)
      - Top-down approach (Pre-order logic)

3. The Specialist: Trie (Prefix Tree)
    - Core Implementation
        - Building a `TrieNode` (using Arrays vs. HashMaps for children)
        - `insert(word)` and `search(word)` logic
        - `startsWith(prefix)` implementation
    - Interview Applications
        - Designing Auto-complete systems
        - Storing and searching a dictionary efficiently

4. The Utility: Heap (Priority Queue)
    - Mechanics
        - Min-Heap vs. Max-Heap properties
        - Array-based implementation (Index math: $2i+1$, $2i+2$)
        - Heapify process (Up-heap and Down-heap)
    - Common Patterns
        - Top 'K' Frequent Elements
        - Merging 'K' Sorted Lists
        - Finding the Median in a stream of data

---

## 1. The Foundation: Binary Search Tree BST

A **Binary Search Tree (BST)** is a specific type of binary tree designed for fast searching, sorting, and data retrieval. It follows a strict "ordering rule" that makes it much more efficient than a standard linked list or a basic binary tree.

---

## 1. The Core Rule

In a BST, for every single node (the "parent"):

1. All values in the **Left Subtree** must be **less than** the parent's value.
2. All values in the **Right Subtree** must be **greater than** the parent's value.
3. Each node can have a maximum of **two children**.

---

## 2. Why use a BST? (The "Divide and Conquer" Logic)

Imagine you are looking for the number **7** in a list of 1,000,000 numbers.

* **In a Linked List:** You might have to check all 1,000,000 numbers one by one ().
* **In a Balanced BST:** Every time you move from a parent to a child, you eliminate **half** of the remaining possibilities ().

Searching a million items in a balanced BST takes only about **20 steps**.

---

## 3. Basic Operations

### A. Searching

To find a value `X`:

* Start at the **Root**.
* If `X` is smaller than the current node, go **Left**.
* If `X` is larger than the current node, go **Right**.
* Repeat until you find `X` or hit a `null` (meaning it’s not there).

### B. Insertion

You search for the value until you hit a `null` spot. That empty spot is exactly where the new node belongs to maintain the order.

### C. Deletion (The Tricky Part)

* **Leaf Node:** Just delete it.
* **One Child:** The child moves up to take the parent's place.
* **Two Children:** You must find the **In-order Successor** (the smallest value in the right subtree) to replace the deleted node, ensuring the tree rules stay intact.

---

## 4. The "Magic" Traversal

If you perform an **In-order Traversal** (Left -> Root -> Right) on a BST, the output will **always be a sorted list**. This is one of the most common "aha!" moments in DSA interviews.

---

## 5. The "Skewed" Problem (Warning)

A BST is only fast if it is "bushy" (balanced). If you insert numbers in order (1, 2, 3, 4, 5), the tree turns into a straight line.

* **Balanced BST:** Search is .
* **Skewed BST:** Search becomes , essentially becoming a Linked List.
* *Note: This is why advanced trees like AVL or Red-Black trees were invented—to automatically fix this "leaning" problem.*

---

### Implementation Template (Python)

Here is how you define a BST node and a basic insert function:

```python
class Node:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None

def insert(root, val):
    if root is None:
        return Node(val)
    
    if val < root.val:
        root.left = insert(root.left, val)
    else:
        root.right = insert(root.right, val)
    return root

```

Think of **Traversals** as different ways to visit every house in a neighborhood. In a tree, because it's not a straight line, we need a specific order to make sure we don't miss any "houses" (nodes).

---

## 1. The Big Three Traversals

The names tell you exactly when you visit the **Root** (the Parent) relative to its children.

### A. In-order (Left → **Root** → Right)

* **The Rule:** Visit the entire left side, then the root, then the entire right side.
* **The Result:** In a BST, this always gives you the numbers in **sorted order**.
* **Use Case:** When you need to process data in non-decreasing order.

### B. Pre-order (**Root** → Left → Right)

* **The Rule:** Visit the root first, then the left side, then the right side.
* **The Result:** You see the "top" of the tree first.
* **Use Case:** Perfect for **cloning/copying** a tree or calculating directory structures.

### C. Post-order (Left → Right → **Root**)

* **The Rule:** Visit both children first, and only visit the root last.
* **The Result:** You process the "leaves" before the parents.
* **Use Case:** Essential for **deleting** a tree (you can't delete a parent until its children are gone) or calculating the size of a folder.

---

## 2. How Deletion Works (With Examples)

Deleting is the hardest part of a BST because you cannot leave a "hole" in the middle of the structure. There are three scenarios:

### Case 1: Deleting a Leaf (No Children)

* **Scenario:** You want to delete **5** from the bottom of the tree.
* **Action:** Just remove it. It has no dependencies.
* **Example:**
> Before: `(10) -> (5)`
> After: `(10) -> null`



### Case 2: Deleting a Node with One Child

* **Scenario:** You want to delete a node that has one "leg."
* **Action:** The child "steps up" and takes the parent's place (like a promotion).
* **Example:** > If **10** has a child **15**, and you delete **10**, then **15** just connects directly to **10**'s old parent.

### Case 3: Deleting a Node with Two Children (The "Swap")

* **Scenario:** You want to delete the **Root (20)**, but it has a left child **10** and a right child **30**.
* **The Problem:** Who takes the throne?
* **The Solution:** You find the **In-order Successor** (the smallest value in the right subtree).
* **Step-by-Step Example:**
1. Look at the right child (**30**).
2. Go as far **left** as possible from there to find the smallest value (let's say it's **22**).
3. Replace the node you want to delete (**20**) with this value (**22**).
4. Delete the original **22** from its old spot (which is easy because it will be a Case 1 or Case 2 deletion).



---

## 3. Visual Summary Table

| Traversal | Logic Sequence | Best For... |
| --- | --- | --- |
| **In-order** | Left  Node  Right | Getting sorted data from a BST. |
| **Pre-order** | Node  Left  Right | Creating a copy of the tree. |
| **Post-order** | Left  Right  Node | Deleting nodes or math expressions. |

---

### Practice Exercise

Imagine a small tree: Root is **10**, Left is **5**, Right is **15**.

### 1. In-order Traversal
1. Start at the root **10**.
2. Go left to **5**.
3. Go right to **10**.
4. Go right to **15**.

Result: **5**, **10**, **15**

### 2. Pre-order Traversal
1. Start at the root **10**.
2. Go left to **5**.
3. Go right to **15**.

Result: **10**, **5**, **15**

### 3. Post-order Traversal
1. Start at the root **10**.
2. Go left to **5**.
3. Go right to **15**.
4. Go back to the root **10**.

Result: **5**, **15**, **10**

---

### 1. Unbalanced (Standard) BST

In a basic BST, the levels depend entirely on the order in which you insert numbers. If you insert 1, 2, 3, 4, every leaf will be on a different level, forming a straight line. This is bad for performance because searching becomes slow ($O(n)$).

### 2. Perfect Binary Trees

A tree where all leaf nodes are at the same level and every parent has exactly two children is called a **Perfect Binary Tree**. These are rare in real-world data because you need exactly $2^h - 1$ nodes to make it work.

### 3. Balanced BSTs (The Goal)

In interviews, you will often hear about **Balanced** trees (like AVL or Red-Black Trees). In these trees:

- The leaves are not necessarily at the exact same level.
- However, the difference in height between the left and right subtrees is kept to a minimum (usually a difference of no more than 1).
- This ensures that the tree stays "bushy" and the search time remains fast ($O(\log n)$).

### Pro-Tip for FAANG Interviews

There is a very famous interview question: "Given a sorted array, convert it into a Height-Balanced BST."

#### The Logic:

1. **Find the mid element of the array.**
2. **Make mid the root.**
3. **Recursively do the same for the left half** (to build root.left).
4. **Recursively do the same for the right half** (to build root.right).