---
title: Distance of Nearest Cell Having 1 (Multi-Source BFS)
lecture: G-13
topic: Graphs
tags: [graph, bfs, multi-source, matrix, distance, shortest-path]
difficulty: Medium
created: 2026-01-20
updated: 2026-01-20
---

# G-13. Distance of Nearest Cell Having 1

> **Lecture:** G-13
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-12-Detect-Cycle-DFS|G-12. Detect Cycle using DFS]]
> **Next:** [[Step 15 - Graphs/G-14-Rotten-Oranges|G-14. Rotten Oranges (Coming Soon)]]

---

## 🧐 The Problem

**Problem Statement:**
Given a binary grid of size $N \times M$ (containing `0`s and `1`s), find the **distance of the nearest `1`** for each cell.

**Distance Calculation:**
Distance = `|row1 - row2| + |col1 - col2|` (Manhattan Distance)

**Similar Problem:**
- **LeetCode 542:** 01 Matrix (find distance to nearest `0`)

---

## 🖼️ Understanding the Problem

**Example Grid:**
```
1  0  1
1  1  0
1  0  0
```

**Expected Output:**
```
0  1  0
0  0  1
0  1  2
```

**Explanation:**
- `(0,0)`: Has `1` → Distance = `0`
- `(0,1)`: Nearest `1` is at `(0,0)` or `(0,2)` → Distance = `1`
- `(0,2)`: Has `1` → Distance = `0`
- `(2,2)`: Nearest `1` is at `(2,0)` or `(1,1)` → Distance = `2`

> [!important] Key Insight
> We need to find the **minimum distance** from each `0` to any `1`. This screams **BFS** because BFS explores level-by-level (step-by-step)!

---

## 💡 Why BFS? Why Not DFS?

**Why BFS?**
- BFS moves **step-by-step** (level-wise).
- All cells at distance `1` are visited before cells at distance `2`.
- Guarantees the **shortest path** in an unweighted grid.

**Why NOT DFS?**
- DFS goes **deep** into one path.
- May find a longer path before finding the shortest one.
- Not suitable for shortest distance problems.

---

## 🚀 The Multi-Source BFS Concept

**Traditional BFS:** Start from **one source**.

**Multi-Source BFS:** Start from **multiple sources simultaneously**!

**In this problem:**
- All cells with `1` are our **starting sources**.
- We push **all `1`s** into the queue initially.
- Then perform BFS from all of them **together**.

**Visualization:**
```
Step 0: All 1s are at distance 0
    1  .  1
    1  1  .
    1  .  .

Step 1: Take 1 step from all 1s
    1  1  1
    1  1  1
    1  1  .

Step 2: Take 1 more step
    1  1  1
    1  1  1
    1  1  2
```

---

## 🛠️ The Algorithm

### Step-by-Step:
1. **Initialize:**
   - Create `visited` matrix (all `false`).
   - Create `distance` matrix (all `0`).
   - Create a `queue`.

2. **Find all `1`s:**
   - Loop through the grid.
   - For each cell with `1`:
     - Add `(row, col, 0)` to queue (distance = 0).
     - Mark as visited.

3. **BFS Traversal:**
   - While queue is not empty:
     - Pop `(row, col, steps)`.
     - Store `steps` in `distance[row][col]`.
     - Check all 4 neighbors (up, down, left, right).
     - If neighbor is valid and not visited:
       - Mark visited.
       - Add `(nRow, nCol, steps+1)` to queue.

4. **Return `distance` matrix.**

---

## 💻 Detailed Dry Run

**Grid:**
```
1  0  1
1  1  0
1  0  0
```

**Initial Queue:**
All `1`s at step 0:
```
Queue: [(0,0,0), (0,2,0), (1,0,0), (1,1,0), (2,0,0)]
Visited: All 1s marked as true
Distance: All 0s initially
```

