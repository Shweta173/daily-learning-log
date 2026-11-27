# Day 14 — Trees & traversals
📅 2026-11-27 · Topic: Trees & traversals · Level: Beginner

## The idea in plain words
A **tree** is like a family tree turned upside down. There is one node at the top called the **root**. Each node can point to child nodes below it. A node with no children is called a **leaf**. In a **binary tree**, every node has at most two children: a **left** one and a **right** one.

A linked list (Day 10) is a chain where each node points to *one* next node. A binary tree is the same idea, except each node can point to *two*.

A **traversal** just means "visit every node once, in some order". There are four classic orders. Using this tree:

```
      3
     / \
    9   20
       /  \
      15   7
```

| Traversal | Rule | Order for the tree above |
|---|---|---|
| Preorder | **node**, then left, then right | 3, 9, 20, 15, 7 |
| Inorder | left, then **node**, then right | 9, 3, 15, 20, 7 |
| Postorder | left, then right, then **node** | 9, 15, 7, 20, 3 |
| Level order (BFS) | top row first, left to right, then the next row | 3, 9, 20, 15, 7 |

The first three go **deep** first, and recursion (Day 9) makes them very short to write. Level order goes **wide** first, and it uses a queue (Day 12).

## Problem: Binary Tree Level Order Traversal (LeetCode #102)
Given the `root` of a binary tree, return its values **level by level**, as a list of lists, from top to bottom and left to right on each level.

Example: the tree above → `[[3], [9, 20], [15, 7]]`

**Brute force**: first find the tree's **height** (how many levels it has). Then, for each level `d`, start again from the root and walk down `d` steps to collect the nodes at that depth. This is easy to understand, but the top of the tree gets visited again for every level. For a tall, thin tree (like a chain of n nodes) that adds up to **O(n²)**.

**Better**: use **BFS with a queue**. Put the root in the queue. Then, while the queue is not empty:
- Count how many nodes are in the queue right now. Those nodes make up exactly one level.
- Pop that many nodes, write down their values, and push their children to the back of the queue.
- The children wait their turn, so they become the next level.

Every node goes into the queue once and comes out once, so this is **O(n)**.

```python
from collections import deque


class TreeNode:
    def __init__(self, val, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def height(node):
    # Number of levels in the tree (empty tree = 0)
    if node is None:
        return 0
    return 1 + max(height(node.left), height(node.right))


def collect_level(node, depth, out):
    # Walk down from the root and keep only nodes exactly `depth` steps away
    if node is None:
        return
    if depth == 0:
        out.append(node.val)
        return
    collect_level(node.left, depth - 1, out)
    collect_level(node.right, depth - 1, out)


def level_order_brute(root):
    # Brute force: for EACH level, start again from the root and dig down to it
    result = []
    for d in range(height(root)):
        level = []
        collect_level(root, d, level)
        result.append(level)
    return result


def level_order(root):
    # Better: BFS with a queue — visit every node exactly once, level by level
    if root is None:
        return []
    result = []
    queue = deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):           # only the nodes of THIS level
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)       # children wait for the next level
            if node.right:
                queue.append(node.right)
        result.append(level)
    return result


#       3
#      / \
#     9   20
#        /  \
#       15   7
root = TreeNode(3, TreeNode(9), TreeNode(20, TreeNode(15), TreeNode(7)))

print(level_order(root))               # [[3], [9, 20], [15, 7]]
print(level_order_brute(root))         # [[3], [9, 20], [15, 7]]
print(level_order(TreeNode(1)))        # [[1]]
print(level_order(None))               # []
```

## Dry run: the tree `3 → (9, 20 → (15, 7))`

| Round | Queue at start | Nodes in this level | Popped → children pushed | Queue after | result so far |
|---|---|---|---|---|---|
| 1 | `[3]` | 1 | 3 → push 9, 20 | `[9, 20]` | `[[3]]` |
| 2 | `[9, 20]` | 2 | 9 → (no children); 20 → push 15, 7 | `[15, 7]` | `[[3], [9, 20]]` |
| 3 | `[15, 7]` | 2 | 15 → (none); 7 → (none) | `[]` | `[[3], [9, 20], [15, 7]]` |

The queue is empty, so we stop. The trick is `len(queue)` at the start of each round: it tells us exactly how many nodes belong to the current level. Any children pushed during the round wait for the next one.

## Complexity
- Brute force (restart from the root for each level): Time O(n · h), where h is the height. That is O(n²) for a chain-shaped tree. Space O(h) for the recursion.
- BFS with a queue: Time **O(n)**, because each node is pushed and popped once. Space O(w), where w is the widest level (up to about n/2 for a full tree).

## Key takeaway
A tree is a linked list where each node can have two "next" pointers. To go **deep**, use recursion (pre/in/postorder). To go **level by level**, use a queue, and measure `len(queue)` at the start of each round to know where one level ends and the next begins.

## My notes
<!-- Write 1–2 lines in your own words here -->
