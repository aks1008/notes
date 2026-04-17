# Graphs

## Table of Contents

1. **Key Components**
   - Vertex (Node)
   - Edge
   - Weight

2. **Core Classifications**
   - Directed vs. Undirected
   - Weighted vs. Unweighted
   - Cyclic vs. Acyclic

3. **Representation in Memory**
   - Adjacency Matrix
   - Adjacency List
   - Comparison Table

4. **Essential Graph Algorithms**
   - Traversal Algorithms
     - Breadth-First Search (BFS)
     - Depth-First Search (DFS)
   - Shortest Path & Optimization
     - Dijkstra's Algorithm
     - Bellman-Ford
     - Prim's & Kruskal's (MST)

5. **Common Use Cases**
   - Social Networks
   - Navigation
   - Dependency Resolution
   - Recommendation Engines

6. **Adjacency Matrix vs. Adjacency List**
   - Core Comparison Table
   - When to use Adjacency Matrix
   - When to use Adjacency List
   - Practical Examples
   - Summary Recommendation

7. **Python Implementation**
   - Adjacency List Implementation
   - Weighted Graph Implementation
   - Essential Operations
     - BFS Implementation
   - Python-Specific Tips

---

In Data Structures and Algorithms (DSA), a **Graph** is a non-linear data structure consisting of a finite set of vertices (or nodes) and a set of edges that connect these vertices. Unlike trees, graphs do not have a strict root-child hierarchy and can contain cycles.

### 1. Key Components
* **Vertex (Node):** The fundamental unit of a graph, representing an entity (e.g., a person in a social network).
* **Edge:** The link between two vertices. This can represents a relationship or a path.
* **Weight:** A numerical value assigned to an edge (e.g., distance, cost, or time).

### 2. Core Classifications
Graphs are categorized based on the nature of their edges:

* **Directed vs. Undirected:** In a **Directed Graph (Digraph)**, edges have a direction (u → v). In an **Undirected Graph**, edges are bidirectional.
* **Weighted vs. Unweighted:** **Weighted Graphs** have values on edges, while **Unweighted Graphs** treat all connections as equal.
* **Cyclic vs. Acyclic:** A **Cyclic Graph** contains at least one path that starts and ends at the same vertex. A **Directed Acyclic Graph (DAG)** is a special type often used in task scheduling and data processing.



---

### 3. Representation in Memory
There are two primary ways to store a graph in code:

| Representation | Data Structure | Best For... | Space Complexity |
| :--- | :--- | :--- | :--- |
| **Adjacency Matrix** | 2D Array / Matrix | Dense graphs; fast edge lookups. | $O(V^2)$ |
| **Adjacency List** | Array of Lists/Vectors | Sparse graphs; saving memory. | $O(V + E)$ |

---

### 4. Essential Graph Algorithms
To master graphs, you must understand these fundamental traversal and path-finding techniques:

#### Traversal Algorithms
* **Breadth-First Search (BFS):** Uses a **Queue** to visit neighbors level-by-level. It is ideal for finding the shortest path in an unweighted graph.
* **Depth-First Search (DFS):** Uses a **Stack** (or recursion) to go as deep as possible along a branch before backtracking. Useful for cycle detection and topological sorting.



#### Shortest Path & Optimization
* **Dijkstra’s Algorithm:** Finds the shortest path from a source to all other vertices in a weighted graph (with non-negative weights).
* **Bellman-Ford:** Similar to Dijkstra but can handle negative edge weights.
* **Prim’s & Kruskal’s:** Used to find the **Minimum Spanning Tree (MST)**, which connects all vertices with the minimum total edge weight.

---

### 5. Common Use Cases
* **Social Networks:** Representing friendships (LinkedIn, Facebook).
* **Navigation:** GPS systems calculating the fastest route between cities.
* **Dependency Resolution:** Package managers (like NuGet or NPM) determining the order of installations.
* **Recommendation Engines:** Suggesting products based on related user interests.

The choice between an **Adjacency Matrix** and an **Adjacency List** depends entirely on the nature of your data (the density of the graph) and the specific operations you need to perform.

There is no single "better" option; rather, there is a "better for this scenario" option.

### 1. The Core Comparison

| Feature | Adjacency Matrix | Adjacency List |
| :--- | :--- | :--- |
| **Data Structure** | 2D Array / Matrix | Array of linked lists or sets |
| **Space Complexity** | $O(V^2)$ | $O(V + E)$ |
| **Edge Lookup** | $O(1)$ (Direct access) | $O(V)$ (Must traverse list) |
| **Finding Neighbors**| $O(V)$ (Must check entire row) | $O(\text{Degree of V})$ (Only checks actual neighbors) |
| **Add/Delete Edge** | $O(1)$ | $O(V)$ (to find/remove in list) |



---

### 2. When to use an Adjacency Matrix
An Adjacency Matrix is better for **Dense Graphs**, where the number of edges ($E$) is close to the square of the number of vertices ($V^2$).