**Step 1: Process (0,0,0):**
- Current: `(0,0)` at step `0`.
- Store: `distance[0][0] = 0`.
- Neighbors: `(0,1)` (right), `(1,0)` (down).
- `(0,1)`: Not visited → Add `(0,1,1)` to queue.
- `(1,0)`: Already visited (it's a `1`).

**Step 2: Process (0,2,0):**
- Current: `(0,2)` at step `0`.
- Store: `distance[0][2] = 0`.
- Neighbors: `(0,1)` (left), `(1,2)` (down).
- `(0,1)`: Already in queue.
- `(1,2)`: Not visited → Add `(1,2,1)` to queue.

**... Continue for all initial `1`s ...**

**Step 3: Process (0,1,1):**
- Current: `(0,1)` at step `1`.
- Store: `distance[0][1] = 1`.
- All neighbors already visited.

**Step 4: Process (1,2,1):**
- Current: `(1,2)` at step `1`.
- Store: `distance[1][2] = 1`.
- Neighbor `(2,2)`: Not visited → Add `(2,2,2)` to queue.

**Step 5: Process (2,2,2):**
- Current: `(2,2)` at step `2`.
- Store: `distance[2][2] = 2`.

**Final Distance Matrix:**
```
0  1  0
0  0  1
0  1  2
```

---

## 💻 Java Implementation

```java
import java.util.*;

class Solution {
    // Pair class to store (row, col, steps)
    static class Pair {
        int row, col, steps;
        Pair(int r, int c, int s) {
            row = r;
            col = c;
            steps = s;
        }
    }
    
    // Main Function
    public int[][] nearest(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        
        // Visited and Distance matrices
        boolean[][] vis = new boolean[n][m];
        int[][] dist = new int[n][m];
        
        // Queue for BFS
        Queue<Pair> q = new LinkedList<>();
        
        // Step 1: Add all 1s to queue
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1) {
                    q.add(new Pair(i, j, 0));
                    vis[i][j] = true;
                }
            }
        }
        
        // Direction arrays for 4 neighbors
        int[] delRow = {-1, 0, +1, 0};
        int[] delCol = {0, +1, 0, -1};
        
        // Step 2: BFS
        while (!q.isEmpty()) {
            Pair curr = q.poll();
            int row = curr.row;
            int col = curr.col;
            int steps = curr.steps;
            
            // Store distance
            dist[row][col] = steps;
            
            // Check all 4 neighbors
            for (int i = 0; i < 4; i++) {
                int nRow = row + delRow[i];
                int nCol = col + delCol[i];
                
                // Check validity
                if (nRow >= 0 && nRow < n && 
                    nCol >= 0 && nCol < m && 
                    !vis[nRow][nCol]) {
                    
                    vis[nRow][nCol] = true;
                    q.add(new Pair(nRow, nCol, steps + 1));
                }
            }
        }
        
        return dist;
    }
}
```

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(N \times M)$
-   **Initialization Loop:** $O(N \times M)$ to find all `1`s.
-   **BFS:** Each cell is visited exactly once.
-   **For each cell:** Check 4 neighbors.
-   **Total:** $O(N \times M) + O(N \times M \times 4) \approx O(N \times M)$.

### Space Complexity: $O(N \times M)$
-   **Visited Matrix:** $O(N \times M)$.
-   **Distance Matrix:** $O(N \times M)$.
-   **Queue:** At most $O(N \times M)$ cells.

---

## 🎯 Key Takeaways

1.  **Multi-Source BFS:** Start from multiple sources simultaneously for problems involving "nearest" or "shortest distance from any source".
2.  **Why BFS?** Guarantees shortest path in unweighted grids (step-by-step exploration).
3.  **4-Direction Trick:** Use `delRow` and `delCol` arrays for clean neighbor checking.
4.  **Data Integrity:** Create separate `visited` and `distance` matrices instead of modifying the input.

---

## 🔄 Variation: LeetCode 542 (01 Matrix)

**Problem:** Find distance to nearest `0` (instead of `1`).

**Solution:** Same algorithm! Just initialize queue with all `0`s instead of `1`s.

```java
// Only change needed:
if (grid[i][j] == 0) {  // Instead of == 1
    q.add(new Pair(i, j, 0));
    vis[i][j] = true;
}
```

---
