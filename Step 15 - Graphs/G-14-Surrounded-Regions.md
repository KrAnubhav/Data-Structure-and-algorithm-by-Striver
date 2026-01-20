---
title: Surrounded Regions - Replace O's with X's (LeetCode 130)
lecture: G-14
topic: Graphs
tags: [graph, dfs, bfs, matrix, boundary-traversal]
difficulty: Medium
created: 2026-01-20
updated: 2026-01-20
---

# G-14. Surrounded Regions (Replace O's with X's)

> **Lecture:** G-14
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-13-Distance-Nearest-Cell|G-13. Distance of Nearest Cell]]
> **Next:** [[Step 15 - Graphs/G-15-Number-of-Enclaves|G-15. Number of Enclaves (Coming Soon)]]

---

## 🧐 The Problem

**Problem Statement:**
Given a matrix of size $N \times M$ where every element is either `O` or `X`, replace all `O`s with `X`s that are **surrounded by `X`s**.

**What does "surrounded" mean?**
An `O` (or a group of `O`s) is surrounded if there are `X`s at locations:
- **Top**
- **Bottom**
- **Left**
- **Right**

**Note:** Diagonals are NOT considered!

---

## 🖼️ Understanding the Problem

**Example:**
```
Initial Grid:
X  X  X  X
X  O  O  X
X  X  O  X
X  O  X  X

Analysis:
- O at (1,1): Connected to (1,2) and (2,2)
- O at (3,1): On boundary (row 3)

After Replacement:
X  X  X  X
X  X  X  X
X  X  X  X
X  O  X  X
```

**Why?**
- `O` at `(1,1), (1,2), (2,2)` are completely surrounded by `X` → Convert to `X`.
- `O` at `(3,1)` is on the **boundary** → Keep as `O`.

> [!important] Key Observation
> Any `O` **connected to a boundary `O`** cannot be surrounded (because boundaries are open). Only convert `O`s that are NOT connected to any boundary `O`!

---

## 💡 The Core Insight

