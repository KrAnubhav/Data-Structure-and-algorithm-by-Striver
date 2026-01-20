---
title: Connected Components in a Graph
lecture: G-04
topic: Graphs
tags: [graph, connected-components, traversal, logic]
difficulty: Easy
created: 2026-01-20
updated: 2026-01-20
---

# G-04. Connected Components in a Graph

> **Lecture:** G-04
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-03-Graph-Representation|G-03. Graph Representation (Previous)]]
> **Next:** [[Step 15 - Graphs/G-05-BFS|G-05. BFS Traversal (Next)]]

---

## 🧐 What are Connected Components?

Until now, we have mostly seen **Connected Graphs** where you can reach any node from any other node (indirectly).

**Example of what we've seen:**
```
    1 —— 2
    |    |
    3 —— 4
```
You can say the whole structure is ONE graph.

**❓ BUT, is this a single graph?**

```
    Component 1      Component 2      Component 3      Component 4
    
    1 —— 2             5 —— 7             8 —— 9             10
    |    |             |
    3 —— 4             6
```
**(Imagine all these 4 pieces are part of ONE input)**

-   **You might say:** "No, these are 4 different graphs!"
-   **Actually:** In coding problems, this is treated as **A SINGLE GRAPH** with **4 Connected Components**.

---

## 📥 Why does this matter? (The Problem)

Imagine you are given $N=10$ nodes and you need to perform a **Traversal** (like BFS or DFS which we will learn soon).

### The Standard Approach Fails ❌
If you just start your traversal from **Node 1**:
1.  You will visit: `1 -> 2 -> 3 -> 4`
2.  The algorithm stops because there is no edge connecting `4` to `5` or others.
3.  **Result:** You miss visiting nodes `5, 6, 7, 8, 9, 10`.

This is why we cannot simply start a traversal from a single node and expect to visit the entire graph if it has multiple components.

---

## 🛠️ The Solution: "Visited Array" Logic

To handle disconnected components, we use a standard **Algorithm Pattern** that involves a `visited` array.

### 🔹 Core Logic
1.  Create a `visited` array initialized to `false`.
2.  Run a loop from `1` to `N` (for all nodes).
3.  If a node `i` is **NOT Visited**:
    -   Call the Traversal Function (BFS/DFS) starting from `i`.
    -   This traversal will visit **all nodes in that particular component** and mark them as visited.

### 🔹 Dry Run Visualization

**Initial State:**
`visited` = `[F, F, F, F, F, F, F, F, F, F]` (Indices 1 to 10)

**Step 1:** Loop `i = 1`
-   Is `1` visited? **No**.
-   **Start Traversal(1)**:
    -   Visits `1, 2, 3, 4`.
    -   Marks them `True`.
-   **Traversal Ends**.

**Step 2:** Loop `i = 2`
-   Is `2` visited? **Yes** (marked in Step 1).
-   **Skip**.

**Step 3:** Loop `i = 3, 4`
-   Visited? **Yes**.
-   **Skip**.

**Step 4:** Loop `i = 5`
-   Is `5` visited? **No**.
-   **Start Traversal(5)**:
    -   Visits `5, 6, 7`.
    -   Marks them `True`.
-   **Traversal Ends**.

**Step 5:** Loop `i = 8`
-   Is `8` visited? **No**.
-   **Start Traversal(8)**:
    -   Visits `8, 9`.
    -   Marks them `True`.

**Step 6:** Loop `i = 10`
-   Is `10` visited? **No**.
-   **Start Traversal(10)**:
    -   Visits `10`.
    -   Marks it `True`.

**✅ Result:** All 10 nodes are visited!

---

## 💻 Java Implementation Code Pattern

This is the standard template you will use for **BFS** and **DFS** problems to handle disconnected graphs.

```java
// Given:
// int N = 10;
// ArrayList<ArrayList<Integer>> adj = ...;

// 1. Create Visited Array (size N+1 for 1-based indexing)
boolean[] visited = new boolean[N + 1];

// 2. The Universal Loop
for (int i = 1; i <= N; i++) {
    // 3. Check if unvisited
    if (!visited[i]) {
        // 4. Call Traversal (BFS or DFS)
        traversal(i, adj, visited);
    }
}

// Dummy Traversal Function for context
void traversal(int node, ArrayList<ArrayList<Integer>> adj, boolean[] visited) {
    visited[node] = true;
    // ... logic to visit neighbors ...
}
```

---

## 🎯 Key Takeaways

1.  **Multiple Components:** A graph isn't always one single piece; it can be broken into multiple disconnected parts.
2.  **A Single Start Node is Not Enough:** Starting from Node 1 won't reach disconnected parts.
3.  **The Master Loop:** We ALWAYS loop from `1 to N` and start a traversal only if the node is `!visited`.
4.  **Visited Array:** Crucial to avoid re-processing nodes (and avoiding cycles).

> [!tip] Pro Tip
> This pattern is also used to **count** the number of connected components. Just increment a counter inside the `if(!visited[i])` block!

---
