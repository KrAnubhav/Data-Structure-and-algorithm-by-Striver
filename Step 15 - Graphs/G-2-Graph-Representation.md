---
title: Graph Representation in Java
lecture: G-2
topic: Graphs
tags: [graph, java, implementation, matrix, list]
difficulty: Easy
created: 2026-01-20
updated: 2026-01-20
---

# G-2. Graph Representation in Java

> **Lecture:** G-2
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-1-Introduction-to-Graph|G-1. Introduction]]
> **Next:** [[Step 15 - Graphs/README|Next Lecture (Coming Soon)]]

---

## 📥 Part 1: Graph Input Format

Usually given as $N$ (nodes) and $M$ (edges), followed by $M$ lines representing edges.

**Example Input:**
```text
5 6       <-- Nodes, Edges
1 2       <-- Edge between 1 and 2
1 3
3 4
2 4
2 5
4 5
```

---

## 🗄️ Part 2: Storage Methods

### Method 1: Adjacency Matrix

Create a 2D array of size `(N+1) x (N+1)` (for 1-based indexing).

**Logic:**
- If edge $u-v$ exists $\rightarrow$ `adj[u][v] = 1` and `adj[v][u] = 1`.

```java
int[][] adj = new int[n+1][n+1];

// For every edge u-v
adj[u][v] = 1;
adj[v][u] = 1; 
```

> [!warning] Space Complexity
> **Space:** $O(N^2)$
> This is expensive! Only use for small $N$ ($N \le 1000$).

---

### Method 2: Adjacency List (Recommended ✅)

Use an `ArrayList` of `ArrayLists`. Each index stores a list of its neighbors.

**Logic:**
- For edge $u-v$, add $v$ to $u$'s list, and $u$ to $v$'s list.

```java
ArrayList<ArrayList<Integer>> adj = new ArrayList<>();

// Initialize
for(int i = 0; i <= n; i++) 
    adj.add(new ArrayList<>());

// For every edge u-v
adj.get(u).add(v);
adj.get(v).add(u);
```

> [!success] Space Complexity
> **Space:** $O(2M)$ (Twice the number of edges)
> This is much more efficient for sparse graphs.

---

## ⚖️ Variations

### 1. Directed Graphs
Only add the edge in the direction specified.
```java
// u -> v
adj.get(u).add(v);
// Do NOT add u to v's list
```

### 2. Weighted Graphs
Instead of storing just the neighbor Integer, store a **Pair** `(node, weight)`.

```java
class Pair {
    int node;
    int weight;
    Pair(int n, int w) { node = n; weight = w; }
}

ArrayList<ArrayList<Pair>> adj = new ArrayList<>();

// Input: u v w
adj.get(u).add(new Pair(v, w));
adj.get(v).add(new Pair(u, w));
```

---

## 📊 Matrix vs List

| Feature | Adjacency Matrix | Adjacency List |
| :--- | :--- | :--- |
| **Space** | $O(N^2)$ | $O(2M)$ |
| **Lookup** | $O(1)$ | $O(\text{degree})$ |
| **Optimal For** | Dense Graphs | Sparse Graphs |

> [!tip] Recommendation
> Always use **Adjacency List** unless strict constraints require Matrix or $N$ is very small.
