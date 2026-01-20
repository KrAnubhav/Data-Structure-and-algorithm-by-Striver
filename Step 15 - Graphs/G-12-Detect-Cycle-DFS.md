---
title: Detect Cycle in Undirected Graph using DFS
lecture: G-12
topic: Graphs
tags: [graph, dfs, cycle-detection, undirected, recursion]
difficulty: Medium
created: 2026-01-20
updated: 2026-01-20
---

# G-12. Detect Cycle in Undirected Graph (DFS)

> **Lecture:** G-12
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-11-Detect-Cycle-BFS|G-11. Detect Cycle using BFS]]
> **Next:** [[Step 15 - Graphs/G-13-Distance-Nearest-Cell|G-13. Distance of Nearest Cell (Next)]]

---

## 🧐 The Problem

**Problem Statement:**
Given an undirected graph with $V$ vertices and $E$ edges, detect if the graph contains a **cycle** using **DFS (Depth First Search)**.

**What is a Cycle?**
Start from a node, traverse through edges, and **come back to the same node** without breaking any edge.

---

## 🖼️ Understanding DFS for Cycle Detection

**Example Graph:**
```
      1
     / \
    2   3
    |   |\
    5   4 6
     \ /
      7
```

**DFS Path:**
- Start at `1` → Go deep to `2` → `5` → `7` → `6` → `3`.
- At `3`, we check neighbors: `1`, `4`, `6`.
- `1` is **visited** and **NOT the parent** (we came from `6`, not `1`).
- **Cycle detected!**

**Key Insight:**
If during DFS, we encounter a **visited node** that is **NOT our parent**, it means we've reached a node via a different path → **Cycle exists!**

---

## 💡 The DFS Intuition

**Core Idea:**
- DFS goes **deep** into one path before backtracking.
- We track the **parent** of each node to avoid false positives.
- If we visit a node that:
  - Is **already visited** AND
  - Is **NOT our parent**
- Then we've found a cycle!

**Why track the parent?**
- In an undirected graph, edge `1-2` means both `1→2` and `2→1`.
- When at node `2` (came from `1`), we'll see `1` as a neighbor.
- But `1` is our parent, so it's NOT a cycle.
- We need to distinguish between "came from parent" vs "reached via different path".

---

## 🛠️ The Algorithm

### Recursive DFS Function:
```
dfs(node, parent):
    1. Mark node as visited
    2. For each neighbor of node:
        a. If neighbor is NOT visited:
            - Call dfs(neighbor, node)
            - If it returns true → return true
        b. If neighbor IS visited AND neighbor != parent:
            - Cycle found! Return true
    3. Return false (no cycle found)
```

### Main Function:
```
For each node i from 0 to V-1:
    If node i is NOT visited:
        If dfs(i, -1) returns true:
            Return true (cycle found)
Return false (no cycle in any component)
```

---

## 💻 Detailed Dry Run

**Graph:**
```
    1 —— 2
    |    |
    3 —— 4
```

**Adjacency List:**
```
1: [2, 3]
2: [1, 5]
3: [1, 4, 6]
4: [3]
5: [2, 7]
6: [3, 7]
7: [5, 6]
```

**Execution:**

1. **Call `dfs(1, -1)`:**
   - Mark `1` visited.
   - Neighbors: `2, 3`.
   - **Call `dfs(2, 1)`** (go deep first).

2. **Inside `dfs(2, 1)`:**
   - Mark `2` visited.
   - Neighbors: `1` (parent, skip), `5`.
   - **Call `dfs(5, 2)`**.

3. **Inside `dfs(5, 2)`:**
   - Mark `5` visited.
   - Neighbors: `2` (parent, skip), `7`.
   - **Call `dfs(7, 5)`**.

4. **Inside `dfs(7, 5)`:**
   - Mark `7` visited.
   - Neighbors: `5` (parent, skip), `6`.
   - **Call `dfs(6, 7)`**.

5. **Inside `dfs(6, 7)`:**
   - Mark `6` visited.
   - Neighbors: `3`, `7` (parent, skip).
   - **Call `dfs(3, 6)`**.

6. **Inside `dfs(3, 6)`:**
   - Mark `3` visited.
   - Neighbors: `1`, `4`, `6`.
   - Check `1`: **Visited AND NOT parent** (parent is `6`).
   - **Cycle detected!** Return `true`.

7. **Backtrack:**
   - `dfs(3, 6)` returns `true`.
   - `dfs(6, 7)` returns `true`.
   - ... all the way back to main.
   - **Result:** Cycle exists!

---

## 💻 Java Implementation

```java
import java.util.*;

class Solution {
    // Main Function
    public boolean isCycle(int V, ArrayList<ArrayList<Integer>> adj) {
        boolean[] vis = new boolean[V];
        
        // Handle multiple components
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                if (dfs(i, -1, vis, adj)) {
                    return true; // Cycle found
                }
            }
        }
        
        return false; // No cycle
    }
    
    // DFS Function
    private boolean dfs(int node, int parent, boolean[] vis, ArrayList<ArrayList<Integer>> adj) {
        // Mark current node as visited
        vis[node] = true;
        
        // Check all neighbors
        for (Integer neighbor : adj.get(node)) {
            // If neighbor is NOT visited
            if (!vis[neighbor]) {
                // Recursively call DFS
                if (dfs(neighbor, node, vis, adj)) {
                    return true; // Cycle found in recursion
                }
            }
            // If neighbor IS visited AND is NOT the parent
            else if (neighbor != parent) {
                return true; // Cycle detected!
            }
        }
        
        return false; // No cycle found from this node
    }
}
```

---

## 🔍 Key Logic Explained

### The Critical Check:
```java
if (!vis[neighbor]) {
    if (dfs(neighbor, node, vis, adj)) {
        return true; // Propagate cycle detection upwards
    }
}
else if (neighbor != parent) {
    return true; // Found a visited non-parent node → Cycle!
}
```

**Why return true immediately?**
- Once we detect a cycle anywhere in the recursion tree, we propagate `true` all the way up.
- No need to check other neighbors once a cycle is found.

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(V + 2E)$
-   **Outer Loop:** Runs for all $V$ vertices.
-   **DFS:** Each node is visited once, and each edge is explored twice (undirected).
-   **Total:** $O(V) + O(2E) \approx O(V + E)$.

### Space Complexity: $O(V)$
-   **Visited Array:** $O(V)$.
-   **Recursion Stack:** $O(V)$ in worst case (skewed graph like a straight line).

---

## 🎯 Key Takeaways

1.  **DFS Goes Deep:** Unlike BFS (level-wise), DFS explores one path completely before backtracking.
2.  **Parent Tracking:** Essential to avoid false cycle detection from bidirectional edges.
3.  **Recursion Propagation:** Once `true` is returned, it propagates all the way up.
4.  **Components:** Always loop through all nodes to handle disconnected graphs.

---

## 🔄 BFS vs DFS for Cycle Detection

| Feature | BFS (G-10) | DFS (G-11) |
|---------|------------|------------|
| **Data Structure** | Queue | Recursion Stack |
| **Traversal** | Level-wise | Depth-wise |
| **Parent Tracking** | Store in Queue | Pass as parameter |
| **Space** | $O(V)$ Queue | $O(V)$ Stack |
| **Time** | $O(V + E)$ | $O(V + E)$ |

**Both are equally valid!** Choose based on preference or problem constraints.

---
