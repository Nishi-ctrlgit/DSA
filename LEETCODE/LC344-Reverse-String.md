---
tags: [leetcode, two-pointers, strings, arrays, easy]
lc_number: 344
title: "Reverse String"
difficulty: "Easy"
pattern: "Two Pointers"
date_solved: 2025-06-13
attempts: 1
solved_without_hint: true
---

# LC 344 — Reverse String

🔗 [Problem Link](https://leetcode.com/problems/reverse-string/)
🏷️ Difficulty: `Easy` | Pattern: `[[Two Pointers]]`

---

## 📋 Problem Summary

> Write a function that reverses a character array in-place.
> You must do this by modifying the input array directly with **O(1) extra memory**.

**Input:** `char[] s` — array of characters
**Output:** `void` — modify the array in-place, return nothing
**Constraint to note:** Must be in-place. O(1) extra space. Do NOT allocate a new array.

---

## 🔍 Examples

```
Input:  s = ['h','e','l','l','o']
Output: ['o','l','l','e','h']
Why:    Swap first ↔ last, then second ↔ second-last, until pointers meet
```

```
Input:  s = ['H','a','n','n','a','h']
Output: ['h','a','n','n','a','H']
Why:    Same swap logic — note 'H' and 'h' are different chars, case is preserved
```

```
Input:  s = ['a']         ← Edge case: single element
Output: ['a']
Why:    left == right at start, while loop never executes, nothing changes
```

```
Input:  s = ['a','b']     ← Edge case: two elements
Output: ['b','a']
Why:    One swap, then left (1) == right (0) after increment/decrement → stops
```

---

## 💭 My Thought Process

### Brute Force Idea:
Create a new `char[]`, copy elements from end to start into it, then copy back. Works but uses **O(n) extra space** — violates the constraint.

### Optimization Insight:
Swap characters at the two ends simultaneously and move inward. No extra space needed. The array reverses itself in `n/2` swaps.

### Final Approach:
Left pointer at index 0, right pointer at index `n-1`. Swap `s[left]` and `s[right]`, then `left++` and `right--`. Stop when `left >= right`.

---

## ☕ Java Solution

### Optimal — Two Pointers, O(1) Space
```java
class Solution {

    public void reverseString(char[] s) {
        int i = 0,
            j = s.length - 1;
        while(i < j){
             char temp = s[i];
             s[i] = s[j];
             s[j] = temp;
             
             i = i + 1;
             j = j - 1;

        }

    }

}
```

### XOR Swap (no temp variable) — Clever but avoid in interviews
```java
class Solution {
    public void reverseString(char[] s) {
        int left = 0, right = s.length - 1;

        while (left < right) {
            // XOR swap — works only when left != right (different indices)
            s[left]  ^= s[right];
            s[right] ^= s[left];
            s[left]  ^= s[right];

            left++;
            right--;
        }
    }
}
```

> ⚠️ XOR swap breaks if `left == right` (same index, same memory location) — the value becomes 0. Safe here because the while condition `left < right` prevents it, but don't use this pattern blindly elsewhere.

### One-liner using Java built-in (NOT acceptable for this problem)
```java
// Only for reference — violates O(1) space constraint
new StringBuilder(new String(s)).reverse().toString().getChars(0, s.length, s, 0);
```

---

## ⏱️ Complexity

| | Complexity | Reason |
|--|-----------|--------|
| Time | `O(n)` | Each element visited exactly once (n/2 swaps) |
| Space | `O(1)` | Only two pointers + one temp variable |

---

## ⚠️ Edge Cases Considered

- [x] Empty array `[]` → nothing happens, no crash
- [x] Single element `['a']` → left == right from start, loop skipped
- [x] Two elements `['a','b']` → one swap, done
- [x] Even length array → pointers meet perfectly in the middle
- [x] Odd length array → middle element is never touched (doesn't need to be)
- [x] All same characters `['a','a','a']` → swaps happen but result is same
- [x] Mixed case `['A','b']` → case is preserved, no lowercasing

---

## 🪤 Mistakes I Made / Traps

1. **Using `left <= right` instead of `left < right`** — when the array has odd length, the middle element gets swapped with itself when `left == right`. Wastes a cycle, and in XOR swap it would zero out the element. Always use strict `<`.
2. **Forgetting it's `char[]` not `String`** — Java strings are immutable. The problem gives you a `char[]` specifically so you can modify it in-place. You cannot do `s = s.reverse()` or anything like that.
3. **Returning the array** — the method signature is `void`. You modify in-place, you return nothing. A common reflex is to `return s` at the end — that's a compile error here.
4. **Using `s.length()` instead of `s.length`** — `String` uses `.length()` (method). `char[]` uses `.length` (field, no parentheses). Classic Java gotcha.

---

## 🔁 Alternative Approaches

| Approach | Time | Space | Why not chosen |
|----------|------|-------|----------------|
| New array + copy back | `O(n)` | `O(n)` | Violates O(1) space constraint |
| Recursion | `O(n)` | `O(n)` | Call stack counts as space — violates constraint |
| Two pointers (chosen) | `O(n)` | `O(1)` | ✅ Optimal, clean, interview-standard |
| XOR swap | `O(n)` | `O(1)` | Same complexity, less readable, risky if misapplied |

---

## 🔗 Similar Problems

- [[LC 125 — Valid Palindrome]] — two pointers on a string, skip non-alphanumeric
- [[LC 541 — Reverse String II]] — reverse every 2k characters with conditions
- [[LC 557 — Reverse Words in a String III]] — reverse each word in-place
- [[LC 151 — Reverse Words in a String]] — reverse full words, handle extra spaces
- [[LC 917 — Reverse Only Letters]] — skip non-letter chars, reverse only letters

---

## 📝 Key Takeaway (1 line)
> Swap from both ends moving inward — `n/2` swaps reverses any array in O(n) time, O(1) space.

---

## 🔁 Revision Log

| Date | Attempt | Solved Without Hint | Notes |
|------|---------|---------------------|-------|
| 2025-06-13 | 1st | ✅ Yes | Straightforward, remembered `s.length` not `s.length()` |
