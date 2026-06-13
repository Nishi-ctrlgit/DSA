---
tags: [leetcode, two-pointers, strings, easy, premium, meta]
lc_number: 408
title: "Valid Word Abbreviation"
difficulty: "Easy"
pattern: "Two Pointers"
date_solved: 2025-06-13
attempts: 2
solved_without_hint: false
company: "Meta (asked frequently)"
---

# LC 408 — Valid Word Abbreviation

🔗 [Problem Link](https://leetcode.com/problems/valid-word-abbreviation/)
🏷️ Difficulty: `Easy` | Pattern: `[[Two Pointers]]` | 🔒 Premium | 🏢 Meta Favourite

---

## 📋 Problem Summary

> A string can be abbreviated by replacing any number of non-adjacent substrings with their lengths.
> Given a string `word` and an abbreviation `abbr`, return `true` if `abbr` is a **valid abbreviation** of `word`.

**Input:** `String word` — the original word | `String abbr` — the abbreviation
**Output:** `boolean` — true if abbr is a valid abbreviation of word
**Constraint to note:**
- A number in `abbr` represents how many characters to skip in `word`
- Numbers **cannot have leading zeros** (e.g. `"01"` is invalid)
- The entire `word` must be consumed — partial matches are false

---

## 🔍 Examples

```
Input:  word = "internationalization", abbr = "i12iz4n"
Output: true
Why:    i → 'i'
        12 → skip 12 chars ("nternationali")
        i  → 'i' matches
        z  → 'z' matches
        4  → skip 4 chars ("atio")
        n  → 'n' matches
        Total chars consumed in word = 1+12+1+1+4+1 = 20 = word.length() ✅
```

```
Input:  word = "apple", abbr = "a3e"
Output: true
Why:    a → 'a' matches
        3 → skip 3 chars ("ppl")
        e → 'e' matches ✅
```

```
Input:  word = "apple", abbr = "a2e"
Output: false  
Why:    a → 'a' matches
        2 → skip 2 chars ("pp")
        e → word[3] = 'l', abbr has 'e' → mismatch ❌
```

```
Input:  word = "apple", abbr = "01"     ← Edge case: leading zero
Output: false
Why:    '0' is a leading zero — invalid abbreviation by definition
```

```
Input:  word = "apple", abbr = "5"      ← Edge case: entire word as number
Output: true
Why:    5 → skip all 5 chars, both pointers reach end simultaneously ✅
```

```
Input:  word = "apple", abbr = "6"      ← Edge case: number exceeds word length
Output: false
Why:    Skipping 6 chars overshoots word.length() = 5 → false
```

---

## 💭 My Thought Process

### Brute Force Idea:
Parse `abbr` to generate all possible expanded strings and check if any equals `word`. Way too slow and complex — exponential in nature.

### Optimization Insight:
Use **two pointers** — one on `word` (`i`) and one on `abbr` (`j`). When `abbr[j]` is a letter, it must match `word[i]` directly. When `abbr[j]` is a digit, parse the full number (could be multi-digit like `12`), then advance `i` by that number. At the end, both pointers must have consumed their entire strings simultaneously.

### Final Approach:
- `i` walks `word`, `j` walks `abbr`
- If `abbr[j]` is a digit → check for leading zero → parse full number → advance `i` by that number
- If `abbr[j]` is a letter → must match `word[i]` → advance both
- At the end: return `i == word.length() && j == abbr.length()`

---

## ☕ Java Solution

### Optimal — Two Pointers, O(n)
```java
class Solution {
    public boolean validWordAbbreviation(String word, String abbr) {
        int i = 0; // pointer on word
        int j = 0; // pointer on abbr

        while (i < word.length() && j < abbr.length()) {
            char c = abbr.charAt(j);

            if (Character.isDigit(c)) {
                // leading zero check — immediately invalid
                if (c == '0') return false;

                // parse the full number (could be multi-digit: "12", "100")
                int num = 0;
                while (j < abbr.length() && Character.isDigit(abbr.charAt(j))) {
                    num = num * 10 + (abbr.charAt(j) - '0');
                    j++;
                }

                // skip 'num' characters in word
                i += num;

            } else {
                // letter — must match exactly
                if (word.charAt(i) != abbr.charAt(j)) return false;
                i++;
                j++;
            }
        }

        // both pointers must reach the end of their strings together
        return i == word.length() && j == abbr.length();
    }
}
```

---

## 🧩 Dry Run — Step by Step

```
word = "internationalization"  (length = 20)
abbr = "i12iz4n"

j=0  c='i'  → letter → word[0]='i' ✅  → i=1,  j=1
j=1  c='1'  → digit, not '0' → parse: '1','2' → num=12 → i=1+12=13, j=3
j=3  c='i'  → letter → word[13]='i' ✅  → i=14, j=4
j=4  c='z'  → letter → word[14]='z' ✅  → i=15, j=5
j=5  c='4'  → digit, not '0' → parse: '4' → num=4 → i=15+4=19, j=6
j=6  c='n'  → letter → word[19]='n' ✅  → i=20, j=7

Loop ends: i=20 == word.length()=20 ✅
           j=7  == abbr.length()=7  ✅
Return: true ✅
```

---

## ⏱️ Complexity

| | Complexity | Reason |
|--|-----------|--------|
| Time | `O(n)` | Each character in word and abbr visited at most once |
| Space | `O(1)` | Only two pointers + one integer for num |

---

## ⚠️ Edge Cases Considered

- [x] Leading zero in abbr `"01"` → immediately return false on `c == '0'`
- [x] Number overshoots word length `abbr="6"` for `word="apple"` → `i` exceeds `word.length()`, final check fails
- [x] Entire word abbreviated as one number `abbr="5"` → valid if matches length exactly
- [x] Multi-digit numbers `"i12iz4n"` → inner while loop parses full number correctly
- [x] Number `"0"` alone → leading zero → false
- [x] `abbr` longer than word (extra letters) → final `j == abbr.length()` check catches it
- [x] `abbr` shorter than word (doesn't consume all) → final `i == word.length()` check catches it
- [x] Empty abbr `""` and non-empty word → false (i never moves)
- [x] Both empty `""`, `""` → both pointers at 0 == length 0 → true

---

## 🪤 Mistakes I Made / Traps

1. **Not checking for leading zeros** — `"01"` is invalid even though `1` would be valid. The check `if (c == '0') return false` must be the very first thing inside the digit branch, before parsing.

2. **Not handling multi-digit numbers** — parsing only one digit at a time (`num = abbr.charAt(j) - '0'`) breaks for `"i12n"`. Need the inner while loop to accumulate: `num = num * 10 + digit`.

3. **Wrong end condition — checking only one pointer** — `return i == word.length()` is wrong. Both `i` and `j` must reach the end. A short `abbr` that doesn't consume all of `word` would falsely return true otherwise.

4. **Using `j < abbr.length()` only in outer while** — the inner digit-parsing while also needs `j < abbr.length()` as a guard or it throws `StringIndexOutOfBoundsException`.

5. **Confusing this with LC 125** — no need to clean characters here. Every character in both strings is meaningful — letters must match exactly, digits are skip counts.

---

## 🔑 Core Decision at Each Step

```
abbr[j] is a digit?
├── YES
│    ├── Is it '0'?  → return false immediately (leading zero)
│    └── Parse full number (multi-digit) → advance word pointer by that number
└── NO (it's a letter)
     ├── Does it match word[i]? → advance both pointers
     └── Mismatch?             → return false

After loop:
     i == word.length() AND j == abbr.length() → true
     Anything else                              → false
```

---

## 🔁 Alternative Approaches

| Approach | Time | Space | Why not chosen |
|----------|------|-------|----------------|
| Expand abbr to full string, compare | `O(n)` | `O(n)` | Extra string allocation, unnecessary |
| Regex parsing | `O(n)` | `O(n)` | Overkill, harder to read, slower in practice |
| Two pointers (chosen) | `O(n)` | `O(1)` | ✅ Optimal, clean, interview standard |

---

## 🔗 Similar Problems

- [[LC 125 — Valid Palindrome]] — two pointers on string, foundational
- [[LC 680 — Valid Palindrome II]] — two pointers with one skip decision
- [[LC 344 — Reverse String]] — two pointers, foundational
- [[LC 271 — Encode and Decode Strings]] — string parsing with length encoding
- [[LC 443 — String Compression]] — compress string using counts, reverse idea

---

## 📝 Key Takeaway (1 line)
> Two pointers — one on word, one on abbr — parse multi-digit numbers with an inner while loop, reject leading zeros immediately, and verify BOTH pointers hit the end.

---

## 🏢 Interview Context

> This is a **Meta favourite** — appears frequently in Meta phone screens and onsite rounds.
> The leading zero trap and multi-digit number parsing are the two things interviewers specifically watch for.
> Always walk through the dry run out loud — it shows structured thinking.

---

## 🔁 Revision Log

| Date | Attempt | Solved Without Hint | Notes |
|------|---------|---------------------|-------|
| 2025-06-13 | 1st | ❌ No | Missed leading zero check and single-digit parse only |
| 2025-06-13 | 2nd | ✅ Yes | Inner while for multi-digit + leading zero guard |
