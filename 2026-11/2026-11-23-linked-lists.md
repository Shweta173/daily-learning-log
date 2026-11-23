# Day 10 — Linked lists
📅 2026-11-23 · Topic: Linked lists · Level: Beginner

## The idea in plain words
A **linked list** is a chain of small boxes called **nodes**. Each node holds two things: a **value** and a **pointer** (`next`) to the next node. The last node points to `None`, which means "the chain ends here". We only keep a reference to the first node, called the **head**.

Think of a treasure hunt: each clue tells you where the next clue is. You cannot jump to clue #5 directly — you must follow the clues one by one from the start.

`1 → 2 → 3 → None`

| Operation | Array (Python list) | Linked list |
|---|---|---|
| Get item at index i | O(1) — jump straight there | O(n) — walk from the head |
| Insert / delete at the front | O(n) — shift everything | O(1) — just change one pointer |
| Insert / delete in the middle (node known) | O(n) — shift items | O(1) — rewire pointers |
| Memory | one solid block | nodes spread out + extra pointer each |

The main skill with linked lists is **changing pointers carefully** without losing the rest of the chain.

## Problem: Reverse Linked List (LeetCode #206)
Given the `head` of a singly linked list, reverse the list and return the new head.

Example: `1 → 2 → 3 → 4 → 5` → `5 → 4 → 3 → 2 → 1`

**Brute force** — **copy values into an array**: walk the list, put every value into a Python list, then build a brand-new linked list from the values in reverse order. It works and is easy to think about, but it uses **O(n) extra memory** for the array and the new nodes.

**Better** — **reverse the pointers in place**: walk the list once and flip each node's `next` arrow to point backwards. We use three pointers:
- `prev` — the part already reversed (starts as `None`)
- `curr` — the node we are fixing now
- `nxt` — saves the rest of the list **before** we break the link

At each step: save `nxt`, flip `curr.next` to `prev`, then move `prev` and `curr` one step forward. When `curr` becomes `None`, `prev` is the new head. Only **O(1) extra memory**.

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def build(values):
    # Helper: turn a Python list into a linked list, return the head
    head = None
    for v in reversed(values):
        head = ListNode(v, head)
    return head


def to_list(head):
    # Helper: turn a linked list back into a Python list (for printing)
    out = []
    while head:
        out.append(head.val)
        head = head.next
    return out


def reverse_brute(head):
    # Brute force: copy values into an array, build a new reversed list
    values = []
    while head:
        values.append(head.val)
        head = head.next
    return build(values[::-1])      # O(n) extra memory


def reverse_list(head):
    # Better: flip each pointer in place with prev / curr / nxt
    prev = None
    curr = head
    while curr:
        nxt = curr.next         # 1. save the rest of the list
        curr.next = prev        # 2. flip the arrow backwards
        prev = curr             # 3. move prev forward
        curr = nxt              # 4. move curr forward
    return prev                 # prev is the new head


print(to_list(reverse_list(build([1, 2, 3, 4, 5]))))   # [5, 4, 3, 2, 1]
print(to_list(reverse_list(build([1, 2]))))            # [2, 1]
print(to_list(reverse_list(build([]))))                # []
print(to_list(reverse_list(build([7]))))               # [7]
print(to_list(reverse_brute(build([1, 2, 3]))))        # [3, 2, 1]
```

## Dry run: reverse `1 → 2 → 3`

| Step | curr | nxt (saved) | Flip | prev after | Reversed part | Still to do |
|---|---|---|---|---|---|---|
| start | 1 | — | — | None | (empty) | 1 → 2 → 3 |
| 1 | 1 | 2 | 1.next = None | 1 | 1 → None | 2 → 3 |
| 2 | 2 | 3 | 2.next = 1 | 2 | 2 → 1 → None | 3 |
| 3 | 3 | None | 3.next = 2 | 3 | 3 → 2 → 1 → None | (empty) |
| end | None | — | loop stops | 3 | return prev = 3 | — |

Answer: **3 → 2 → 1**. Notice why we save `nxt` first: after `curr.next = prev`, the link to the rest of the list is gone.

## Complexity
- Brute force (copy to array): Time O(n), Space O(n) — array of values + new nodes
- In-place three pointers: Time O(n) — each node visited once; Space O(1) — only three pointer variables

## Key takeaway
With linked lists, **always save the next node before you change a pointer**, or you lose the rest of the chain. The `prev / curr / nxt` pattern reverses a list in one pass with O(1) memory, and it shows up inside many harder problems (reverse in groups, palindrome list, reorder list).

## My notes
<!-- Write 1–2 lines in your own words here -->
