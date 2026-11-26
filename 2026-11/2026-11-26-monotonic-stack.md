# Day 13 — Monotonic stack
📅 2026-11-26 · Topic: Monotonic stack · Level: Beginner

## The idea in plain words
A **monotonic stack** is just a normal stack (Day 11) with one extra rule: the items inside always stay **in order** — either always going down from bottom to top, or always going up.

Think of people standing in a line, all facing forward, where each person can only see over people **shorter** than them. When a tall person arrives, every shorter person in front of the stack's top gets "blocked" — we pop them off. Before popping, we note: *"the first taller person after you is this new one."*

That's the whole trick: when you pop an item, you have just found its **next greater** (or next smaller) element.

| Stack type | Order (bottom → top) | Pop when the new item is… | Finds |
|---|---|---|---|
| Decreasing | big → small | **bigger** than the top | next **greater** element |
| Increasing | small → big | **smaller** than the top | next **smaller** element |

Use it whenever a question sounds like *"for each item, find the next/previous one that is bigger/smaller."*

## Problem: Daily Temperatures (LeetCode #739)
Given a list `temps` of daily temperatures, return a list `answer` where `answer[i]` is how many days you must wait after day `i` to get a **warmer** temperature. If no warmer day ever comes, put `0`.

Example: `temps = [73, 74, 75, 71, 69, 72, 76, 73]` → `[1, 1, 4, 2, 1, 1, 0, 0]`
(Day 2 is 75°; the next warmer day is day 6 at 76°, so it waits `6 - 2 = 4` days.)

**Brute force** — **for every day, walk forward** one day at a time until you find a warmer one. Easy to write, but for a list that keeps getting colder (like `[90, 80, 70, ...]`) every day scans to the end, so it is **O(n²)**.

**Better** — **use a decreasing monotonic stack of indexes**. The stack holds days that are *still waiting* for a warmer day, and their temperatures go down from bottom to top.
- For each new day `i` with temperature `t`:
  - While the day on top of the stack is **colder** than `t`, pop it — today is its answer: `answer[j] = i - j`.
  - Push `i` onto the stack (today is now waiting too).
- Days still in the stack at the end never got warmer, so they keep `0`.

```python
def daily_temperatures_brute(temps):
    # Brute force: for each day, walk forward until we find a warmer day
    n = len(temps)
    answer = [0] * n
    for i in range(n):
        for j in range(i + 1, n):
            if temps[j] > temps[i]:
                answer[i] = j - i             # days waited
                break
    return answer


def daily_temperatures(temps):
    # Better: a monotonic stack of indexes whose temps go DOWN from bottom to top
    answer = [0] * len(temps)
    stack = []                                # indexes still waiting for a warmer day
    for i, t in enumerate(temps):
        while stack and temps[stack[-1]] < t: # today is warmer than the top day?
            j = stack.pop()                   # day j finally found its answer
            answer[j] = i - j
        stack.append(i)                       # today now waits too
    return answer                             # days left in stack keep 0


print(daily_temperatures([73, 74, 75, 71, 69, 72, 76, 73]))        # [1, 1, 4, 2, 1, 1, 0, 0]
print(daily_temperatures_brute([73, 74, 75, 71, 69, 72, 76, 73]))  # [1, 1, 4, 2, 1, 1, 0, 0]
print(daily_temperatures([30, 40, 50, 60]))                        # [1, 1, 1, 0]
print(daily_temperatures([60, 50, 40]))                            # [0, 0, 0]
```

## Dry run: `temps = [73, 74, 75, 71, 69, 72, 76, 73]`
Stack shows indexes, with their temperature in brackets.

| i | temp | Popped (answer set) | Stack after push | answer so far |
|---|---|---|---|---|
| 0 | 73 | — | `[0(73)]` | `[0,0,0,0,0,0,0,0]` |
| 1 | 74 | `0` → ans[0] = 1 | `[1(74)]` | `[1,0,0,0,0,0,0,0]` |
| 2 | 75 | `1` → ans[1] = 1 | `[2(75)]` | `[1,1,0,0,0,0,0,0]` |
| 3 | 71 | — (75 is warmer) | `[2(75), 3(71)]` | `[1,1,0,0,0,0,0,0]` |
| 4 | 69 | — (71 is warmer) | `[2(75), 3(71), 4(69)]` | `[1,1,0,0,0,0,0,0]` |
| 5 | 72 | `4` → ans[4] = 1, `3` → ans[3] = 2 | `[2(75), 5(72)]` | `[1,1,0,2,1,0,0,0]` |
| 6 | 76 | `5` → ans[5] = 1, `2` → ans[2] = 4 | `[6(76)]` | `[1,1,4,2,1,1,0,0]` |
| 7 | 73 | — (76 is warmer) | `[6(76), 7(73)]` | `[1,1,4,2,1,1,0,0]` |

At the end, days 6 and 7 are still in the stack — no warmer day came — so they stay `0`. Notice the temperatures in the stack always go **down** from bottom to top.

## Complexity
- Brute force (walk forward): Time O(n²); Space O(1) extra (besides the answer list)
- Monotonic stack: Time **O(n)** — each index is pushed once and popped at most once, so the inner `while` runs at most n times in total; Space O(n) for the stack

## Key takeaway
When a problem asks *"for each element, find the next greater (or smaller) one"*, keep a stack of the items still waiting, in sorted order. Each new item pops everyone it "beats", and the moment an item is popped is the moment its answer is found. Store **indexes**, not values, so you can measure distances.

## My notes
<!-- Write 1–2 lines in your own words here -->
