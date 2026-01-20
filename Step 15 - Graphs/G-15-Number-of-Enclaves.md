---
title: Number of Enclaves (LeetCode 1020)
lecture: G-15
topic: Graphs
tags: [graph, bfs, dfs, matrix, boundary-traversal, counting]
difficulty: Medium
created: 2026-01-20
updated: 2026-01-20
---

# G-15. Number of Enclaves

> **Lecture:** G-15
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-14-Surrounded-Regions|G-14. Surrounded Regions]]
> **Next:** [[Step 15 - Graphs/G-16-Word-Ladder|G-16. Word Ladder (Coming Soon)]]

---

## 🧐 The Problem

**Problem Statement:**
Given an $N \times M$ binary matrix where:
- `0` = Sea (water)
- `1` = Land

You can move in **4 directions** (up, down, left, right) from one land cell to another.

Find the **number of land cells** from which you **cannot walk off the boundary** of the grid.

---

## 🖼️ Understanding the Problem

**Example 1:**
```
Grid:
0  0  0  0
1  0  1  0
0  1  1  0
0  0  0  0

Analysis:
- Land at (1,0): Can move off boundary (already on boundary)
- Land at (1,2): Can move off boundary (connected to boundary)
- Land at (2,1): Can move off boundary (connected to boundary)
- Land at (2,2): Can move off boundary (connected to boundary)

Answer: 0 (all lands can reach boundary)
```

**Example 2:**
```
Grid:
0  0  0  1
0  1  1  1
0  1  1  0
0  0  0  1

Boundary lands: (0,3), (1,3), (3,3)
Connected to boundary: (1,1), (1,2), (2,1), (2,2)

Enclosed lands: NONE (all connected to boundary)
Answer: 0
```

**Example 3:**
```
Grid:
0  0  0  0
0  1  1  0
0  1  1  0
0  0  0  0

No boundary lands!
Enclosed lands: (1,1), (1,2), (2,1), (2,2)

Answer: 4
```

> [!important] Key Insight
> If a land cell is **connected to a boundary land cell**, it can walk off the boundary. Only count lands that are **NOT connected** to any boundary land!

---

## 💡 The Core Strategy

**The Algorithm:**
1. Find all **boundary land cells** (`1`s on edges).
2. From each boundary land, perform **BFS/DFS** to mark all connected lands.
3. These marked lands **can reach the boundary** → Don't count them.
4. Count all **unmarked land cells** → These are enclaves!

