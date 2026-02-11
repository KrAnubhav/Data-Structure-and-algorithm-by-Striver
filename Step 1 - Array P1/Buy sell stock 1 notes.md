# Best Time to Buy and Sell Stock - DP on Stocks (Problem 1)

## Introduction to DP on Stocks

This is the **first problem** in the DP on Stocks series, which consists of **6 problems total**.

**Important Notes:**
- Follow problems **sequentially** (don't skip to problem 4!)
- Intuition builds problem by problem
- **Space optimization is crucial** for interviews
- Interviewers expect space-optimized solutions

---

## Problem Statement

You are given an array representing **stock prices** on different days.

### Rules

1. You can **buy** the stock on **one day**
2. You can **sell** the stock on **another day**
3. **Selling must happen AFTER buying** (you can't sell before buying)
4. You can only do **one transaction** (one buy + one sell)
5. **Goal:** Maximize profit

### Example

```
Day:     1   2   3   4   5   6
Price:  [7,  1,  5,  3,  6,  4]
Index:   0   1   2   3   4   5
```

**Possible Transactions:**

```
Buy on Day 2 (price = 1), Sell on Day 5 (price = 6)
Profit = 6 - 1 = 5 ✓ Maximum!

Buy on Day 2 (price = 1), Sell on Day 3 (price = 5)
Profit = 5 - 1 = 4

Buy on Day 4 (price = 3), Sell on Day 5 (price = 6)
Profit = 6 - 3 = 3

Buy on Day 1 (price = 7), Sell on Day 2 (price = 1)
Profit = 1 - 7 = -6 (Loss, not valid)
```

**Answer:** 5 (maximum profit)

---

## Key Insight

**Question:** If you're selling on day `i`, when should you buy?

**Answer:** On the day with **minimum price** before day `i`!

### Why?

```
Selling on Day 5 (price = 6):
- Buy on Day 1 (price = 7): Profit = 6 - 7 = -1 ❌
- Buy on Day 2 (price = 1): Profit = 6 - 1 = 5  ✓
- Buy on Day 3 (price = 5): Profit = 6 - 5 = 1  ❌
- Buy on Day 4 (price = 3): Profit = 6 - 3 = 3  ❌

Best: Buy on minimum price (Day 2, price = 1)
```

**Core Principle:** 
```
For each day i (potential selling day):
    Find minimum price from day 0 to day i-1
    Profit = price[i] - minimum_price
    Track maximum profit
```

---

## Approach: Greedy with DP Concept

### Intuition

1. **For each day:** Consider it as a potential selling day
2. **Track minimum price** seen so far (before current day)
3. **Calculate profit:** Current price - minimum price
4. **Update maximum profit**

### Why is this Dynamic Programming?

**Definition of DP:** Remembering the past to solve the present

**In this problem:** 
- We **remember** the minimum price seen so far
- Use it to calculate profit for current day
- This is **DP** because we're using stored information from previous states

---

## Step-by-Step Walkthrough

**Array:** [7, 1, 5, 3, 6, 4]

**Initial State:**
```
minimum = 7 (first price)
maxProfit = 0 (can't make profit on first day)
```

### Day 0: Price = 7
```
Index 0, Price = 7
- First day, can't sell (no previous day to buy)
- minimum = 7
- maxProfit = 0
```

### Day 1: Price = 1
```
Index 1, Price = 1
- Selling price = 1
- Minimum buy price so far = 7
- Profit = 1 - 7 = -6 (loss)
- maxProfit = max(0, -6) = 0
- Update minimum = min(7, 1) = 1
```

### Day 2: Price = 5
```
Index 2, Price = 5
- Selling price = 5
- Minimum buy price so far = 1
- Profit = 5 - 1 = 4
- maxProfit = max(0, 4) = 4 ✓
- Update minimum = min(1, 5) = 1
```

### Day 3: Price = 3
```
Index 3, Price = 3
- Selling price = 3
- Minimum buy price so far = 1
- Profit = 3 - 1 = 2
- maxProfit = max(4, 2) = 4
- Update minimum = min(1, 3) = 1
```

### Day 4: Price = 6
```
Index 4, Price = 6
- Selling price = 6
- Minimum buy price so far = 1
- Profit = 6 - 1 = 5
- maxProfit = max(4, 5) = 5 ✓
- Update minimum = min(1, 6) = 1
```

### Day 5: Price = 4
```
Index 5, Price = 4
- Selling price = 4
- Minimum buy price so far = 1
- Profit = 4 - 1 = 3
- maxProfit = max(5, 3) = 5
- Update minimum = min(1, 4) = 1
```

**Final Answer:** maxProfit = 5

---

## Visual Representation

```
Prices:     7    1    5    3    6    4
            ↓    ↓    ↓    ↓    ↓    ↓
Minimum:    7    1    1    1    1    1
Profit:     0   -6    4    2    5    3
MaxProfit:  0    0    4    4    5    5
            
Action:
Day 0: Initialize minimum = 7, maxProfit = 0
Day 1: Found new minimum = 1, profit negative
Day 2: Profit = 4 (sell at 5, buy at 1)
Day 3: Profit = 2 (less than maxProfit)
Day 4: Profit = 5 (sell at 6, buy at 1) ✓ Best!
Day 5: Profit = 3 (less than maxProfit)
```

---

## Algorithm

```
1. Initialize:
   - minimum = prices[0]
   - maxProfit = 0

2. For each day i from 1 to n-1:
   a. Calculate cost = prices[i] - minimum
   b. Update maxProfit = max(maxProfit, cost)
   c. Update minimum = min(minimum, prices[i])

3. Return maxProfit
```

---

## Complete Java Code

```java
public class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        
        // Edge case: can't make profit with 0 or 1 day
        if(n <= 1) return 0;
        
        // Track minimum price seen so far
        int minimum = prices[0];
        
        // Track maximum profit
        int maxProfit = 0;
        
        // Check each day as potential selling day
        for(int i = 1; i < n; i++) {
            // Calculate profit if we sell on day i
            int cost = prices[i] - minimum;
            
            // Update maximum profit
            maxProfit = Math.max(maxProfit, cost);
            
            // Update minimum price for future days
            minimum = Math.min(minimum, prices[i]);
        }
        
        return maxProfit;
    }
    
    public static void main(String[] args) {
        Solution sol = new Solution();
        
        int[] prices = {7, 1, 5, 3, 6, 4};
        System.out.println("Prices: " + Arrays.toString(prices));
        System.out.println("Maximum Profit: " + sol.maxProfit(prices));
        // Output: 5
    }
}
```

---

## Alternative Code (Starting from Index 0)

```java
public class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        if(n == 0) return 0;
        
        int minimum = prices[0];
        int maxProfit = 0;
        
        // Can start from 0 as well
        for(int i = 0; i < n; i++) {
            // Calculate profit
            int cost = prices[i] - minimum;
            maxProfit = Math.max(maxProfit, cost);
            
            // Update minimum
            minimum = Math.min(minimum, prices[i]);
        }
        
        return maxProfit;
    }
}
```

**Note:** Starting from index 0 or 1 both work because:
- At index 0: cost = prices[0] - prices[0] = 0 (no effect on maxProfit)

---

## Complexity Analysis

### Time Complexity: **O(N)**

**Breakdown:**
- Single pass through array: O(N)
- Each iteration does constant work: O(1)
- Total: O(N)

### Space Complexity: **O(1)**

**Breakdown:**
- Only using 2 variables: `minimum` and `maxProfit`
- No extra data structures
- Space does not depend on input size

---

## Why This is Considered DP

**Dynamic Programming Definition:**
> Using solutions to subproblems to solve the main problem

**In This Problem:**

**Subproblem:** "What's the minimum price seen so far?"

**Main Problem:** "What's the maximum profit?"

**DP Aspect:**
- We **store** (remember) the minimum price
- We **reuse** this stored value for each day
- This is **memoization** of the minimum value

**Recurrence Relation (Implicit):**
```
minimum[i] = min(minimum[i-1], prices[i])
maxProfit[i] = max(maxProfit[i-1], prices[i] - minimum[i-1])
```

**Space Optimization:**
- Instead of maintaining arrays, we use variables
- `minimum` represents `minimum[i]`
- `maxProfit` represents `maxProfit[i]`

---

## Edge Cases

### Case 1: Prices Always Decreasing
```
Input: [5, 4, 3, 2, 1]
Output: 0

Explanation: No profit possible, best is to not trade
```

### Case 2: Prices Always Increasing
```
Input: [1, 2, 3, 4, 5]
Output: 4

Explanation: Buy on day 1 (price=1), sell on day 5 (price=5)
Profit = 5 - 1 = 4
```

### Case 3: Single Day
```
Input: [5]
Output: 0

Explanation: Can't buy and sell on same day
```

### Case 4: Two Days
```
Input: [3, 1]
Output: 0

Explanation: Price decreases, no profit

Input: [1, 5]
Output: 4

Explanation: Buy on day 1, sell on day 2
```

### Case 5: All Same Prices
```
Input: [3, 3, 3, 3]
Output: 0

Explanation: No price difference, no profit
```

---

## Common Mistakes

❌ **Mistake 1: Selling before buying**
```java
// WRONG: Calculating max - min without considering order
int maxProfit = Collections.max(prices) - Collections.min(prices);
```
**Why wrong?** Maximum might come before minimum!

❌ **Mistake 2: Not updating minimum**
```java
// WRONG: Using initial minimum throughout
int minimum = prices[0];
for(int i = 1; i < n; i++) {
    maxProfit = Math.max(maxProfit, prices[i] - minimum);
    // Missing: minimum = Math.min(minimum, prices[i]);
}
```

❌ **Mistake 3: Allowing negative profit**
```java
// WRONG: Not initializing maxProfit to 0
int maxProfit = Integer.MIN_VALUE;
```
**Why wrong?** We should return 0 if no profitable transaction exists!

✅ **Correct Approach:**
```java
int minimum = prices[0];
int maxProfit = 0;  // Initialize to 0
for(int i = 1; i < n; i++) {
    int cost = prices[i] - minimum;
    maxProfit = Math.max(maxProfit, cost);  // Won't go below 0
    minimum = Math.min(minimum, prices[i]);  // Keep updating
}
```

---

## Comparison with Brute Force

### Brute Force Approach

```java
// Try all pairs (buy day, sell day)
int maxProfit = 0;
for(int i = 0; i < n; i++) {        // Buy day
    for(int j = i+1; j < n; j++) {  // Sell day
        int profit = prices[j] - prices[i];
        maxProfit = Math.max(maxProfit, profit);
    }
}
return maxProfit;
```

**Time Complexity:** O(N²)  
**Space Complexity:** O(1)

### Optimized Approach (Our Solution)

**Time Complexity:** O(N)  
**Space Complexity:** O(1)

**Improvement:** Massive speedup from O(N²) to O(N)!

---

## Interview Tips

1. **Start with explanation:**
   - "For each selling day, I need the minimum buying day before it"
   - "I'll track the minimum as I go"

2. **Mention it's DP:**
   - "This uses DP concept of remembering past minimum"
   - "We're doing implicit memoization"

3. **State complexity:**
   - "O(N) time - single pass"
   - "O(1) space - only two variables"

4. **Handle edge cases:**
   - "If prices always decrease, return 0"
   - "If only one day, return 0"

5. **Code cleanly:**
   - Use meaningful variable names: `minimum`, `maxProfit`
   - Add comments for clarity

---

## Key Takeaways

1. **Core Strategy:** Track minimum price seen so far

2. **For Each Day:**
   - Calculate profit if selling today
   - Update maximum profit
   - Update minimum price

3. **Why It Works:**
   - Always buying at best price before selling
   - Greedy choice of minimum is optimal

4. **DP Aspect:**
   - Remember minimum from past
   - Use it for current decision

5. **Optimization:**
   - O(N²) brute force → O(N) optimized
   - Space optimized to O(1)

---

## Practice Variations

After mastering this problem, try:

1. **Best Time to Buy and Sell Stock II** (Multiple transactions)
2. **Best Time to Buy and Sell Stock III** (At most 2 transactions)
3. **Best Time to Buy and Sell Stock IV** (At most k transactions)
4. **Best Time to Buy and Sell Stock with Cooldown**
5. **Best Time to Buy and Sell Stock with Transaction Fee**

All build on concepts from this problem!

---

## Connection to DP on Stocks Series

**Problem 1 (This one):** 1 transaction, no constraints
- **Learn:** Basic buy/sell logic, tracking minimum

**Upcoming Problems:**
- Problem 2: Multiple transactions
- Problem 3: 2 transactions max
- Problem 4: K transactions max
- Problem 5: With cooldown period
- Problem 6: With transaction fee

Each problem builds on the previous!

---

## Summary

**Problem:** Maximize profit from one buy-sell transaction

**Solution:** Track minimum price, calculate profit for each day

**Complexity:** O(N) time, O(1) space

**Key Insight:** For selling day i, buy on minimum price before i

**DP Concept:** Remember past minimum to optimize current decision

This is a **foundation problem** for the entire DP on Stocks series!