# Kadane's Algorithm - Maximum Subarray Sum

## Problem Statement

Given an array of integers, find the **maximum sum** among all possible subarrays.

### What is a Subarray?

**Subarray:** A **contiguous** part of the array

**Valid Subarrays:**
```
Array: [-2, -3, 4, -1, -2, 1, 5]

Examples of subarrays:
[-2]           ✓ Single element
[-2, -3]       ✓ Contiguous elements
[4, -1, -2]    ✓ Contiguous elements
[-2, -3, 4, -1, -2, 1, 5]  ✓ Entire array
```

**Not a Subarray:**
```
[-2, 4]        ✗ Not contiguous (missing -3)
[-2, -3, 5]    ✗ Not contiguous (missing 4, -1, -2, 1)
```

**Note:** Not contiguous = **Subsequence** (different concept)

### Example

**Input:** [-2, -3, 4, -1, -2, 1, 5]

**All possible subarrays and their sums:**
```
[-2]                        → sum = -2
[-2, -3]                    → sum = -5
[-2, -3, 4]                 → sum = -1
[-2, -3, 4, -1]             → sum = -2
[-2, -3, 4, -1, -2]         → sum = -4
[-2, -3, 4, -1, -2, 1]      → sum = -3
[-2, -3, 4, -1, -2, 1, 5]   → sum = 2

[-3]                        → sum = -3
[-3, 4]                     → sum = 1
[-3, 4, -1]                 → sum = 0
... and so on

[4, -1, -2, 1, 5]          → sum = 7  ✓ Maximum!
```

**Answer:** 7

---

## Approach 1: Brute Force (Try All Subarrays)

### Pattern Observation

**How subarrays are formed:**
```
Standing at index 0:
[-2]
[-2, -3]
[-2, -3, 4]
[-2, -3, 4, -1]
... (take elements one by one till end)

Standing at index 1:
[-3]
[-3, 4]
[-3, 4, -1]
... (take elements one by one till end)

Standing at index 2:
[4]
[4, -1]
[4, -1, -2]
...
```

**Pattern:**
1. Stand at each index i
2. From i, extend to each index j (i to end)
3. For each (i, j) pair, calculate subarray sum

### Algorithm

```
For each starting point i (0 to n-1):
    For each ending point j (i to n-1):
        Calculate sum of subarray from i to j:
            sum = 0
            For k from i to j:
                sum += arr[k]
        Update maximum if sum > maximum
```

### Visual Example

**Array:** [-2, -3, 4, -1, -2, 1, 5]

```
i=0, j=0: sum of [-2]                     = -2
i=0, j=1: sum of [-2, -3]                 = -5
i=0, j=2: sum of [-2, -3, 4]              = -1
i=0, j=3: sum of [-2, -3, 4, -1]          = -2
...

i=1, j=1: sum of [-3]                     = -3
i=1, j=2: sum of [-3, 4]                  = 1
i=1, j=3: sum of [-3, 4, -1]              = 0
...

i=2, j=2: sum of [4]                      = 4
i=2, j=3: sum of [4, -1]                  = 3
i=2, j=4: sum of [4, -1, -2]              = 1
i=2, j=5: sum of [4, -1, -2, 1]           = 2
i=2, j=6: sum of [4, -1, -2, 1, 5]        = 7  ✓ Maximum
```

### Code

```java
public class Solution {
    public static int maxSubarraySum(int[] arr) {
        int n = arr.length;
        int maximum = Integer.MIN_VALUE;
        
        // Starting point of subarray
        for(int i = 0; i < n; i++) {
            // Ending point of subarray
            for(int j = i; j < n; j++) {
                // Calculate sum from i to j
                int sum = 0;
                for(int k = i; k <= j; k++) {
                    sum += arr[k];
                }
                // Update maximum
                maximum = Math.max(maximum, sum);
            }
        }
        
        return maximum;
    }
}
```

### Complexity Analysis

**Time Complexity:** O(N³)
- Outer loop i: O(N)
- Inner loop j: O(N)
- Innermost loop k: O(N)
- Total: O(N × N × N) ≈ O(N³)

**Space Complexity:** O(1)
- No extra space used

**Verdict:** ❌ Too slow for large arrays

---

## Approach 2: Better Solution

### Key Observation

When calculating subarray sums, we're recalculating unnecessarily:

```
Subarray [i to j]:   sum = arr[i] + arr[i+1] + ... + arr[j]
Subarray [i to j+1]: sum = arr[i] + arr[i+1] + ... + arr[j] + arr[j+1]
                                                    ↑______________↑
                                                    Already calculated!
```

**Optimization:** Reuse previous sum and just add next element!

### Algorithm

