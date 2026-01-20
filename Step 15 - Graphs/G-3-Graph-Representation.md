---
title: Graph Representation in Java | Adjacency Matrix vs List
lecture: G-2
topic: Graphs
tags: [graph, representation, java, matrix, list, weighted]
difficulty: Easy
created: 2026-01-20
updated: 2026-01-20
---

# G-2. Graph Representation in Java | Adjacency Matrix & List

> **Lecture:** G-2
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-1-Introduction-to-Graph|G-1. Introduction]]
> **Next:** [[Step 15 - Graphs/G-3-Connected-Components|G-3. Connected Components (Next)]]

---

## 🧐 The Challenge
In the previous video, we learned *what* a graph is (Nodes & Edges).
Now, the big question is: **How do we store this complex structure in our code?**

We need a way to represent:
1.  **Nodes** (Entities)
2.  **Edges** (Relationships between them)

---

## 📥 Part 1: Logic & Input Format

Before storing it, we must understand how the **Input** is given in coding problems.

### Typical Input Format
Usually, you are given two integers, $N$ and $M$:
-   $N$: Number of Nodes
-   $M$: Number of Edges
-   Followed by $M$ lines, each representing an **edge** between two nodes ($u$ and $v$).

### Example Graph
```
      1 —— 2
      |    |
      |    |
      3 —— 4
       \  /
        5
```

### Corresponding Input
```text
5 6        <-- N=5 Nodes, M=6 Edges
1 2        <-- Edge between 1 and 2
1 3        <-- Edge between 1 and 3
3 4        <-- ...
2 4
2 5
4 5
```

> [!note] Note
> The order of edges ($1-2$ vs $2-1$) or the order of lines does **not** matter. As long as the connection is specified, it's valid.

---

## 🗄️ Part 2: Storage Methods

We use two primary data structures to store graphs:
1.  **Adjacency Matrix** (2D Array)
2.  **Adjacency List** (Array of Lists) `(Most Common ✅)`

---

## 🧱 Method 1: Adjacency Matrix

An **Adjacency Matrix** is a 2D array of size $(N+1) \times (N+1)$ where the value at `matrix[i][j]` represents whether there is an edge between $i$ and $j$.

