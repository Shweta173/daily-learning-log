# Day 8 — Binary search
📅 2026-11-19 · Topic: Binary search · Level: Beginner

## The idea in plain words
**Binary search** finds an item in a **sorted** list by cutting the search area in half every step — like looking up a word in a dictionary. You open the middle page: if your word comes earlier, you throw away the second half; if it comes later, you throw away the first half. Repeat until you find it (or nothing is left).

Because the list halves every step, even 1,000,000 items need only about 20 checks. That is **O(log n)**.

The one rule: the list **must be sorted** (that's why Day 7 came first!).

| List size n | Linear search (check one by one) | Binary search (halve each time) |
|---|---|---|
| 8 | up to 8 checks | 3 checks |
| 1,000 | up to 1,000 | 10 |
| 1,000,000 | up to 1,000,000 | 20 |

## Problem: Binary Search (LeetCode #704)
Given a sorted array of integers `nums` (ascending) and a `target`, return the **index** of `target`, or `-1` if it is not there. You must write an O(log n) solution.

Example: `nums = [-1, 0, 3, 5, 9, 12]`, `target = 9` → `4`.

**Brute force** — **linear search**: check every item from left to right until you find the target. Easy, but O(n) — it ignores the fact that the list is sorted.

**Better** — **binary search**: keep two pointers, `left` and `right`, marking the part of the list that could still hold the target. Look at the middle item:
- equal to target → found it, return `mid`
- smaller than target → target must be on the right, so `left = mid + 1`
- bigger than target → target must be on the left, so `right = mid - 1`

Stop when `left > right` (nothing left to search) and return `-1`.

```python
def search_brute(nums, target):
    # Linear search: check every item one by one
    for i, x in enumerate(nums):
        if x == target:
            return i
    return -1


def search(nums, target):
    # Binary search: keep halving the range [left, right]
    left, right = 0, len(nums) - 1
    while left <= right:                # range is not empty yet
        mid = (left + right) // 2       # middle index
        if nums[mid] == target:
            return mid                  # found it
        elif nums[mid] < target:
            left = mid + 1              # target is in the right half
        else:
            right = mid - 1             # target is in the left half
    return -1                           # range became empty → not found


print(search([-1, 0, 3, 5, 9, 12], 9))      # 4
print(search([-1, 0, 3, 5, 9, 12], 2))      # -1
print(search([5], 5))                       # 0
print(search([], 3))                        # -1
print(search_brute([-1, 0, 3, 5, 9, 12], 9))  # 4
```

## Dry run: nums = [-1, 0, 3, 5, 9, 12], target = 9

| Step | left | right | mid | nums[mid] | Compare | Action |
|---|---|---|---|---|---|---|
| 1 | 0 | 5 | 2 | 3 | 3 < 9 | left = 3 |
| 2 | 3 | 5 | 4 | 9 | 9 == 9 | return 4 ✅ |

Answer: **4** (found in 2 checks instead of 5)

**Not found case: target = 2**

| Step | left | right | mid | nums[mid] | Compare | Action |
|---|---|---|---|---|---|---|
| 1 | 0 | 5 | 2 | 3 | 3 > 2 | right = 1 |
| 2 | 0 | 1 | 0 | -1 | -1 < 2 | left = 1 |
| 3 | 1 | 1 | 1 | 0 | 0 < 2 | left = 2 |
| 4 | 2 | 1 | — | — | left > right | return -1 |

## Complexity
- Linear search: Time O(n), Space O(1)
- Binary search: Time O(log n) — the range halves every step; Space O(1)

## Key takeaway
If the data is **sorted**, don't scan it — **halve it**. Keep `left` and `right`, check the middle, and throw away the half that can't contain the answer. Watch the details: loop while `left <= right`, and move to `mid + 1` / `mid - 1` so the range always shrinks.

## My notes
<!-- Write 1–2 lines in your own words here -->
