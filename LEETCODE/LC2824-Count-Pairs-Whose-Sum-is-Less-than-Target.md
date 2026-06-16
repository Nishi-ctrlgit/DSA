---
tags: [leetcode, two-pointers, arrays, sorting, easy]
lc_number: 2824
title: "Count Pairs Whose Sum is Less than Target"
difficulty: "Easy"
pattern: "Two Pointers"
date_solved: 2025-06-13
attempts: 1
solved_without_hint: true
---

# LC 2824 — Count Pairs Whose Sum is Less than Target

🔗 [Problem Link](https://leetcode.com/problems/count-pairs-whose-sum-is-less-than-target/)
🏷️ Difficulty: `Easy` | Pattern: `[[Two Pointers]]`

---

## 📋 Problem Summary

> Given a 0-indexed integer array `nums` of length `n` and an integer `target`, return the number of pairs `(i, j)` where `0 <= i < j < n` and `nums[i] + nums[j] < target`.

**Input:** `int[] nums` — unsorted array | `int target` — threshold value
**Output:** `int` — count of valid index pairs
**Constraint to note:** Pairs are counted by **index** (`i < j`), but since we only care about the sum, sorting the values first doesn't break correctness — it just changes which physical indices we're looking at, not the count.

---

## 🔍 Examples

```
Input:  nums = [-1, 1, 2, 3, 1], target = 2
Output: 3
Why:    Valid pairs: (0,1) → -1+1=0 < 2 ✅
                      (0,2) → -1+2=1 < 2 ✅
                      (0,4) → -1+1=0 < 2 ✅
        Total = 3
```

```
Input:  nums = [-6, 2, 5, -2, -7, -1, 3], target = -2
Output: 10
Why:    Sort first: [-7, -6, -2, -1, 2, 3, 5]
        Use two pointers to count efficiently instead of checking all C(7,2)=21 pairs
```

```
Input:  nums = [0, 0, 0], target = 1     ← Edge case: all zeros
Output: 3
Why:    Every pair sums to 0 < 1 → all C(3,2) = 3 pairs valid
```

```
Input:  nums = [5, 5], target = 1        ← Edge case: no valid pairs
Output: 0
Why:    5+5=10, not < 1 → zero valid pairs
```

```
Input:  nums = [1]                        ← Edge case: single element
Output: 0
Why:    Need at least 2 elements to form a pair → no pairs possible
```

---

## 💭 My Thought Process

### Brute Force Idea:
Check every pair `(i, j)` with nested loops, count if `nums[i] + nums[j] < target`. Straightforward but **O(n²)** — fine for small constraints (n ≤ 50 here) but not optimal.

### Optimization Insight:
**Sort the array first.** Once sorted, use two pointers: if `nums[left] + nums[right] < target`, then `nums[left]` paired with **every** element between `left+1` and `right` is also valid (because the array is sorted, all those sums are even smaller). Count all of them at once, then move `left` forward. If the sum is `>= target`, move `right` inward — that pair and any pair with this `right` and elements after `left` won't work either.

### Final Approach:
Sort `nums`. Two pointers `left=0`, `right=n-1`. If sum < target → add `(right - left)` to count (all valid pairs with this left), then `left++`. Else → `right--`.

---

## ☕ Java Solution

### Optimal — Sort + Two Pointers, O(n log n)
```java
class Solution {

    public int countPairs(List<Integer> nums, int target) {

        Collections.sort(nums);

  
// TC : O(n log n)
// SC : O(log n)
        int i = 0;

        int j = nums.size() - 1;

        int count = 0;

  

        while(i<j){

            int sum = nums.get(i) + nums.get(j);

            if(sum < target){

                count = count + (j - i);

                i = i + 1;

                } else {

                    j = j - 1;

                }

        }

        return count;

    }

}
```

> Note: LeetCode's signature uses `List<Integer> nums`, not `int[]`. If working with a plain array, convert first or adjust accordingly — shown below.

### Array Version (if input is `int[]`)
```java
class Solution {
    public int countPairs(int[] nums, int target) {
        Arrays.sort(nums);

        int left  = 0;
        int right = nums.length - 1;
        int count = 0;

        while (left < right) {
            if (nums[left] + nums[right] < target) {
                count += (right - left);
                left++;
            } else {
                right--;
            }
        }

        return count;
    }
}
```

### Brute Force — Nested Loops, O(n²)
```java
class Solution {
    public int countPairs(List<Integer> nums, int target) {
        int count = 0;
        int n = nums.size();

        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (nums.get(i) + nums.get(j) < target) {
                    count++;
                }
            }
        }
        return count;
    }
}
```

---

## 🧩 Dry Run — Step by Step

```
nums = [-1, 1, 2, 3, 1], target = 2
Sorted: [-1, 1, 1, 2, 3]
Indices: 0   1  2  3  4

left=0, right=4 → sum = -1+3 = 2     → NOT < 2 → right-- → right=3
left=0, right=3 → sum = -1+2 = 1     → < 2 → count += (3-0)=3 → count=3 → left++ → left=1
left=1, right=3 → sum = 1+2 = 3      → NOT < 2 → right-- → right=2
left=1, right=2 → sum = 1+1 = 2      → NOT < 2 → right-- → right=1
left=1, right=1 → loop ends (left < right fails)

Final count = 3 ✅ (matches expected output)
```

---

## 💡 The Key Insight (don't skip this)

```
Sorted array: [-1, 1, 1, 2, 3]
                ^           ^
              left        right

If nums[left] + nums[right] < target:
   → nums[left] paired with ANYTHING between left and right also satisfies < target
   → because all those values are <= nums[right] (sorted!)
   → so add (right - left) pairs all at once, then move left forward

If nums[left] + nums[right] >= target:
   → nums[right] is too big for nums[left] — and it's the LARGEST remaining value
   → so nums[right] won't work with ANY left either
   → shrink from the right
```

This is what makes it O(n) after sorting instead of checking pairs one by one.

---

## ⏱️ Complexity

| | Complexity | Reason |
|--|-----------|--------|
| Time | `O(n log n)` | Sorting dominates; two-pointer scan itself is O(n) |
| Space | `O(1)` | In-place sort, only pointer variables (excluding sort's internal space) |
| Time (brute) | `O(n²)` | Nested loop checks every pair |

---

## ⚠️ Edge Cases Considered

- [x] All elements the same `[0,0,0]` → every pair counts if sum < target
- [x] No valid pairs `[5,5], target=1` → returns 0
- [x] Single element `[1]` → can't form a pair, returns 0
- [x] Negative numbers present `[-6,2,5,-2,-7,-1,3]` → sorting handles sign correctly
- [x] Target is very small (e.g., negative) → very few or zero valid pairs
- [x] Target is very large → all pairs valid, count = n*(n-1)/2
- [x] Empty or near-empty array → loop doesn't execute, returns 0

---

## 🪤 Mistakes I Made / Traps

1. **Forgetting to sort first** — two pointers only work correctly once the array is sorted. Without sorting, the "if sum < target, all elements between left and right also work" logic breaks completely.

2. **Adding `count++` instead of `count += (right - left)`** — the most important optimization in this problem. Without this bulk-counting step, you're back to checking pairs one at a time and lose the benefit of two pointers (though technically still correct, just slower — O(n²) in the worst case if you increment one at a time inside a sub-loop).

3. **Confusing `List<Integer>` vs `int[]`** — LeetCode's exact signature for this problem uses `List<Integer>`. Calling `.size()` not `.length`, and `.get(i)` not `[i]`. Easy to mix up if you're used to array-based two-pointer problems.

4. **Worrying about original indices** — since we only care about the **count** of valid pairs (not which specific indices), sorting and losing the original index order is totally fine. Don't overthink preserving index order.

---

## 🔁 Alternative Approaches

| Approach | Time | Space | Why not chosen |
|----------|------|-------|----------------|
| Brute force nested loop | `O(n²)` | `O(1)` | Works for small n, but not optimal |
| Sort + Two Pointers (chosen) | `O(n log n)` | `O(1)` | ✅ Optimal, clean bulk-counting trick |
| Sort + Binary Search per element | `O(n log n)` | `O(1)` | Also valid — for each left, binary search the rightmost valid right. More complex code for same complexity |

---

## 🔗 Similar Problems

- [[LC 167 — Two Sum II]] — two pointers, find exact pair, not count
- [[LC 15 — 3Sum]] — fix one + two pointer for the rest
- [[LC 11 — Container With Most Water]] — two pointers, maximize instead of count
- [[LC 977 — Squares of a Sorted Array]] — two pointers, sorted array property
- [[LC 16 — 3Sum Closest]] — two pointers, find closest instead of count

---

## 📝 Key Takeaway (1 line)
> Sort first, then use two pointers — when a pair satisfies the condition, count ALL pairs between the pointers at once instead of one by one.

---

## 🔁 Revision Log

| Date | Attempt | Solved Without Hint | Notes |
|------|---------|---------------------|-------|
| 2025-06-13 | 1st | ✅ Yes | Remembered bulk-counting trick from similar problems |
