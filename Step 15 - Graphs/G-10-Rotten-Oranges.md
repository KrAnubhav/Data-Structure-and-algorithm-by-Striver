---
title: Rotten Oranges (Multi-Source BFS with Time Tracking)
lecture: G-10
topic: Graphs
tags: [graph, bfs, multi-source, matrix, time, simulation]
difficulty: Medium
created: 2026-01-20
updated: 2026-01-20
---

# G-10. Rotten Oranges

> **Lecture:** G-10
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-09-Flood-Fill|G-09. Flood Fill]]
> **Next:** [[Step 15 - Graphs/G-11-Detect-Cycle-BFS|G-11. Detect Cycle in Undirected Graph (BFS)]]

---

## 🧐 The Problem

**Problem Statement:**
Given a grid of size $N \times M$ where:
- `0` = Empty cell
- `1` = Fresh orange
- `2` = Rotten orange

A rotten orange can rot all **adjacent fresh oranges** (up, down, left, right) in **1 unit of time**.

Find the **minimum time** required to rot all oranges. If it's impossible, return `-1`.

---

## 🖼️ Understanding the Problem

**Example 1:**
```
Initial Grid:
2  1  1
1  1  0
0  1  1

Time = 0: Start with rotten at (0,0)
Time = 1: Rot neighbors
2  2  1
2  1  0
0  1  1

Time = 2: Continue rotting
2  2  2
2  2  0
0  2  1

Time = 3: Continue
2  2  2
2  2  0
0  2  2

Time = 4: Final
2  2  2
2  2  0
0  2  2

Answer: 4
```

**Example 2:**
```
Initial:
2  1  1
0  1  1
1  0  1

After Time = 1:
2  2  1
0  2  1
1  0  1

After Time = 2:
2  2  2
0  2  2
1  0  2

Fresh orange at (2,0) is UNREACHABLE!
Answer: -1
```

> [!important] Key Insight
> This is **Multi-Source BFS** with **Time Tracking**. All rotten oranges spread simultaneously, level by level!

---

## 💡 Why BFS? Why Not DFS?

**Why BFS?**
- All rotten oranges rot their neighbors **simultaneously** (same time step).
- BFS explores **level-by-level**, which perfectly models time progression.
- Guarantees **minimum time**.

**Why NOT DFS?**
- DFS goes deep into one path.
- Doesn't model simultaneous spreading.
- Won't give minimum time.

---

## 🛠️ The Algorithm

### Step-by-Step:
1. **Initialize:**
   - Create `visited` matrix.
   - Create a `queue`.
   - Count total `fresh` oranges.

2. **Find all rotten oranges:**
   - Loop through grid.
   - For each cell with `2`:
     - Add `(row, col, 0)` to queue (time = 0).
     - Mark as visited.

3. **BFS with Time Tracking:**
   - While queue is not empty:
     - Pop `(row, col, time)`.
     - Update `maxTime = max(maxTime, time)`.
     - Check all 4 neighbors.
     - If neighbor is fresh (`1`) and not visited:
       - Mark visited.
       - Decrement `fresh` count.
       - Add `(nRow, nCol, time+1)` to queue.

4. **Check Result:**
   - If `fresh == 0`: Return `maxTime`.
   - Else: Return `-1` (some oranges unreachable).

---

## 💻 Detailed Dry Run

**Grid:**
```
2  1  1
1  1  0
0  1  1
```

**Step 1: Initialize**
- Queue: `[(0,0,0)]` (one rotten orange at time 0)
- Fresh count: `6`
- Visited: Mark `(0,0)` as visited

**Step 2: Process (0,0,0)**
- Time = `0`, maxTime = `0`
- Neighbors: `(0,1)` fresh, `(1,0)` fresh
- Add `(0,1,1)` and `(1,0,1)` to queue
- Fresh count: `4`
- Queue: `[(0,1,1), (1,0,1)]`

**Step 3: Process (0,1,1)**
- Time = `1`, maxTime = `1`
- Neighbors: `(0,2)` fresh, `(1,1)` fresh
- Add them with time `2`
- Fresh count: `2`

**Step 4: Process (1,0,1)**
- Time = `1`, maxTime = `1`
- Neighbor: `(1,1)` (already in queue)
- No new additions

**... Continue until queue is empty ...**

**Final:**
- maxTime = `4`
- Fresh count = `0`
- **Answer: 4**

---

## 💻 Java Implementation

```java
import java.util.*;

class Solution {
    // Pair class to store (row, col, time)
    static class Pair {
        int row, col, time;
        Pair(int r, int c, int t) {
            row = r;
            col = c;
            time = t;
        }
    }
    
    // Main Function
    public int orangesRotting(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        
        // Visited matrix
        boolean[][] vis = new boolean[n][m];
        
        // Queue for BFS
        Queue<Pair> q = new LinkedList<>();
        
        // Count fresh oranges
        int fresh = 0;
        
        // Step 1: Add all rotten oranges to queue
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 2) {
                    q.add(new Pair(i, j, 0));
                    vis[i][j] = true;
                } else if (grid[i][j] == 1) {
                    fresh++;
                }
            }
        }
        
        // Direction arrays for 4 neighbors
        int[] delRow = {-1, 0, +1, 0};
        int[] delCol = {0, +1, 0, -1};
        
        int maxTime = 0;
        
        // Step 2: BFS
        while (!q.isEmpty()) {
            Pair curr = q.poll();
            int row = curr.row;
            int col = curr.col;
            int time = curr.time;
            
            // Update max time
            maxTime = Math.max(maxTime, time);
            
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
                    fresh--;
                    q.add(new Pair(nRow, nCol, time + 1));
                }
            }
        }
        
        // Step 3: Check if all fresh oranges rotted
        return (fresh == 0) ? maxTime : -1;
    }
}
```

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(N \times M)$
-   **Initialization Loop:** $O(N \times M)$ to find all rotten oranges and count fresh.
-   **BFS:** Each cell is visited at most once.
-   **For each cell:** Check 4 neighbors.
-   **Total:** $O(N \times M) + O(N \times M \times 4) \approx O(N \times M)$.

### Space Complexity: $O(N \times M)$
-   **Visited Matrix:** $O(N \times M)$.
-   **Queue:** At most $O(N \times M)$ cells.

---

## 🎯 Key Takeaways

1.  **Multi-Source BFS with Time:** Start from all rotten oranges simultaneously, tracking time at each level.
2.  **Fresh Counter:** Efficient way to check if all oranges rotted (instead of scanning grid again).
3.  **Why BFS?** Models simultaneous spreading perfectly (level = time unit).
4.  **Edge Case:** If `fresh > 0` after BFS, return `-1` (unreachable oranges).

---

## 🔄 Alternative: Check at the End

Instead of tracking `fresh` count, you can check the grid after BFS:

```java
// After BFS completes
for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
        if (grid[i][j] == 1 && !vis[i][j]) {
            return -1; // Found unrotted fresh orange
        }
    }
}
return maxTime;
```

Both approaches work! The `fresh` counter is slightly more efficient.

---
