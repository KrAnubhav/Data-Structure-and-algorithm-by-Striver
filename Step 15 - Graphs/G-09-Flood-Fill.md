---
title: Flood Fill Algorithm (LeetCode 733)
lecture: G-09
topic: Graphs
tags: [graph, dfs, bfs, matrix, flood-fill, 4-directions]
difficulty: Easy
created: 2026-01-20
updated: 2026-01-20
---

# G-09. Flood Fill Algorithm

> **Lecture:** G-09
> **Topic:** [[Step 15 - Graphs/README|Graphs]]
> **Previous:** [[Step 15 - Graphs/G-08-Number-of-Islands|G-08. Number of Islands]]
> **Next:** [[Step 15 - Graphs/G-10-Rotten-Oranges|G-10. Rotten Oranges (Coming Soon)]]

---

## 🧐 The Problem

**Problem Statement:**
You are given:
- An `image` (2D matrix of integers representing pixel values)
- A starting pixel `(sr, sc)` where `sr` = row, `sc` = column
- A `newColor` to fill

Perform a **Flood Fill** starting from `(sr, sc)`:
- Replace the color of the starting pixel with `newColor`.
- Replace all pixels **connected 4-directionally** (up, down, left, right) that have the **same color** as the starting pixel.

---

## 🖼️ Visualizing Flood Fill

**Example 1:**
```
Initial Image:        Starting: (1, 1)
                      Initial Color: 1
1  1  1               New Color: 2
1  1  0
1  0  1

Step-by-step:
1. Color (1,1) with 2
2. Color neighbors with same initial color (1):
   - (0,1), (1,0), (1,2)
3. Continue from those neighbors:
   - (0,0), (0,2), (2,0)

Final Image:
2  2  2
2  2  0
2  0  1
```

**Example 2:**
```
Initial:              Starting: (2, 0)
                      Initial Color: 2
1  1  1               New Color: 3
2  2  0
2  2  2

Process:
(2,0) → (1,0) → (1,1) → (2,1) → (2,2)

Final:
1  1  1
3  3  0
3  3  3
```

> [!important] Key Insight
> Only pixels with the **same initial color** as the starting pixel get filled. The fill spreads through connected pixels like water flooding an area!

---

## 🛠️ The Solution Strategy

We can use either **BFS** or **DFS**. Since we solved the previous problem with BFS, let's use **DFS** here!

### Algorithm:
1. Store the **initial color** of the starting pixel.
2. Create a **copy** of the image (to avoid modifying input data).
3. Call `dfs(sr, sc)` to start the flood fill.
4. In the DFS:
   - Color the current pixel with `newColor`.
   - Check all **4 neighbors** (up, down, left, right).
   - If a neighbor:
     - Is **valid** (within bounds)
     - Has the **initial color**
     - Has **NOT been colored yet** (not `newColor`)
   - Then recursively call `dfs(neighbor)`.

---

## 💡 The 4-Direction Neighbor Trick

Instead of writing 4 separate conditions, use arrays!

**Neighbors of `(row, col)`:**
```
        (row-1, col)     ← Up
(row, col-1)  X  (row, col+1)  ← Left, Right
        (row+1, col)     ← Down
```

**Pattern:**
- Row changes: `-1, 0, +1, 0`
- Col changes: `0, +1, 0, -1`

**Code:**
```java
int[] delRow = {-1, 0, +1, 0};
int[] delCol = {0, +1, 0, -1};

for (int i = 0; i < 4; i++) {
    int nRow = row + delRow[i];
    int nCol = col + delCol[i];
    // Check validity...
}
```

---

## 💻 Java Implementation (DFS Approach)

```java
class Solution {
    // Main Function
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        // Get initial color
        int initialColor = image[sr][sc];
        
        // Create a copy of the image (to preserve original data)
        int[][] ans = new int[image.length][image[0].length];
        for (int i = 0; i < image.length; i++) {
            for (int j = 0; j < image[0].length; j++) {
                ans[i][j] = image[i][j];
            }
        }
        
        // Direction arrays for 4 neighbors
        int[] delRow = {-1, 0, +1, 0};
        int[] delCol = {0, +1, 0, -1};
        
        // Start DFS
        dfs(sr, sc, ans, image, newColor, delRow, delCol, initialColor);
        
        return ans;
    }
    
    // DFS Function
    private void dfs(int row, int col, int[][] ans, int[][] image, 
                     int newColor, int[] delRow, int[] delCol, int initialColor) {
        
        // Color the current pixel
        ans[row][col] = newColor;
        
        int n = image.length;
        int m = image[0].length;
        
        // Check all 4 neighbors
        for (int i = 0; i < 4; i++) {
            int nRow = row + delRow[i];
            int nCol = col + delCol[i];
            
            // Check validity and conditions
            if (nRow >= 0 && nRow < n && 
                nCol >= 0 && nCol < m &&
                image[nRow][nCol] == initialColor && 
                ans[nRow][nCol] != newColor) {
                
                dfs(nRow, nCol, ans, image, newColor, delRow, delCol, initialColor);
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
    
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int initialColor = image[sr][sc];
        
        // Edge case: if initial color is same as new color
        if (initialColor == newColor) return image;
        
        int n = image.length;
        int m = image[0].length;
        
        int[] delRow = {-1, 0, +1, 0};
        int[] delCol = {0, +1, 0, -1};
        
        Queue<Pair> q = new LinkedList<>();
        q.add(new Pair(sr, sc));
        image[sr][sc] = newColor;
        
        while (!q.isEmpty()) {
            Pair curr = q.poll();
            int row = curr.row;
            int col = curr.col;
            
            for (int i = 0; i < 4; i++) {
                int nRow = row + delRow[i];
                int nCol = col + delCol[i];
                
                if (nRow >= 0 && nRow < n && 
                    nCol >= 0 && nCol < m &&
                    image[nRow][nCol] == initialColor) {
                    
                    image[nRow][nCol] = newColor;
                    q.add(new Pair(nRow, nCol));
                }
            }
        }
        
        return image;
    }
}
```

---

## ⏱️ Complexity Analysis

### Time Complexity: $O(N \times M)$
-   In the worst case, the entire grid has the same color.
-   We visit every cell once: $O(N \times M)$.
-   For each cell, we check 4 neighbors: $O(4)$.
-   **Total:** $O(N \times M \times 4) \approx O(N \times M)$.

### Space Complexity: $O(N \times M)$
-   **Answer Matrix:** $O(N \times M)$ (if we create a copy).
-   **Recursion Stack (DFS):** $O(N \times M)$ in worst case (entire grid is a straight line).
-   **Queue (BFS):** $O(N \times M)$ in worst case.

> [!tip] Space Optimization
> You can modify the original `image` array in-place to save the $O(N \times M)$ space for the answer matrix. However, this modifies the input, which may not be acceptable in production code.

---

## 🎯 Key Takeaways

1.  **Flood Fill = DFS/BFS:** Both approaches work perfectly.
2.  **Initial Color Check:** Only spread to pixels with the **same initial color**.
3.  **4-Direction Trick:** Use `delRow` and `delCol` arrays for clean code.
4.  **Edge Case:** If `initialColor == newColor`, return immediately (no work needed).
5.  **Data Integrity:** Prefer creating a copy over modifying the input.

---
