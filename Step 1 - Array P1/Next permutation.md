# Next Permutation - Lecture Notes

## Understanding Permutation

**Permutation:** All possible rearrangements of elements in an array.

### Example: Array [3, 1, 2]

Number of permutations = **3! = 6 ways**

**All possible permutations (in sorted/dictionary order):**
```
1. [1, 2, 3]
2. [1, 3, 2]
3. [2, 1, 3]
4. [2, 3, 1]
5. [3, 1, 2]  ← Given array
6. [3, 2, 1]
```

**Dictionary Order:** Permutations arranged in ascending order (like words in a dictionary)
- [1, 2, 3] < [1, 3, 2] (because at index 1: 2 < 3)
- [1, 3, 2] < [2, 1, 3] (because at index 0: 1 < 2)

---

## Problem Statement

Given an array of integers, find the **next permutation** in dictionary order.

### What is Next Permutation?

**Given:** [3, 1, 2]  
**Position:** 5th in sorted permutations  
**Next Permutation:** [3, 2, 1] (6th position)

### Edge Case

**Given:** [3, 2, 1] (last permutation)  
**Next:** [1, 2, 3] (wrap around to first)

**Rule:** If no next permutation exists, return the smallest permutation (sorted ascending).

---

## Approach 1: Brute Force

### Algorithm

1. **Generate all permutations** in sorted order
2. **Linear search** to find current permutation
3. **Return next** permutation (or first if at end)

### Example

**Given:** [3, 1, 2]

**Step 1 - Generate all:**
```
[1, 2, 3]
[1, 3, 2]
[2, 1, 3]
[2, 3, 1]
[3, 1, 2]  ← Found at index 4
[3, 2, 1]  ← Next permutation (answer)
```

**Step 2 - Linear search:** Find [3, 1, 2] at index 4  
**Step 3 - Return:** permutation[5] = [3, 2, 1]

### How to Generate All Permutations?

Use **recursion** - refer to Recursion Playlist:
- Lecture 12: Permutation generation method 1
- Lecture 13: Permutation generation method 2

### Complexity Analysis

**Time Complexity:** O(N! × N)
- Generate N! permutations
- Each permutation has length N
- For N=5: 5! = 120 permutations
- For N=15: 15! ≈ 10¹² permutations (extremely slow!)

**Space Complexity:** O(N!)
- Store all permutations

**Verdict:** ❌ Too slow, not acceptable

---

## Approach 2: Better (For C++ Users Only)

### Using STL

C++ provides built-in `next_permutation()` function:

```cpp
#include <algorithm>
vector<int> nextPermutation(vector<int>& arr) {
    next_permutation(arr.begin(), arr.end());
    return arr;
}
```

**Note:** This is an inbuilt function. Interviewer may ask: **"Can you implement this yourself?"**

---

## Approach 3: Optimal Solution

### Key Observations (Dictionary Analogy)

In English dictionary:
```
"raj"   < "racks" < "rbx"
```

**Why this order?**
- "raj" vs "racks": Match prefix "ra", then 'j' < 'c'
- "racks" vs "rbx": Match prefix "r", then 'a' < 'b'

**Key Insight:** Next element has **longer prefix match** with current element.

### Observation on Array: [2, 1, 5, 4, 3, 0, 0]

Let's try different prefix matches to find next permutation:

#### Attempt 1: Match All 7 Elements
```
Prefix: [2, 1, 5, 4, 3, 0, 0]
Remaining: []
Result: Same array ❌
```

#### Attempt 2: Match 6 Elements  
```
Prefix: [2, 1, 5, 4, 3, 0]
Remaining: [0]
Result: Can only arrange as [0] → Same ❌
```

#### Attempt 3: Match 5 Elements
```
Prefix: [2, 1, 5, 4, 3]
Remaining: [0, 0]
Result: Can arrange as [0, 0] → Same ❌
```

