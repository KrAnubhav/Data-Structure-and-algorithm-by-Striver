# Find the Repeating and Missing Number

## Problem Statement

Given an array of **N integers** where all numbers are in the range **[1, N]**, find:
1. The **repeating number** (appears twice)
2. The **missing number** (doesn't appear)

### Example

**Input:** [4, 3, 6, 2, 1, 1]  
**Size:** N = 6  
**Range:** Numbers should be from 1 to 6

**Analysis:**
```
Expected: [1, 2, 3, 4, 5, 6]
Actual:   [4, 3, 6, 2, 1, 1]

4 appears: ✓ once
3 appears: ✓ once
6 appears: ✓ once
2 appears: ✓ once
1 appears: ✗ TWICE (repeating!)
5 appears: ✗ NOT AT ALL (missing!)
```

**Output:** [1, 5]  
(Repeating = 1, Missing = 5)

---

## Approach 1: Brute Force

### Idea

For each number from 1 to N:
- Count how many times it appears in array
- If appears **2 times** → Repeating
- If appears **0 times** → Missing

### Algorithm

```
For i = 1 to N:
    Count occurrences of i in array
    If count == 2: i is repeating
    If count == 0: i is missing
```

### Code

```java
public class Solution {
    public int[] findRepeatingAndMissingBruteForce(int[] arr) {
        int n = arr.length;
        int repeating = -1;
        int missing = -1;
        
        // Check each number from 1 to n
        for(int i = 1; i <= n; i++) {
            int count = 0;
            
            // Count occurrences
            for(int j = 0; j < n; j++) {
                if(arr[j] == i) {
                    count++;
                }
            }
            
            // Check if repeating or missing
            if(count == 2) {
                repeating = i;
            } else if(count == 0) {
                missing = i;
            }
            
            // Early exit if both found
            if(repeating != -1 && missing != -1) {
                break;
            }
        }
        
        return new int[]{repeating, missing};
    }
}
```

### Complexity Analysis

**Time Complexity:** O(N²)
- Outer loop: O(N)
- Inner loop: O(N)
- Total: O(N × N)

**Space Complexity:** O(1)
- No extra space used

**Verdict:** ❌ Too slow - interviewer asks to optimize

---

## Approach 2: Better (Hashing)

### Idea

Use a **hash array** to count occurrences of each number.

### Key Observation

Numbers are in range **[1, N]**, so we can use array indexing.

### Algorithm

**Step 1:** Create hash array of size N+1

**Step 2:** Count occurrences
```
For each element in array:
    hash[element]++
```

**Step 3:** Find repeating and missing
```
For i = 1 to N:
    If hash[i] == 2: i is repeating
    If hash[i] == 0: i is missing
```

### Detailed Walkthrough

**Array:** [4, 3, 6, 2, 1, 1], N = 6

**Step 1: Initialize hash array**
```
Index: 0  1  2  3  4  5  6
Hash: [0, 0, 0, 0, 0, 0, 0]
```

**Step 2: Process each element**

**Process arr[0] = 4:**
```
hash[4]++
Hash: [0, 0, 0, 0, 1, 0, 0]
```

**Process arr[1] = 3:**
```
hash[3]++
Hash: [0, 0, 0, 1, 1, 0, 0]
```

**Process arr[2] = 6:**
```
hash[6]++
Hash: [0, 0, 0, 1, 1, 0, 1]
```

**Process arr[3] = 2:**
```
hash[2]++
Hash: [0, 0, 1, 1, 1, 0, 1]
```

**Process arr[4] = 1:**
```
hash[1]++
Hash: [0, 1, 1, 1, 1, 0, 1]
```

**Process arr[5] = 1:**
```
hash[1]++
Hash: [0, 2, 1, 1, 1, 0, 1]
       ↑
     Count = 2!
```

**Step 3: Find answers**
```
Check i=1: hash[1]=2 → Repeating = 1
Check i=2: hash[2]=1 → Normal
Check i=3: hash[3]=1 → Normal
Check i=4: hash[4]=1 → Normal
Check i=5: hash[5]=0 → Missing = 5
Check i=6: hash[6]=1 → Normal
```

**Answer:** [1, 5]

### Code

```java
public class Solution {
    public int[] findRepeatingAndMissingBetter(int[] arr) {
        int n = arr.length;
        
        // Step 1: Create hash array
        int[] hash = new int[n + 1];
        
        // Step 2: Count occurrences
        for(int i = 0; i < n; i++) {
            hash[arr[i]]++;
        }
        
        // Step 3: Find repeating and missing
        int repeating = -1;
        int missing = -1;
        
        for(int i = 1; i <= n; i++) {
            if(hash[i] == 2) {
                repeating = i;
            } else if(hash[i] == 0) {
                missing = i;
            }
            
            // Early exit
            if(repeating != -1 && missing != -1) {
                break;
            }
        }
        
        return new int[]{repeating, missing};
    }
}
```

### Complexity Analysis

**Time Complexity:** O(N) + O(N) = **O(2N)** ≈ **O(N)**
- Counting: O(N)
- Finding: O(N)

**Space Complexity:** O(N)
- Hash array of size N+1

**Verdict:** ✅ Better time, but interviewer asks to optimize space

---

## Approach 3: Optimal 1 (Mathematics)

### Key Idea

Use **mathematical equations** with two unknowns:
- Let **X = repeating number**
- Let **Y = missing number**

Create two equations to solve for X and Y.

### Equation 1: Sum

**Sum of array elements:**
```
S = arr[0] + arr[1] + ... + arr[n-1]
```

**Sum of first N natural numbers:**
```
Sn = 1 + 2 + 3 + ... + N = N × (N + 1) / 2
```

**Subtract:**
```
S - Sn = ?
```

### Deriving Equation 1

**Array:** [4, 3, 6, 2, 1, 1]

**S (sum of array):**
```
S = 4 + 3 + 6 + 2 + 1 + 1 = 17
```

**Sn (sum 1 to 6):**
```
Sn = 1 + 2 + 3 + 4 + 5 + 6 = 21
```

**S - Sn:**
```
S - Sn = (4 + 3 + 6 + 2 + 1 + 1) - (1 + 2 + 3 + 4 + 5 + 6)
       = 4 + 3 + 6 + 2 + 1 + 1 - 1 - 2 - 3 - 4 - 5 - 6

Cancellations:
4 - 4 = 0
3 - 3 = 0
6 - 6 = 0
2 - 2 = 0
1 - 1 = 0 (one cancels)

Remaining:
1 - 5 = -4
```

**Observation:**
```
S - Sn = 1 - 5 = -4

But 1 = X (repeating)
And 5 = Y (missing)

Therefore: X - Y = -4  ... Equation 1
```

### Equation 2: Sum of Squares

**Sum of squares of array:**
```
S2 = arr[0]² + arr[1]² + ... + arr[n-1]²
```

**Sum of squares of first N natural numbers:**
```
S2n = 1² + 2² + ... + N² = N × (N + 1) × (2N + 1) / 6
```

**Subtract:**
```
S2 - S2n = ?
```

### Deriving Equation 2

**S2 (sum of squares):**
```
S2 = 4² + 3² + 6² + 2² + 1² + 1²
   = 16 + 9 + 36 + 4 + 1 + 1
   = 67
```

**S2n (sum of squares 1 to 6):**
```
S2n = 1² + 2² + 3² + 4² + 5² + 6²
    = 1 + 4 + 9 + 16 + 25 + 36
    = 91
```

**S2 - S2n:**
```
S2 - S2n = 67 - 91 = -24

After cancellations:
= 1² - 5² = 1 - 25 = -24

= X² - Y²
= (X - Y)(X + Y) = -24  ... Equation 2
```

### Solving the Equations

**We have:**
```
Equation 1: X - Y = -4
Equation 2: (X - Y)(X + Y) = -24
```

**From Equation 2:**
```
(X - Y)(X + Y) = -24
(-4)(X + Y) = -24
X + Y = -24 / -4
X + Y = 6
```

**Now solve:**
```
X - Y = -4  ... (1)
X + Y = 6   ... (2)

Add equations:
2X = 2
X = 1  ✓ (Repeating)

Substitute in (2):
1 + Y = 6
Y = 5  ✓ (Missing)
```

### Code

```java
public class Solution {
    public int[] findRepeatingAndMissingOptimal1(int[] arr) {
        int n = arr.length;
        
        // Calculate Sn and S2n
        long Sn = (long)n * (n + 1) / 2;
        long S2n = (long)n * (n + 1) * (2 * n + 1) / 6;
        
        // Calculate S and S2
        long S = 0;
        long S2 = 0;
        
        for(int i = 0; i < n; i++) {
            S += arr[i];
            S2 += (long)arr[i] * arr[i];
        }
        
        // Equation 1: X - Y = val1
        long val1 = S - Sn;
        
        // Equation 2: X² - Y² = val2
        // (X - Y)(X + Y) = val2
        // X + Y = val2 / val1
        long val2 = S2 - S2n;
        val2 = val2 / val1;  // Now val2 = X + Y
        
        // Solve for X and Y
        long X = (val1 + val2) / 2;  // Repeating
        long Y = X - val1;            // Missing
        
        return new int[]{(int)X, (int)Y};
    }
}
```

### Why Use Long?

```java
// For large N, values can overflow int
S2 = arr[0]² + arr[1]² + ... 
// Squares can be very large!

// Use long to prevent overflow
long S2 = (long)arr[i] * arr[i];
```

### Complexity Analysis

**Time Complexity:** O(N)
- Single loop through array

**Space Complexity:** O(1)
- Only using variables

---

## Approach 4: Optimal 2 (XOR Method)

### Prerequisites

Understanding of **bitwise XOR**:
```
Same XOR Same = 0
1 XOR 1 = 0
2 XOR 2 = 0

Even occurrences cancel out:
1 XOR 1 XOR 1 = 1 (odd count)
2 XOR 2 = 0 (even count)
```

### Key Insight

**XOR all array elements with 1 to N:**
```
XOR = arr[0] XOR arr[1] XOR ... XOR 1 XOR 2 XOR ... XOR N
```

**What remains?**
- All numbers appearing **even times** cancel out
- Only **X** (repeating, appears 3 times) and **Y** (missing, appears 1 time) remain

**Result:**
```
XOR = X XOR Y
```

### Why X and Y Differ in At Least One Bit

**Since X ≠ Y, their binary representations differ:**
```
X = 1 = 001 (binary)
Y = 5 = 101 (binary)
        ↑
    Bit 2 differs!

X XOR Y = 001 XOR 101 = 100 = 4
```

**Find differentiating bit:**
- XOR result has 1s where X and Y differ
- Find any set bit (we'll use **rightmost set bit**)

### Algorithm Steps

**Step 1:** XOR all elements
```
XOR = (array elements) XOR (1 to N)
Result: XOR = X XOR Y
```

**Step 2:** Find differentiating bit
```
Find any bit where XOR has 1
(This bit differs between X and Y)
```

**Step 3:** Partition into two groups
```
Group 0: Numbers with bit = 0
Group 1: Numbers with bit = 1

All pairs cancel in each group
Only X remains in one group
Only Y remains in other group
```

**Step 4:** Identify which is X and which is Y
```
Count occurrences in original array
If count == 2: It's X (repeating)
Else: It's Y (missing)
```

### Detailed Walkthrough

**Array:** [4, 3, 6, 2, 1, 1], N = 6

**Step 1: Calculate XOR**

```
XOR all array elements:
4 XOR 3 XOR 6 XOR 2 XOR 1 XOR 1

XOR all 1 to N:
1 XOR 2 XOR 3 XOR 4 XOR 5 XOR 6

Combined:
1 appears 3 times (array: 2 times, range: 1 time) → 1 remains
2 appears 2 times → cancels
3 appears 2 times → cancels
4 appears 2 times → cancels
5 appears 1 time (only in range) → 5 remains
6 appears 2 times → cancels

Result: XOR = 1 XOR 5 = 001 XOR 101 = 100 (binary) = 4
```

**Step 2: Find bit number**

```
XOR = 4 = 100 (binary)

Bit positions:
Position 0: 0
Position 1: 0
Position 2: 1  ✓ (first set bit)

Differentiating bit = 2
```

**How to find bit:**
```java
int bitNumber = 0;
while((XOR & (1 << bitNumber)) == 0) {
    bitNumber++;
}
// bitNumber = 2
```

**Step 3: Partition numbers**

**Check bit 2 for each number:**

```
Array elements:
4 = 100 → bit 2 = 1 → Group 1
3 = 011 → bit 2 = 0 → Group 0
6 = 110 → bit 2 = 1 → Group 1
2 = 010 → bit 2 = 0 → Group 0
1 = 001 → bit 2 = 0 → Group 0
1 = 001 → bit 2 = 0 → Group 0

Range 1 to N:
1 = 001 → bit 2 = 0 → Group 0
2 = 010 → bit 2 = 0 → Group 0
3 = 011 → bit 2 = 0 → Group 0
4 = 100 → bit 2 = 1 → Group 1
5 = 101 → bit 2 = 1 → Group 1
6 = 110 → bit 2 = 1 → Group 1
```

**Group 0 (bit 2 = 0):**
```
From array: 3, 2, 1, 1
From range: 1, 2, 3

XOR:
3 XOR 3 = 0
2 XOR 2 = 0
1 XOR 1 = 0
Remaining: 1

zero = 1
```

**Group 1 (bit 2 = 1):**
```
From array: 4, 6
From range: 4, 5, 6

XOR:
4 XOR 4 = 0
6 XOR 6 = 0
Remaining: 5

one = 5
```

**Step 4: Identify X and Y**

```
We have: zero = 1, one = 5

Count occurrences of 1 in array:
Appears 2 times → Repeating!

Therefore:
X (repeating) = 1
Y (missing) = 5
```

### Complete Code

```java
public class Solution {
    public int[] findRepeatingAndMissingOptimal2(int[] arr) {
        int n = arr.length;
        int XOR = 0;
        
        // Step 1: XOR all elements
        // XOR array elements
        for(int i = 0; i < n; i++) {
            XOR ^= arr[i];
        }
        
        // XOR with 1 to n
        for(int i = 1; i <= n; i++) {
            XOR ^= i;
        }
        
        // Step 2: Find differentiating bit (rightmost set bit)
        int bitNumber = 0;
        while((XOR & (1 << bitNumber)) == 0) {
            bitNumber++;
        }
        
        // Alternative: Find rightmost set bit directly
        // int number = XOR & ~(XOR - 1);
        
        // Step 3: Partition into two groups
        int zero = 0;  // Group with bit = 0
        int one = 0;   // Group with bit = 1
        
        // Partition array elements
        for(int i = 0; i < n; i++) {
            if((arr[i] & (1 << bitNumber)) != 0) {
                one ^= arr[i];  // Bit is set
            } else {
                zero ^= arr[i];  // Bit is not set
            }
        }
        
        // Partition 1 to n
        for(int i = 1; i <= n; i++) {
            if((i & (1 << bitNumber)) != 0) {
                one ^= i;
            } else {
                zero ^= i;
            }
        }
        
        // Step 4: Identify which is repeating
        int count = 0;
        for(int i = 0; i < n; i++) {
            if(arr[i] == zero) {
                count++;
            }
        }
        
        if(count == 2) {
            return new int[]{zero, one};  // zero is repeating
        } else {
            return new int[]{one, zero};  // one is repeating
        }
    }
}
```

### Understanding Bit Operations

**Check if bit is set:**
```java
// Check if bit at position 'bitNumber' is set
if((num & (1 << bitNumber)) != 0) {
    // Bit is set
}

Example:
num = 5 = 101 (binary)
bitNumber = 2

1 << 2 = 100 (binary)
101 & 100 = 100 (non-zero) → Bit 2 is set!
```

**Find rightmost set bit:**
```java
// Method 1: Loop
int bitNumber = 0;
while((XOR & (1 << bitNumber)) == 0) {
    bitNumber++;
}

// Method 2: Direct formula
int number = XOR & ~(XOR - 1);

Example:
XOR = 4 = 100
XOR - 1 = 3 = 011
~(XOR - 1) = ~011 = 100
XOR & ~(XOR - 1) = 100 & 100 = 100
```

### Why This Works - Mathematical Proof

**Given:**
- X appears 3 times total (2 in array, 1 in range)
- Y appears 1 time total (0 in array, 1 in range)
- All others appear 2 times (even)

**After XOR:**
```
Even occurrences cancel → 0
Odd occurrences remain

Result = X XOR Y
```

**After partitioning:**
```
All even occurrences split evenly
X and Y go to different groups (differ in chosen bit)
Each group XORs to one of X or Y
```

### Complexity Analysis

**Time Complexity:** O(N)
- XOR array: O(N)
- XOR 1 to N: O(N)
- Find bit: O(log N) worst case (32 bits max)
- Partition: O(N)
- Count: O(N)
- **Total:** O(N)

**Space Complexity:** O(1)
- Only using variables

---

## Complete Solution Class

```java
import java.util.*;

public class FindRepeatingMissing {
    
    // Approach 1: Brute Force
    public int[] findBruteForce(int[] arr) {
        int n = arr.length;
        int repeating = -1, missing = -1;
        
        for(int i = 1; i <= n; i++) {
            int count = 0;
            for(int j = 0; j < n; j++) {
                if(arr[j] == i) count++;
            }
            if(count == 2) repeating = i;
            else if(count == 0) missing = i;
            
            if(repeating != -1 && missing != -1) break;
        }
        
        return new int[]{repeating, missing};
    }
    
    // Approach 2: Better (Hashing)
    public int[] findBetter(int[] arr) {
        int n = arr.length;
        int[] hash = new int[n + 1];
        
        for(int num : arr) hash[num]++;
        
        int repeating = -1, missing = -1;
        for(int i = 1; i <= n; i++) {
            if(hash[i] == 2) repeating = i;
            else if(hash[i] == 0) missing = i;
        }
        
        return new int[]{repeating, missing};
    }
    
    // Approach 3: Optimal (Math)
    public int[] findOptimal1(int[] arr) {
        int n = arr.length;
        long Sn = (long)n * (n + 1) / 2;
        long S2n = (long)n * (n + 1) * (2 * n + 1) / 6;
        
        long S = 0, S2 = 0;
        for(int num : arr) {
            S += num;
            S2 += (long)num * num;
        }
        
        long val1 = S - Sn;
        long val2 = (S2 - S2n) / val1;
        
        long X = (val1 + val2) / 2;
        long Y = X - val1;
        
        return new int[]{(int)X, (int)Y};
    }
    
    // Approach 4: Optimal (XOR)
    public int[] findOptimal2(int[] arr) {
        int n = arr.length;
        int XOR = 0;
        
        for(int num : arr) XOR ^= num;
        for(int i = 1; i <= n; i++) XOR ^= i;
        
        int bitNumber = 0;
        while((XOR & (1 << bitNumber)) == 0) bitNumber++;
        
        int zero = 0, one = 0;
        
        for(int num : arr) {
            if((num & (1 << bitNumber)) != 0) one ^= num;
            else zero ^= num;
        }
        
        for(int i = 1; i <= n; i++) {
            if((i & (1 << bitNumber)) != 0) one ^= i;
            else zero ^= i;
        }
        
        int count = 0;
        for(int num : arr) {
            if(num == zero) count++;
        }
        
        return count == 2 ? new int[]{zero, one} : new int[]{one, zero};
    }
    
    public static void main(String[] args) {
        FindRepeatingMissing solution = new FindRepeatingMissing();
        int[] arr = {4, 3, 6, 2, 1, 1};
        
        System.out.println("Array: " + Arrays.toString(arr));
        System.out.println("Result: " + Arrays.toString(solution.findOptimal2(arr)));
        // Output: [1, 5]
    }
}
```

---

## Approach Comparison

| Approach | Time | Space | Difficulty |
|----------|------|-------|------------|
| Brute Force | O(N²) | O(1) | Easy |
| Hashing | O(N) | O(N) | Easy |
| Math | O(N) | O(1) | Medium |
| XOR | O(N) | O(1) | Hard |

---

## Key Takeaways

1. **Brute Force:** Count each number 1 to N
2. **Hashing:** Track frequencies in array
3. **Math:** Use sum and sum of squares equations
4. **XOR:** Use bit manipulation and partitioning

Both optimal solutions acceptable in interviews - Math is easier to explain!

This problem tests **mathematical reasoning** and **bit manipulation skills**!