**The Algorithm:**
1. Start from **all boundary `O`s**.
2. Mark all `O`s **connected** to boundary `O`s (using DFS/BFS).
3. These marked `O`s **cannot be converted** (they're connected to the boundary).
4. Convert all **unmarked `O`s** to `X`s.

**Why this works?**
- If an `O` is connected to a boundary `O`, it has an "escape route" → Not surrounded.
- If an `O` is NOT connected to any boundary `O`, it's completely enclosed → Surrounded!

---

## 🛠️ The Algorithm

### Step-by-Step:
1. **Create a `visited` matrix** (same size as grid).
2. **Traverse all boundaries:**
   - First row, Last row, First column, Last column.
   - For each boundary cell with `O`:
     - Call `dfs(row, col)` to mark all connected `O`s.
3. **Scan the entire grid:**
   - If cell is `O` AND NOT visited → Convert to `X`.
   - If cell is `O` AND visited → Keep as `O`.
4. **Return the modified grid.**

---

## 💻 Detailed Dry Run

**Grid:**
```
X  X  X  X
X  O  O  X
X  X  O  X
X  O  X  X
```

**Step 1: Check Boundaries**
- **First Row (0):** All `X` → No DFS.
- **Last Row (3):** `O` at `(3,1)` → **Call `dfs(3,1)`**.
- **First Column (0):** All `X` → No DFS.
- **Last Column (3):** All `X` → No DFS.

**Step 2: DFS from (3,1)**
- Mark `(3,1)` as visited.
- Check neighbors: Up, Down, Left, Right.
- No connected `O`s found.
- **Result:** Only `(3,1)` is marked.

**Step 3: Scan Grid**
- `(1,1)`: `O` and NOT visited → Convert to `X`.
- `(1,2)`: `O` and NOT visited → Convert to `X`.
- `(2,2)`: `O` and NOT visited → Convert to `X`.
- `(3,1)`: `O` and visited → Keep as `O`.

**Final Grid:**
```
X  X  X  X
X  X  X  X
X  X  X  X
X  O  X  X
```

---

## 💻 Java Implementation

```java
class Solution {
    // Main Function
    public void solve(char[][] board) {
        int n = board.length;
        int m = board[0].length;
        
        // Visited matrix
        boolean[][] vis = new boolean[n][m];
        
        // Direction arrays for 4 neighbors
        int[] delRow = {-1, 0, +1, 0};
        int[] delCol = {0, +1, 0, -1};
        
        // Step 1: Traverse First and Last Row
        for (int j = 0; j < m; j++) {
            // First row
            if (!vis[0][j] && board[0][j] == 'O') {
                dfs(0, j, vis, board, delRow, delCol);
            }
            // Last row
            if (!vis[n-1][j] && board[n-1][j] == 'O') {
                dfs(n-1, j, vis, board, delRow, delCol);
            }
        }
        
        // Step 2: Traverse First and Last Column
        for (int i = 0; i < n; i++) {
            // First column
            if (!vis[i][0] && board[i][0] == 'O') {
                dfs(i, 0, vis, board, delRow, delCol);
            }
            // Last column
            if (!vis[i][m-1] && board[i][m-1] == 'O') {
                dfs(i, m-1, vis, board, delRow, delCol);
            }
        }
        
        // Step 3: Convert unmarked O's to X's
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (!vis[i][j] && board[i][j] == 'O') {
                    board[i][j] = 'X';
                }
            }
        }
    }
    
    // DFS Function
    private void dfs(int row, int col, boolean[][] vis, char[][] board, 
                     int[] delRow, int[] delCol) {
        int n = board.length;
        int m = board[0].length;
        
        // Mark as visited
        vis[row][col] = true;
        
        // Check all 4 neighbors
        for (int i = 0; i < 4; i++) {
            int nRow = row + delRow[i];
            int nCol = col + delCol[i];
            
            // Check validity
            if (nRow >= 0 && nRow < n && 
                nCol >= 0 && nCol < m && 
                !vis[nRow][nCol] && 
                board[nRow][nCol] == 'O') {
                
                dfs(nRow, nCol, vis, board, delRow, delCol);
            }
        }
    }
}
```

---

## 🔄 BFS Approach (Alternative)

You can also solve this using **BFS**!

```java
import java.util.*;

class Solution {
    static class Pair {
        int row, col;
        Pair(int r, int c) { row = r; col = c; }
    }
    
    private void bfs(int row, int col, boolean[][] vis, char[][] board) {
        int n = board.length;
        int m = board[0].length;
        
        Queue<Pair> q = new LinkedList<>();
        q.add(new Pair(row, col));
        vis[row][col] = true;
        
        int[] delRow = {-1, 0, +1, 0};
        int[] delCol = {0, +1, 0, -1};
        
        while (!q.isEmpty()) {
            Pair curr = q.poll();
            int r = curr.row;
            int c = curr.col;
            
            for (int i = 0; i < 4; i++) {
                int nRow = r + delRow[i];
                int nCol = c + delCol[i];
                
                if (nRow >= 0 && nRow < n && 
                    nCol >= 0 && nCol < m && 
                    !vis[nRow][nCol] && 
                    board[nRow][nCol] == 'O') {
                    
                    vis[nRow][nCol] = true;
                    q.add(new Pair(nRow, nCol));
                }
            }
        }
    }
}
```

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(N \times M)$
-   **Boundary Traversal:** $O(N + M)$ to check all boundary cells.
-   **DFS Calls:** In worst case (entire grid is `O`), DFS visits all $N \times M$ cells.
-   **Final Scan:** $O(N \times M)$ to convert unmarked `O`s.
-   **Total:** $O(N \times M)$.

### Space Complexity: $O(N \times M)$
-   **Visited Matrix:** $O(N \times M)$.
-   **Recursion Stack (DFS):** $O(N \times M)$ in worst case.

---

## 🎯 Key Takeaways

1.  **Boundary Traversal:** The key insight is to start from boundaries, not from the interior.
2.  **Reverse Thinking:** Instead of finding surrounded `O`s, find `O`s that are NOT surrounded (connected to boundary).
3.  **DFS/BFS:** Both work! DFS is slightly simpler for this problem.
4.  **Data Integrity:** Use a separate `visited` matrix instead of modifying the input during traversal.

---

## 🔄 Variation: In-Place Solution

You can solve this **without** a `visited` matrix by temporarily marking boundary-connected `O`s:

```java
// During DFS, mark boundary-connected O's as '#'
board[row][col] = '#';

// After all DFS calls, scan grid:
for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
        if (board[i][j] == 'O') board[i][j] = 'X';  // Surrounded
        if (board[i][j] == '#') board[i][j] = 'O';  // Boundary-connected
    }
}
```

This saves $O(N \times M)$ space but modifies the input temporarily.

---