#### Attempt 4: Match 4 Elements
```
Prefix: [2, 1, 5, 4]
Remaining: [3, 0, 0]
Possible: [0, 0, 3] → Smaller ❌
Possible: [0, 3, 0] → Smaller ❌
Possible: [3, 0, 0] → Same ❌
```

**Why fails?** Need element > 4, but remaining has only 3, 0, 0

#### Attempt 5: Match 3 Elements
```
Prefix: [2, 1, 5]
Remaining: [4, 3, 0, 0]
Need: Element > 5
Available: 4, 3, 0, 0 (all ≤ 5) ❌
```

#### Attempt 6: Match 2 Elements
```
Prefix: [2, 1]
Remaining: [5, 4, 3, 0, 0]
Need: Element > 1
Available: 5, 4, 3 (all > 1) ✅

Which to choose?
- [2, 5, ...] → Far from [2, 1, ...]
- [2, 4, ...] → Closer
- [2, 3, ...] → Closest ✅

Choose: 3 (smallest element > 1)
```

**Success!** Match prefix [2], replace 1 with 3

---

### Three Key Observations

#### Observation 1: Find the Breakpoint (Longest Prefix Match)

**Visual Pattern:**
```
Index:  0  1  2  3  4  5  6
Array: [2, 1, 5, 4, 3, 0, 0]
             ↑___↑___↑___↑___↑
            Increasing sequence
```

**Graph View:**
```
5 →     ●
       /
4 →   ●
     /
3 → ●
   /
1  ●           
  /
0 ●               ● ● 
  +-----------------
  2 1 5 4 3 0 0
      ↑
   Breakpoint (dip)
```

**Breakpoint:** Index where `arr[i] < arr[i+1]` breaks (dip occurs)

**Algorithm:**
```java
int index = -1;
for(int i = n-2; i >= 0; i--) {
    if(arr[i] < arr[i+1]) {
        index = i;  // Breakpoint found
        break;
    }
}
```

**Edge Case:** If index = -1 (no breakpoint found)
- Array is in descending order: [5, 4, 3, 2, 1]
- This is the **last permutation**
- Next = **first permutation** (reverse array)

#### Observation 2: Find Smallest Element Greater Than Breakpoint

After breakpoint at index 1 (element = 1):
```
Remaining elements: [5, 4, 3, 0, 0]
Elements > 1: [5, 4, 3]
```

**Which to choose?**
- 5 → Too far from next
- 4 → Still far
- 3 → **Closest to 1** ✅

**Why smallest?** To stay as close as possible to current permutation.

**Algorithm:**
```java
// Start from end (array is increasing after breakpoint)
for(int i = n-1; i > index; i--) {
    if(arr[i] > arr[index]) {
        swap(arr[i], arr[index]);
        break;
    }
}
```

#### Observation 3: Arrange Remaining in Sorted Order

After swap:
```
[2, 3, 5, 4, 1, 0, 0]
     ↑  ___________
   Swapped  Remaining
```

**Goal:** Make remaining part as **small as possible**

Current remaining: [5, 4, 1, 0, 0]  
**Sorted:** [0, 0, 1, 4, 5]

**Key Insight:** After breakpoint, array was already **increasing**. After swap, it's still **increasing**!

**Proof:**
```
Before swap: [..., 1, 5, 4, 3, 0, 0]  (increasing after 1)
After swap:  [..., 3, 5, 4, 1, 0, 0]  (still increasing after 3)
```

To get smallest arrangement: **Reverse the increasing sequence**

**Algorithm:**
```java
reverse(arr, index+1, n-1);
```

---

### Complete Algorithm

**Input:** [2, 1, 5, 4, 3, 0, 0]

**Step 1:** Find breakpoint (longest prefix match)
```
i=5: arr[5]=0 < arr[6]=0? No
i=4: arr[4]=3 < arr[5]=0? No
i=3: arr[3]=4 < arr[4]=3? No
i=2: arr[2]=5 < arr[3]=4? No
i=1: arr[1]=1 < arr[2]=5? Yes ✓

Breakpoint index = 1
```

