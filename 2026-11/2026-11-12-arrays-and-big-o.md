# Day 1 — Arrays & Big-O Basics
📅 2026-11-12 · Topic: Arrays · Level: Beginner

## The idea in plain words
An **array** stores items next to each other in memory, so you can jump to any index instantly.
**Big-O** describes how the work grows as the input (n) grows.

| Operation on an array | Time |
|---|---|
| Read `arr[i]` | O(1) |
| Search for a value (unsorted) | O(n) |
| Insert / delete at the end | O(1) (amortized) |
| Insert / delete at the start or middle | O(n) — everything shifts |

## Problem: Two Sum (LeetCode #1)
Given `nums` and `target`, return the indices of two numbers that add up to `target`.

**Brute force** — check every pair → O(n²) time.

**Better** — remember what you've seen in a hash map. For each number, check if `target - num` was already seen → O(n) time, O(n) space.

```python
def two_sum(nums, target):
    seen = {}                      # value -> index
    for i, num in enumerate(nums):
        need = target - num
        if need in seen:
            return [seen[need], i]
        seen[num] = i
    return []

print(two_sum([2, 7, 11, 15], 9))  # [0, 1]
```

## Dry run: nums = [2, 7, 11, 15], target = 9
| i | num | need | seen before | result |
|---|---|---|---|---|
| 0 | 2 | 7 | {} | store 2 |
| 1 | 7 | 2 | {2: 0} | found → [0, 1] |

## Key takeaway
Trading a little extra memory (a hash map) for speed is one of the most common DSA tricks: O(n²) → O(n).

## My notes
<!-- Write 1–2 lines in your own words here -->
