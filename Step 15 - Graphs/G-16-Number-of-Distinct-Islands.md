---
title: Number of Distinct Islands (LeetCode 694)
lecture: G-16
topic: Graphs
tags: [graph, dfs, bfs, matrix, hashing, set, unique-shapes]
difficulty: Medium
created: 2026-01-20
updated: 2026-01-20
---

# G-16. Number of Distinct Islands

> **Lecture:** G-16
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-15-Number-of-Enclaves|G-15. Number of Enclaves]]
> **Next:** [[Step 15 - Graphs/G-17-Bipartite-Graph|G-17. Bipartite Graph (Coming Soon)]]

---

## 🧐 The Problem

**Problem Statement:**
Given a boolean 2D matrix of size $N \times M$, find the number of **distinct islands**.

**Island Definition:**
- A group of connected `1`s forms an island.
- Connected means **horizontally or vertically adjacent** (4 directions only).

**Distinct Definition:**
Two islands are considered **distinct** if one island is **NOT equal** to another (without rotation or reflection).

---

## 🖼️ Understanding Distinct vs Identical

**Example:**
```
Grid:
1  1  0  0  1
1  0  0  1  1
0  0  1  0  0
0  0  1  0  1
```

**Islands:**
```
Island 1:        Island 2:        Island 3:        Island 4:
1  1             1                1                1
1                1                1
```

**Analysis:**
- Island 1 at `(0,0)`: Shape `L`
- Island 2 at `(0,4)`: Shape `vertical line`
- Island 3 at `(2,2)`: Shape `vertical line`
- Island 4 at `(3,4)`: Shape `single cell`

**Are they distinct?**
- Island 2 and 3: **Same shape** (both vertical lines) → Count as 1
- Island 1, 2, 4: All **different shapes** → Count each

**Answer: 3 distinct islands**

> [!important] Rotation/Reflection NOT Allowed
> If an island is rotated or reflected to match another, they are still considered **different**!
> 
> Example:
> ```
> Island A:  1 1    Island B:  1
>            1                 1 1
> ```
> These are **different** even though B is A rotated 90°!

---

## 💡 The Core Challenge

**How do we represent island shapes?**
- We need a way to store each island's shape.
- Two identical shapes should have the **same representation**.
- Store all shapes in a **Set** → Set size = number of distinct islands.

**The Problem:**
```
Island at (0,0):  [(0,0), (0,1), (1,0)]
Island at (2,3):  [(2,3), (2,4), (3,3)]
```
These coordinates look different, but if they have the same shape, we need to recognize that!

---

## 🎯 The Solution: Normalize Coordinates

**Key Insight:**
Subtract the **base coordinates** (starting point) from all coordinates to normalize the shape!

**Example:**
```
Island 1 starts at (0,0):
Coordinates: (0,0), (0,1), (1,0)
Normalize: 
  (0,0) - (0,0) = (0,0)
  (0,1) - (0,0) = (0,1)
  (1,0) - (0,0) = (1,0)
Result: [(0,0), (0,1), (1,0)]

Island 2 starts at (2,3):
Coordinates: (2,3), (2,4), (3,3)
Normalize:
  (2,3) - (2,3) = (0,0)
  (2,4) - (2,3) = (0,1)
  (3,3) - (2,3) = (1,0)
Result: [(0,0), (0,1), (1,0)]

Same normalized shape! → Same island!
```

> [!tip] Traversal Order Matters
> Always traverse in the **same order** (e.g., Up, Right, Down, Left) to ensure consistent coordinate lists for identical shapes!

---

## 🛠️ The Algorithm

### Step-by-Step:
1. **Create `visited` matrix** and a **Set** to store unique shapes.
2. **Traverse the grid:**
   - For each unvisited `1`:
     - Create an empty list.
     - Call `dfs(row, col, baseRow, baseCol, list)`.
     - In DFS, add `(row - baseRow, col - baseCol)` to list.
     - After DFS completes, add list to Set.
3. **Return Set size** (number of distinct islands).

---

## 💻 Detailed Dry Run

**Grid:**
```
1  1  0  0  1
1  0  0  0  1
0  0  0  1  0
```

**Step 1: Find Island at (0,0)**
- Start DFS from `(0,0)`, base = `(0,0)`.
- Visit `(0,0)`, `(0,1)`, `(1,0)`.
- Normalize:
  - `(0,0) - (0,0) = (0,0)`
  - `(0,1) - (0,0) = (0,1)`
  - `(1,0) - (0,0) = (1,0)`
- List: `[(0,0), (0,1), (1,0)]`
- Add to Set.

**Step 2: Find Island at (0,4)**
- Start DFS from `(0,4)`, base = `(0,4)`.
- Visit `(0,4)`, `(1,4)`.
- Normalize:
  - `(0,4) - (0,4) = (0,0)`
  - `(1,4) - (0,4) = (1,0)`
- List: `[(0,0), (1,0)]`
- Add to Set.

**Step 3: Find Island at (2,3)**
- Start DFS from `(2,3)`, base = `(2,3)`.
- Visit `(2,3)`.
- Normalize:
  - `(2,3) - (2,3) = (0,0)`