**Step 2:** Find smallest element > arr[index] from right
```
i=6: arr[6]=0 > arr[1]=1? No
i=5: arr[5]=0 > arr[1]=1? No
i=4: arr[4]=3 > arr[1]=1? Yes ✓

Swap arr[1] with arr[4]
[2, 3, 5, 4, 1, 0, 0]
```

**Step 3:** Reverse from index+1 to end
```
Reverse [5, 4, 1, 0, 0] → [0, 0, 1, 4, 5]

Final: [2, 3, 0, 0, 1, 4, 5]
```

---

### Dry Run Example

**Given:** [2, 1, 5, 4, 3, 0, 0]

#### Finding Breakpoint
```
Index:     0  1  2  3  4  5  6
Array:    [2, 1, 5, 4, 3, 0, 0]
Compare:     ↓  ↑
           1 < 5? Yes → index = 1
```

**Visual:**
```
  5 ●
     \
  4   ●
       \
  3     ●
         \___
  1 ●        ●● 0
 /
2●
```

**Breakpoint = index 1 (value = 1)**

#### Finding Swap Element
```
Start from end:
arr[6] = 0 > 1? No
arr[5] = 0 > 1? No
arr[4] = 3 > 1? Yes! → Swap

Before: [2, 1, 5, 4, 3, 0, 0]
               ↑        ↑
After:  [2, 3, 5, 4, 1, 0, 0]
```

#### Reversing Remaining
```
Remaining: [5, 4, 1, 0, 0] (index 2 to 6)

Reverse:   [0, 0, 1, 4, 5]

Final:     [2, 3, 0, 0, 1, 4, 5]
```

---

### Edge Case Example

**Given:** [3, 2, 1] (largest permutation)

**Step 1:** Find breakpoint
```
i=1: arr[1]=2 < arr[2]=1? No
i=0: arr[0]=3 < arr[1]=2? No

index = -1 (no breakpoint found)
```

**Step 2:** Since index = -1, reverse entire array
```
Reverse [3, 2, 1] → [1, 2, 3]

Answer: [1, 2, 3] (smallest permutation)
```

---

## Complete Java Code

```java
import java.util.*;

public class Solution {
    public static void nextPermutation(int[] arr) {
        int n = arr.length;
        int index = -1;
        
        // Step 1: Find breakpoint
        for(int i = n-2; i >= 0; i--) {
            if(arr[i] < arr[i+1]) {
                index = i;
                break;
            }
        }
        
        // Edge case: If no breakpoint, reverse entire array
        if(index == -1) {
            reverse(arr, 0, n-1);
            return;
        }
        
        // Step 2: Find smallest element greater than arr[index]
        for(int i = n-1; i > index; i--) {
            if(arr[i] > arr[index]) {
                // Swap
                int temp = arr[i];
                arr[i] = arr[index];
                arr[index] = temp;
                break;
            }
        }
        
        // Step 3: Reverse from index+1 to end
        reverse(arr, index+1, n-1);
    }
    
    // Helper function to reverse array from start to end
    public static void reverse(int[] arr, int start, int end) {
        while(start < end) {
            int temp = arr[start];
            arr[start] = arr[end];
            arr[end] = temp;
            start++;
            end--;
        }
    }
    
    public static void main(String[] args) {
        int[] arr = {2, 1, 5, 4, 3, 0, 0};
        System.out.println("Original: " + Arrays.toString(arr));
        
        nextPermutation(arr);
        System.out.println("Next Permutation: " + Arrays.toString(arr));
    }
}
```

---

## Complexity Analysis

### Time Complexity: O(3N) ≈ **O(N)**

**Breakdown:**
- **Step 1:** Finding breakpoint → O(N)
- **Step 2:** Finding swap element → O(N) worst case
- **Step 3:** Reversing → O(N)
- **Total:** O(N) + O(N) + O(N) = O(3N) ≈ O(N)

### Space Complexity: **O(1)**

