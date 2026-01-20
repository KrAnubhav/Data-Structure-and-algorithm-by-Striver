---
title: DFS Traversal (Depth First Search)
lecture: G-06
topic: Graphs
tags: [graph, dfs, traversal, recursion, backtracking]
difficulty: Easy
created: 2026-01-20
updated: 2026-01-20
---

# G-06. DFS Traversal (Depth First Search)

> **Lecture:** G-06
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-05-BFS|G-05. BFS Traversal]]
> **Next:** [[Step 15 - Graphs/G-07-Number-of-Provinces|G-07. Number of Provinces (Next)]]

---

## 🧐 What is DFS?

**DFS** stands for **Depth First Search**.
Unlike BFS (which goes level-wise), DFS goes **Depth-wise**.

-   Start at a node.
-   Keep moving forward to a neighbor, then that neighbor's neighbor, going as **deep** as possible.
-   When you hit a dead end (or all neighbors are visited), you **backtrack** to the previous node and try another path.

> [!important] Key Mechanism
> DFS relies heavily on **Recursion** (which uses the System Stack).

---

## 🖼️ Visualizing Depth

Let's take this graph:

```
        1
       / \
      2   3
     /     \
    5       4
    |       |
    6       8
            |
            7
```

**How DFS Moves (Starting at 1):**

1.  Start at **1**.
2.  Go to a neighbor, say **2**.
3.  From **2**, go deep to **5**.
4.  From **5**, go deep to **6**.
5.  **6** has no unvisited neighbors. **Backtrack** $\to$ 5 $\to$ 2 $\to$ 1.
6.  Back at **1**, go to the other neighbor **3**.
7.  From **3**, go deep to **4** $\to$ **8** $\to$ **7**.

**DFS Order:** `1 -> 2 -> 5 -> 6 -> 3 -> 4 -> 8 -> 7`

> [!note] Note on Order
> The order depends on the adjacency list. `1 -> 3 -> ...` is also a valid DFS if 3 appears before 2 in the list.

---

## 🛠️ Logic & Recursion

To implement DFS, we simply trust the **Recursive Leap of Faith**.

### Notations:
-   `visited[]`: Array to keep track of visited nodes.
-   `adj`: Adjacency list.
-   `dfs(node)`: The recursive function.

### The Algorithm Steps:
1.  **Mark** the current `node` as **Visited**.
2.  **Add** the node to the output list (or print it).
3.  **Explore** all neighbors of the current node:
    -   If a neighbor is **NOT visited**, recursively call `dfs(neighbor)`.
    -   If it IS visited, ignore it.

---

## 💻 Step-by-Step Dry Run

**Graph:**
```
    1 —— 2
    |
    3
```
**Adjacency List:**
-   1: `[2, 3]`
-   2: `[1]`
-   3: `[1]`

**Execution:**
1.  **Call `dfs(1)`**
    -   Mark `1` visited. List: `[1]`
    -   Loop neighbors of 1: `2`, `3`.
    -   **Neighbor 2:** Not visited. **Call `dfs(2)`**.
        -   Mark `2` visited. List: `[1, 2]`
        -   Loop neighbors of 2: `1`.
        -   **Neighbor 1:** Visited! Ignore.
        -   Function `dfs(2)` returns (Backtracks).
    -   **Neighbor 3:** Not visited. **Call `dfs(3)`**.
        -   Mark `3` visited. List: `[1, 2, 3]`
        -   Loop neighbors of 3: `1`.
        -   **Neighbor 1:** Visited! Ignore.
        -   Function `dfs(3)` returns.
    -   Function `dfs(1)` returns.

---

## 💻 Java Implementation

Here is the standard recursive implementation.

```java
import java.util.*;

class Solution {
    
    // The Main Function
    public ArrayList<Integer> dfsOfGraph(int V, ArrayList<ArrayList<Integer>> adj) {
        // 1. Visited Array
        boolean[] vis = new boolean[V]; // 0-based indexing assumed
        
        // 2. Result List
        ArrayList<Integer> ls = new ArrayList<>();
        
        // 3. Handle Connected Components (Optional but recommended generic practice)
        // For simple problems with 1 component starting at 0:
        dfs(0, adj, vis, ls);
        
        // For multiple components, loop 0 to V-1 and call dfs if !vis[i]
        
        return ls;
    }

    // The Recursive DFS Function
    private void dfs(int node, ArrayList<ArrayList<Integer>> adj, boolean[] vis, ArrayList<Integer> ls) {
        // Step 1: Mark visited
        vis[node] = true;
        
        // Step 2: Add to result
        ls.add(node);
        
        // Step 3: Traverse Neighbors
        for (Integer neighbor : adj.get(node)) {
            // Only make a recursive call if not visited
            if (!vis[neighbor]) {
                dfs(neighbor, adj, vis, ls);
            }
        }
    }
}
```

---

## ⏱️ Complexity Analysis

This is asked in **every interview**.

### 1. Space Complexity: $O(N)$
-   **Visited Array:** Stores $N$ nodes $\to O(N)$.
-   **Result List:** Stores $N$ nodes $\to O(N)$.
-   **Recursion Stack Space:**
    -   In the worst case (Skewed Graph like a straight line $1-2-3-4...$), the recursion goes $N$ levels deep.
    -   Stack Cost: **$O(N)$**.

### 2. Time Complexity: $O(N + 2E)$
-   **$O(N)$**: The `dfs` function is called exactly **once** for every node (due to the `visited` check).
-   **$O(2E)$**: Inside every `dfs` call, the `for` loop runs over the **neighbors**.
    -   Total iterations = Sum of all degrees.
    -   Total Degree = $2 \times \text{Edges}$ (in undirected graph).
-   **Total:** $O(N) + O(2E)$.

> [!tip] Comparison with BFS
> Both BFS and DFS have the **same** Time Complexity $O(N+E)$ and Space Complexity $O(N)$. The difference is the **pattern** of traversal.

---

## 🎯 Key Takeaways

1.  **Depth First:** Always goes deep before going wide.
2.  **Backtracking:** When stuck, return to the parent and try the next path.
3.  **Components:** Like BFS, if the graph is disconnected, loop from `1` to `N` to call `dfs()` on unvisited nodes.
4.  **Data Structure:** Uses **Stack** (implicitly via Recursion).

---
