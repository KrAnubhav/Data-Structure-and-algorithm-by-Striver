---
title: Number of Provinces (LeetCode 547)
lecture: G-07
topic: Graphs
tags: [graph, dfs, bfs, connected-components, leetcode]
difficulty: Medium
created: 2026-01-20
updated: 2026-01-20
---

# G-07. Number of Provinces

> **Lecture:** G-07
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-06-DFS|G-06. DFS Traversal]]
> **Next:** [[Step 15 - Graphs/G-08-Number-of-Islands|G-08. Number of Islands (Next)]]

---

## 🧐 The Problem
**Problem Statement:**
Given an undirected graph with $V$ vertices, we say two vertices $u$ and $v$ belong to a single **province** if there is a path from $u$ to $v$. Find the number of provinces.

*(Basically, find the number of **Connected Components** in the graph)*

---

## 🖼️ Visualizing Provinces

Imagine the graph looks like this:

```
    Province 1         Province 2       Province 3
    
      1 —— 2             4 —— 5             7
      |                  |
      3                  6
```

-   **Node 1, 2, 3** calculate their connectivity $\to$ Province 1
-   **Node 4, 5, 6** calculate their connectivity $\to$ Province 2
-   **Node 7** is isolated $\to$ Province 3

**Total Provinces = 3**

> [!imporant] Key Insight
> A single BFS or DFS traversal starting from a node visits **ALL** nodes in that particular connected component (Province).

---

## 🛠️ The Solution Strategy

We need to count **how many times** we initiate a fresh Traversal.

### Algorithm:
1.  Create a `visited` array (size $V$ or $V+1$).
2.  Initialize `count = 0`.
3.  Loop from `1` to `V` (or `0` to `V-1`):
    -   If node `i` is **NOT Visited**:
        -   Increment `count++` (Found a new province!).
        -   Call `dfs(i)` or `bfs(i)` to mark the **entire province** as visited.
4.  Return `count`.

### Dry Run with the Example:
-   **Loop i=1:** Unvisited. `count=1`. Call `dfs(1)`.
    -   `dfs(1)` visits 1, 2, 3. Marks them `true`.
-   **Loop i=2:** Visited (by 1). Skip.
-   **Loop i=3:** Visited (by 1). Skip.
-   **Loop i=4:** Unvisited. `count=2`. Call `dfs(4)`.
    -   `dfs(4)` visits 4, 5, 6. Marks them `true`.
-   ...
-   **Loop i=7:** Unvisited. `count=3`. Call `dfs(7)`.
-   **Final Answer:** 3.

---

## 💻 Java Implementation

The problem usually gives an **Adjacency Matrix** (`isConnected[][]`).
We first convert it to an **Adjacency List** (for better complexity practice), then apply DFS.

```java
import java.util.*;

class Solution {
    // Main Function to find number of provinces
    public int findCircleNum(int[][] isConnected) {
        int V = isConnected.length;
        
        // Step 1: Convert Adjacency Matrix to List
        ArrayList<ArrayList<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            adj.add(new ArrayList<>());
        }
        
        // Matrix to List logic
        for (int i = 0; i < V; i++) {
            for (int j = 0; j < V; j++) {
                // If there's an edge and it's not a self-loop
                if (isConnected[i][j] == 1 && i != j) {
                    adj.get(i).add(j);
                    adj.get(j).add(i);
                }
            }
        }
        
        // Step 2: DFS Initialization
        boolean[] vis = new boolean[V];
        int count = 0;
        
        // Step 3: The Counting Loop
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                count++; // New Province found
                dfs(i, adj, vis);
            }
        }
        
        return count;
    }
    
    // Standard DFS Function
    private void dfs(int node, ArrayList<ArrayList<Integer>> adj, boolean[] vis) {
        vis[node] = true;
        for (Integer neighbor : adj.get(node)) {
            if (!vis[neighbor]) {
                dfs(neighbor, adj, vis);
            }
        }
    }
}
```

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(N) + O(V + 2E)$
-   **$O(N)$**: The outer loop runs for every node $0$ to $N-1$.
-   **$O(V + 2E)$**: The DFS function runs for total nodes + total degrees.
    -   Combined, the DFS visits every node once and every edge twice.
-   **Note on Matrix Conversion:** If specifically solving with the matrix conversion step, there is an added cost of $O(V^2)$ to build the list. But the core DFS logic is $O(N + 2E)$.

### Space Complexity: $O(N) + O(N)$
-   **Visited Array:** $O(N)$.
-   **Recursion Stack:** $O(N)$ (Worst case skewed graph).

---

## 🎯 Key Takeaways

1.  **Counting Components:** The "Number of Provinces" is literally the count of Connected Components.
2.  **Logic:** `count` increments only when we find an *unvisited* node from the main loop.
3.  **Versatility:** You can use either BFS or DFS. Both work perfectly!

---
