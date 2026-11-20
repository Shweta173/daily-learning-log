# Day 9 — Recursion
📅 2026-11-20 · Topic: Recursion · Level: Beginner

## The idea in plain words
**Recursion** means a function that **calls itself** to solve a smaller copy of the same problem. Think of Russian nesting dolls: to reach the smallest doll, you open one doll, and inside is the same problem again, just smaller.

Every recursive function needs two parts:
1. **Base case** — the smallest problem, answered directly with no more calls. Without it, the function calls itself forever (Python stops it with `RecursionError`).
2. **Recursive case** — break the problem into a smaller piece and call the function again on that piece.

Each call waits on the **call stack** until the smaller call returns, then finishes its own work.

| Part | Fibonacci example | What it does |
|---|---|---|
| Base case | `fib(0) = 0`, `fib(1) = 1` | Stops the recursion |
| Recursive case | `fib(n) = fib(n-1) + fib(n-2)` | Shrinks the problem |
| Call stack depth | up to n calls waiting | Uses O(n) memory |

## Problem: Fibonacci Number (LeetCode #509)
The Fibonacci numbers start `0, 1, 1, 2, 3, 5, 8, ...` — each number is the sum of the two before it. Given `n`, return `F(n)`.

Example: `n = 4` → `3` (because 0, 1, 1, 2, **3**).

**Brute force** — **plain recursion**: just write the formula `fib(n-1) + fib(n-2)`. It works, but it solves the same small problems again and again. `fib(5)` computes `fib(3)` twice and `fib(2)` three times. The number of calls roughly doubles with each step of n → **O(2ⁿ)**. Try `n = 40` and you will wait a long time.

**Better** — **recursion + memo** (a notebook): before computing `fib(k)`, check if you already wrote the answer down. If yes, reuse it. If no, compute it once and save it. Now each value from 0 to n is computed only once → **O(n)**.

```python
def fib_brute(n):
    # Plain recursion: correct, but repeats work
    if n <= 1:                  # base case: fib(0)=0, fib(1)=1
        return n
    return fib_brute(n - 1) + fib_brute(n - 2)   # recursive case


def fib(n, memo=None):
    # Recursion + memo: save every answer so it is computed only once
    if memo is None:
        memo = {}
    if n <= 1:                  # base case
        return n
    if n in memo:               # already solved? reuse it
        return memo[n]
    memo[n] = fib(n - 1, memo) + fib(n - 2, memo)   # solve once, save
    return memo[n]


calls = 0
def fib_count(n):
    # Same as fib_brute, but counts how many calls happen
    global calls
    calls += 1
    if n <= 1:
        return n
    return fib_count(n - 1) + fib_count(n - 2)


print(fib(4))           # 3
print(fib(10))          # 55
print(fib(0))           # 0
print(fib(50))          # 12586269025
print(fib_brute(10))    # 55
fib_count(20)
print(calls)            # 21891  (plain recursion makes this many calls for n=20!)
```

## Dry run: fib(4) with memo

| Step | Call | Base case? | In memo? | Result | memo after |
|---|---|---|---|---|---|
| 1 | fib(4) | no | no | needs fib(3) + fib(2) | {} |
| 2 | fib(3) | no | no | needs fib(2) + fib(1) | {} |
| 3 | fib(2) | no | no | needs fib(1) + fib(0) | {} |
| 4 | fib(1) | yes | — | 1 | {} |
| 5 | fib(0) | yes | — | 0 | {} |
| 6 | fib(2) finishes | — | — | 1 + 0 = 1 | {2: 1} |
| 7 | fib(1) | yes | — | 1 | {2: 1} |
| 8 | fib(3) finishes | — | — | 1 + 1 = 2 | {2: 1, 3: 2} |
| 9 | fib(2) | no | **yes** ✅ | 1 (reused, no new calls) | {2: 1, 3: 2} |
| 10 | fib(4) finishes | — | — | 2 + 1 = 3 | {2: 1, 3: 2, 4: 3} |

Answer: **3**. Notice step 9: without the memo, fib(2) would have been computed again from scratch.

## Complexity
- Plain recursion: Time O(2ⁿ) — calls double at each level; Space O(n) — the call stack is at most n deep
- Recursion + memo: Time O(n) — each fib(k) is solved once; Space O(n) — memo dictionary + call stack

## Key takeaway
Every recursive function needs a **base case** (when to stop) and a **recursive case** (how to shrink the problem). If the same smaller problems show up again and again, **save their answers in a memo** — that one change turns O(2ⁿ) into O(n), and it is the first step toward dynamic programming.

## My notes
<!-- Write 1–2 lines in your own words here -->
