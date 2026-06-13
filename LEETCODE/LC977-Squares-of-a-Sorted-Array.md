---
tags:
  - leetcode
  - two-pointers
  - arrays
  - sorting
  - easy
lc_number: 977
title: Squares of a Sorted Array
difficulty: Easy
pattern: Two Pointers
date_solved: 2025-06-13
attempts: 1
solved_without_hint: true
---

# LC 977 — Squares of a Sorted Array

🔗 [Problem Link](https://leetcode.com/problems/squares-of-a-sorted-array/)
🏷️ Difficulty: `Easy` | Pattern: `[[Two Pointers]]`

---

## 📋 Problem Summary

> Given an integer array `nums` sorted in non-decreasing order, return an array of the squares of each number, also sorted in non-decreasing order.

**Input:** `int[] nums` — sorted array (non-decreasing), may contain negative numbers
**Output:** `int[]` — squares of each element, sorted in non-decreasing order
**Constraint to note:** Input is already sorted but has **negatives** — squaring breaks the order. Must return a new sorted-squares array. Follow-up asks for O(n) solution.

---

## 🔍 Examples

```
Input:  nums = [-4, -1, 0, 3, 10]
Output: [0, 1, 9, 16, 100]
Why:    Squares → [16, 1, 0, 9, 100] → sorted → [0, 1, 9, 16, 100]
        Largest square is always at one of the two ends (most negative or most positive)
```

```
Input:  nums = [-7, -3, 2, 3, 11]
Output: [4, 9, 9, 49, 121]
Why:    Squares → [49, 9, 4, 9, 121] → sorted → [4, 9, 9, 49, 121]
```

```
Input:  nums = [-5, -4, -3, -2, -1]   ← Edge case: all negatives
Output: [1, 4, 9, 16, 25]
Why:    All squares are positive, right pointer has the biggest absolute value
        Two pointers fill result from right to left correctly
```

```
Input:  nums = [0, 1, 2, 3]           ← Edge case: all non-negatives
Output: [0, 1, 4, 9]
Why:    No negatives, left pointer squares are always smallest
        Left pointer fills result from right to left (right pointer always wins or ties)
```

```
Input:  nums = [0]                     ← Edge case: single element
Output: [0]
Why:    left == right, one comparison, placed at result[0]
```

---

## 💭 My Thought Process

### Brute Force Idea:
Square every element, then sort the result array using `Arrays.sort()`.
Works but costs **O(n log n)** — ignores the fact that input is already sorted.

### Optimization Insight:
The input is sorted, so the **largest squares** are always at the two ends — either the most negative (left) or the most positive (right). Compare absolute values at both pointers, place the larger square at the **end** of the result array, and move that pointer inward. Fill result from right to left.

### Final Approach:
Two pointers at both ends. Result array of same size filled from index `n-1` down to `0`. At each step, compare `|nums[left]|` vs `|nums[right]|`, square the larger, place it at `result[pos]`, move that pointer inward, decrement `pos`.

---

## ☕ Java Solution

### Optimal — Two Pointers, O(n) Time
```java
class Solution {
   public int[] sortedSquares(int[] nums) {
       // TC = o(n)
       // SC = O(1)
       int[] res = new int[nums.length];
       
       int i = 0,
           j = nums.length - 1;
       
       int k = nums.length - 1;
    
       while(i <= j){
               if(Math.abs(nums[i]) > Math.abs(nums[j])){
                     res[k] = nums[i] * nums[i];
                     i = i + 1
                } else {
                  res[k] = nums[j] * nums[j];
                  j = j - 1;
                }
                k = k - 1;
       }
        return res;
         }

}
```

### Brute Force — Square then Sort, O(n log n)
```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int[] result = new int[nums.length];

        for (int i = 0; i < nums.length; i++) {
            result[i] = nums[i] * nums[i];       // square each element
        }

        Arrays.sort(result);                     // sort the squares
        return result;
    }
}
```

> ✅ Brute force is acceptable for Easy problems but the interviewer will ask for O(n). Always mention it first, then optimize.

---

## ⏱️ Complexity

|                 | Complexity   | Reason                                                       |
| --------------- | ------------ | ------------------------------------------------------------ |
| Time (optimal)  | `O(n)`       | Single pass, each element touched once                       |
| Space (optimal) | `O(1)`       | Result array of size n (output doesn't count as extra space) |
| Time (brute)    | `O(n log n)` | Sorting dominates                                            |
| Space (brute)   | `O(n)`       | Result array                                                 |

> 📝 Note: The result array is **required output**, not extra auxiliary space. So both solutions are technically O(1) extra space beyond the output.

---

## ⚠️ Edge Cases Considered

- [x] All negative numbers `[-5,-4,-3,-2,-1]` → right pointer always wins, fills correctly
- [x] All non-negative numbers `[0,1,2,3]` → left pointer squares always smallest, right fills result
- [x] Mix of negatives and positives `[-4,-1,0,3,10]` → standard case
- [x] Single element `[0]` → left == right, one iteration, placed at result[0]
- [x] Two elements `[-1,1]` → equal squares, right branch taken, both placed correctly
- [x] Contains zero `[-3,0,1]` → zero square is 0, smallest, placed first
- [x] Duplicate values `[-3,-3,3,3]` → equal squares handled by `else` branch

---

## 🪤 Mistakes I Made / Traps

1. **Using `left < right` instead of `left <= right`** — the critical difference here vs LC 125/344. When `left == right` there is still one element left to process. Using strict `<` skips the middle element in odd-length arrays.
2. **Filling result from left to right** — natural instinct is to fill index 0 first, but we know the largest square, not the smallest. Fill from the **right end** (`pos = n-1`) downward.
3. **Integer overflow on large inputs** — `nums[i]` can be up to 10,000, so `nums[i] * nums[i]` = 100,000,000 which fits in `int` (max ~2.1 billion). Safe here, but always check constraints.
4. **Mutating the input array** — don't square `nums[i]` in-place. Always work on a separate `result[]` array, otherwise you lose the original values needed for comparison.
5. **Forgetting `Arrays.sort()` import** — for brute force, needs `import java.util.Arrays` or use the full path.

---

## 🔁 Alternative Approaches

| Approach | Time | Space | Why not chosen |
|----------|------|-------|----------------|
| Square + Arrays.sort() | `O(n log n)` | `O(n)` | Ignores sorted property, suboptimal |
| Two pointers fill from end (chosen) | `O(n)` | `O(n)` | ✅ Optimal, uses sorted input property |
| Find split point + merge | `O(n)` | `O(n)` | Works but more complex code — find index where negatives end, merge two sorted halves |

---

## 💡 The Key Insight (don't skip this)

```
Original:  [ -4,  -1,   0,   3,  10 ]
Squares:   [ 16,   1,   0,   9, 100 ]
             ^                    ^
           left               right

Largest square MUST be at one of the two ends.
Why? Because the array is sorted → ends have largest absolute values → largest squares.

Fill result[] from RIGHT to LEFT placing the larger square each time.
```

---

## 🔗 Similar Problems

- [[LC 344 — Reverse String]] — two pointers, fill from ends
- [[LC 125 — Valid Palindrome]] — two pointers, skip and compare from ends
- [[LC 88 — Merge Sorted Array]] — fill from the end trick (same core idea)
- [[LC 167 — Two Sum II]] — two pointers on sorted array
- [[LC 189 — Rotate Array]] — in-place array manipulation

---

## 📝 Key Takeaway (1 line)
> Largest squares live at the ends — use two pointers filling the result from right to left, and use `left <= right` (not strict) to catch every element.

---

## 🔁 Revision Log

| Date | Attempt | Solved Without Hint | Notes |
|------|---------|---------------------|-------|
| 2025-06-13 | 1st | ❌ No | Forgot `<=` in while condition, missed middle element on odd-length input |
