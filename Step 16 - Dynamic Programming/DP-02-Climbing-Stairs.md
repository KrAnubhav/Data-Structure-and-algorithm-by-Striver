---
title: Climbing Stairs
lecture: DP-02
topic: Dynamic Programming
tags: [dp, recursion, counting, fibonacci, interview-prep]
difficulty: Easy
created: 2026-01-26
updated: 2026-01-26
---

# DP-02. Climbing Stairs

> **Lecture:** DP-02
> **Topic:** [[Step 16 - Dynamic Programming/README|Dynamic Programming]]
> **Previous:** [[Step 16 - Dynamic Programming/DP-01-Introduction-to-Dynamic-Programming|DP-01. Introduction to Dynamic Programming]]
> **Next:** [[Step 16 - Dynamic Programming/DP-03-Frog-Jump|DP-03. Frog Jump]]

---

## 🚀 Introduction

Welcome back to the second lecture of our **Ultimate Dynamic Programming Playlist**! I hope you all are doing extremely well.

In this lecture, we are going to solve a very classic problem: **Count ways to reach the Nth stair**. This problem is crucial because it helps us establish a pattern for solving almost all 1D DP problems. I will teach you a **General Framework (Trick)** that you can apply to any DP problem involving "counting ways" or "finding min/max".

---

## 🧐 The Problem Statement

You are given a number of stairs, say `N`.
*   You start at the **0th stair**.
*   You need to reach the **Nth stair**.
*   Each time, you can climb either **1 step** or **2 steps**.

**Goal:** Return the total number of **distinct ways** to reach the Nth stair.

### Example
Let `N = 3`. Use 0-based indexing for steps: 0, 1, 2, 3.
Ways to reach step 3 from 0:
1.  `0 -> 1 -> 2 -> 3` (1 step + 1 step + 1 step)
2.  `0 -> 2 -> 3` (2 steps + 1 step)
3.  `0 -> 1 -> 3` (1 step + 2 steps)

**Answer:** 3 distinct ways.

---

## 💡 How to Identify a DP Problem?

Before jumping into the solution, you must ask: **"Is this a DP problem?"**
Generally, you can identify DP problems if the question asks for:
1.  **Count values:** "Count total number of ways".
2.  **Min/Max:** "Find the minimum cost", "Find the maximum profit", etc.

Whenever you see **"Count all ways"** or **"Find the best way"**, it implies that you need to **try all possible ways**.
*   If we need to try all possible ways $\rightarrow$ **Recursion**.
*   If the recursion has overlapping subproblems $\rightarrow$ **Dynamic Programming**.

So the flow is: **Problem $\rightarrow$ Recursion $\rightarrow$ DP**.

---

## 🛠️ The 3-Step Framework for Recurrence

A lot of you ask, "How do I write the recurrence relation?"
Here is a **Universal Shortcut** that works for almost all 1D DP problems:

1.  **Represent the problem in terms of Indexes:**
    Treat the problem arguments (like stairs, array size) as an index. Let's define `f(ind)` as the number of ways to reach stair `ind`.
2.  **Do all possible stuff on that Index:**
    Look at the problem statement. What choices do you have at a specific index?
    *   Here: You can jump **1 step** or **2 steps**.
    *   If you are at `ind`, you could have reached here from `ind-1` (by taking 1 step) or from `ind-2` (by taking 2 steps).
3.  **Sum / Min / Max:**
    *   If the question asks to **Count all ways** $\rightarrow$ **Sum** all the results.
    *   If the question asks for **Min/Max** $\rightarrow$ Take **Min** or **Max** of the choices.

---

## 🧠 Interview Explanation

> "Can you walk me through your thought process for solving this problem?"

"Absolutely. So, the problem asks us to find the number of distinct ways to climb $N$ stairs, where at each step, we can climb either 1 or 2 stairs.

**1. Re-framing the Problem & Pattern Recognition:**
First, I'll think about this problem in reverse. To reach the $N^{th}$ stair, I must have come from one of the immediately preceding possible locations.
Since I can only take 1 or 2 steps, the only ways to arrive at step $N$ are:
1.  Coming from step $N-1$ (by taking a single 1-step jump).
2.  Coming from step $N-2$ (by taking a single 2-step jump).

There are no other valid moves to reach $N$ directly. This implies a very strong relationship:
**Total Ways to $N$ = (Total Ways to $N-1$) + (Total Ways to $N-2$)**

This indicates that the problem has an **Optimal Substructure**—the solution to the larger problem ($N$) depends directly on the solution to smaller subproblems ($N-1$ and $N-2$).
Recognizing this, the structure is identical to the classic **Fibonacci Sequence**.

