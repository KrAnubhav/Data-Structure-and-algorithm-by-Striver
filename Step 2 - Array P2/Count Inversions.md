# Count Inversions

## Problem Statement

Given an array of integers, count the number of **inversions**.

**Inversion:** A pair (i, j) where:
- i < j (left element comes before right)
- arr[i] > arr[j] (left element is greater than right)

### Example

**Input:** [5, 3, 2, 4, 1]

**All inversions:**
```
(5, 3): 5 > 3 ✓
(5, 2): 5 > 2 ✓
(5, 4): 5 > 4 ✓
(5, 1): 5 > 1 ✓
(3, 2): 3 > 2 ✓
(3, 1): 3 > 1 ✓
(2, 1): 2 > 1 ✓
(4, 1): 4 > 1 ✓
```

**Output:** 8 inversions

**Important:** Return the **count**, not the pairs themselves.

---

## Approach 1: Brute Force

### Idea

For each element, check all elements to its right and count inversions.

### Algorithm

```
count = 0
For i = 0 to n-1:
    For j = i+1 to n-1:
        If arr[i] > arr[j]:
            count++
Return count
```

### Visual Example

**Array:** [5, 3, 2, 4, 1]

```
i=0 (5): Check [3, 2, 4, 1]
  5 > 3 ✓ count++
  5 > 2 ✓ count++
  5 > 4 ✓ count++
  5 > 1 ✓ count++
  Subtotal: 4

i=1 (3): Check [2, 4, 1]
  3 > 2 ✓ count++
  3 > 4 ✗
  3 > 1 ✓ count++
  Subtotal: 2

i=2 (2): Check [4, 1]
  2 > 4 ✗
  2 > 1 ✓ count++
  Subtotal: 1

i=3 (4): Check [1]
  4 > 1 ✓ count++
  Subtotal: 1

Total: 4 + 2 + 1 + 1 = 8
```

### Code

```java
public class Solution {
    public int countInversionsBruteForce(int[] arr) {
        int n = arr.length;
        int count = 0;
        
        // Check all pairs
        for(int i = 0; i < n; i++) {
            for(int j = i + 1; j < n; j++) {
                if(arr[i] > arr[j]) {
                    count++;
                }
            }
        }
        
        return count;
    }
}
```

### Complexity Analysis

**Time Complexity:** O(N²)
- Nested loops through array

**Space Complexity:** O(1)
- No extra space

**Verdict:** ❌ Too slow - interviewer asks to optimize

---

## Key Insight: Sorted Arrays

### Helper Problem

**Given two sorted arrays, count inversions where:**
- Left element from left array
- Right element from right array
- Left > Right

**Example:**
```
Left:  [3, 5, 6]
Right: [2, 2, 4, 4]
```

**Brute Force:** Check all pairs = O(N × M)

**Optimized:** Use two pointers!

### Two Pointer Approach

**Initial:**
```
Left:  [3, 5, 6]
        ↑
Right: [2, 2, 4, 4]
        ↑
```

**Step 1:**
```
Compare 3 and 2
3 > 2? Yes!

Key Insight:
If 3 > 2, then ALL elements after 3 are also > 2
(because left array is sorted)

Elements after 3: [5, 6] (2 elements)
Plus 3 itself = 3 elements total

Add 3 to count
Move right pointer
```

**Step 2:**
```
Left:  [3, 5, 6]
        ↑
Right: [2, 2, 4, 4]
           ↑

Compare 3 and 2
3 > 2? Yes!

Again, 3 elements can pair with this 2
Add 3 to count
Move right pointer
```

**Step 3:**
```
Left:  [3, 5, 6]
        ↑
Right: [2, 2, 4, 4]
              ↑

Compare 3 and 4
3 > 4? No

Move left pointer
```

**Step 4:**
```
Left:  [3, 5, 6]
           ↑
Right: [2, 2, 4, 4]
              ↑

Compare 5 and 4
5 > 4? Yes!

Elements from 5 onward: [5, 6] (2 elements)
Add 2 to count
Move right pointer
```

