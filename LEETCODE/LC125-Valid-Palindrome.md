---
tags:
  - leetcode
  - two-pointers
  - strings
  - easy
lc_number: 125
title: Valid Palindrome
difficulty: Easy
pattern: Two Pointers
date_solved: 2025-06-13
attempts: 1
solved_without_hint: true
---

# LC 125 — Valid Palindrome

🔗 [Problem Link](https://leetcode.com/problems/valid-palindrome/)
🏷️ Difficulty: `Easy` | Pattern: `[[Two Pointers]]`

---

## 📋 Problem Summary

> A phrase is a palindrome if, after converting all uppercase letters to lowercase and removing all non-alphanumeric characters, it reads the same forward and backward.
> Given a string `s`, return `true` if it is a palindrome, or `false` otherwise.

**Input:** `String s` — a sentence/phrase (may contain spaces, punctuation, mixed case)
**Output:** `boolean` — true if palindrome after cleaning, false otherwise
**Constraint to note:** Only consider alphanumeric characters (`a-z`, `A-Z`, `0-9`). Ignore everything else. Empty string counts as a valid palindrome.

---

## 🔍 Examples

```
Input:  "A man, a plan, a canal: Panama"
Output: true
Why:    After cleaning → "amanaplanacanalpanama" → reads same forwards & backwards
```

```
Input:  "race a car"
Output: false
Why:    After cleaning → "raceacar" → not a palindrome
```

```
Input:  " "      ← Edge case: only spaces
Output: true
Why:    After cleaning → "" (empty string) → empty is a palindrome
```

---

## 💭 My Thought Process

### Brute Force Idea:
Clean the string first (remove non-alphanumeric, lowercase), store it in a new string, then compare it with its reverse using `StringBuilder.reverse()`. Works but uses **O(n) extra space**.

### Optimization Insight:
Don't clean at all — just use **two pointers** (left and right) and skip non-alphanumeric characters on the fly. Compare only valid characters. This gives O(1) space.

### Final Approach:
Left pointer starts at 0, right pointer starts at end. Skip invalid chars from both sides. Compare `Character.toLowerCase()` of both. If mismatch → return false. If pointers cross → return true.

---

## ☕ Java Solution

### Optimal — Two Pointers, O(1) Space
```java
class Solution {
    public boolean isPalindrome(String s) {
         int i = 0;
         int j = s.length() - 1;
         
         while(i < j) {
         char left = s.charAt(i);
         char right = s.charAt(j);
         
            if(!Character.isLetterOrDigit(left)){
               i = i + 1;
               continue;
             }
             if(!Character.isLetterOrDigit(right)){
               j = j - 1;
               continue;
             }
             if((Character.toLowerCase(left) != Character.toLowerCase(right))){
                return false;
              }

           i = i + 1;
           j = j - 1;
        }
    return true;
    }
}
```

### Brute Force — Clean First (O(n) Space)
```java
class Solution {
    public boolean isPalindrome(String s) {
        StringBuilder sb = new StringBuilder();

        for (char c : s.toCharArray()) {
            if (Character.isLetterOrDigit(c)) {
                sb.append(Character.toLowerCase(c));
            }
        }

        String cleaned = sb.toString();
        String reversed = sb.reverse().toString();

        return cleaned.equals(reversed);
        // Note: sb is already reversed at this point, so compare cleaned (saved before) with reversed
    }
}
```

> ⚠️ Fix for brute force above — save cleaned string BEFORE reversing:
```java
String cleaned  = sb.toString();
String reversed = new StringBuilder(cleaned).reverse().toString();
return cleaned.equals(reversed);
```

---

## ⏱️ Complexity

| | Complexity | Reason |
|--|-----------|--------|
| Time | `O(n)` | Each character visited at most once |
| Space | `O(1)` | No extra string created (optimal solution) |
| Space (brute) | `O(n)` | Cleaned string stored separately |

---

## ⚠️ Edge Cases Considered

- [x] Empty string `""` → true (vacuously a palindrome)
- [x] Only spaces / punctuation `" "` → true (cleans to empty)
- [x] Single character `"a"` → true
- [x] All same characters `"aaaa"` → true
- [x] Mixed case `"Aa"` → true (case-insensitive compare)
- [x] Numbers in string `"0P"` → false
- [x] Only numbers `"121"` → true

---

## 🪤 Mistakes I Made / Traps

1. **Forgetting `left < right` inside inner while loops** — without this guard, the inner `while` can push `left` past `right` when the string has only non-alphanumeric characters (like `" "`), causing incorrect comparisons.
2. **Using `==` for char comparison after toLowerCase** — this actually works for `char` primitives in Java (not objects), but the habit is dangerous. Always be conscious of primitive vs object comparison.
3. **Brute force StringBuilder bug** — calling `sb.reverse()` mutates `sb` in place. If you saved `cleaned = sb.toString()` before reversing, then `cleaned` still holds the original. Easy to mess up the order.

---

## 🔁 Alternative Approaches

| Approach | Time | Space | Why not chosen |
|----------|------|-------|----------------|
| Clean string + reverse compare | `O(n)` | `O(n)` | Extra space, less elegant |
| Regex clean + reverse | `O(n)` | `O(n)` | Readable but slow due to regex overhead |
| Two pointers (chosen) | `O(n)` | `O(1)` | ✅ Optimal |

---

## 🔗 Similar Problems

- [[LC 680 — Valid Palindrome II]] — can delete at most one character
- [[LC 234 — Palindrome Linked List]] — same idea on a linked list
- [[LC 647 — Palindromic Substrings]] — count all palindromic substrings
- [[LC 5 — Longest Palindromic Substring]] — expand around center

---

## 📝 Key Takeaway (1 line)
> Skip non-alphanumeric chars on the fly with two pointers — never waste space cleaning the string first.

---

## 🔁 Revision Log

| Date | Attempt | Solved Without Hint | Notes |
|------|---------|---------------------|-------|
| 2025-06-13 | 1st | ✅ Yes | Clean solution, remembered inner guard condition |