**2. Justifying Dynamic Programming:**
If I were to solve this using a brute-force recursive approach, I would write a function, say `ways(n)`, that calls `ways(n-1)` and `ways(n-2)`.
However, `ways(n-1)` would *also* call `ways(n-2)` and `ways(n-3)`.
Notice that `ways(n-2)` is being computed twice. As $N$ grows, this redundancy explodes exponentially ($O(2^N)$), leading to a Time Limit Exceeded error.
Because we have **Overlapping Subproblems** (we are solving the same states repeatedly), this is a perfect candidate for **Dynamic Programming**, where we calculate each state once and store the result.

**3. Proposed Algorithm (Step-by-Step):**
I will use an **Iterative (Bottom-Up)** approach to solve this, as it generally avoids the overhead of recursion depth.

*   **Step 1: Base Cases.**
    I'll handle the smallest inputs first.
    *   If $N=0$ (Ground), there is 1 way (we are essentially already there).
    *   If $N=1$, there is 1 way (jump 0 $\to$ 1).
    *   If $N=2$, there are 2 ways (0 $\to$ 1 $\to$ 2 OR 0 $\to$ 2).
*   **Step 2: Iteration.**
    I will iterate from step 2 up to $N$.
    For every step `i`, I'll update the number of ways as `ways[i] = ways[i-1] + ways[i-2]`.
*   **Step 3: Space Optimization.**
    A standard DP table `dp[N+1]` would take $O(N)$ space.
    However, looking at the relation, to find `ways[i]`, I *only* need the values of the previous two steps, `ways[i-1]` and `ways[i-2]`. I don't need the history before that.
    Therefore, I can reduce the space complexity to $O(1)$ by simply maintaining two variables:
    *   `prev` (representing ways to $i-1$)
    *   `prev2` (representing ways to $i-2$)
    In each iteration, I’ll calculate `current = prev + prev2` and then shift the pointers forward (`prev2 = prev`, `prev = current`).

**4. Edge Cases:**
*   **$N=0$ or $N=1$:** My logic handles this by returning 1 immediately or initializing variables such that the loop doesn't break anything.
*   **Integer Overflow:** For very large $N$ (like $N=500$), the number of ways will exceed the range of a standard 32-bit integer. In a real application, I would use formatting types like `BigInteger` or return the result modulo $10^9 + 7$, depending on the specific requirements.

**5. Complexity Analysis:**
*   **Time Complexity:** $O(N)$, because we run a single loop from 2 to $N$.
*   **Space Complexity:** $O(1)$, because we are only using constant extra space (variables `prev` and `prev2`) instead of a full array.

This approach is efficient, readable, and optimal for the given constraints."

---

## 1️⃣ Approach 1: Recursion

Let's apply our 3-Step Framework:
1.  **Index:** Let `f(n)` be the number of ways to reach the Nth stair.
2.  **Choices:** To reach `n`, we could have jumped from `n-1` or `n-2`.
    *   Call `f(n-1)`
    *   Call `f(n-2)`
3.  **Sum:** Since we need total ways, we add them: `f(n) = f(n-1) + f(n-2)`.

### Base Cases
*   If `n == 0`: You are already at the ground. There is **1 way** to be there (do nothing). Return 1.
*   If `n == 1`: You can only reach from 0 (1 jump). Return 1.
    *   *Alternative View:* Taking `f(n-2)` from `n=1` gives `f(-1)`. We can handle index < 0 by returning 0, or handle `n=1` explicitly.

### Recursive Code (Java)

```java
// Logic: Count ways to reach N = (ways to reach N-1) + (ways to reach N-2)
class Solution {
    public int climbStairs(int n) {
        // Base Condition:
        // If n is 0, we found 1 valid path (resting state).
        // If n is 1, only 1 way to reach (step 0 -> 1).
        if (n == 0 || n == 1) {
            return 1;
        }
        
        // Recursive Hypothesis:
        // Attempt infinite jumps until we hit base case.
        // Problem: This causes exponential complexity O(2^N) due to re-calculating same states.
        return climbStairs(n - 1) + climbStairs(n - 2);
    }
}
```

**Wait... does this look familiar?**
Yes! `f(n) = f(n-1) + f(n-2)`. This is exactly the **Fibonacci Sequence**.

The logic is identical to finding the Nth Fibonacci number.
*   Ways to reach 0: 1
*   Ways to reach 1: 1
*   Ways to reach 2: 1 + 1 = 2
*   Ways to reach 3: 2 + 1 = 3
*   Ways to reach 4: 3 + 2 = 5

---

## 2️⃣ Approach 2: Memoization (Top-Down)

Since this problem overlaps exactly with Fibonacci, we simply memoize it.