**Continue until all processed...**

**Final count:** 6 + 2 = 8 or similar

**Key Formula:**
```
When arr[left] > arr[right]:
    count += (mid - left + 1)
    // All elements from left to mid can pair
```

**Time Complexity:** O(N + M) - Single pass!

---

## Connection to Merge Sort

### Why Merge Sort?

**Merge Sort properties:**
1. Divides array into left and right halves
2. **Both halves become sorted** after recursion
3. **Merges two sorted arrays**

**Perfect for our helper problem!**

### Merge Sort Visualization

**Array:** [5, 3, 2, 4, 1]

**Division Phase:**
```
             [5, 3, 2, 4, 1]
            /               \
      [5, 3, 2]            [4, 1]
      /       \            /     \
  [5, 3]      [2]      [4]      [1]
   /   \
 [5]   [3]
```

**Merge Phase (bottom-up):**

**Level 1: Merge [5] and [3]**
```
Left:  [5] (sorted)
Right: [3] (sorted)

Compare 5 and 3:
5 > 3? Yes!
Elements after 5: 0
Plus 5 itself: 1 element
Count += 1

Result: [3, 5] with 1 inversion
```

**Level 2: Merge [3, 5] and [2]**
```
Left:  [3, 5] (sorted)
Right: [2] (sorted)

Compare 3 and 2:
3 > 2? Yes!
Elements from 3 to end: [3, 5] (2 elements)
Count += 2

Result: [2, 3, 5] with 2 more inversions
Total so far: 1 + 2 = 3
```

**Level 1 (right): Merge [4] and [1]**
```
Left:  [4]
Right: [1]

Compare 4 and 1:
4 > 1? Yes!
Count += 1

Result: [1, 4] with 1 inversion
Total so far: 3 + 1 = 4
```

**Level 3: Merge [2, 3, 5] and [1, 4]**
```
Left:  [2, 3, 5]
        ↑
Right: [1, 4]
        ↑

Step 1: Compare 2 and 1
2 > 1? Yes!
Elements from 2 to end: [2, 3, 5] (3 elements)
Count += 3

Step 2: Compare 2 and 4
2 > 4? No, move left

Step 3: Compare 3 and 4
3 > 4? No, move left

Step 4: Compare 5 and 4
5 > 4? Yes!
Elements from 5 to end: [5] (1 element)
Count += 1

Total new inversions: 3 + 1 = 4
Grand total: 4 + 4 = 8
```

---

## Approach 2: Optimal (Modified Merge Sort)

### Algorithm

**Modify merge sort to count inversions during merge step:**

```
mergeSort(arr, low, high):
    if low >= high:
        return 0
    
    mid = (low + high) / 2
    
    count = 0
    count += mergeSort(arr, low, mid)      // Left inversions
    count += mergeSort(arr, mid+1, high)   // Right inversions
    count += merge(arr, low, mid, high)    // Cross inversions
    
    return count
```

**Key insight:** Count inversions in merge step!

### Merge Function with Counting

```java
private static int merge(int[] arr, int low, int mid, int high) {
    List<Integer> temp = new ArrayList<>();
    int left = low;
    int right = mid + 1;
    int count = 0;
    
    while(left <= mid && right <= high) {
        if(arr[left] <= arr[right]) {
            temp.add(arr[left]);
            left++;
        }
        else {
            // arr[left] > arr[right]
            // All elements from left to mid can pair with arr[right]
            count += (mid - left + 1);
            temp.add(arr[right]);
            right++;
        }
    }
    
    // Copy remaining
    while(left <= mid) {
        temp.add(arr[left]);
        left++;
    }
    while(right <= high) {
        temp.add(arr[right]);
        right++;
    }
    
    // Copy back
    for(int i = low; i <= high; i++) {
        arr[i] = temp.get(i - low);
    }
    
    return count;
}
```

**Critical line:**
```java
count += (mid - left + 1);
```