```
For each starting point i:
    sum = 0
    For each ending point j (from i to n-1):
        sum += arr[j]  // Add current element to previous sum
        Update maximum if sum > maximum
```

### Visual Example

**Array:** [-2, -3, 4, -1, -2, 1, 5]

```
i=0:
  j=0: sum = 0 + (-2) = -2,  max = -2
  j=1: sum = -2 + (-3) = -5, max = -2
  j=2: sum = -5 + 4 = -1,    max = -2
  j=3: sum = -1 + (-1) = -2, max = -2
  j=4: sum = -2 + (-2) = -4, max = -2
  j=5: sum = -4 + 1 = -3,    max = -2
  j=6: sum = -3 + 5 = 2,     max = 2

i=1:
  j=1: sum = 0 + (-3) = -3,  max = 2
  j=2: sum = -3 + 4 = 1,     max = 2
  j=3: sum = 1 + (-1) = 0,   max = 2
  ...

i=2:
  j=2: sum = 0 + 4 = 4,      max = 4
  j=3: sum = 4 + (-1) = 3,   max = 4
  j=4: sum = 3 + (-2) = 1,   max = 4
  j=5: sum = 1 + 1 = 2,      max = 4
  j=6: sum = 2 + 5 = 7,      max = 7  ✓
```

### Code

```java
public class Solution {
    public static int maxSubarraySum(int[] arr) {
        int n = arr.length;
        int maximum = Integer.MIN_VALUE;
        
        // Starting point
        for(int i = 0; i < n; i++) {
            int sum = 0;
            
            // Ending point - reuse sum
            for(int j = i; j < n; j++) {
                sum += arr[j];  // Just add current element
                maximum = Math.max(maximum, sum);
            }
        }
        
        return maximum;
    }
}
```

### Complexity Analysis

**Time Complexity:** O(N²)
- Outer loop: O(N)
- Inner loop: O(N)
- Total: O(N × N) = O(N²)

**Space Complexity:** O(1)

**Verdict:** ✅ Better, but can we do even better?

---

## Approach 3: Optimal - Kadane's Algorithm

### Key Intuition

**Observation 1: Negative Sums are Harmful**

If current sum becomes negative, it will **reduce** future sums:
```
Current sum = -5
Next element = 3
New sum = -5 + 3 = -2 (worse than just taking 3)

Better: Drop -5, start fresh with 3
```

**Observation 2: Carry Forward Only Positive Sums**

Positive sums **add value** to future elements:
```
Current sum = 4 (positive)
Next element = -1
New sum = 4 + (-1) = 3 (still positive, still valuable)

Keep carrying: Even with -1, we gain from previous +4
```

### Algorithm Steps

1. **Initialize:**
   - `sum = 0` (current subarray sum)
   - `maximum = Integer.MIN_VALUE` (best sum found)

2. **Iterate through array:**
   - Add current element to sum
   - Update maximum if sum > maximum
   - **If sum < 0:** Reset sum to 0 (drop negative baggage)

3. **Return maximum**

### Detailed Walkthrough

**Array:** [-2, -3, 4, -1, -2, 1, 5]

```
Initial: sum = 0, maximum = Integer.MIN_VALUE

Index 0: Element = -2
  sum = 0 + (-2) = -2
  maximum = max(Integer.MIN_VALUE, -2) = -2
  sum < 0? Yes → sum = 0  (Drop negative sum)

Index 1: Element = -3
  sum = 0 + (-3) = -3
  maximum = max(-2, -3) = -2
  sum < 0? Yes → sum = 0  (Drop negative sum)

Index 2: Element = 4
  sum = 0 + 4 = 4
  maximum = max(-2, 4) = 4
  sum < 0? No → Keep sum = 4  (Positive is valuable!)

Index 3: Element = -1
  sum = 4 + (-1) = 3
  maximum = max(4, 3) = 4
  sum < 0? No → Keep sum = 3  (Still positive!)

Index 4: Element = -2
  sum = 3 + (-2) = 1
  maximum = max(4, 1) = 4
  sum < 0? No → Keep sum = 1  (Still positive!)

Index 5: Element = 1
  sum = 1 + 1 = 2
  maximum = max(4, 2) = 4
  sum < 0? No → Keep sum = 2

Index 6: Element = 5
  sum = 2 + 5 = 7
  maximum = max(4, 7) = 7  ✓
  sum < 0? No → Keep sum = 7

Final Answer: 7
```

### Why Kadane's Algorithm Works

**Visual Representation:**
```
Array:  [-2, -3,  4, -1, -2,  1,  5]
Sum:     -2   0   4   3   1   2   7
Action: DROP DROP KEEP KEEP KEEP KEEP KEEP
```

