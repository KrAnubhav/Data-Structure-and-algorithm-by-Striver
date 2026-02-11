# Dutch National Flag Algorithm - Sort 0s, 1s, and 2s

## Problem Statement

Given an array containing **only 0s, 1s, and 2s**, sort the array in ascending order.

### Example

**Input:** [0, 1, 2, 0, 1, 2, 1, 2, 0, 0, 0, 1]

**Output:** [0, 0, 0, 0, 0, 1, 1, 1, 1, 2, 2, 2]

**Constraint:** Array contains ONLY three values: 0, 1, and 2

---

## Approach 1: Brute Force (Sorting Algorithm)

### Idea

Use any standard sorting algorithm like Merge Sort.

### Code

```java
import java.util.*;

public class Solution {
    public static void sortArray(int[] arr) {
        Arrays.sort(arr);
    }
}
```

### Complexity Analysis

**Time Complexity:** O(N log N)
- Merge Sort takes O(N log N)

**Space Complexity:** O(N)
- Merge Sort uses temporary array

**Verdict:** ❌ Not optimal - interviewer asks to optimize

---

## Approach 2: Better Solution (Counting)

### Idea

1. **Count** occurrences of 0s, 1s, and 2s
2. **Overwrite** array:
   - First count0 positions with 0
   - Next count1 positions with 1
   - Last count2 positions with 2

### Algorithm Steps

**Step 1: Count**
```
Array: [0, 1, 2, 0, 1, 2, 1, 2, 0, 0, 0, 1]

count0 = 5 (five 0s)
count1 = 4 (four 1s)
count2 = 3 (three 2s)
```

**Step 2: Overwrite**
```
Positions 0 to 4:   Fill with 0 → [0, 0, 0, 0, 0, ?, ?, ?, ?, ?, ?, ?]
Positions 5 to 8:   Fill with 1 → [0, 0, 0, 0, 0, 1, 1, 1, 1, ?, ?, ?]
Positions 9 to 11:  Fill with 2 → [0, 0, 0, 0, 0, 1, 1, 1, 1, 2, 2, 2]
```

### Code

```java
public class Solution {
    public static void sortArray(int[] arr) {
        int n = arr.length;
        int count0 = 0, count1 = 0, count2 = 0;
        
        // Step 1: Count occurrences
        for(int i = 0; i < n; i++) {
            if(arr[i] == 0) count0++;
            else if(arr[i] == 1) count1++;
            else count2++;
        }
        
        // Step 2: Overwrite array
        // Fill 0s from index 0 to count0-1
        for(int i = 0; i < count0; i++) {
            arr[i] = 0;
        }
        
        // Fill 1s from index count0 to count0+count1-1
        for(int i = count0; i < count0 + count1; i++) {
            arr[i] = 1;
        }
        
        // Fill 2s from index count0+count1 to end
        for(int i = count0 + count1; i < n; i++) {
            arr[i] = 2;
        }
    }
}
```

### Complexity Analysis

**Time Complexity:** O(N) + O(N) = **O(2N)**
- First pass: Count occurrences → O(N)
- Second pass: Overwrite array → O(N)
- Total: O(2N) ≈ O(N)

**Space Complexity:** O(1)
- Only using 3 counter variables
- Modifying input array in-place

**Verdict:** ✅ Better, but interviewer asks: "Can you do it in **one pass**?"

---

## Approach 3: Optimal - Dutch National Flag Algorithm

### The Three Rules (Core Concept)

The algorithm maintains three regions in the array using three pointers:

```
┌─────────┬─────────┬─────────────┬─────────┐
│   0s    │   1s    │  Unsorted   │   2s    │
└─────────┴─────────┴─────────────┴─────────┘
    ↑         ↑         ↑     ↑         ↑
    0      low-1     low  mid high  high+1  n-1

Rule 1: [0 to low-1]      → All 0s
Rule 2: [low to mid-1]    → All 1s  
Rule 3: [high+1 to n-1]   → All 2s
```