* **Fast Lookups:** If your algorithm constantly asks "Is Node A connected to Node B?", a matrix gives you the answer instantly.
* **Weighted Graphs:** Storing weights is very easy; you simply store the weight value at `matrix[i][j]` instead of a 1.
* **Matrix Math:** If you are using linear algebra (like Eigenvectors or PageRank), matrix representation is required.
* **Simplicity:** It is often easier to implement for small, fixed-size graphs.

---

### 3. When to use an Adjacency List
An Adjacency List is better for **Sparse Graphs**, which include most real-world networks (social networks, road maps, web links).

* **Memory Efficiency:** If you have 1 million users (vertices) but each only has 200 friends (edges), a matrix would require 1 trillion entries ($10^{12}$), whereas a list only stores the 200 connections per user.
* **Faster Iteration:** When performing a Breadth-First Search (BFS) or Depth-First Search (DFS), you only visit the actual neighbors. In a matrix, you would waste time checking every possible node to see if a connection exists.
* **Dynamic Graphs:** It is much easier to add new vertices to a list-based structure than it is to resize a 2D matrix.

---

### 4. Practical Example
Imagine representing a **Map of Cities in a Country**:
* There are 500 cities (Vertices).
* Each city is connected to maybe 3 or 4 other cities by highway (Edges).
* **Verdict:** Use an **Adjacency List**. A matrix would be 99% empty space (zeros), wasting memory.

Now imagine a **Flight Network** for a major airline:
* Every city they fly to has a direct flight to every other city.
* **Verdict:** Use an **Adjacency Matrix**. Since almost every node is connected, the $O(1)$ lookup speed outweighs the memory concerns.



### Summary Recommendation
* If your graph is **Sparse** (few edges): Use an **Adjacency List**.
* If your graph is **Dense** (many edges): Use an **Adjacency Matrix**.
* If you are **Unsure**: Start with an **Adjacency List**; it is the industry standard for most general DSA problems.


Implementing a graph in Python is often more concise because of built-in data structures like dictionaries and lists. The most common and flexible way to implement a graph in Python is using an **Adjacency List** via a dictionary.

### 1. Adjacency List Implementation (Python)
This implementation uses a class to encapsulate the graph logic. The dictionary keys represent the nodes, and the values are sets of neighboring nodes (using a `set` makes edge lookups $O(1)$ and prevents duplicate edges).

```python
class Graph:
    def __init__(self):
        # Initialize an empty dictionary for the adjacency list
        self.graph = {}

    def add_vertex(self, vertex):
        """Adds a new node to the graph."""
        if vertex not in self.graph:
            self.graph[vertex] = set()

    def add_edge(self, v1, v2, bidirectional=True):
        """Adds a connection between two nodes."""
        if v1 not in self.graph:
            self.add_vertex(v1)
        if v2 not in self.graph:
            self.add_vertex(v2)
            
        self.graph[v1].add(v2)
        if bidirectional:
            self.graph[v2].add(v1)

    def display(self):
        """Prints the graph structure."""
        for vertex, neighbors in self.graph.items():
            print(f"{vertex}: {', '.join(map(str, neighbors))}")

# Usage:
g = Graph()
g.add_edge("A", "B")
g.add_edge("A", "C")
g.add_edge("B", "D")
g.display()
```

---

### 2. Weighted Graph Implementation
If you need to store weights (e.g., distances or costs), you can modify the adjacency list to store a list of tuples or another dictionary: `self.graph[v1] = {v2: weight}`.



```python
class WeightedGraph:
    def __init__(self):
        self.graph = {}

    def add_edge(self, v1, v2, weight):
        if v1 not in self.graph:
            self.graph[v1] = {}
        self.graph[v1][v2] = weight

# Example: A -> B with weight 5
# self.graph['A'] = {'B': 5}
```

---

### 3. Essential Operations
Once the structure is built, you usually want to traverse it. Python’s `collections.deque` is the standard tool for implementing **BFS**, while **DFS** is typically done with recursion.



#### Breadth-First Search (BFS)
```python
from collections import deque

def bfs(graph, start_node):
    visited = {start_node}
    queue = deque([start_node])
    
    while queue:
        vertex = queue.popleft()
        print(vertex, end=" ")
        
        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

---

### Python-Specific Tips
* **`defaultdict`:** Use `from collections import defaultdict` for the adjacency list to avoid checking if a key exists before appending.
    * `self.graph = defaultdict(list)`
* **Memory Efficiency:** If you have millions of nodes and the graph is static, consider using a **Compressed Sparse Row (CSR)** format or libraries like `NetworkX` or `PyG` (PyTorch Geometric) for high-performance computing.
* **Set vs List:** Use a `set` for neighbors if you frequently check "Is A connected to B?". Use a `list` if the order of insertion or duplicate edges matters.