**Key Insight:**
- At index 2, we **start fresh** with 4
- Even though -1 and -2 are negative, the **accumulated positive sum** (4) makes it worth carrying
- The subarray [4, -1, -2, 1, 5] gives maximum sum = 7

**What if we didn't carry positive sums?**
```
Taking only [5]: sum = 5
Taking [4, -1, -2, 1, 5]: sum = 7  ✓ Better!
```

The positive start (4) and positive additions (1) outweigh the negatives (-1, -2).

### Code

```java
public class Solution {
    public static long maxSubarraySum(int[] arr, int n) {
        long sum = 0;
        long maximum = Long.MIN_VALUE;
        
        for(int i = 0; i < n; i++) {
            sum += arr[i];
            
            // Update maximum
            if(sum > maximum) {
                maximum = sum;
            }
            
            // Drop negative sums
            if(sum < 0) {
                sum = 0;
            }
        }
        
        return maximum;
    }
}
```

### Complexity Analysis

**Time Complexity:** O(N)
- Single pass through array
- Constant work per element

**Space Complexity:** O(1)
- Only using two variables

**Verdict:** ✅✅ Optimal!

---

## Special Case: Empty Subarray

### Problem Variation

Some problems allow returning **empty subarray** (sum = 0) if all elements are negative.

**Example:**
```
Input: [-4, -2, -3, -1]

All subarrays have negative sums:
[-4]           → -4
[-2]           → -2
[-3]           → -3
[-1]           → -1 (best among all)

If empty subarray allowed:
[]             → 0  ✓ Better!
```

### Modified Code

```java
public class Solution {
    public static long maxSubarraySum(int[] arr, int n) {
        long sum = 0;
        long maximum = Long.MIN_VALUE;
        
        for(int i = 0; i < n; i++) {
            sum += arr[i];
            
            if(sum > maximum) {
                maximum = sum;
            }
            
            if(sum < 0) {
                sum = 0;
            }
        }
        
        // If all elements are negative, return 0 (empty subarray)
        if(maximum < 0) {
            maximum = 0;
        }
        
        return maximum;
    }
}
```

---

## Follow-up: Print the Maximum Subarray

### Problem

Not just return the sum, but **print the actual subarray** that gives maximum sum.

### Key Observation

**When does a new subarray start?**
- When `sum = 0` (we dropped previous negative sum)

**When does a subarray end?**
- When we find a new maximum sum

### Modified Algorithm

Track two things:
1. `start` - Where current subarray started
2. `answerStart`, `answerEnd` - Indices of maximum subarray

```java
public class Solution {
    public static long maxSubarraySum(int[] arr, int n) {
        long sum = 0;
        long maximum = Long.MIN_VALUE;
        
        int start = 0;          // Current subarray start
        int answerStart = -1;   // Maximum subarray start
        int answerEnd = -1;     // Maximum subarray end
        
        for(int i = 0; i < n; i++) {
            // New subarray starts when sum was 0
            if(sum == 0) {
                start = i;
            }
            
            sum += arr[i];
            
            // Found new maximum
            if(sum > maximum) {
                maximum = sum;
                answerStart = start;  // Record where this max started
                answerEnd = i;        // Record where this max ended
            }
            
            if(sum < 0) {
                sum = 0;
            }
        }
        
        // Print the subarray
        System.out.print("Maximum subarray: [");
        for(int i = answerStart; i <= answerEnd; i++) {
            System.out.print(arr[i]);
            if(i < answerEnd) System.out.print(", ");
        }
        System.out.println("]");
        
        return maximum;
    }
}
```

### Example Walkthrough

**Array:** [-2, -3, 4, -1, -2, 1, 5]

```
Index 0: sum = 0 → start = 0
  Add -2: sum = -2, max = -2, answerStart = 0, answerEnd = 0
  sum < 0 → sum = 0

Index 1: sum = 0 → start = 1
  Add -3: sum = -3, max = -2
  sum < 0 → sum = 0

Index 2: sum = 0 → start = 2  ← Maximum subarray starts here!
  Add 4: sum = 4, max = 4, answerStart = 2, answerEnd = 2
  sum ≥ 0 → keep

Index 3: sum = 4 (not 0) → start stays 2
  Add -1: sum = 3, max = 4
  sum ≥ 0 → keep

Index 4: sum = 3 (not 0) → start stays 2
  Add -2: sum = 1, max = 4
  sum ≥ 0 → keep

Index 5: sum = 1 (not 0) → start stays 2
  Add 1: sum = 2, max = 4
  sum ≥ 0 → keep

Index 6: sum = 2 (not 0) → start stays 2
  Add 5: sum = 7, max = 7, answerStart = 2, answerEnd = 6  ✓
  sum ≥ 0 → keep

Result:
answerStart = 2
answerEnd = 6
Subarray: [4, -1, -2, 1, 5]
Sum: 7
```