```java
import java.util.*;

class Solution {
    public int climbStairs(int n) {
        // Step 1: Initialize DP array to store computed results.
        // Size is n + 1 to accommodate index 'n'.
        int[] dp = new int[n + 1];
        
        // Fill array with -1 to indicate that no state has been solved yet.
        Arrays.fill(dp, -1);
        
        // Start the recursive problem solving with memoization.
        return solve(n, dp);
    }

    private int solve(int n, int[] dp) {
        // Base Case: Standard Fibonacci base cases.
        // If stairs are 0 or 1, distinct ways are 1.
        if (n <= 1) return 1;
        
        // Step 2: Check if this subproblem (n) is already computed.
        // If dp[n] is not -1, return stored value to save time (Pruning).
        if (dp[n] != -1) return dp[n];
        
        // Step 3: Compute and Store.
        // Logic: ways(n) = ways(n-1) + ways(n-2).
        // Store result in dp[n] before returning.
        return dp[n] = solve(n - 1, dp) + solve(n - 2, dp);
    }
}
```
*   **Time Complexity:** $O(N)$
*   **Space Complexity:** $O(N)$ (Stack) + $O(N)$ (Array)

---

## 3️⃣ Approach 3: Tabulation (Bottom-Up)

Convert the recursion to iteration. Start from base cases and build up.

```java
class Solution {
    public int climbStairs(int n) {
        // Handle Edge Case for very small inputs
        if (n <= 1) return 1;
        
        // Step 1: Create a DP array to store values.
        int[] dp = new int[n + 1];
        
        // Step 2: Initialize Base Cases in the array.
        // Known: 1 way to be at 0, 1 way to reach 1.
        dp[0] = 1;
        dp[1] = 1;

        // Step 3: Iterate from step 2 up to n.
        for (int i = 2; i <= n; i++) {
            // Apply the recurrence relation iteratively.
            // Current step ways = (Previous Step) + (Second Previous Step)
            
            // DRY RUN TRACE (Example N=3):
            // i=2: dp[2] = dp[1] (1) + dp[0] (1) = 2.
            // i=3: dp[3] = dp[2] (2) + dp[1] (1) = 3.
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        
        // Return the final answer store at index n.
        return dp[n];
    }
}
```
*   **Time Complexity:** $O(N)$
*   **Space Complexity:** $O(N)$ (Array) - **No Recursion Stack!**

---

## 4️⃣ Approach 4: Space Optimization

Just like Fibonacci, we only need the last two values (`prev` and `prev2`) to compute the current value. We don't need the full array.
This section is extremely important, so I have included a detailed **Dry Run** trace inside the code.

```java
class Solution {
    public int climbStairs(int n) {
        // Handle base small cases immediately.
        if (n <= 1) return 1;
        
        // Space Optimization Logic:
        // We only need the last two values to calculate the new one.
        // Let's declare two variables instead of a whole array.
        
        int prev2 = 1; // Represents ways to reach step (i-2), initially step 0.
        int prev = 1;  // Represents ways to reach step (i-1), initially step 1.
        
        // Iterate for remaining steps from 2 to n.
        for (int i = 2; i <= n; i++) {
            // Calculate current ways: Sum of last two steps.
            int cur_i = prev + prev2;
            
            // --- DRY RUN TRACE for N = 4 ---
            // Iteration i = 2:
            //   cur_i = prev(1) + prev2(1) = 2
            //   Update: prev2 becomes 1, prev becomes 2
            //
            // Iteration i = 3:
            //   cur_i = prev(2) + prev2(1) = 3
            //   Update: prev2 becomes 2, prev becomes 3
            //
            // Iteration i = 4:
            //   cur_i = prev(3) + prev2(2) = 5
            //   Update: prev2 becomes 3, prev becomes 5
            // -------------------------------
            
            // Shift pointers forward for the next iteration.
            // Discard old prev2, move prev to prev2.
            prev2 = prev;
            // Move cur_i to prev.
            prev = cur_i;
        }
        
        // Comparison with DP array:
        // prev is essentially dp[n] at the end of the loop.
        return prev;
    }
}
```
*   **Time Complexity:** $O(N)$
*   **Space Complexity:** $O(1)$

---

## 🗒️ Important Note on Limits

In generic online judges like LeetCode, $N$ is usually small ($N \le 45$), so $O(N)$ works perfectly.
However, if $N$ is very large (e.g., $10^{18}$), such as in CodeStudio/coding rounds, an $O(N)$ loop will give TLE (Time Limit Exceeded).
For such massive constraints, you must use **Matrix Exponentiation** to solve this in $O(\log N)$ time. (This is an advanced topic, but keep it in mind!).

---

## 🎯 Summary

1.  **Identify:** "Count ways" $\rightarrow$ Recursion $\rightarrow$ DP.
2.  **Shortcut Strategy:**
    *   Index: `f(i)`
    *   Do stuff: `f(i-1)`, `f(i-2)`
    *   Count? Sum them up.
3.  **Result:** This specific problem reduces to the **Fibonacci Sequence**.

If you understood this 3-step framework, comment **"Understood"** below! This pattern will be used in almost every lecture going forward.

See you in the next one where we tackle the **Frog Jump** problem! 👋
