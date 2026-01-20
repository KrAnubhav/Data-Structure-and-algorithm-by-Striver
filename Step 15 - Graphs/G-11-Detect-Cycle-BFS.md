---
title: Detect Cycle in Undirected Graph using BFS
lecture: G-10
topic: Graphs
tags: [graph, bfs, cycle-detection, undirected, queue]
difficulty: Medium
created: 2026-01-20
updated: 2026-01-20
---

# G-10. Detect Cycle in Undirected Graph (BFS)

> **Lecture:** G-10
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-9-Flood-Fill|G-9. Flood Fill]]
> **Next:** [[Step 15 - Graphs/G-11-Detect-Cycle-DFS|G-11. Detect Cycle using DFS (Next)]]

---

## 🧐 The Problem

**Problem Statement:**
Given an undirected graph with $V$ vertices and $E$ edges, detect if the graph contains a **cycle**.

**What is a Cycle?**
A cycle exists when you start from a node, traverse through edges, and **come back to the same node**.

---

## 🖼️ Understanding Cycle Detection

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

**Is there a cycle?**
Yes! Path: `3 → 4 → 7 → 6 → 3` forms a cycle.

**Key Insight:**
- Start BFS from node `1`.
- Go to `2` and `3` (different paths from `1`).
- From `2` → `5` → `7`.
- From `3` → `4` → `7` and `3` → `6` → `7`.
- **Collision!** Both `5` and `6` reach `7`.
- This collision happens because there's a cycle connecting them!

---

## 💡 The BFS Intuition

**Core Idea:**
If two nodes that came from **different parents** both try to visit the **same node**, it means there's a cycle!

**Why?**
- In BFS, we move level-by-level.
- If node `A` visits node `X` at level 2.
- And node `B` (different parent) also tries to visit `X` at level 2.
- They must have taken **different paths** to reach `X`.
- This is only possible if there's a **cycle**!

---

## 🛠️ The Algorithm

### Step-by-Step:
1. **Queue Structure:** Store `(node, parent)` pairs.
2. **Visited Array:** Track which nodes have been visited.
3. **BFS Traversal:**
   - Pop a node from the queue.
   - Check all its neighbors.
   - **If neighbor is NOT visited:**
     - Mark it visited.
     - Add `(neighbor, current_node)` to queue.
   - **If neighbor IS visited AND it's NOT the parent:**
     - **Cycle detected!** Return `true`.
4. **Handle Components:** Loop through all nodes (in case of disconnected components).

---

## 💻 Dry Run Example

**Graph:**
```
    1 —— 2
    |    |
    3 —— 4
```

**Adjacency List:**
```
1: [2, 3]
2: [1, 4]
3: [1, 4]
4: [2, 3]
```

**Execution:**

1. **Start BFS from 1:**
   - Queue: `[(1, -1)]`
   - Visited: `[F, T, F, F, F]`

2. **Pop (1, -1):**
   - Neighbors: `2, 3`
   - Add `(2, 1)` and `(3, 1)` to queue.
   - Queue: `[(2, 1), (3, 1)]`
   - Visited: `[F, T, T, T, F]`

3. **Pop (2, 1):**
   - Neighbors: `1` (parent, skip), `4` (not visited)
   - Add `(4, 2)` to queue.
   - Queue: `[(3, 1), (4, 2)]`
   - Visited: `[F, T, T, T, T]`

4. **Pop (3, 1):**
   - Neighbors: `1` (parent, skip), `4` (visited!)
   - **Is 4 the parent of 3?** No! (Parent is 1)
   - **Cycle detected!** Return `true`.

---

## 💻 Java Implementation

```java
import java.util.*;

class Solution {
    // Pair class to store (node, parent)
    static class Pair {
        int node, parent;
        Pair(int n, int p) {
            node = n;
            parent = p;
        }
    }
    
    // Main Function
    public boolean isCycle(int V, ArrayList<ArrayList<Integer>> adj) {
        boolean[] vis = new boolean[V];
        
        // Handle multiple components
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                if (detectCycle(i, adj, vis)) {
                    return true; // Cycle found in this component
                }
            }
        }
        
        return false; // No cycle in any component
    }
    
    // BFS Function to detect cycle
    private boolean detectCycle(int src, ArrayList<ArrayList<Integer>> adj, boolean[] vis) {
        Queue<Pair> q = new LinkedList<>();
        
        // Start BFS from source
        q.add(new Pair(src, -1));
        vis[src] = true;
        
        while (!q.isEmpty()) {
            Pair curr = q.poll();
            int node = curr.node;
            int parent = curr.parent;
            
            // Check all neighbors
            for (Integer neighbor : adj.get(node)) {
                // If not visited, add to queue
                if (!vis[neighbor]) {
                    vis[neighbor] = true;
                    q.add(new Pair(neighbor, node));
                }
                // If visited and NOT the parent → Cycle!
                else if (neighbor != parent) {
                    return true;
                }
            }
        }
        
        return false; // No cycle found from this source
    }
}
```

---

## 🔍 Key Logic Explained

### The Parent Check:
```java
if (!vis[neighbor]) {
    // Not visited → Safe to explore
}
else if (neighbor != parent) {
    // Visited AND not where we came from → CYCLE!
}
```

**Why check `neighbor != parent`?**
- In an undirected graph, edge `1-2` means both `1→2` and `2→1`.
- When at node `2` (came from `1`), we'll see `1` as a neighbor.
- But `1` is already visited (it's our parent!).
- This is **NOT a cycle**, just the edge we came from.
- So we skip it with `neighbor != parent`.

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(V + 2E)$
-   **Outer Loop:** Runs for all $V$ vertices.
-   **BFS:** Each node is visited once, and each edge is checked twice (undirected).
-   **Total:** $O(V) + O(2E) \approx O(V + E)$.

### Space Complexity: $O(V)$
-   **Visited Array:** $O(V)$.
-   **Queue:** At most $O(V)$ nodes in worst case.

---

## 🎯 Key Takeaways

1.  **Cycle Detection = Collision Detection:** If two different paths reach the same node, there's a cycle.
2.  **Parent Tracking:** Essential to avoid false positives from the bidirectional nature of undirected edges.
3.  **Components:** Always loop through all nodes to handle disconnected graphs.
4.  **BFS vs DFS:** Both can detect cycles. BFS uses a queue, DFS uses recursion (next lecture!).

---

## 🔄 Next Up

In the next lecture, we'll solve the same problem using **DFS** (Depth First Search) and compare the approaches!

---