### Visual Timeline

```
Array:  [-2, -3,  4, -1, -2,  1,  5]
Index:    0   1   2   3   4   5   6

sum = 0 at indices: 0, 1, 2
↓
start variable: 0 → 1 → 2 (stays)
↓
Maximum found at index 6
↓
answerStart = 2 (where sum was 0 before max)
answerEnd = 6 (where maximum was found)
↓
Subarray: arr[2..6] = [4, -1, -2, 1, 5]
```

---

## Complete Code with All Features

```java
public class Solution {
    public static long maxSubarraySum(int[] arr, int n) {
        long sum = 0;
        long maximum = Long.MIN_VALUE;
        
        // For printing subarray
        int start = 0;
        int answerStart = -1;
        int answerEnd = -1;
        
        for(int i = 0; i < n; i++) {
            // Track where current subarray starts
            if(sum == 0) {
                start = i;
            }
            
            sum += arr[i];
            
            // Update maximum and track indices
            if(sum > maximum) {
                maximum = sum;
                answerStart = start;
                answerEnd = i;
            }
            
            // Drop negative sums
            if(sum < 0) {
                sum = 0;
            }
        }
        
        // Handle empty subarray case (if problem requires)
        if(maximum < 0) {
            maximum = 0;
        }
        
        // Optional: Print the subarray
        if(answerStart != -1 && answerEnd != -1) {
            System.out.print("Maximum subarray: [");
            for(int i = answerStart; i <= answerEnd; i++) {
                System.out.print(arr[i]);
                if(i < answerEnd) System.out.print(", ");
            }
            System.out.println("]");
        }
        
        return maximum;
    }
    
    public static void main(String[] args) {
        int[] arr = {-2, -3, 4, -1, -2, 1, 5};
        int n = arr.length;
        
        long maxSum = maxSubarraySum(arr, n);
        System.out.println("Maximum sum: " + maxSum);
    }
}
```

**Output:**
```
Maximum subarray: [4, -1, -2, 1, 5]
Maximum sum: 7
```

---

## Why "sum == 0" Indicates New Start?

### Proof by Cases

**Case 1: After dropping negative sum**
```
... sum becomes -3 → drop to 0 → next element starts fresh ✓
```

**Case 2: Initial state**
```
sum = 0 (initial) → first element starts here ✓
```

**Case 3: After elements sum to exactly 0**
```
[2, -2] → sum = 0 → next element could start fresh
```

**Visual Example:**
```
Array: [-2, -3, 4, -1, -2, 1, 5]

sum = 0 at:
  Index 0 (initial)       → subarray could start
  Index 1 (after drop)    → subarray could start  
  Index 2 (after drop)    → subarray DOES start (leads to max!)
```

The subarray [4, -1, -2, 1, 5] started exactly when sum became 0 at index 2!

---

## Approach Comparison

| Approach | Time | Space | Description |
|----------|------|-------|-------------|
| Brute Force | O(N³) | O(1) | Try all subarrays, recalculate sum each time |
| Better | O(N²) | O(1) | Try all subarrays, reuse previous sum |
| Kadane's | O(N) | O(1) | Drop negatives, carry positives |

---

## Key Takeaways

1. **Kadane's Algorithm = Greedy Approach**
   - Keep positive sums (they add value)
   - Drop negative sums (they reduce value)

2. **Core Insight:**
   - Never carry forward negative sum
   - Always update maximum before dropping

3. **Edge Cases:**
   - All negative numbers → return smallest negative OR 0 (empty)
   - All positive numbers → return sum of all elements
   - Mixed → Kadane's handles perfectly

4. **Follow-up:**
   - Track `start` when sum = 0
   - Track `answerStart/End` when maximum updates

5. **Time Complexity:**
   - O(N³) → O(N²) → **O(N)** (massive improvement!)

---

## Common Mistakes to Avoid

❌ Carrying negative sums forward  
❌ Not resetting sum to 0 when negative  
❌ Initializing maximum to 0 (fails for all negative arrays)  
❌ Forgetting to update maximum before dropping sum  
✅ Always update maximum first, then check if sum < 0  
✅ Use Long.MIN_VALUE for initialization  
✅ Reset sum only when sum < 0, not sum ≤ 0

---

## Interview Tips

1. **Always start with brute force** - shows you understand the problem
2. **Optimize step by step** - brute → better → optimal
3. **Explain the intuition** - why drop negatives?
4. **Handle edge cases** - mention all negative arrays
5. **Code cleanly** - variable names matter (sum, maximum, start)
6. **Be ready for follow-up** - printing the subarray

This is a **classic algorithm** - knowing Kadane's is essential for interviews!