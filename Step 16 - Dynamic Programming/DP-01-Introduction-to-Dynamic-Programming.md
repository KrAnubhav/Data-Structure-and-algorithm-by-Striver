---
title: Introduction to Dynamic Programming (Fibonacci)
lecture: DP-01
topic: Dynamic Programming
tags: [dp, recursion, memoization, tabulation, space-optimization, fibonacci]
difficulty: Easy
created: 2026-01-26
updated: 2026-01-26
---

# DP-01. Introduction to Dynamic Programming

> **Lecture:** DP-01
> **Topic:** [[Step 16 - Dynamic Programming/README|Dynamic Programming]]
> **Next:** [[Step 16 - Dynamic Programming/DP-02-Climbing-Stairs|DP-02. Climbing Stairs]]

---

## 🚀 Introduction

Hey everyone, welcome back to the channel! I hope you guys are doing extremely well. Finally, the **Ultimate Dynamic Programming Playlist** is here!

You might be wondering, "What is the hype all about?" Let me tell you, this is going to be the **best** DP playlist. I've already delivered the best Graph, Tree, and Recursion playlists, and now, we are starting the Dynamic Programming series. By the end of this series, you will master DP, from basic logic building to the most advanced interview questions. This series will contain around **60 to 70 videos**, covering every possible pattern.

Most people teach either just Tabulation or just Memoization. Here, we will do it differently for every single problem:
1.  **Recursion** (The foundation)
2.  **Memoization** (Top-Down)
3.  **Tabulation** (Bottom-Up)
4.  **Space Optimization** (The most optimal solution)

This ensures that you are interview-ready and can explain the most optimized approach.

---

## 🤔 What is Dynamic Programming?

> "Those who cannot remember the past are condemned to repeat it."

This famous quote perfectly sums up Dynamic Programming. In programming terms, if we solve a sub-problem, we should store its result so we don't have to re-compute it later.

There are two main techniques in DP:

1.  **Tabulation (Bottom-Up):**
    *   As the name suggests, we start from the base case (bottom) and build our way up to the required answer.
    *   It generally uses an iterative approach.
2.  **Memoization (Top-Down):**
    *   We start from the required answer (top) and break it down into smaller sub-problems.
    *   It is essentially **Recursion + Caching** (storing values).

---

## 🔢 Example: Fibonacci Number

The prerequisite for this playlist is my Recursion playlist (especially lectures 6 and 7). Let's start with the classic **Fibonacci Number** problem to understand the DP flow.

**The Series:**
`0, 1, 1, 2, 3, 5, 8, 13, 21...`

**Observation:**
*   First two numbers are `0` and `1`.
*   Any subsequent number is the sum of the previous two numbers.
    *   $1 = 0 + 1$
    *   $2 = 1 + 1$
    *   $3 = 1 + 2$
    *   $5 = 2 + 3$

**Recurrence Relation:**
$$ f(n) = f(n-1) + f(n-2) $$

---

## 1️⃣ Approach 1: Recursion (Brute Force)

If I ask you for the 4th Fibonacci number (0-indexed), you can write a simple recursion:

```java
class Solution {
    public static int fib(int n) {
        if (n <= 1) return n;
        return fib(n-1) + fib(n-2);
    }
}
```

### The Issue with Recursion
Let's look at the recursion tree for $f(5)$:

*   $f(5)$ calls $f(4)$ and $f(3)$.
*   $f(4)$ calls $f(3)$ and $f(2)$.
*   Wait! We are computing $f(3)$ **twice**.
*   Deeper down, $f(2)$ is computed multiple times (inside $f(3)$, inside $f(4)$).

These are called **Overlapping Subproblems**. We are solving the same problem again and again. For larger $n$, this recursion takes exponential time $O(2^n)$, which is terrible.

---

## 2️⃣ Approach 2: Memoization (Top-Down)

To fix the overlapping subproblems, we use **Memoization**.
*   **Definition:** We tend to store the value of subproblems in some map or table.
*   When we encounter a subproblem again, we just return the stored value instead of recomputing.

