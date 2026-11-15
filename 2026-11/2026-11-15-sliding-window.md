# Day 4 — Sliding Window
📅 2026-11-15 · Topic: Sliding window · Level: Beginner

## The idea in plain words
A **window** is a continuous piece of an array or string, marked by two indexes: `left` (start) and `right` (end).
Instead of checking every possible piece from scratch, you **slide** the window along:

- move `right` forward to **grow** the window (add one new item)
- move `left` forward to **shrink** it when the window breaks a rule

Both pointers only ever move forward, so each item is added once and removed at most once → a single pass.

| Window type | Size | Example question |
|---|---|---|
| Fixed | always `k` | max sum of any `k` days in a row |
| Variable | grows and shrinks | longest piece with no repeated letters |

## Problem: Longest Substring Without Repeating Characters (LeetCode #3)
Given a string `s`, return the length of the longest **substring** (continuous piece) that has no repeated characters.

**Brute force** — check every substring and test if its letters are unique → O(n²) substrings, O(n³) with the check.

**Better** — variable sliding window + a hash map that remembers where each character was last seen. When the new character is already inside the window, jump `left` just past its old position → O(n) time.

```python
def length_of_longest_substring(s):
    last_seen = {}   # char -> last index where we saw it
    left = 0         # start of the current window
    best = 0

    for right, ch in enumerate(s):
        # If ch is already inside the window, shrink: jump left past its old spot
        if ch in last_seen and last_seen[ch] >= left:
            left = last_seen[ch] + 1
        last_seen[ch] = right                 # remember newest position of ch
        best = max(best, right - left + 1)    # window size = right - left + 1

    return best


print(length_of_longest_substring("abcabcbb"))  # 3  ("abc")
print(length_of_longest_substring("bbbbb"))     # 1  ("b")
print(length_of_longest_substring("pwwkew"))    # 3  ("wke")
print(length_of_longest_substring(""))          # 0
```

## Dry run: s = "pwwkew"
| right | ch | Repeat inside window? | left | Window | best |
|---|---|---|---|---|---|
| 0 | p | no | 0 | "p" | 1 |
| 1 | w | no | 0 | "pw" | 2 |
| 2 | w | yes (at 1) → left = 2 | 2 | "w" | 2 |
| 3 | k | no | 2 | "wk" | 2 |
| 4 | e | no | 2 | "wke" | 3 |
| 5 | w | yes (at 2) → left = 3 | 3 | "kew" | 3 |

Answer: **3**

## Complexity
- Time: O(n) — `right` visits each character once, and `left` only moves forward
- Space: O(k) — the map holds at most one entry per distinct character (k ≤ size of the alphabet)

## Key takeaway
If a question says **"longest / shortest / max / min continuous subarray or substring"**, think sliding window: grow with `right`, shrink with `left` when a rule breaks. It is two pointers (Day 3) moving in the **same** direction instead of towards each other.

## My notes
<!-- Write 1–2 lines in your own words here -->
