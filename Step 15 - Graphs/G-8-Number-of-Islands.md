---
title: Number of Islands (BFS/DFS on 2D Grid)
lecture: G-8
topic: Graphs
tags: [graph, bfs, dfs, matrix, 2d-grid, island]
difficulty: Medium
created: 2026-01-20
updated: 2026-01-20
---

# G-8. Number of Islands

> **Lecture:** G-8
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-7-Number-of-Provinces|G-7. Number of Provinces]]
> **Next:** [[Step 15 - Graphs/G-9-Flood-Fill|G-9. Flood Fill Algorithm (Next)]]

---

## 🧐 Problem Statement
Given a grid of size $n \times m$ where:
- `0` represents **Water**.
- `1` represents **Land**.

**Task:** Find the number of **islands**.
An island is formed by connecting adjacent lands **horizontally, vertically, or diagonally** in all **8 directions**.

### 🖼️ Visualizing Islands
```text
Grid:
  0  1  1  0
  0  1  1  0
  0  0  1  0
  0  0  0  0
  1  1  0  1
```
In this grid:
1.  The top-right cluster of `1`s is all connected (8-directions) $\to$ **Island 1**.
2.  The bottom-left `1 1` is connected $\to$ **Island 2**.
3.  The single `1` at the bottom-right is isolated $\to$ **Island 3**.

**Result:** 3 Islands.

---

## 💡 Intuition: Grid as a Graph

How is a 2D Matrix a Graph problem?
-   **Nodes:** Each cell `(row, col)` where value is `1`.
-   **Edges:** A connection exists if an adjacent cell is also a `1`.
-   **Goal:** Count the number of **Connected Components**.

Instead of an adjacency list, your "neighbors" are the **8 surrounding cells**.

---

## 🛠️ The 8-Direction "Delta" Trick

To find all 8 neighbors of a cell `(r, c)`, we can use two loops from `-1` to `1`.

```text
    (r-1, c-1)  (r-1, c)  (r-1, c+1)
    (r, c-1)     [r, c]    (r, c+1)
    (r+1, c-1)  (r+1, c)  (r+1, c+1)
```

**Logic:**
```java
for(int delrow = -1; delrow <= 1; delrow++) {
    for(int delcol = -1; delcol <= 1; delcol++) {
        int neighborRow = r + delrow;
        int neighborCol = c + delcol;
        // Check if inside boundaries, not visited, and IS land
    }
}
```

---

## 💻 Java Implementation (BFS-based)

We use a `Pair` class to store `(row, col)` in the Queue.

```java
import java.util.*;

// Helper class for Queue
class Pair {
    int first;
    int second;
    public Pair(int first, int second) {
        this.first = first;
        this.second = second;
    }
}

class Solution {
    // Main function to count islands
    public int numIslands(char[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        int[][] vis = new int[n][m];
        int count = 0;

        for (int row = 0; row < n; row++) {
            for (int col = 0; col < m; col++) {
                // If it is land and NOT visited
                if (vis[row][col] == 0 && grid[row][col] == '1') {
                    count++;
                    bfs(row, col, vis, grid);
                }
            }
        }
        return count;
    }

    private void bfs(int ro, int co, int[][] vis, char[][] grid) {
        vis[ro][co] = 1;
        Queue<Pair> q = new LinkedList<Pair>();
        q.add(new Pair(ro, co));
        
        int n = grid.length;
        int m = grid[0].length;

        while (!q.isEmpty()) {
            int row = q.peek().first;
            int col = q.peek().second;
            q.remove();

            // Traverse in all 8 neighbors
            for (int delrow = -1; delrow <= 1; delrow++) {
                for (int delcol = -1; delcol <= 1; delcol++) {
                    int nrow = row + delrow;
                    int ncol = col + delcol;

                    // Boundary checks & unvisited land check
                    if (nrow >= 0 && nrow < n && ncol >= 0 && ncol < m
                        && grid[nrow][ncol] == '1' && vis[nrow][ncol] == 0) {
                        vis[nrow][ncol] = 1;
                        q.add(new Pair(nrow, ncol));
                    }
                }
            }
        }
    }
}
```

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(N \times M)$
-   We iterate through the entire matrix twice (nested loops cover $N \times M$).
-   In the BFS, we visit each land cell exactly once.
-   For each cell, we check 8 neighbors (constant work).
-   **Overall:** $O(N \times M)$

### Space Complexity: $O(N \times M)$
-   **Visited Array:** Store $N \times M$ status.
-   **Queue:** In the worst case (all land), the queue might store a large portion of the grid.
-   **Overall:** $O(N \times M)$

---

## 🎯 Key Takeaways

1.  **Grid Traversal:** Instead of vertex numbers, use `(row, col)`.
2.  **8-Directions vs 4-Directions:** Always read the problem! If diagonal connections are allowed, use the "Delta" loops $(-1, 0, 1)$.
3.  **The Master Pattern:**
    -   Nested loop to traverse all cells.
    -   If unvisited land found $\to$ increment count and trigger BFS/DFS.
4.  **Visited Matrix:** Crucial for grid-based problems to stop infinite recursion/loops.

---
