---
tags: [pattern, dsa, arrays, strings]
pattern: "Two Pointers"
date: 2025-06-13
status: "Mastered"
problems_solved: 7
---

# 🔷 Pattern: Two Pointers

## 📌 What is this pattern?
> Use two index variables that traverse a linear data structure like array or string or LL simultaneously — either from opposite ends moving inward, or from the same end moving at different speeds — to solve problems in **O(n)** that would otherwise cost **O(n²)**.

---

## 🧠 Core Intuition

Brute force checks every pair → O(n²). Two pointers eliminates redundant pairs by exploiting **sorted order** or a **monotonic property**: if `left + right` is too big, move `right` inward; if too small, move `left` inward. You converge on the answer without backtracking.

**Trigger Keywords in problem statement:**
- "sorted array", "find pair with sum X"
- "reverse / palindrome / in-place"
- "remove duplicates & merge / filter without extra space"
- "container with most water", "trap rain water"
- "two sum on sorted input"
- finding pairs with specific properties (SUM = TARGET)
- detecting loops or middle elements ( Linked List cycle, middle node)

**WHEN TO USE TWO POINTERS
- PCS - Partitioning, Searching, Comparing

---

## 🏗️ Pattern Template (Reusable Code Frame)

### 1 Input , Opposite-End (Converging) Pointer 
```java
// ===== TWO POINTERS — Opposite Ends =====
// Use: pair-sum problems, palindrome check

arr = [1,4,3,2,9]

    int left  = 0;
    int right = arr.length - 1;

    while (left < right) {
        if (condition):
            left = left + 1;
        else:
             right = right - 1;
    return ans

```

### 2 Inputs, Exhaust Both - Same-Direction Pointer
```java
// ===== TWO POINTERS — Same Direction =====
// Use: remove duplicates, merge sorted array

arr1 = [1,4,3,2,9]
arr2 = [9,7,6,2,0,8]

     int left  = 0;
     int right = arr.length - 1;

    while (left < right) {
        if (condition):
            left = left + 1;
        else:
             right = right - 1;
    while (left < arr1.length){
             left = left + 1
    }
    while (right < arr2.length){
             right = right + 1
    }       
    
}
```

### Three Pointers (Fix + Two-Pointer Inner)
```java
// ===== THREE POINTERS — 3Sum Template =====
// Use: 3Sum, 3Sum Closest, 4Sum (nested)

public static List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    Arrays.sort(nums);                       // MUST sort first

    for (int i = 0; i < nums.length - 2; i++) {
        if (i > 0 && nums[i] == nums[i - 1]) continue; // skip outer duplicate

        int left = i + 1, right = nums.length - 1;

        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];

            if (sum == 0) {
                result.add(Arrays.asList(nums[i], nums[left], nums[right]));
                while (left < right && nums[left]  == nums[left  + 1]) left++;  // skip inner dupe
                while (left < right && nums[right] == nums[right - 1]) right--; // skip inner dupe
                left++; right--;
            } else if (sum < 0) left++;
            else                right--;
        }
    }
    return result;
}
```

---

## 🔍 Pattern Variants

| Variant | When to Use | Key Difference |
|---------|-------------|----------------|
| Opposite ends | Sorted array, pair-sum, palindrome | Pointers start at 0 and n-1, converge |
| Slow + Fast | In-place remove, filter, cycle detect | Same direction, different speeds |
| Three pointers | 3Sum, 4Sum | Fix outer with loop, two-pointer inner |
| Partition pointer | Dutch National Flag, move zeroes | One read + one write pointer |

---

## 📊 Complexity Profile

| | Time | Space |
|-|------|-------|
| Typical | `O(n)` | `O(1)` |
| Three-pointer variant | `O(n²)` | `O(1)` |
| Worst case (sort needed first) | `O(n log n)` | `O(1)` |

---

## ✅ Solved Problems

| # | Problem Name | LC # | Difficulty | Key Insight |
|---|-------------|------|------------|-------------|
| 1 | Two Sum II | 167 | Easy | Textbook opposite-end pattern |
| 2 | Valid Palindrome | 125 | Easy | Skip non-alphanumeric with `Character.isLetterOrDigit()` |
| 3 | 3Sum | 15 | Medium | Sort first, fix outer, two-pointer inner. Skip duplicates |
| 4 | Container With Most Water | 11 | Medium | Move the *shorter* side inward — key greedy insight |
| 5 | Remove Duplicates from Sorted Array | 26 | Easy | Classic slow/fast |
| 6 | Sort Colors | 75 | Medium | Dutch National Flag — 3-pointer partition |
| 7 | Trapping Rain Water | 42 | Hard | Track `maxLeft` & `maxRight` as pointers move |

---

## 🆚 Confused With (Common Mistakes)

| I confuse it with | How they differ |
|-------------------|----------------|
| [[Sliding Window]] | Sliding window maintains a *range* between pointers (window size matters). Two pointers just track positions — no window state. |
| [[Binary Search]] | Binary search halves the search space each step. Two pointers move linearly — no halving. |
| Floyd's Cycle (Linked List) | Same slow/fast idea, but on a linked list following `.next` links, not array indices. |

---

## 🧩 Decision Guide

```
Array is sorted + find pair/triplet with sum?   → Opposite-end two pointer
In-place remove/filter without extra array?     → Slow + fast pointer
Check palindrome or reverse in-place?           → Opposite-end two pointer
Need to count subarrays / track a sum range?    → Probably Sliding Window instead
Linked list, detect cycle / find middle?        → Slow + fast pointer (Floyd's)
3Sum, 4Sum?                                     → Sort + fix outer loop + two-pointer inner
```

---

## ⚠️ Gotchas & Things I Always Forget

> *Write confusing things, edge cases, or things you always forget.*

1. **Sort first** — two pointers only works because sorted order guarantees monotonicity. If input isn't sorted, sort it first (costs O(n log n)).
2. In **3Sum**, skip duplicate outer values after fixing `i`, and also skip duplicates inside the inner two-pointer loop after finding a valid triplet — otherwise you get repeated answers.
3. **Container With Most Water** — the area is bounded by the *shorter* bar. Moving the taller bar inward can never help, so always move the shorter one.
4. When doing in-place removal with slow/fast, `slow` represents the "last valid write position". Return `slow + 1` as the new length, not `slow`.
5. Always check `left < right` in the while condition, not `left <= right` — the `=` case means both pointers are at the same element, which is usually invalid for pair problems.

---

## 🔗 Related Topics
- [ ] [[Sliding Window]]
- [ ] [[Binary Search]]
- [ ] [[Sorting Algorithms]]
- [ ] [[LinkedList — Floyd's Cycle Detection]]
- [ ] [[Dutch National Flag Algorithm]]

---

## 📝 Personal Notes
> *Add your own aha-moments here as you solve more problems.*

---

## 🔁 Revision Log

| Date | Problems Done | Confidence (1–5) |
|------|--------------|-----------------|
| 2025-06-01 | LC 167, LC 125, LC 26 | ⭐⭐⭐ |
| 2025-06-08 | LC 15, LC 11 | ⭐⭐⭐⭐ |
| 2025-06-13 | LC 75, LC 42 | ⭐⭐⭐⭐⭐ |