- No extra space used
- Only modifying input array in-place
- Few variables for swapping

**Note:** If interviewer counts input array modification as space, it's still O(1) auxiliary space.

---

## Step-by-Step Visual Walkthrough

### Example: [2, 1, 5, 4, 3, 0, 0]

**Initial State:**
```
Index: 0  1  2  3  4  5  6
Array: 2  1  5  4  3  0  0
```

**Step 1: Find Breakpoint**
```
       2  1  5  4  3  0  0
             ↑
          Breakpoint at index 1
          (1 < 5, increasing sequence breaks)
```

**Step 2: Find Swap Element**
```
Start from right:
2  1  5  4  3  0  0
   ↑        ↑
 index=1   i=4
 (arr[4]=3 > arr[1]=1, smallest greater element)
 
After swap:
2  3  5  4  1  0  0
   ↑        ↑
```

**Step 3: Reverse After Breakpoint**
```
Before: 2  3  5  4  1  0  0
              ←_________→
              Reverse this part

After:  2  3  0  0  1  4  5
           ✓ ✓ ✓ ✓ ✓
           Smallest arrangement
```

---

## Why This Algorithm Works

### 1. Longest Prefix Match Ensures "Next"

By finding breakpoint, we ensure:
- Maximum prefix stays same
- Only change minimum necessary elements
- Result is immediately next in dictionary order

### 2. Smallest Greater Element Stays Close

Choosing smallest element > breakpoint:
- Doesn't jump too far ahead
- Maintains "next" property

### 3. Reversing Minimizes Remaining

After breakpoint, sequence is increasing:
```
[..., 1, 5, 4, 3, 0, 0]
         ↑____________↑
         Increasing
```

After swap, still increasing:
```
[..., 3, 5, 4, 1, 0, 0]
         ↑____________↑
         Still increasing
```

Reversing increasing sequence = smallest arrangement!

---

## Common Edge Cases

### Case 1: Last Permutation
```
Input:  [3, 2, 1]
Output: [1, 2, 3]

Explanation: No breakpoint found, reverse entire array
```

### Case 2: First Permutation
```
Input:  [1, 2, 3]
Output: [1, 3, 2]

Breakpoint: index 1 (2 < 3)
Swap: 2 with 3
Result: [1, 3, 2]
```

### Case 3: Two Elements
```
Input:  [1, 2]
Output: [2, 1]

Input:  [2, 1]
Output: [1, 2] (reverse entire array)
```

### Case 4: All Same Elements
```
Input:  [1, 1, 1]
Output: [1, 1, 1]

No breakpoint, reverse gives same array
```

---

## Key Takeaways

1. **Dictionary Order = Sorted Permutations**
   - Think like English dictionary
   - Longer prefix match = closer elements

2. **Three-Step Algorithm:**
   - Find breakpoint (longest prefix)
   - Swap with smallest greater element
   - Reverse remaining for smallest arrangement

3. **Time Optimization:**
   - O(N!) brute force → O(N) optimal
   - Massive improvement!

4. **Space Optimization:**
   - In-place modification
   - O(1) auxiliary space

5. **Edge Cases Matter:**
   - Handle last permutation specially
   - Check for no breakpoint (index = -1)

---

## Interview Tips

1. **Start with Brute Force:** Mention generating all permutations, but don't code it
2. **Explain Observations:** Use dictionary analogy to build intuition
3. **Draw Diagrams:** Visual breakpoint and increasing sequence
4. **Handle Edge Cases:** Last permutation is important
5. **Optimize Step-by-Step:** Show thought process from O(N!) to O(N)

---

## Common Mistakes to Avoid

❌ Starting with code before understanding pattern  
❌ Not handling last permutation edge case  
❌ Forgetting to reverse (trying to sort instead)  
❌ Not finding smallest greater element (picking any greater element)  
✅ Draw the array, find pattern visually  
✅ Test with [3, 2, 1] edge case  
✅ Remember: increasing sequence → just reverse  
✅ Iterate from right to find smallest greater element