**Unsorted Region:** [mid to high]
- This is what we need to sort
- All other regions are already sorted

### Visual Representation

**Initial State:**
```
Array: [0, 1, 2, 0, 1, 2, 1, 2, 0, 0, 0, 1]
        ↑                               ↑
       low                            high
       mid
```

**Goal:**
```
Shrink unsorted region [mid to high] to nothing
When mid > high, we're done!
```

### Key Insight

**Question:** Where do we start low, mid, and high?

**Answer:**
- Initially, **entire array is unsorted**
- So unsorted region = entire array = [0 to n-1]
- Therefore:
  - `low = 0` (0 to low-1 = empty, which is valid for "all 0s")
  - `mid = 0` (low to mid-1 = empty, which is valid for "all 1s")
  - `high = n-1` (high+1 to n-1 = empty, which is valid for "all 2s")

### The Three Cases (Based on arr[mid])

We always work with `arr[mid]` and handle three cases:

#### Case 1: arr[mid] == 0

**Problem:** 0 belongs to left region (with other 0s)

**Solution:** 
```
1. Swap arr[low] with arr[mid]
2. low++  (0s region expanded)
3. mid++  (element at mid is now sorted)
```

**Why both move?**
- After swap, arr[mid] contains what was at arr[low]
- What was at arr[low]? Either 0 or 1 (sorted region)
- If 0: already sorted, move mid
- If 1: also sorted (1s region), move mid
- Either way, mid position is now sorted, so move ahead

**Visual:**
```
Before: [0 0 0 | 1 1 | 0 2 1 | 2 2]
              ↑     ↑   ↑
            low   mid  (arr[mid] = 0)

Swap low and mid:
After:  [0 0 0 0 | 1 | 1 2 1 | 2 2]
                ↑     ↑
              low   mid
```

#### Case 2: arr[mid] == 1

**Problem:** 1 is already in correct region

**Solution:**
```
1. mid++  (just move forward)
```

**Why only mid moves?**
- 1 belongs to region [low to mid-1]
- Current position is perfect for it
- Just expand the 1s region by moving mid

**Visual:**
```
Before: [0 0 0 | 1 1 | 1 2 0 | 2 2]
              ↑       ↑
            low     mid (arr[mid] = 1)

After:  [0 0 0 | 1 1 1 | 2 0 | 2 2]
              ↑         ↑
            low       mid
```

#### Case 3: arr[mid] == 2

**Problem:** 2 belongs to right region (with other 2s)

**Solution:**
```
1. Swap arr[mid] with arr[high]
2. high--  (2s region expanded)
3. mid stays (need to check swapped element)
```

**Why mid doesn't move?**
- After swap, arr[mid] contains what was at arr[high]
- What was at arr[high]? Unknown! (unsorted region)
- Could be 0, 1, or 2
- Need to process it in next iteration

**Visual:**
```
Before: [0 0 0 | 1 1 | 2 1 0 | 2 2]
              ↑       ↑     ↑
            low     mid   high (arr[mid] = 2)

Swap mid and high:
After:  [0 0 0 | 1 1 | 0 1 0 | 2 2 2]
              ↑       ↑   ↑
            low     mid high
                    ↑
              Check this next!
```

---

### Complete Dry Run

**Array:** [0, 1, 2, 0, 1, 2, 1, 2, 0, 0, 0, 1]

**Initial:**
```
Index:  0  1  2  3  4  5  6  7  8  9 10 11
Array: [0, 1, 2, 0, 1, 2, 1, 2, 0, 0, 0, 1]
        ↑                                  ↑
       low                               high
       mid
```

**Iteration 1:** arr[mid] = 0
```
Action: Swap arr[low] with arr[mid], low++, mid++
Result: [0, 1, 2, 0, 1, 2, 1, 2, 0, 0, 0, 1]
           ↑                               ↑
          low                            high
          mid
```

**Iteration 2:** arr[mid] = 1
```
Action: mid++
Result: [0, 1, 2, 0, 1, 2, 1, 2, 0, 0, 0, 1]
           ↑                               ↑
          low                            high
              mid
```