**Why?**
- Left section: [low ... mid]
- If arr[right] < arr[left], then arr[right] < ALL elements from [left ... mid]
- Number of such elements: `mid - left + 1`

### Complete Code

```java
public class Solution {
    public int countInversions(int[] arr) {
        return mergeSort(arr, 0, arr.length - 1);
    }
    
    private int mergeSort(int[] arr, int low, int high) {
        int count = 0;
        
        if(low >= high) {
            return count;
        }
        
        int mid = low + (high - low) / 2;
        
        // Count inversions in left half
        count += mergeSort(arr, low, mid);
        
        // Count inversions in right half
        count += mergeSort(arr, mid + 1, high);
        
        // Count cross inversions during merge
        count += merge(arr, low, mid, high);
        
        return count;
    }
    
    private int merge(int[] arr, int low, int mid, int high) {
        List<Integer> temp = new ArrayList<>();
        int left = low;
        int right = mid + 1;
        int count = 0;
        
        while(left <= mid && right <= high) {
            if(arr[left] <= arr[right]) {
                temp.add(arr[left]);
                left++;
            }
            else {
                // Inversion found!
                // All elements from left to mid form inversions with arr[right]
                count += (mid - left + 1);
                temp.add(arr[right]);
                right++;
            }
        }
        
        // Add remaining elements
        while(left <= mid) {
            temp.add(arr[left]);
            left++;
        }
        
        while(right <= high) {
            temp.add(arr[right]);
            right++;
        }
        
        // Copy back to original array
        for(int i = low; i <= high; i++) {
            arr[i] = temp.get(i - low);
        }
        
        return count;
    }
    
    public static void main(String[] args) {
        Solution sol = new Solution();
        
        int[] arr = {5, 3, 2, 4, 1};
        System.out.println("Array: " + Arrays.toString(arr));
        
        int inversions = sol.countInversions(arr);
        System.out.println("Inversions: " + inversions);
        // Output: 8
    }
}
```

---

## Detailed Dry Run

**Array:** [5, 3, 2, 4, 1]

### Recursion Tree

```
                 mergeSort([5,3,2,4,1], 0, 4)
                /                              \
        mergeSort([5,3,2], 0, 2)          mergeSort([4,1], 3, 4)
        /              \                    /              \
mergeSort([5,3], 0, 1)  mergeSort([2], 2, 2)  mergeSort([4], 3, 3)  mergeSort([1], 4, 4)
    /           \
mergeSort([5], 0, 0)  mergeSort([3], 1, 1)
```

### Bottom-Up Execution

**Call 1: merge([5], [3])**
```
left=0, mid=0, right=1
arr[0]=5, arr[1]=3

Compare 5 and 3:
5 > 3? Yes!
count += (0 - 0 + 1) = 1

Result: [3, 5]
Return: 1
```

**Call 2: merge([3, 5], [2])**
```
left=0, mid=1, right=2
Left array: [3, 5]
Right array: [2]

Compare 3 and 2:
3 > 2? Yes!
count += (1 - 0 + 1) = 2

Result: [2, 3, 5]
Return: 2
```

**Subtotal from left side: 1 + 2 = 3**

**Call 3: merge([4], [1])**
```
left=3, mid=3, right=4
arr[3]=4, arr[4]=1

Compare 4 and 1:
4 > 1? Yes!
count += (3 - 3 + 1) = 1

Result: [1, 4]
Return: 1
```

**Call 4: merge([2, 3, 5], [1, 4])**
```
Left: [2, 3, 5] (indices 0-2)
Right: [1, 4] (indices 3-4)

Step 1: Compare 2 and 1
2 > 1? Yes!
count += (2 - 0 + 1) = 3
Take 1

Step 2: Compare 2 and 4
2 > 4? No
Take 2

Step 3: Compare 3 and 4
3 > 4? No
Take 3

Step 4: Compare 5 and 4
5 > 4? Yes!
count += (2 - 2 + 1) = 1
Take 4

Step 5: Take remaining 5

Result: [1, 2, 3, 4, 5]
Return: 4
```

