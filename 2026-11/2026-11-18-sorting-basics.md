# Day 7 — Sorting basics
📅 2026-11-18 · Topic: Sorting basics · Level: Beginner

## The idea in plain words
**Sorting** means putting items in order (smallest to biggest, A to Z). Lots of problems get much easier once the data is sorted — two pointers (Day 3) and binary search (coming soon) both need sorted input.

There are two families of simple sorts:
1. **Slow but easy** — compare neighbours and swap them (bubble sort, insertion sort). About n × n steps.
2. **Fast "divide and conquer"** — cut the list in half, sort each half, then **merge** the two sorted halves (merge sort). About n × log n steps.

In real code you just call Python's built-in `sorted(nums)` or `nums.sort()` (it is O(n log n)). But knowing how sorting works helps you understand *why* it costs O(n log n), and the **merge** step shows up again in many problems.

| Sort | Time | Extra space | Stable?* |
|---|---|---|---|
| Bubble sort | O(n²) | O(1) | Yes |
| Insertion sort | O(n²) (fast if almost sorted) | O(1) | Yes |
| Merge sort | O(n log n) | O(n) | Yes |
| Python `sorted()` | O(n log n) | O(n) | Yes |

*Stable = equal items keep their original order.

## Problem: Sort an Array (LeetCode #912)
Given an array of integers `nums`, return it sorted in ascending order — **without** using the built-in sort, in O(n log n) time.

Example: `[5, 2, 3, 1]` → `[1, 2, 3, 5]`.

**Brute force** — **bubble sort**: walk through the list, and whenever two neighbours are in the wrong order, swap them. After each pass the biggest remaining item "bubbles" to the end. Simple, but O(n²) — far too slow for 50,000 numbers.

**Better** — **merge sort**: split the list into two halves, sort each half (by calling the same function again), then merge the two sorted halves using two pointers. Splitting in half again and again gives about log n levels, and each level does O(n) merging work → **O(n log n)**.

```python
def sort_array_brute(nums):
    # Bubble sort: keep swapping neighbours that are in the wrong order
    nums = nums[:]                      # work on a copy
    n = len(nums)
    for i in range(n):
        for j in range(n - 1 - i):      # the last i items are already in place
            if nums[j] > nums[j + 1]:
                nums[j], nums[j + 1] = nums[j + 1], nums[j]
    return nums


def merge(left, right):
    # Join two already-sorted lists into one sorted list
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:         # "<=" keeps equal items in order (stable)
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])             # add whatever is left over
    result.extend(right[j:])
    return result


def sort_array(nums):
    # Merge sort: split in half, sort each half, then merge
    if len(nums) <= 1:                  # 0 or 1 item is already sorted
        return nums
    mid = len(nums) // 2
    left = sort_array(nums[:mid])
    right = sort_array(nums[mid:])
    return merge(left, right)


print(sort_array([5, 2, 3, 1]))            # [1, 2, 3, 5]
print(sort_array([5, 1, 1, 2, 0, 0]))      # [0, 0, 1, 1, 2, 5]
print(sort_array([]))                      # []
print(sort_array_brute([3, -1, 2]))        # [-1, 2, 3]
print(merge([1, 4, 6], [2, 3, 7]))         # [1, 2, 3, 4, 6, 7]
```

## Dry run: nums = [5, 2, 3, 1]

**Splitting (going down):**

| Level | Pieces |
|---|---|
| 0 | `[5, 2, 3, 1]` |
| 1 | `[5, 2]` and `[3, 1]` |
| 2 | `[5]` `[2]` `[3]` `[1]` (size 1 → already sorted) |

**Merging (coming back up):**

| Step | left | right | Compare & pick | Result |
|---|---|---|---|---|
| 1 | `[5]` | `[2]` | 2 < 5 → take 2, then add 5 | `[2, 5]` |
| 2 | `[3]` | `[1]` | 1 < 3 → take 1, then add 3 | `[1, 3]` |
| 3 | `[2, 5]` | `[1, 3]` | 1 < 2 → take 1 | `[1]` |
| 4 | `[2, 5]` | `[3]` | 2 < 3 → take 2 | `[1, 2]` |
| 5 | `[5]` | `[3]` | 3 < 5 → take 3 | `[1, 2, 3]` |
| 6 | `[5]` | `[]` | right is empty → add the rest | `[1, 2, 3, 5]` |

Answer: **[1, 2, 3, 5]**

## Complexity
- Bubble sort: Time O(n²), Space O(1)
- Merge sort: Time O(n log n) — log n levels of splitting, O(n) merge work per level; Space O(n) for the merged lists

## Key takeaway
Sorting by swapping neighbours is O(n²); **divide and conquer** (split in half, sort each half, merge) brings it down to O(n log n). The **merge** step is just two pointers on two sorted lists. In real code use `sorted()`, and remember: sorting first often unlocks two pointers or binary search.

## My notes
<!-- Write 1–2 lines in your own words here -->
