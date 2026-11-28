# Day 15 — Binary search trees
📅 2026-11-28 · Topic: Binary search trees · Level: Beginner

## The idea in plain words
Yesterday (Day 14) we met binary trees: each node has at most a left child and a right child. A **binary search tree (BST)** is a binary tree with one extra rule:

> For **every** node, all values in its **left** subtree are **smaller**, and all values in its **right** subtree are **bigger**.

This rule makes searching fast, just like binary search on a sorted array (Day 8). At each node you ask "is my target smaller or bigger?" and throw away half the tree.

```
        8
       / \
      3   10
     / \    \
    1   6    14
```

| Question | How a BST answers it |
|---|---|
| Is 6 in the tree? | 8 → go left (6 < 8) → 3 → go right (6 > 3) → 6 ✅ |
| Smallest value? | Keep going left: 8 → 3 → 1 |
| Biggest value? | Keep going right: 8 → 10 → 14 |
| All values in sorted order? | **Inorder** traversal (left, node, right): 1, 3, 6, 8, 10, 14 |

The last row is the most useful fact about BSTs: **inorder traversal of a BST gives the values in sorted order.**

A common trap: it is **not** enough to check that each node's direct children are on the correct side. The rule is about the *whole* subtree.

```
        5
       / \
      4   6
         / \
        3   7
```
Here 6's left child is 3, and 3 < 6, so the "check only children" idea says OK. But 3 is in the **right** subtree of 5, and 3 < 5. So this is **not** a valid BST.

## Problem: Validate Binary Search Tree (LeetCode #98)
Given the `root` of a binary tree, return `True` if it is a valid BST (left subtree strictly smaller, right subtree strictly bigger, for every node), otherwise `False`.

**Brute force**: for every node, walk its entire left subtree to find the **maximum** and its entire right subtree to find the **minimum**. The node is OK if `max(left) < node < min(right)`. This is correct, but the lower nodes get visited again and again, once for every ancestor above them. For a chain-shaped tree that is **O(n²)**.

**Better**: pass a **range** down the tree. Each node gets an allowed window `(low, high)`:
- The root can be anything: `(-∞, +∞)`.
- When you go **left**, the current value becomes the new **high** (everything there must be smaller).
- When you go **right**, the current value becomes the new **low** (everything there must be bigger).

If any node falls outside its window, the tree is not a BST. Every node is checked once, so this is **O(n)**.

```python
import math


class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def subtree_min(node):
    # Smallest value anywhere in this subtree (empty = +infinity)
    if node is None:
        return math.inf
    return min(node.val, subtree_min(node.left), subtree_min(node.right))


def subtree_max(node):
    # Biggest value anywhere in this subtree (empty = -infinity)
    if node is None:
        return -math.inf
    return max(node.val, subtree_max(node.left), subtree_max(node.right))


def is_valid_bst_brute(node):
    # Brute force: at EVERY node, scan the whole left and right subtrees again
    if node is None:
        return True
    if subtree_max(node.left) >= node.val:
        return False
    if subtree_min(node.right) <= node.val:
        return False
    return is_valid_bst_brute(node.left) and is_valid_bst_brute(node.right)


def is_valid_bst(node, low=-math.inf, high=math.inf):
    # Better: carry the allowed window (low, high) down the tree
    if node is None:
        return True                       # an empty tree is a valid BST
    if not (low < node.val < high):
        return False                      # this node breaks the window
    # Going left: values must stay below node.val
    # Going right: values must stay above node.val
    return (is_valid_bst(node.left, low, node.val) and
            is_valid_bst(node.right, node.val, high))


#         8
#        / \
#       3   10
#      / \    \
#     1   6    14
good = TreeNode(8,
                TreeNode(3, TreeNode(1), TreeNode(6)),
                TreeNode(10, None, TreeNode(14)))

#         5
#        / \
#       4   6
#          / \
#         3   7      <- 3 is on the right of 5, but 3 < 5
bad = TreeNode(5, TreeNode(4), TreeNode(6, TreeNode(3), TreeNode(7)))

print(is_valid_bst(good))                     # True
print(is_valid_bst(bad))                      # False
print(is_valid_bst_brute(good))               # True
print(is_valid_bst_brute(bad))                # False
print(is_valid_bst(TreeNode(2, TreeNode(2)))) # False  (equal values are not allowed)
print(is_valid_bst(None))                     # True
```

## Dry run: the "bad" tree `5 → (4, 6 → (3, 7))`

| Step | Node | Allowed window (low, high) | Inside? | What happens next |
|---|---|---|---|---|
| 1 | 5 | (-∞, +∞) | ✅ | check left with (-∞, 5) |
| 2 | 4 | (-∞, 5) | ✅ | no children → True; now check right of 5 with (5, +∞) |
| 3 | 6 | (5, +∞) | ✅ | check left with (5, 6) |
| 4 | 3 | (5, 6) | ❌ 3 is not > 5 | return False all the way up |

Notice step 4: the window `(5, 6)` "remembers" the 5 from two levels up. That memory is exactly what the "check only children" idea was missing.

## Complexity
- Brute force (scan subtrees at every node): Time O(n · h), which is O(n²) for a chain-shaped tree. Space O(h) for recursion.
- Window `(low, high)` passed down: Time **O(n)**, each node checked once. Space **O(h)** for recursion, where h is the height (O(log n) for a balanced tree, O(n) for a chain).

## Key takeaway
A BST means "everything on the left is smaller, everything on the right is bigger" for the **whole** subtree, not just the children. To check it, pass a `(low, high)` window down: going left tightens `high`, going right tightens `low`. And remember: inorder traversal of a BST comes out sorted.

## My notes
<!-- Write 1–2 lines in your own words here -->