**Final count: 3 + 1 + 4 = 8 ✓**

---

## Why (mid - left + 1)?

**Example:**
```
Left section: [3, 5, 6]
Indices:       0  1  2  (relative to left pointer)
               ↑
            left=0, mid=2

Right section: [2, ...]
                ↑
              right

When 3 > 2:
Elements from left to mid: [3, 5, 6]
Count = mid - left + 1 = 2 - 0 + 1 = 3 ✓
```

**Formula derivation:**
```
Elements from index left to mid:
arr[left], arr[left+1], ..., arr[mid]

Number of elements = mid - left + 1
```

---

## Important Notes

### Array Modification

**Warning:** This algorithm **modifies the input array** (sorts it)!

**Interview tip:**
```java
// Mention to interviewer:
"This approach sorts the array as a side effect.
If we need to preserve the original array,
I can create a copy first."

int[] copy = arr.clone();
int inversions = countInversions(copy);
// Original arr unchanged
```

### Global Variables

**Avoid in interviews:**
```java
// BAD - Don't use global variable
static int count = 0;

public int countInversions(int[] arr) {
    count = 0;
    mergeSort(arr, 0, arr.length - 1);
    return count;
}
```

**Good - Return and accumulate:**
```java
// GOOD - Return values
private int mergeSort(int[] arr, int low, int high) {
    if(low >= high) return 0;
    
    int count = 0;
    count += mergeSort(arr, low, mid);
    count += mergeSort(arr, mid + 1, high);
    count += merge(arr, low, mid, high);
    return count;
}
```

---

## Complexity Analysis

**Time Complexity:** O(N log N)
- Same as merge sort
- Merge step: O(N) per level
- Tree depth: log N levels
- Total: O(N log N)

**Space Complexity:** O(N)
- Temporary array for merging: O(N)
- Recursion stack: O(log N)
- Total: O(N)

---

## Approach Comparison

| Approach | Time | Space | Modifies Array? |
|----------|------|-------|-----------------|
| Brute Force | O(N²) | O(1) | ❌ No |
| Merge Sort | O(N log N) | O(N) | ✅ Yes |

**Improvement:** O(N²) → O(N log N) = Massive speedup!

---

## Key Takeaways

1. **Inversion:** Pair where left element > right element

2. **Brute Force:** Check all pairs - O(N²)

3. **Optimal:** Modified merge sort
   - Count during merge step
   - Use formula: `count += (mid - left + 1)`

4. **Why Merge Sort?**
   - Creates sorted subarrays
   - Merging two sorted arrays = our helper problem
   - Can count in O(N) during merge

5. **Critical Formula:**
   ```
   When arr[left] > arr[right]:
       All elements from left to mid form inversions
       count += (mid - left + 1)
   ```

6. **Interview Tips:**
   - Mention array modification
   - Avoid global variables
   - Explain merge sort connection clearly

---

## Common Mistakes

❌ **Mistake 1: Wrong count formula**
```java
// WRONG
count += 1;  // Only counts one inversion
```

✅ **Correct:**
```java
count += (mid - left + 1);  // Counts all inversions
```

❌ **Mistake 2: Using global variable**
```java
// WRONG - Avoid in interviews
static int count;
```

❌ **Mistake 3: Not mentioning array modification**
```java
// WRONG - Should inform interviewer
public int countInversions(int[] arr) {
    mergeSort(arr, ...);  // Modifies arr!
}
```

✅ **Correct:**
```java
// Mention to interviewer or create copy
int[] copy = arr.clone();
return mergeSort(copy, 0, copy.length - 1);
```

---

## Prerequisites

**Required Knowledge:**
1. Merge Sort algorithm
2. Recursion
3. Divide and conquer

**Recommendation:** Watch merge sort video before attempting this problem!

This problem tests **merge sort modification** and **counting during divide & conquer**!