**Iteration 3:** arr[mid] = 2
```
Action: Swap arr[mid] with arr[high], high--
Result: [0, 1, 1, 0, 1, 2, 1, 2, 0, 0, 0, 2]
           ↑                            ↑
          low                         high
              mid
```

**Iteration 4:** arr[mid] = 1 (swapped from high)
```
Action: mid++
Result: [0, 1, 1, 0, 1, 2, 1, 2, 0, 0, 0, 2]
           ↑                            ↑
          low                         high
                 mid
```

**Iteration 5:** arr[mid] = 0
```
Action: Swap arr[low] with arr[mid], low++, mid++
Result: [0, 0, 1, 1, 1, 2, 1, 2, 0, 0, 0, 2]
              ↑                         ↑
             low                      high
                    mid
```

**Iteration 6:** arr[mid] = 1
```
Action: mid++
Result: [0, 0, 1, 1, 1, 2, 1, 2, 0, 0, 0, 2]
              ↑                         ↑
             low                      high
                       mid
```

**Iteration 7:** arr[mid] = 2
```
Action: Swap arr[mid] with arr[high], high--
Result: [0, 0, 1, 1, 1, 0, 1, 2, 0, 0, 2, 2]
              ↑                      ↑
             low                   high
                       mid
```

**Iteration 8:** arr[mid] = 0 (swapped from high)
```
Action: Swap arr[low] with arr[mid], low++, mid++
Result: [0, 0, 0, 1, 1, 1, 1, 2, 0, 0, 2, 2]
                 ↑                   ↑
                low                high
                          mid
```

**Iteration 9:** arr[mid] = 1
```
Action: mid++
Result: [0, 0, 0, 1, 1, 1, 1, 2, 0, 0, 2, 2]
                 ↑                   ↑
                low                high
                             mid
```

**Iteration 10:** arr[mid] = 2
```
Action: Swap arr[mid] with arr[high], high--
Result: [0, 0, 0, 1, 1, 1, 1, 0, 0, 2, 2, 2]
                 ↑                ↑
                low             high
                             mid
```

**Iteration 11:** arr[mid] = 0 (swapped from high)
```
Action: Swap arr[low] with arr[mid], low++, mid++
Result: [0, 0, 0, 0, 1, 1, 1, 1, 0, 2, 2, 2]
                    ↑             ↑
                   low          high
                                mid
```

**Iteration 12:** arr[mid] = 0
```
Action: Swap arr[low] with arr[mid], low++, mid++
Result: [0, 0, 0, 0, 0, 1, 1, 1, 1, 2, 2, 2]
                       ↑          ↑
                      low       high
                                   mid
```

**Iteration 13:** mid > high, STOP!
```
Final: [0, 0, 0, 0, 0, 1, 1, 1, 1, 2, 2, 2]
                       ↑          ↑
                      low       high
                                   mid

Verification:
[0 to low-1]:     [0, 0, 0, 0, 0]      ✓ All 0s
[low to mid-1]:   [1, 1, 1, 1]         ✓ All 1s
[high+1 to n-1]:  [2, 2, 2]            ✓ All 2s
```

---

### Visualizing the Three Regions

**During Execution:**
```
Array: [0, 0, 0, 0 | 1, 1, 1, 1 | 0, 2 | 2, 2, 2]
        ↑_________↑  ↑_________↑  ↑___↑  ↑_______↑
          0s           1s        unsorted   2s
        [0,low-1]  [low,mid-1]  [mid,high] [high+1,n-1]
                      ↑           ↑     ↑
                     low        mid  high
```

**Key Points:**
- **Left sorted:** All 0s in correct position
- **Middle sorted:** All 1s in correct position  
- **Right sorted:** All 2s in correct position
- **Middle unsorted:** Working region that shrinks

---

## Complete Java Code

