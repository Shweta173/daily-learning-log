# Day 12 — Queues & deques
📅 2026-11-25 · Topic: Queues & deques · Level: Beginner

## The idea in plain words
A **queue** is a line of people at a ticket counter. New people join at the **back**, and the person at the **front** is served first. The first one in is the first one out. This rule is called **FIFO** — *First In, First Out* (the opposite of yesterday's stack, which was LIFO).

A **deque** (say "deck") is a *double-ended queue*: you can add or remove from **both** ends quickly. In Python, use `collections.deque`.

Why not just a list? `list.pop(0)` has to shift every other item one step left, so it is **O(n)**. A deque removes from the front in **O(1)**.

| Operation | What it does | Python (`d = deque()`) | Time |
|---|---|---|---|
| enqueue | add to the back | `d.append(x)` | O(1) |
| dequeue | remove from the front | `d.popleft()` | O(1) |
| peek front | look at the oldest item | `d[0]` | O(1) |
| add to front | (deque only) | `d.appendleft(x)` | O(1) |
| remove from back | (deque only) | `d.pop()` | O(1) |
| size | how many items | `len(d)` | O(1) |

Queues are perfect when things must be handled **in the order they arrived** — print jobs, tasks waiting for a CPU, and (soon) Breadth-First Search on graphs.

## Problem: Number of Recent Calls (LeetCode #933)
Build a `RecentCounter` class. Each call `ping(t)` adds a new request at time `t` milliseconds and returns how many requests happened in the last 3000 ms, i.e. in the range `[t - 3000, t]` (both ends included). Every `t` is larger than the one before.

Example: `ping(1) → 1`, `ping(100) → 2`, `ping(3001) → 3`, `ping(3002) → 3` (at 3002 the window is `[2, 3002]`, so the ping at time 1 no longer counts).

**Brute force** — **keep every ping in a list** and, on each call, scan the whole list counting the ones inside the window. Simple, but the list grows forever and each call is **O(n)** where n is the total number of pings so far.

**Better** — **use a queue**. Old pings fall out of the window from the *front* (they are the oldest), and new pings arrive at the *back*. So:
- `append(t)` the new ping at the back.
- While the front ping is older than `t - 3000`, `popleft()` it — it will never count again because times only increase.
- The queue now holds exactly the pings in the window, so the answer is `len(queue)`.

```python
from collections import deque


class RecentCounterBrute:
    # Brute force: keep every ping forever, count the recent ones each time
    def __init__(self):
        self.pings = []

    def ping(self, t):
        self.pings.append(t)
        count = 0
        for p in self.pings:                  # scan ALL pings ever made
            if t - 3000 <= p <= t:
                count += 1
        return count


class RecentCounter:
    # Better: a queue that only holds pings from the last 3000 ms
    def __init__(self):
        self.q = deque()

    def ping(self, t):
        self.q.append(t)                      # newest ping joins the back
        while self.q[0] < t - 3000:           # oldest ping is too old?
            self.q.popleft()                  # remove it from the front
        return len(self.q)                    # everything left is recent


rc = RecentCounter()
print(rc.ping(1))       # 1
print(rc.ping(100))     # 2
print(rc.ping(3001))    # 3
print(rc.ping(3002))    # 3
print(rc.ping(7000))    # 1

brute = RecentCounterBrute()
print([brute.ping(t) for t in [1, 100, 3001, 3002]])  # [1, 2, 3, 3]

d = deque([2, 3])
d.appendleft(1)         # add to the front
d.append(4)             # add to the back
print(list(d))          # [1, 2, 3, 4]
print(d.popleft(), d.pop())  # 1 4
```

## Dry run: pings at `1, 100, 3001, 3002, 7000`

| Call | Window `[t-3000, t]` | After append | Removed from front | Queue after | Return |
|---|---|---|---|---|---|
| `ping(1)` | `[-2999, 1]` | `[1]` | — | `[1]` | 1 |
| `ping(100)` | `[-2900, 100]` | `[1, 100]` | — | `[1, 100]` | 2 |
| `ping(3001)` | `[1, 3001]` | `[1, 100, 3001]` | — (1 is still inside) | `[1, 100, 3001]` | 3 |
| `ping(3002)` | `[2, 3002]` | `[1, 100, 3001, 3002]` | `1` | `[100, 3001, 3002]` | 3 |
| `ping(7000)` | `[4000, 7000]` | `[100, 3001, 3002, 7000]` | `100`, `3001`, `3002` | `[7000]` | 1 |

Notice we only ever look at the **front** to throw things out — never the middle. That's what makes it fast.

## Complexity
- Brute force (list scan): Time O(n) per `ping` (n = all pings so far); Space O(n) — nothing is ever thrown away
- Queue: Time **O(1) amortized** per `ping` — each ping is appended once and popped at most once; Space O(W), where W = pings inside one 3000 ms window

## Key takeaway
When items must be handled **in arrival order**, or you need to **drop the oldest items** as a window moves forward, reach for a queue — and in Python that means `collections.deque`, not `list.pop(0)`. Stacks take from the newest end; queues take from the oldest end.

## My notes
<!-- Write 1–2 lines in your own words here -->