**Why this works?**
- Boundary lands can trivially walk off (they're already on the edge).
- Any land connected to a boundary land can also walk off (via that connection).
- Only isolated inland lands (not connected to boundary) are trapped!

---

## 🛠️ The Algorithm (BFS Approach)

### Step-by-Step:
1. **Create `visited` matrix** (same size as grid).
2. **Create a queue**.
3. **Traverse all boundaries:**
   - First row, Last row, First column, Last column.
   - For each boundary cell with `1`:
     - Add to queue.
     - Mark as visited.
4. **BFS Traversal:**
   - While queue is not empty:
     - Pop cell.
     - Check all 4 neighbors.
     - If neighbor is `1` and not visited:
       - Mark visited.
       - Add to queue.
5. **Count enclaves:**
   - Loop through entire grid.
   - If cell is `1` AND NOT visited → `count++`.
6. **Return count.**

---

## 💻 Detailed Dry Run

**Grid:**
```
0  0  0  0
0  1  1  0
0  1  1  0
0  0  0  0
```

**Step 1: Find Boundary Lands**
- Check all edges: **No `1`s on any boundary!**
- Queue: `[]` (empty)

**Step 2: BFS**
- Queue is empty → No BFS needed.

**Step 3: Count Enclaves**
- `(1,1)`: `1` and NOT visited → `count = 1`
- `(1,2)`: `1` and NOT visited → `count = 2`
- `(2,1)`: `1` and NOT visited → `count = 3`
- `(2,2)`: `1` and NOT visited → `count = 4`

**Answer: 4**

---

**Another Example:**
```
Grid:
0  0  0  1
0  1  1  1
0  1  1  0
0  0  0  1
```

**Step 1: Find Boundary Lands**
- `(0,3)`: Boundary land → Add to queue, mark visited.
- `(3,3)`: Boundary land → Add to queue, mark visited.

**Step 2: BFS from (0,3)**
- Pop `(0,3)`.
- Neighbor `(1,3)`: Land, not visited → Add to queue, mark visited.

**Step 3: BFS from (3,3)**
- Pop `(3,3)`.
- No unvisited land neighbors.

**Step 4: BFS from (1,3)**
- Pop `(1,3)`.
- Neighbor `(1,2)`: Land, not visited → Add to queue, mark visited.

**... Continue BFS ...**
- Eventually marks: `(0,3), (1,3), (1,2), (2,2), (2,1), (1,1), (3,3)`

**Step 5: Count Enclaves**
- All lands are visited → `count = 0`

**Answer: 0**

---

## 💻 Java Implementation (BFS)

```java
import java.util.*;

class Solution {
    // Pair class for queue
    static class Pair {
        int row, col;
        Pair(int r, int c) {
            row = r;
            col = c;
        }
    }
    
    // Main Function
    public int numEnclaves(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        
        // Visited matrix
        boolean[][] vis = new boolean[n][m];
        
        // Queue for BFS
        Queue<Pair> q = new LinkedList<>();
        
        // Step 1: Add all boundary lands to queue
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                // Check if it's a boundary cell
                if (i == 0 || i == n-1 || j == 0 || j == m-1) {
                    if (grid[i][j] == 1) {
                        q.add(new Pair(i, j));
                        vis[i][j] = true;
                    }
                }
            }
        }
        
        // Direction arrays
        int[] delRow = {-1, 0, +1, 0};
        int[] delCol = {0, +1, 0, -1};
        
        // Step 2: BFS to mark all boundary-connected lands
        while (!q.isEmpty()) {
            Pair curr = q.poll();
            int row = curr.row;
            int col = curr.col;
            
            // Check all 4 neighbors
            for (int i = 0; i < 4; i++) {
                int nRow = row + delRow[i];
                int nCol = col + delCol[i];
                
                // Check validity
                if (nRow >= 0 && nRow < n && 
                    nCol >= 0 && nCol < m && 
                    !vis[nRow][nCol] && 
                    grid[nRow][nCol] == 1) {
                    
                    vis[nRow][nCol] = true;
                    q.add(new Pair(nRow, nCol));
                }
            }
        }
        
        // Step 3: Count enclaves (unvisited lands)
        int count = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1 && !vis[i][j]) {
                    count++;
                }
            }
        }
        
        return count;
    }
}
```

---

## 🔄 DFS Approach (Alternative)

You can also solve this using **DFS**!

```java
class Solution {
    public int numEnclaves(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        boolean[][] vis = new boolean[n][m];
        
        int[] delRow = {-1, 0, +1, 0};
        int[] delCol = {0, +1, 0, -1};
        
        // DFS from all boundary lands
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (i == 0 || i == n-1 || j == 0 || j == m-1) {
                    if (grid[i][j] == 1 && !vis[i][j]) {
                        dfs(i, j, vis, grid, delRow, delCol);
                    }
                }
            }
        }
        
        // Count enclaves
        int count = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1 && !vis[i][j]) {
                    count++;
                }
            }
        }
        
        return count;
    }
    
    private void dfs(int row, int col, boolean[][] vis, int[][] grid, 
                     int[] delRow, int[] delCol) {
        int n = grid.length;
        int m = grid[0].length;
        vis[row][col] = true;
        
        for (int i = 0; i < 4; i++) {
            int nRow = row + delRow[i];
            int nCol = col + delCol[i];
            
            if (nRow >= 0 && nRow < n && 
                nCol >= 0 && nCol < m && 
                !vis[nRow][nCol] && 
                grid[nRow][nCol] == 1) {
                
                dfs(nRow, nCol, vis, grid, delRow, delCol);
            }
        }
    }
}
```

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(N \times M)$
-   **Boundary Check:** $O(N \times M)$ to scan all cells for boundaries.
-   **BFS/DFS:** In worst case, visits all $N \times M$ cells.
-   **Final Count:** $O(N \times M)$ to count enclaves.
-   **Total:** $O(N \times M)$.

### Space Complexity: $O(N \times M)$
-   **Visited Matrix:** $O(N \times M)$.
-   **Queue (BFS):** At most $O(N \times M)$ cells.
-   **Recursion Stack (DFS):** At most $O(N \times M)$ in worst case.

---

## 🎯 Key Takeaways

1.  **Boundary Traversal Pattern:** Similar to [[Step 15 - Graphs/G-14-Surrounded-Regions|G-14 Surrounded Regions]].
2.  **Reverse Thinking:** Instead of finding enclaves directly, find lands that CAN reach boundary, then count the rest.
3.  **BFS vs DFS:** Both work equally well for this problem.
4.  **Edge Case:** If all lands are on or connected to boundary, answer is `0`.

---

## 🔄 Comparison with G-14

| Feature | G-14 (Surrounded Regions) | G-15 (Enclaves) |
|---------|---------------------------|-----------------|
| **Goal** | Replace surrounded `O`s with `X` | Count enclosed lands |
| **Boundary Check** | Start from boundary `O`s | Start from boundary `1`s |
| **After Traversal** | Convert unmarked `O`s to `X` | Count unmarked `1`s |
| **Return Type** | Void (modify in-place) | Integer (count) |

**Same Pattern, Different Application!**

---