```java
public class Solution {
    public static void sortArray(int[] arr) {
        int n = arr.length;
        int low = 0;
        int mid = 0;
        int high = n - 1;
        
        // Process until unsorted region is empty
        while(mid <= high) {
            if(arr[mid] == 0) {
                // Swap with low, move both pointers
                int temp = arr[low];
                arr[low] = arr[mid];
                arr[mid] = temp;
                low++;
                mid++;
            }
            else if(arr[mid] == 1) {
                // Already in correct position
                mid++;
            }
            else {  // arr[mid] == 2
                // Swap with high, move only high
                int temp = arr[mid];
                arr[mid] = arr[high];
                arr[high] = temp;
                high--;
            }
        }
    }
    
    public static void main(String[] args) {
        int[] arr = {0, 1, 2, 0, 1, 2, 1, 2, 0, 0, 0, 1};
        
        System.out.println("Before: " + Arrays.toString(arr));
        sortArray(arr);
        System.out.println("After:  " + Arrays.toString(arr));
    }
}
```

**Output:**
```
Before: [0, 1, 2, 0, 1, 2, 1, 2, 0, 0, 0, 1]
After:  [0, 0, 0, 0, 0, 1, 1, 1, 1, 2, 2, 2]
```

---

## Time Complexity Analysis

### Understanding the Single Pass

**Key Observation:** Each iteration processes exactly **one element**.

**Three cases and their impact:**

1. **arr[mid] == 0:**
   - Move mid forward → One element processed ✓
   
2. **arr[mid] == 1:**
   - Move mid forward → One element processed ✓
   
3. **arr[mid] == 2:**
   - Move high backward → One element processed ✓
   - mid stays, but next iteration will process swapped element

**Proof that it's O(N):**

```
Initially:
- Unsorted region = [0 to n-1]
- Size = n

Each iteration:
- Either mid moves right (shrinks from left)
- Or high moves left (shrinks from right)
- Size of unsorted region decreases by 1

After n iterations:
- Unsorted region size = 0
- Algorithm terminates
```

**Visual Timeline:**
```
Iteration 1:  [unsorted region size = 12] → process 1 element
Iteration 2:  [unsorted region size = 11] → process 1 element
Iteration 3:  [unsorted region size = 10] → process 1 element
...
Iteration 12: [unsorted region size = 1]  → process 1 element
Done!         [unsorted region size = 0]
```

**Time Complexity:** **O(N)**
- Single pass through array
- Each element processed exactly once

**Space Complexity:** **O(1)**
- Only 3 pointer variables used
- In-place sorting

---

## Why This Algorithm is Genius

### Comparison with Better Solution

**Better Solution (Counting):**
```
Pass 1: Count all elements         → O(N)
Pass 2: Overwrite array            → O(N)
Total: O(2N)
```

**Dutch National Flag:**
```
Single Pass: Sort while traversing → O(N)
Total: O(N)
```

**Improvement:** 2x faster in terms of passes!

### The Beauty of Three Pointers

**Single Traversal Achieves:**
1. ✅ Identify 0s and move to left
2. ✅ Identify 1s and keep in middle
3. ✅ Identify 2s and move to right
4. ✅ Maintain sorted regions throughout

All in **one pass**!

---

## Common Mistakes to Avoid

❌ **Moving mid when arr[mid] == 2**
```java
// WRONG!
if(arr[mid] == 2) {
    swap(arr[mid], arr[high]);
    high--;
    mid++;  // ← WRONG! Don't move mid
}
```
**Why wrong?** Swapped element from high is unknown, must check it!

❌ **Not moving low when arr[mid] == 0**
```java
// WRONG!
if(arr[mid] == 0) {
    swap(arr[low], arr[mid]);
    mid++;  // ← Missing low++
}
```
**Why wrong?** 0s region didn't expand, violates Rule 1!

❌ **Using mid < high instead of mid <= high**
```java
// WRONG!
while(mid < high) {  // ← Should be mid <= high
```
**Why wrong?** When mid == high, still one element to process!

