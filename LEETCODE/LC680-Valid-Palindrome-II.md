---
tags:
  - leetcode
  - two-pointers
  - strings
  - easy
lc_number: 680
title: Valid Palindrome II
difficulty: Easy
pattern: Two Pointers
date_solved: 2025-06-13
attempts: 2
solved_without_hint: true
---

# LC 680 — Valid Palindrome II

🔗 [Problem Link](https://leetcode.com/problems/valid-palindrome-ii/)
🏷️ Difficulty: `Easy` | Pattern: `[[Two Pointers]]`

---

## 📋 Problem Summary

> Given a string `s`, return `true` if the string can be a palindrome after deleting **at most one character**.

**Input:** `String s` — lowercase English letters only (no spaces, no punctuation)
**Output:** `boolean` — true if palindrome after removing at most 1 character
**Constraint to note:** You can delete **at most one** character — meaning zero deletions is also valid. Input is only lowercase letters (unlike LC 125, no cleaning needed).

---

## 🔍 Examples

```
Input:  s = "aba"
Output: true
Why:    Already a palindrome — zero deletions needed
```

```
Input:  s = "abca"
Output: true
Why:    Delete 'c' → "aba" → palindrome
        OR delete 'b' → "aca" → palindrome
        Either works — just need one valid option
```

```
Input:  s = "abc"
Output: false
Why:    Delete 'a' → "bc" (no), delete 'b' → "ac" (no), delete 'c' → "ab" (no)
        No single deletion makes it a palindrome
```

```
Input:  s = "deeee"              ← Edge case: one mismatch near end
Output: true
Why:    left='d', right='e' → mismatch at index 0 and 4
        Try skip left → "eeee" → palindrome ✅
```

```
Input:  s = "eeccccbebaeeabebccceea"   ← Edge case: tricky nested mismatch
Output: false
Why:    First mismatch found, both skip-left and skip-right still fail
        Common trap — must check BOTH substrings fully
```

---

## 💭 My Thought Process

### Brute Force Idea:
Try deleting each character one by one (n deletions), check if each result is a palindrome using LC 125 logic. Total: **O(n²)** — too slow for large inputs.

### Optimization Insight:
Run two pointers normally. When we hit the **first mismatch**, we have exactly two choices:
- Skip the left character → check if `s[left+1 .. right]` is a palindrome
- Skip the right character → check if `s[left .. right-1]` is a palindrome

If **either** substring is a palindrome → return `true`. If both fail → return `false`. We only ever encounter one mismatch (one deletion budget), so this is O(n).

### Final Approach:
Two pointers from both ends. On mismatch, call a helper `isPalindrome(s, left, right)` twice — once skipping left, once skipping right. Return `true` if either passes.

---

## ☕ Java Solution

### Optimal — Two Pointers + Helper, O(n)
```java
class Solution {
public boolean palindromeHelper(int i,int j,String s){
    while(i < j){
         if(s.charAt(i) != s.charAt(j)) {
               return false;
         }
         i = i + 1;
         j = j - 1;
    }
 return true;
 }

// T = O(n)  S = O(1)
public boolean validPalindrome(String s) {
       int i = 0,
       j = s.length() - 1;
       
       while(i < j){
              char left = s.charAt(i),
                  right = s.charAt(j);

               if(left != right){
               //use one superpower
                  return palindromeHelper(i + 1,j,s) || palindromeHelper(i,j - 1,s);

                } else {
                     i = i + 1;
                     j = j - 1;
                 }
        }
    return true;
}

}```

### Why the helper must check the FULL substring
```java
// WRONG — common mistake: only check one more step after mismatch
if (s.charAt(left + 1) == s.charAt(right) ||
    s.charAt(left) == s.charAt(right - 1)) {
    return true; // ❌ This is incorrect — one matching step is NOT enough
}

// RIGHT — must verify the entire remaining substring is a palindrome
return isPalindrome(s, left + 1, right) ||
       isPalindrome(s, left, right - 1);
```

> Example where the wrong approach fails: `s = "aguokepatgbnvfqmgmlcupuufxoohdfpgjdmysgvhmvffcnqxjjxqncffvmhvgsymdjgpfdhooxi"`
> Characters match for a while after the skip, but fail later — only a full check catches this.

---

## ⏱️ Complexity

| | Complexity | Reason |
|--|-----------|--------|
| Time | `O(n)` | Main loop O(n) + at most two O(n) helper calls = O(n) total |
| Space | `O(1)` | No extra data structures, just pointers |

---

## ⚠️ Edge Cases Considered

- [x] Already a palindrome `"aba"` → zero deletions, returns true
- [x] Empty string `""` → left >= right immediately, returns true
- [x] Single character `"a"` → left >= right, returns true
- [x] Two same characters `"aa"` → match, returns true
- [x] Two different characters `"ab"` → mismatch at first step → skip left = `""` (palindrome ✅) → true
- [x] Mismatch near the start `"deeee"` → skip left works
- [x] Mismatch near the end `"eeebd"` → skip right works
- [x] Both skips fail `"abc"` → both helpers return false → false
- [x] Long string with nested mismatch after skip → helper must run fully

---

## 🪤 Mistakes I Made / Traps

1. **Only checking one more character after mismatch** — the most common wrong approach. When you skip a character, the REST of the substring still needs to be a full palindrome. One matching step is not enough. Always call `isPalindrome()` on the entire remaining range.

2. **Returning early on the first skip without checking both options** — must check `skip left OR skip right`. Checking only one side misses valid cases like `"abca"` where skipping left fails but skipping right works (or vice versa).

3. **Trying to apply this logic recursively with 2 deletions** — this solution only works for exactly 1 deletion. If the problem said 2 deletions, this approach breaks — you'd need DP. Don't over-engineer.

4. **Confusing this with LC 125** — LC 125 has non-alphanumeric characters to skip; this problem (LC 680) is only lowercase letters — no cleaning needed. Don't add `Character.isLetterOrDigit()` logic here.

5. **Using substring() instead of index parameters** — `s.substring(left+1, right+1)` creates a new String object, costing O(n) extra space. Passing `lo` and `hi` indices to the helper is cleaner and O(1) space.

---

## 🔑 The Core Decision Tree

```
Run two pointers on full string
        │
        ▼
   Characters match?
   ├── YES → move both pointers inward, continue
   └── NO  → first (and only) mismatch found
               │
               ├── Try skip LEFT  → isPalindrome(s, left+1, right)
               │
               └── Try skip RIGHT → isPalindrome(s, left, right-1)
                         │
                         ├── Either returns true  → return true
                         └── Both return false    → return false
```

---

## 🔁 Alternative Approaches

| Approach | Time | Space | Why not chosen |
|----------|------|-------|----------------|
| Try all n deletions | `O(n²)` | `O(n)` | Too slow, creates n new strings |
| Two pointers + helper (chosen) | `O(n)` | `O(1)` | ✅ Optimal |
| Recursive with deletion count | `O(n)` | `O(n)` | Same logic, but call stack adds O(n) space |

---

## 🔗 Similar Problems

- [[LC 125 — Valid Palindrome]] — base version, no deletions allowed, clean non-alphanumeric
- [[LC 344 — Reverse String]] — two pointers from both ends, foundational
- [[LC 1216 — Valid Palindrome III]] — at most k deletions → needs DP
- [[LC 234 — Palindrome Linked List]] — palindrome check on linked list
- [[LC 647 — Palindromic Substrings]] — count all palindromic substrings

---

## 📝 Key Takeaway (1 line)
> On first mismatch, try both skips (left+1 or right-1) and run a full palindrome check on each — one character look-ahead is never enough.

---

## 🔁 Revision Log

| Date | Attempt | Solved Without Hint | Notes |
|------|---------|---------------------|-------|
| 2025-06-13 | 1st | ❌ No | Only checked one more char after mismatch — wrong |
| 2025-06-13 | 2nd | ✅ Yes | Full helper check on both skips — correct |
