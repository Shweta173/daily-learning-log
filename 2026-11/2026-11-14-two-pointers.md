# Day 3 — Two Pointers
📅 2026-11-14 · Topic: Two pointers · Level: Beginner

## The idea in plain words
Put one pointer at the **start** of the array and one at the **end**.
Look at the two values, then move **one** pointer inward based on what you see.
Each step throws away one element for good, so you finish in a single pass.

This works best when the array is **sorted** — then you always know which way to move:

| If the pair sum is… | Move… | Why |
|---|---|---|
| too small | `left` one step right | the next value on the left is bigger |
| too big | `right` one step left | the next value on the right is smaller |
| just right | stop 🎉 | found the answer |

## Problem: Two Sum II – Input Array Is Sorted (LeetCode #167)
Given a **sorted** array `numbers` and a `target`, return the **1-indexed** positions of the two numbers that add up to `target`. There is exactly one answer, and you may not use the same element twice. Use only O(1) extra space.

**Brute force** — try every pair with two nested loops → O(n²) time.

**Better** — two pointers from both ends. Every step moves one pointer, so at most n steps → O(n) time, O(1) space (no hash map needed, unlike Day 1).

```python
def two_sum_sorted(numbers, target):
    left, right = 0, len(numbers) - 1

    while left < right:
        total = numbers[left] + numbers[right]
        if total == target:
            return [left + 1, right + 1]   # problem wants 1-indexed answers
        elif total < target:
            left += 1      # need a bigger sum -> move left pointer right
        else:
            right -= 1     # need a smaller sum -> move right pointer left

    return []  # not reached when an answer is guaranteed


print(two_sum_sorted([2, 7, 11, 15], 9))      # [1, 2]
print(two_sum_sorted([1, 3, 4, 6, 8, 11], 10))  # [3, 4]
print(two_sum_sorted([-1, 0], -1))            # [1, 2]
```

## Dry run: numbers = [1, 3, 4, 6, 8, 11], target = 10
| Step | left | right | numbers[left] + numbers[right] | Action |
|---|---|---|---|---|
| 1 | 0 | 5 | 1 + 11 = 12 | too big → right = 4 |
| 2 | 0 | 4 | 1 + 8 = 9 | too small → left = 1 |
| 3 | 1 | 4 | 3 + 8 = 11 | too big → right = 3 |
| 4 | 1 | 3 | 3 + 6 = 9 | too small → left = 2 |
| 5 | 2 | 3 | 4 + 6 = 10 | found ✅ → return [3, 4] |

## Complexity
- Time: O(n) — each pointer moves inward at most n times in total
- Space: O(1) — just two index variables

## Key takeaway
When the input is **sorted** and you are looking for a **pair**, think two pointers before reaching for a hash map. The same pattern solves *Valid Palindrome*, *Container With Most Water* and *3Sum*.

## My notes
<!-- Write 1–2 lines in your own words here -->