✅ **Correct Template:**
```java
while(mid <= high) {
    if(arr[mid] == 0) {
        swap(arr[low], arr[mid]);
        low++;
        mid++;
    }
    else if(arr[mid] == 1) {
        mid++;
    }
    else {
        swap(arr[mid], arr[high]);
        high--;
        // NO mid++ here!
    }
}
```

---

## The Three Rules - Deep Dive

### Why These Specific Rules?

**Rule 1: [0 to low-1] → All 0s**
- 0 is smallest, belongs at start
- Sorted array: [0, 0, 0, ...]
- Region grows as we find more 0s

**Rule 2: [low to mid-1] → All 1s**
- 1 is middle value, belongs in middle
- After 0s, before 2s
- Region grows as we find more 1s

**Rule 3: [high+1 to n-1] → All 2s**
- 2 is largest, belongs at end
- Sorted array: [..., 2, 2, 2]
- Region grows as we find more 2s

**Unsorted: [mid to high]**
- Everything we haven't processed
- Goal: Make this empty by moving mid and high

### Invariant Maintenance

**Loop Invariant:** At the start of each iteration:
```
✓ All elements in [0, low-1] are 0
✓ All elements in [low, mid-1] are 1
✓ All elements in [high+1, n-1] are 2
? Elements in [mid, high] are unknown
```

Each operation **maintains** this invariant!

---

## Interview Strategy

### When Asked This Problem

1. **Start with Brute Force**
   - "I can use Arrays.sort() - O(N log N)"
   - Shows you know standard approaches

2. **Move to Better**
   - "I can count and overwrite - O(2N)"
   - Shows optimization thinking

3. **Present Optimal**
   - "Dutch National Flag Algorithm - O(N)"
   - Explain the three regions concept
   - Draw the diagram!

4. **Code Cleanly**
   - Use meaningful variable names
   - Add comments for three cases
   - Handle edge cases

5. **Dry Run**
   - Walk through small example
   - Show pointer movements
   - Verify sorted regions

---

## Edge Cases

### All Same Elements

```java
Input:  [0, 0, 0, 0, 0]
Output: [0, 0, 0, 0, 0]

Input:  [1, 1, 1, 1, 1]
Output: [1, 1, 1, 1, 1]

Input:  [2, 2, 2, 2, 2]
Output: [2, 2, 2, 2, 2]
```
Algorithm handles perfectly - all stay in place!

### Already Sorted

```java
Input:  [0, 0, 1, 1, 2, 2]
Output: [0, 0, 1, 1, 2, 2]
```
Algorithm recognizes and maintains order!

### Reverse Sorted

```java
Input:  [2, 2, 1, 1, 0, 0]
Output: [0, 0, 1, 1, 2, 2]
```
Algorithm handles efficiently!

### Two Elements

```java
Input:  [1, 0]
Output: [0, 1]

Input:  [2, 0]
Output: [0, 2]
```

### Single Element

```java
Input:  [0]
Output: [0]
```

All edge cases work because algorithm maintains invariants!

---

## Key Takeaways

1. **Three Pointers Strategy**
   - low: boundary of 0s
   - mid: current element being processed
   - high: boundary of 2s

2. **Three Regions Concept**
   - Sorted left (0s)
   - Sorted middle (1s)
   - Sorted right (2s)
   - Unsorted middle (shrinking)

3. **Critical Movement Rules**
   - 0 found: Move low AND mid
   - 1 found: Move only mid
   - 2 found: Move only high (NOT mid!)

4. **Single Pass Efficiency**
   - O(2N) → O(N)
   - Each element processed once

5. **Loop Condition**
   - `while(mid <= high)` not `mid < high`
   - Process until unsorted region is empty

---

## Practice Tips

1. **Draw the diagram** - visualize three regions
2. **Trace pointer movements** - understand why each moves
3. **Dry run examples** - build intuition
4. **Explain to interviewer** - show thought process
5. **Practice edge cases** - build confidence

This algorithm is **beautiful in its simplicity** and **powerful in its efficiency**!