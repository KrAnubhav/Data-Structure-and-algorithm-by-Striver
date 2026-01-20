---
title: BFS Traversal (Breadth First Search)
lecture: G-5
topic: Graphs
tags: [graph, bfs, traversal, queue, level-order]
difficulty: Easy
created: 2026-01-20
updated: 2026-01-20
---

# G-5. BFS Traversal (Breadth First Search)

> **Lecture:** G-5
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-4-Connected-Components|G-4. Connected Components]]
> **Next:** [[Step 15 - Graphs/G-6-DFS|G-6. DFS Traversal (Next - Coming Soon)]]

---

## 🧐 What is BFS?

**BFS** stands for **Breadth First Search**.

As the name suggests ("Breadth"), it is a **Level Wise Traversal**.
- You start at a node (Level 0).
- Then visit all its immediate neighbors (Level 1).
- Then visit all their neighbors (Level 2).
- And so on...

> [!note] Analogy
> It's like throwing a stone in a pond. The ripples (levels) expand outwards uniformly.

---

## 🖼️ Understanding Levels with an Example

Let's take this graph with **Start Node = 1**:

```
      Level 0:          1
                      /   \
      Level 1:       2     6
                   / | \   | \
      Level 2:    3  4  7  9
                     |     |
      Level 3:       5     8
```

1.  **Level 0:** Node `1` (Starting Point)
2.  **Level 1:** Neighbors of `1` $\to$ `2, 6`
3.  **Level 2:** Neighbors of `2` (`3, 4`) & Neighbors of `6` (`7, 9`)
4.  **Level 3:** Neighbors of `4` (`5`) & Neighbors of `9` (`8`)

**BFS Traversal Order:** `1 -> 2, 6 -> 3, 4, 7, 9 -> 5, 8`
*(Order within a level can vary, e.g., `2, 6` or `6, 2` is fine)*

---

## 🛠️ Initial Configuration (What do we need?)

To implement BFS, we need two key data structures:

1.  **Queue (FIFO):**
    -   Because BFS is "First In First Out" (Level 0 processed before Level 1).
    -   Initially contains the **Start Node**.
    
2.  **Visited Array:**
    -   To keep track of nodes we have already touched/added to the queue.
    -   Prevents cycles and re-processing.
    -   Initially, only **Start Node** is marked `true`.

---

## 🎬 Step-by-Step Dry Run

**Graph:**
```
    1 —— 2
    |
    6
```
*(Simplified for Dry Run)*

**Step 1: Initialization**
-   Queue: `[ 1 ]`
-   Visited: `[F, T, F, F, ..., F]` (1 is visited)
-   Output: `[]`

**Step 2: Pop 1**
-   Remove `1` from Queue. Print it.
-   **Neighbors of 1:** `2, 6`.
-   Are they visited? No.
-   Add `2, 6` to Queue. Mark them visited.
-   Queue: `[ 2, 6 ]`
-   Visited: `2=T, 6=T`
-   Output: `[ 1 ]`

**Step 3: Pop 2**
-   Remove `2` from Queue. Print it.
-   **Neighbors of 2:** `1` (Visited ✅), others...
-   Only add unvisited neighbors.
-   Queue: `[ 6, ... ]`
-   Output: `[ 1, 2 ]`

**Step 4: Pop 6**
-   Remove `6`. Print it.
-   Process its neighbors...
-   Output: `[ 1, 2, 6, ... ]`

---

## 💻 Java Implementation

Here is the standard Java code for BFS Traversal.

```java
import java.util.*;

class Solution {
    // Function to return Breadth First Traversal of given graph.
    public ArrayList<Integer> bfsOfGraph(int V, ArrayList<ArrayList<Integer>> adj) {
        
        // 1. Result List
        ArrayList<Integer> bfs = new ArrayList<>();
        
        // 2. Visited Array (0-based indexing assumed here)
        boolean[] vis = new boolean[V];
        
        // 3. Queue for BFS
        Queue<Integer> q = new LinkedList<>();
        
        // 4. Initial Configuration (Start Node = 0)
        q.add(0);
        vis[0] = true;
        
        // 5. The BFS Loop
        while (!q.isEmpty()) {
            // Get the node from queue front
            Integer node = q.poll();
            bfs.add(node);
            
            // Get all neighbors
            for (Integer it : adj.get(node)) {
                // If neighbor is NOT visited
                if (vis[it] == false) {
                    vis[it] = true; // Mark visited
                    q.add(it);      // Add to queue
                }
            }
        }
        
        return bfs;
    }
}
```

> [!important] Note on Component Handling
> The above code assumes a single connected component starting from `0`.
> If the graph has multiple components (as learned in [[Step 15 - Graphs/G-4-Connected-Components|G-4]]), you need to wrap the BFS call inside the `for(i=0 to N)` loop!

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(N) + O(2E)$
-   **$O(N)$**: The `while` loop runs for every node exactly once (every node enters the queue once).
-   **$O(2E)$**: The inner `for` loop runs for all neighbors. Total neighbors = Total Degrees = $2 \times \text{Edges}$.
-   **Total:** $O(N + 2E)$ which is approximately $O(N + E)$.

### Space Complexity: $O(3N) \approx O(N)$
1.  **Queue:** Can store up to $N$ nodes in worst case.
2.  **Visited Array:** Size $N$.
3.  **Result List:** Size $N$.

---

## 🎯 Key Takeaways

1.  **Level Wise:** BFS moves layer by layer.
2.  **Queue:** The heart of BFS is a Queue Data Structure.
3.  **Visited:** Essential to avoid infinite loops in cyclic graphs.
4.  **Use Cases:** Shortest path in unweighted graphs, Level order traversal, Cycle detection.

---