- List: `[(0,0)]`
- Add to Set.

**Step 4: Result**
- Set contains 3 unique lists.
- **Answer: 3**

---

## 💻 Java Implementation

```java
import java.util.*;

class Solution {
    // Main Function
    public int numDistinctIslands(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        
        // Visited matrix
        boolean[][] vis = new boolean[n][m];
        
        // Set to store unique island shapes
        Set<List<String>> uniqueIslands = new HashSet<>();
        
        // Direction arrays for consistent traversal order
        int[] delRow = {-1, 0, +1, 0};
        int[] delCol = {0, +1, 0, -1};
        
        // Traverse entire grid
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (!vis[i][j] && grid[i][j] == 1) {
                    // Found a new island
                    List<String> shape = new ArrayList<>();
                    dfs(i, j, i, j, vis, grid, shape, delRow, delCol);
                    uniqueIslands.add(shape);
                }
            }
        }
        
        return uniqueIslands.size();
    }
    
    // DFS Function
    private void dfs(int row, int col, int baseRow, int baseCol, 
                     boolean[][] vis, int[][] grid, List<String> shape,
                     int[] delRow, int[] delCol) {
        int n = grid.length;
        int m = grid[0].length;
        
        // Mark as visited
        vis[row][col] = true;
        
        // Add normalized coordinates to shape
        String coordinate = (row - baseRow) + "," + (col - baseCol);
        shape.add(coordinate);
        
        // Explore all 4 neighbors
        for (int i = 0; i < 4; i++) {
            int nRow = row + delRow[i];
            int nCol = col + delCol[i];
            
            // Check validity
            if (nRow >= 0 && nRow < n && 
                nCol >= 0 && nCol < m && 
                !vis[nRow][nCol] && 
                grid[nRow][nCol] == 1) {
                
                dfs(nRow, nCol, baseRow, baseCol, vis, grid, shape, delRow, delCol);
            }
        }
    }
}
```

---

## 🔄 Alternative: Using Pair Objects

```java
import java.util.*;

class Solution {
    static class Pair {
        int row, col;
        Pair(int r, int c) { row = r; col = c; }
        
        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Pair)) return false;
            Pair p = (Pair) o;
            return row == p.row && col == p.col;
        }
        
        @Override
        public int hashCode() {
            return Objects.hash(row, col);
        }
    }
    
    public int numDistinctIslands(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        boolean[][] vis = new boolean[n][m];
        Set<List<Pair>> uniqueIslands = new HashSet<>();
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (!vis[i][j] && grid[i][j] == 1) {
                    List<Pair> shape = new ArrayList<>();
                    dfs(i, j, i, j, vis, grid, shape);
                    uniqueIslands.add(shape);
                }
            }
        }
        
        return uniqueIslands.size();
    }
    
    private void dfs(int row, int col, int baseRow, int baseCol,
                     boolean[][] vis, int[][] grid, List<Pair> shape) {
        int n = grid.length;
        int m = grid[0].length;
        vis[row][col] = true;
        
        shape.add(new Pair(row - baseRow, col - baseCol));
        
        int[] delRow = {-1, 0, +1, 0};
        int[] delCol = {0, +1, 0, -1};
        
        for (int i = 0; i < 4; i++) {
            int nRow = row + delRow[i];
            int nCol = col + delCol[i];
            
            if (nRow >= 0 && nRow < n && nCol >= 0 && nCol < m &&
                !vis[nRow][nCol] && grid[nRow][nCol] == 1) {
                dfs(nRow, nCol, baseRow, baseCol, vis, grid, shape);
            }
        }
    }
}
```

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(N \times M \times \log(N \times M))$
-   **Grid Traversal:** $O(N \times M)$ to check all cells.
-   **DFS for Each Island:** $O(N \times M \times 4)$ in total (all cells, 4 directions).
-   **Set Operations:** Each `add` to HashSet is $O(\log(\text{size}))$ due to hashing.
-   **Total:** $O(N \times M \times \log(N \times M))$.

### Space Complexity: $O(N \times M)$
-   **Visited Matrix:** $O(N \times M)$.
-   **Set:** At most $O(N \times M)$ unique islands.
-   **Recursion Stack:** $O(N \times M)$ in worst case.

---

## 🎯 Key Takeaways

1.  **Normalization is Key:** Subtract base coordinates to make shapes comparable.
2.  **Consistent Traversal:** Always traverse in the same order (Up, Right, Down, Left).
3.  **Set for Deduplication:** Automatically handles counting unique shapes.
4.  **Extension of Number of Islands:** Similar to [[Step 15 - Graphs/G-8-Number-of-Islands|G-8]], but with shape tracking.

---

## 🔄 Comparison: G-8 vs G-16

| Feature | G-8 (Number of Islands) | G-16 (Distinct Islands) |
|---------|-------------------------|-------------------------|
| **Goal** | Count all islands | Count unique island shapes |
| **Approach** | Simple DFS/BFS counter | DFS + Shape normalization |
| **Data Structure** | Visited array | Visited + Set |
| **Per Island** | Just mark visited | Store normalized coordinates |
| **Complexity** | $O(N \times M)$ | $O(N \times M \times \log(N \times M))$ |

---