### 🔹 Logic
-   If we have an edge between **$u$** and **$v$**:
    -   Mark `adj[u][v] = 1`
    -   Mark `adj[v][u] = 1` (because it's undirected!)

### 🔹 Visualization
For the example graph above ($N=5$):

**Table Representation (Indices 0 to 5):**
```
      0  1  2  3  4  5
    ┌──────────────────┐
  0 | 0  0  0  0  0  0 |
  1 | 0  0  1  1  0  0 |  <-- 1 connects to 2, 3
  2 | 0  1  0  0  1  1 |  <-- 2 connects to 1, 4, 5
  3 | 0  1  0  0  1  0 |  <-- 3 connects to 1, 4
  4 | 0  0  1  1  0  1 |  <-- 4 connects to 2, 3, 5
  5 | 0  0  1  0  1  0 |  <-- 5 connects to 2, 4
    └──────────────────┘
```

### 💻 Java Implementation
```java
// N = 5, M = 6
int n = 5;
int m = 6;

// Declare the matrix (1-based indexing, so size is n+1)
int[][] adj = new int[n + 1][n + 1];

// Representing the edges
// Edge 1--2
adj[1][2] = 1;
adj[2][1] = 1;

// Edge 1--3
adj[1][3] = 1;
adj[3][1] = 1;

// ... and so on for all M edges
```

### ⚠️ Analysis (Why not use this?)
-   **Space Complexity:** $O(N^2)$
    -   We created a $6 \times 6$ matrix.
    -   If $N = 10^5$, size = $10^{10}$ cells $\approx 40$ GB RAM! 💥
    -   **Verdict:** Too expensive for large graphs.
-   **Time Complexity:** $O(1)$ to check if an edge exists.

> [!warning] Constraint Check
> Only use Adjacency Matrix if **$N \le 1000$**.

---

## 📜 Method 2: Adjacency List (Preferred)

Instead of a giant table with mostly zeros, we use a **List of Lists**.
Each index $i$ stores a list of all **neighbors** connected to node $i$.

### 🔹 Logic
-   Use `ArrayList<ArrayList<Integer>>`.
-   At index $u$, add $v$.
-   At index $v$, add $u$.

### 🔹 Visualization

```
Index | Neighbors (List)
------------------------
  0   | [ ]
  1   | [ 2, 3 ]
  2   | [ 1, 4, 5 ]
  3   | [ 1, 4 ]
  4   | [ 2, 3, 5 ]
  5   | [ 2, 4 ]
```

**Memory View:**
```
adj[1] ───> {2, 3}
adj[2] ───> {1, 4, 5}
adj[3] ───> {1, 4}
adj[4] ───> {2, 3, 5}
adj[5] ───> {2, 4}
```

### 💻 Java Implementation
```java
// Logic for Undirected Graph

// 1. Define the structure
ArrayList<ArrayList<Integer>> adj = new ArrayList<>();

// 2. Initialize empty lists for each node (0 to n)
for (int i = 0; i <= n; i++) {
    adj.add(new ArrayList<>());
}

// 3. Add Edges (Example Input: u, v)
// Edge 1--2
adj.get(1).add(2);
adj.get(2).add(1);

// Edge 1--3
adj.get(1).add(3);
adj.get(3).add(1);

// ... for all M edges
```

### ✅ Analysis
-   **Space Complexity:** $O(2 \times E)$ or $O(E)$
    -   Why $2E$? Because every edge ($u-v$) is stored twice: once at $u$ and once at $v$.
    -   For $M=6$, we store 12 integers. Much smaller than $N^2$ (25)!
-   **Verdict:** This is the standard way to solve graph problems.

---

## 🔄 Variation 1: Directed Graphs

In a **Directed Graph**, edges have arrows ($u \to v$).
This means you can go from $u$ to $v$, but **NOT** from $v$ to $u$ (unless specified).

```
    1 ──→ 2
```

### 💻 Code Change
Only add the edge to the source node's list.

```java
// Edge 1 -> 2
adj.get(1).add(2); 

// Do NOT do adj.get(2).add(1);
```

**Adjacency Matrix:**
```java
adj[1][2] = 1;
// Do NOT set adj[2][1] = 1;
```

**Space:** $O(E)$ (since we store it only once).

---

## ⚖️ Variation 2: Weighted Graphs

Sometimes, edges have **Weights** (Costs).

```
    1 ──(weight: 5)── 2
```

We need to store **two** things for every neighbor:
1.  The **Node** it connects to.
2.  The **Weight** of that connection.

### 🔹 Storage Strategy
Instead of `ArrayList<Integer>`, we use `ArrayList<Pair>`.

### 💻 Java Implementation using Pair Class

```java
// 1. Create a Pair class
class Pair {
    int node;
    int weight;
    
    Pair(int _node, int _weight) {
        this.node = _node;
        this.weight = _weight;
    }
}

// 2. Define Adjacency List
ArrayList<ArrayList<Pair>> adj = new ArrayList<>();

// 3. Add Edge with Weight (u=1, v=2, w=5)
adj.get(1).add(new Pair(2, 5));
adj.get(2).add(new Pair(1, 5)); // If undirected
```

### 🔹 Visualization (Weighted List)

```
Index | Neighbors: (Node, Weight)
---------------------------------
  1   | [ (2, 5) ]
  2   | [ (1, 5) ]
```

---

## 🎯 Summary Comparison

| Feature | Adjacency Matrix | Adjacency List |
| :--- | :--- | :--- |
| **Structure** | `int[][]` | `ArrayList<ArrayList<T>>` |
| **Space** | $O(N^2)$ (Heavy ❌) | $O(2E)$ (Efficient ✅) |
| **Lookup (u-v)** | $O(1)$ | $O(Degree)$ |
| **Iterate Neighbors** | $O(N)$ | $O(Degree)$ |
| **Best For** | Dense Graphs, Small N | Sparse Graphs, Most Problems |

> [!tip] Pro Tip
> Use **Adjacency List** for 99% of interviews and Competitive Programming problems because $N$ is usually $10^5$.

---
