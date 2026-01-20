---
title: Number of Islands (LeetCode 200)
lecture: G-08
topic: Graphs
tags: [graph, bfs, dfs, matrix, grid, islands, 8-directions]
difficulty: Medium
created: 2026-01-20
updated: 2026-01-20
---

# G-08. Number of Islands

> **Lecture:** G-08
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-07-Number-of-Provinces|G-07. Number of Provinces]]
> **Next:** [[Step 15 - Graphs/G-09-Flood-Fill|G-09. Flood Fill (Coming Soon)]]

---

## 🧐 The Problem

**Problem Statement:**
You are given an $N \times M$ grid where:
- `0` = Water
- `1` = Land

Find the **number of islands**.

**What is an Island?**
An island is formed by connecting adjacent lands **horizontally, vertically, or diagonally** (all 8 directions). It is surrounded by water.

---

## 🖼️ Visualizing the Grid as a Graph

**Example Grid:**
```
0  1  1  0  0
0  1  0  0  1
0  0  0  1  1
0  0  0  0  0
1  1  0  1  0
```

**How to think of this as a Graph?**
- Each cell `(row, col)` is a **Node**.
- Two nodes are **connected** if:
  - Both are `1` (Land).
  - They are adjacent in any of the **8 directions**.

**Islands in this Grid:**
```
Island 1:        Island 2:      Island 3:
(0,1), (0,2)     (1,4)          (4,0), (4,1)
(1,1)            (2,3), (2,4)

Total Islands = 3
```

---

## 🛠️ The Solution Strategy

This is essentially **counting Connected Components** in a 2D grid!

### Algorithm:
1. Create a `visited` matrix (same size as grid).
2. Initialize `count = 0`.
3. Loop through every cell `(i, j)`:
   - If `grid[i][j] == 1` AND `!visited[i][j]`:
     - Increment `count++` (Found a new island!).
     - Call `BFS(i, j)` or `DFS(i, j)` to mark the **entire island** as visited.
4. Return `count`.

---

## 💡 The 8-Direction Neighbor Trick

Instead of writing 8 separate `if` conditions for neighbors, use a **loop**!

**Neighbors of `(row, col)`:**
```
(row-1, col-1)  (row-1, col)  (row-1, col+1)
(row,   col-1)  (row,   col)  (row,   col+1)
(row+1, col-1)  (row+1, col)  (row+1, col+1)
```

**Pattern:**
- Row varies: `-1, 0, +1`
- Col varies: `-1, 0, +1`

**Code:**
```java
for (int delRow = -1; delRow <= 1; delRow++) {
    for (int delCol = -1; delCol <= 1; delCol++) {
        int nRow = row + delRow;
        int nCol = col + delCol;
        // Check validity and conditions...
    }
}
```

> [!tip] Skip the Current Cell
> When `delRow == 0` and `delCol == 0`, it's the current cell itself. It will already be marked visited, so it gets skipped automatically!

---

## 💻 Java Implementation (BFS Approach)

```java
import java.util.*;

class Solution {
    // Pair class to store (row, col)
    static class Pair {
        int row, col;
        Pair(int r, int c) {
            row = r;
            col = c;
        }
    }
    
    // Main Function
    public int numIslands(char[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        
        // Visited matrix
        boolean[][] vis = new boolean[n][m];
        
        int count = 0;
        
        // Loop through every cell
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                // If it's land and not visited
                if (grid[i][j] == '1' && !vis[i][j]) {
                    count++; // New island found
                    bfs(i, j, vis, grid);
                }
            }
        }
        
        return count;
    }
    
    // BFS Function
    private void bfs(int row, int col, boolean[][] vis, char[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        
        // Mark starting cell as visited
        vis[row][col] = true;
        
        // Queue for BFS
        Queue<Pair> q = new LinkedList<>();
        q.add(new Pair(row, col));
        
        // BFS Loop
        while (!q.isEmpty()) {
            Pair curr = q.poll();
            int r = curr.row;
            int c = curr.col;
            
            // Check all 8 neighbors
            for (int delRow = -1; delRow <= 1; delRow++) {
                for (int delCol = -1; delCol <= 1; delCol++) {
                    int nRow = r + delRow;
                    int nCol = c + delCol;
                    
                    // Check validity
                    if (nRow >= 0 && nRow < n && 
                        nCol >= 0 && nCol < m &&
                        grid[nRow][nCol] == '1' && 
                        !vis[nRow][nCol]) {
                        
                        vis[nRow][nCol] = true;
                        q.add(new Pair(nRow, nCol));
                    }
                }
            }
        }
    }
}
```

---

## 🔄 DFS Approach (Alternative)

You can also solve this using **DFS** instead of BFS. The logic remains the same!

```java
private void dfs(int row, int col, boolean[][] vis, char[][] grid) {
    int n = grid.length;
    int m = grid[0].length;
    
    vis[row][col] = true;
    
    // Check all 8 neighbors
    for (int delRow = -1; delRow <= 1; delRow++) {
        for (int delCol = -1; delCol <= 1; delCol++) {
            int nRow = row + delRow;
            int nCol = col + delCol;
            
            if (nRow >= 0 && nRow < n && 
                nCol >= 0 && nCol < m &&
                grid[nRow][nCol] == '1' && 
                !vis[nRow][nCol]) {
                
                dfs(nRow, nCol, vis, grid);
            }
        }
    }
}
```

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(N \times M) + O(N \times M \times 9)$
-   **Outer Loop:** Visits every cell once $\to O(N \times M)$.
-   **BFS/DFS:** In the worst case (entire grid is land), BFS visits all $N \times M$ cells.
    -   For each cell, we check **9 neighbors** (8 directions + current).
    -   Total: $O(N \times M \times 9)$.
-   **Simplified:** $O(N \times M)$.

### Space Complexity: $O(N \times M)$
-   **Visited Matrix:** $O(N \times M)$.
-   **Queue (BFS):** At most $O(N \times M)$ in worst case.
-   **Recursion Stack (DFS):** At most $O(N \times M)$ in worst case (skewed path).

> [!note] Can we avoid the Visited matrix?
> Yes! You can modify the original `grid` itself by marking visited cells as `'0'` or `'#'`. But this modifies the input, which may not always be acceptable.

---

## 🎯 Key Takeaways

1.  **2D Grid = Graph:** Think of each cell as a node.
2.  **8-Direction Trick:** Use nested loops with `delRow` and `delCol` from `-1` to `+1`.
3.  **Counting Components:** The `count` variable tracks how many times we start a fresh BFS/DFS.
4.  **BFS vs DFS:** Both work! Choose based on preference (BFS uses Queue, DFS uses Recursion).

---