### Steps to convert Recursion to Memoization:
1.  **Declare a DP Array:** Size should be $(n+1)$ because we go from 0 to $n$. Initialize it with `-1`.
2.  **Store the Answer:** Before returning the answer in recursion, store it in `dp[n]`.
3.  **Check if Solved:** At the start of the function, check if `dp[n] != -1`. If yes, return `dp[n]`.

### Java Implementation

```java
import java.util.*;

public class Main {
    static int f(int n, int[] dp) {
        // Base Case
        if (n <= 1) return n;
        
        // Step 3: Check if previously solved
        if (dp[n] != -1) return dp[n];
        
        // Step 2: Store the computed value
        return dp[n] = f(n - 1, dp) + f(n - 2, dp);
    }

    public static void main(String[] args) {
        int n = 5;
        // Step 1: Declare DP Array
        int[] dp = new int[n + 1];
        Arrays.fill(dp, -1);
        
        System.out.println(f(n, dp));
    }
}
```

### Complexity Analysis
*   **Time Complexity:** $O(N)$
    *   We solve each subproblem exactly once. The linear chain of calls makes it linear time.
*   **Space Complexity:** $O(N) + O(N) \approx O(N)$
    *   $O(N)$ for the recursion stack space.
    *   $O(N)$ for the DP array.

---

## 3️⃣ Approach 3: Tabulation (Bottom-Up)

Now, let's eliminate the recursion stack space using **Tabulation**.
*   **Goal:** Go from the base case to the required answer.

### Steps:
1.  **Initialize DP Array:** Same as memoization.
2.  **Set Base Cases:** We know `dp[0] = 0` and `dp[1] = 1`.
3.  **Iterate:** Loop from 2 to $n$ and fill the table using the recurrence relation.

### Java Implementation

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        int n = 5;
        int[] dp = new int[n + 1];
        
        // Step 2: Base Cases
        dp[0] = 0;
        dp[1] = 1;
        
        // Step 3: Loop from 2 to n
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        
        System.out.println(dp[n]);
    }
}
```

### Complexity Analysis
*   **Time Complexity:** $O(N)$
    *   Simple loop from 2 to $n$.
*   **Space Complexity:** $O(N)$
    *   We are using an array of size $N$. Recursion stack space is eliminated.

---

## 4️⃣ Approach 4: Space Optimization

Can we do even better?
Look at the relation: `dp[i] = dp[i-1] + dp[i-2]`.

To compute the current value `dp[i]`, we **only** need the previous two values:
1.  `prev` (which is `dp[i-1]`)
2.  `prev2` (which is `dp[i-2]`)

We don't need the entire array! We can just maintain two variables.

### Logic:
*   Initialize `prev2 = 0` and `prev = 1`.
*   In each iteration:
    *   `curr_i = prev + prev2`
    *   Shift pointers: `prev2` becomes `prev`, and `prev` becomes `curr_i`.
*   At the end, `prev` will hold the $n^{th}$ Fibonacci number.

### Java Implementation

```java
public class Main {
    public static void main(String[] args) {
        int n = 5;
        
        if (n == 0) {
            System.out.println(0);
            return;
        }
        
        int prev2 = 0;
        int prev = 1;
        
        for (int i = 2; i <= n; i++) {
            int cur_i = prev + prev2;
            prev2 = prev;
            prev = cur_i;
        }
        
        System.out.println(prev);
    }
}
```

### Complexity Analysis
*   **Time Complexity:** $O(N)$
    *   We are still iterating $N$ times.
*   **Space Complexity:** $O(1)$
    *   We are only using variables. No array or recursion stack.

---

## 🎯 Key Takeaways

1.  **Recursion** is the starting point. Identify overlapping subproblems.
2.  **Memoization** stores recursion results to avoid re-computation. (Time: $O(N)$, Space: $O(N)$).
3.  **Tabulation** builds the solution iteratively from base cases. (Time: $O(N)$, Space: $O(N)$).
4.  **Space Optimization** reduces space to $O(1)$ by observing we only need the last few states.

If you understood this, please comment **"Understood"** below! This series is going to be a game-changer. See you in the next one!
