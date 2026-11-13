# Day 2 — Prefix Sums
📅 2026-11-13 · Topic: Prefix sums · Level: Beginner

## The idea in plain words
A **prefix sum** array stores the running total up to each position.
Once you build it, the sum of **any** range `i..j` is just one subtraction:

```
sum(i..j) = prefix[j + 1] - prefix[i]
```

We keep `prefix[0] = 0` so ranges that start at index 0 work too.

| nums      |   | 3 | 1 | 4 | 1 | 5 |
|-----------|---|---|---|---|---|---|
| prefix    | 0 | 3 | 4 | 8 | 9 | 14 |

Sum of `nums[1..3]` = 1 + 4 + 1 = 6 → `prefix[4] - prefix[1]` = 9 − 3 = 6 ✅

## Problem: Range Sum Query – Immutable (LeetCode #303)
Given an array `nums`, answer many queries `sum_range(left, right)` = sum of elements from `left` to `right` (inclusive).

**Brute force** — loop from `left` to `right` for every query → O(n) per query.

**Better** — build the prefix array once in O(n), then each query is O(1).

```python
class NumArray:
    def __init__(self, nums):
        # prefix[k] = sum of the first k numbers
        self.prefix = [0]
        for num in nums:
            self.prefix.append(self.prefix[-1] + num)

    def sum_range(self, left, right):
        return self.prefix[right + 1] - self.prefix[left]


arr = NumArray([-2, 0, 3, -5, 2, -1])
print(arr.sum_range(0, 2))  # 1
print(arr.sum_range(2, 5))  # -1
print(arr.sum_range(0, 5))  # -3
```

## Dry run: nums = [-2, 0, 3, -5, 2, -1]
| k | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|---|
| prefix[k] | 0 | -2 | -2 | 1 | -4 | -2 | -3 |

| Query | Formula | Answer |
|---|---|---|
| (0, 2) | prefix[3] − prefix[0] = 1 − 0 | 1 |
| (2, 5) | prefix[6] − prefix[2] = −3 − (−2) | −1 |
| (0, 5) | prefix[6] − prefix[0] = −3 − 0 | −3 |

## Complexity
- Build: O(n) time, O(n) space
- Each query: O(1) time

## Key takeaway
If a problem asks for **sums of subarrays many times**, do the work once up front with a prefix array. This same idea powers harder problems like *Subarray Sum Equals K* (prefix sum + hash map).

## My notes
<!-- Write 1–2 lines in your own words here -->
