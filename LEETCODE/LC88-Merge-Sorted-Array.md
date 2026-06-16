---
tags: [leetcode, two-pointers, arrays, easy]
lc_number: 88
title: "Merge Sorted Array"
difficulty: "Easy"
pattern: "Two Pointers"
date_solved: 2025-06-16
attempts: 2
solved_without_hint: false
---

# LC 88 — Merge Sorted Array

🔗 [Problem Link](https://leetcode.com/problems/merge-sorted-array/)
🏷️ Difficulty: `Easy` | Pattern: `[[Two Pointers]]`

---

## 📋 Problem Summary

> You are given two sorted integer arrays `nums1` and `nums2`, and integers `m` and `n` representing the number of valid elements in each. Merge `nums2` into `nums1` as one sorted array, in-place.

**Input:** `int[] nums1` (size `m+n`, first `m` elements valid, rest are padding zeros) | `int[] nums2` (size `n`, all valid) | `int m`, `int n`
**Output:** `void` — modify `nums1` in-place
**Constraint to note:** `nums1` has extra space at the end specifically to hold `nums2`'s elements. Must merge **in-place** without using extra array.

---

## 🔍 Examples

```
Input:  nums1 = [1,2,3,0,0,0], m = 3
        nums2 = [2,5,6],       n = 3
Output: [1,2,3,2,5,6] → merged → [1,2,2,3,5,6]
Why:    Merge both sorted arrays into nums1, using the trailing zeros as space
```

```
Input:  nums1 = [1], m = 1
        nums2 = [],  n = 0
Output: [1]
Why:    nums2 is empty — nums1 stays unchanged
```

```
Input:  nums1 = [0], m = 0      ← Edge case: nums1 has no valid elements
        nums2 = [1], n = 1
Output: [1]
Why:    All elements come from nums2 — nums1 starts completely empty (m=0)
```

---

## 💭 My Thought Process

### Brute Force Idea:
Copy valid elements of `nums2` into the back of `nums1`, then call `Arrays.sort(nums1)`. Works but costs **O((m+n) log(m+n))** — ignores that both inputs are already sorted.

### Optimization Insight:
Merging from the **front** is a trap — overwriting `nums1[i]` before reading it destroys data. Instead, merge from the **back**: compare the largest remaining elements of both arrays, place the bigger one at the last open slot, and move inward. This never overwrites unread data.

### Final Approach:
Three pointers — `p1` at end of valid `nums1` (`m-1`), `p2` at end of `nums2` (`n-1`), and `p` at the very last index of `nums1` (`m+n-1`). Compare `nums1[p1]` vs `nums2[p2]`, place the larger at `nums1[p]`, decrement that pointer and `p`. Continue until `nums2` is exhausted.

---

## ☕ Java Solution

### Optimal — Three Pointers, Fill from Back, O(m+n)
```java
class Solution {

    public void merge(int[] nums1, int m, int[] nums2, int n) {

        int i = m - 1,

            j = n - 1,

            k = m + n - 1;

            // TC : o(m+n)

            // SC : O(1)

            /* nums1 : 1 2 3 0 0 0

            /              i

                                  k

               nums2 : 2 5 6

                           j

            */

  

            while(i >= 0 && j >= 0){

                if(nums1[i] > nums2[j]){

                    nums1[k] = nums1[i];

                    i = i - 1;

                    } else {

                        nums1[k] = nums2[j];

                        j = j - 1;

                    }

                    k =  k - 1;

            }

            while(j >= 0){

                nums1[k] = nums2[j];

                j = j - 1;

                k = k - 1;

            }

    }

}
```

### Brute Force — Copy + Sort, O((m+n) log(m+n))
```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        for (int i = 0; i < n; i++) {
            nums1[m + i] = nums2[i];      // copy nums2 into the back of nums1
        }
        Arrays.sort(nums1);               // sort everything together
    }
}
```

---

## 🧩 Dry Run — Step by Step

```
nums1 = [1, 2, 3, 0, 0, 0],  m = 3
nums2 = [2, 5, 6],           n = 3

p1=2 (val=3)  p2=2 (val=6)  p=5
  3 > 6? No  → nums1[5] = 6, p2-- → p2=1, p--

p1=2 (val=3)  p2=1 (val=5)  p=4
  3 > 5? No  → nums1[4] = 5, p2-- → p2=0, p--

p1=2 (val=3)  p2=0 (val=2)  p=3
  3 > 2? Yes → nums1[3] = 3, p1-- → p1=1, p--

p1=1 (val=2)  p2=0 (val=2)  p=2
  2 > 2? No  → nums1[2] = 2, p2-- → p2=-1, p--

Loop ends (p2 < 0)

Result: [1, 2, 2, 3, 5, 6] ✅
```

---

## ⏱️ Complexity

| | Complexity | Reason |
|--|-----------|--------|
| Time (optimal) | `O(m+n)` | Single pass through both arrays combined |
| Space (optimal) | `O(1)` | No extra array, merges in-place |
| Time (brute) | `O((m+n) log(m+n))` | Sorting dominates |
| Space (brute) | `O(log(m+n))` | Sort's internal call stack (or O(1) depending on sort algo) |

---

## ⚠️ Edge Cases Considered

- [x] `nums2` is empty (`n = 0`) → loop never runs, `nums1` unchanged
- [x] `nums1` has no valid elements (`m = 0`) → all values come from `nums2`
- [x] All elements in `nums2` are smaller than all in `nums1` → `p2` exhausts first while `p1` still has values, which are already correctly placed
- [x] All elements in `nums2` are larger than all in `nums1` → `p1` exhausts early, rest fills directly from `nums2`
- [x] Duplicate values across both arrays → `else` branch handles ties correctly (places `nums2` value, decrements `p2`)
- [x] Single element each (`m=1, n=1`) → one comparison, done

---

## 🪤 Mistakes I Made / Traps

1. **Merging from the front** — instinct says start at index 0, but `nums1`'s real values would get overwritten by merged output before they're read. Always merge **from the back** when merging into one of the two arrays in-place.

2. **Forgetting the `p1 >= 0` check** — once `nums1`'s valid elements are exhausted, the condition `nums1[p1] > nums2[p2]` would throw `ArrayIndexOutOfBoundsException` without this guard. Must check `p1 >= 0` before accessing `nums1[p1]`.

3. **Looping on the wrong condition** — looping `while (p1 >= 0 && p2 >= 0)` and then doing leftover copying afterward works too, but looping `while (p2 >= 0)` alone is cleaner — once `nums2` is exhausted, whatever's left in `nums1[0..p1]` is already in its correct sorted position (no copy needed).

4. **Using brute force in an interview without mentioning the in-place insight** — brute force is O(n log n) and technically "works", but interviewers specifically test if you spot the back-to-front merge trick.

---

## 💡 The Key Insight (don't skip this)

```
nums1 = [1, 2, 3, 0, 0, 0]
                  ^tail space reserved for nums2

If you merge from the FRONT:
  nums1[0] = min(...) → overwrites index 0 BEFORE you've read it fully
  → data loss

If you merge from the BACK:
  You're writing into the "empty" trailing zeros first
  → never touches unread valid data until it's safe to do so
```

This exact "fill from the back" trick reappears in [[LC 977 — Squares of a Sorted Array]] — same core idea, different problem.

---

## 🔁 Alternative Approaches

| Approach | Time | Space | Why not chosen |
|----------|------|-------|----------------|
| Copy + sort | `O((m+n) log(m+n))` | `O(log(m+n))` | Ignores sorted property |
| New merged array, copy back | `O(m+n)` | `O(m+n)` | Extra space not needed |
| Three pointers, fill from back (chosen) | `O(m+n)` | `O(1)` | ✅ Optimal |

---

## 🔗 Similar Problems

- [[LC 977 — Squares of a Sorted Array]] — same "fill from the end" trick
- [[LC 21 — Merge Two Sorted Lists]] — same merge idea, but on linked lists
- [[LC 23 — Merge k Sorted Lists]] — extension to k lists
- [[LC 350 — Intersection of Two Arrays II]] — two pointers on sorted arrays

---

## 📝 Key Takeaway (1 line)
> Merge in-place from the back, not the front — you fill the empty space first, so you never overwrite values before reading them.

---

## 🔁 Revision Log

| Date | Attempt | Solved Without Hint | Notes |
|------|---------|---------------------|-------|
| 2025-06-16 | 1st | ❌ No | Tried merging from front, overwrote unread nums1 values |
| 2025-06-16 | 2nd | ✅ Yes | Switched to back-to-front three pointer approach |
