# Tree Traversal in Python

## Overview
Tree traversal visits each node in a tree according to a consistent rule. For the example below, each traversal produces a different order:

```text
        A
       / \\
      B   C
     / \\   \\
    D   E   F
```

## Preorder — Node, Left, Right (NLR)
Process the current node before its children. Output: `A, B, D, E, C, F`.

```python
def preorder(node):
    if node is None:
        return
    visit(node)
    preorder(node.left)
    preorder(node.right)
```

Useful for serializing/copying a tree and prefix expressions.

## Inorder — Left, Node, Right (LNR)
Process the left subtree, node, then right subtree. Output: `D, B, E, A, C, F`. In a valid binary search tree, inorder returns keys in nondecreasing order.

```python
def inorder(node):
    if node is None:
        return
    inorder(node.left)
    visit(node)
    inorder(node.right)
```

## Postorder — Left, Right, Node (LRN)
Process children before the current node. Output: `D, E, B, F, C, A`. Useful for computing subtree results and evaluating expression trees.

```python
def postorder(node):
    if node is None:
        return
    postorder(node.left)
    postorder(node.right)
    visit(node)
```

## Level-order — Breadth-first search (BFS)
Visit nodes one level at a time. Output: `A, B, C, D, E, F`. Use a queue:

```python
from collections import deque

def level_order(root):
    if root is None:
        return []
    result = []
    queue = deque([root])
    while queue:
        node = queue.popleft()
        result.append(node.value)
        if node.left is not None:
            queue.append(node.left)
        if node.right is not None:
            queue.append(node.right)
    return result
```

## Complete runnable example

```python
from collections import deque

class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def preorder(node):
    if node is None:
        return []
    return [node.value] + preorder(node.left) + preorder(node.right)

def inorder(node):
    if node is None:
        return []
    return inorder(node.left) + [node.value] + inorder(node.right)

def postorder(node):
    if node is None:
        return []
    return postorder(node.left) + postorder(node.right) + [node.value]

def level_order(root):
    if root is None:
        return []
    result, queue = [], deque([root])
    while queue:
        node = queue.popleft()
        result.append(node.value)
        if node.left is not None:
            queue.append(node.left)
        if node.right is not None:
            queue.append(node.right)
    return result

#         A
#        / \\
#       B   C
#      / \\   \\
#     D   E   F
root = Node('A')
root.left, root.right = Node('B'), Node('C')
root.left.left, root.left.right = Node('D'), Node('E')
root.right.right = Node('F')

print('Preorder:   ', preorder(root))
print('Inorder:    ', inorder(root))
print('Postorder:  ', postorder(root))
print('Level-order:', level_order(root))
```

Expected results:
- Preorder: `['A', 'B', 'D', 'E', 'C', 'F']`
- Inorder: `['D', 'B', 'E', 'A', 'C', 'F']`
- Postorder: `['D', 'E', 'B', 'F', 'C', 'A']`
- Level-order: `['A', 'B', 'C', 'D', 'E', 'F']`

For larger inputs, prefer recursive helpers that append to one shared result list instead of repeatedly concatenating lists, which copies intermediate results.

## Iterative DFS
An explicit stack can avoid Python's recursion-depth limit on deep trees.

### Iterative preorder
Push the right child first so the left child is popped first.

```python
def preorder_iterative(root):
    if root is None:
        return []
    result, stack = [], [root]
    while stack:
        node = stack.pop()
        result.append(node.value)
        if node.right is not None:
            stack.append(node.right)
        if node.left is not None:
            stack.append(node.left)
    return result
```

### Iterative inorder

```python
def inorder_iterative(root):
    result, stack = [], []
    current = root
    while current is not None or stack:
        while current is not None:
            stack.append(current)
            current = current.left
        current = stack.pop()
        result.append(current.value)
        current = current.right
    return result
```

### Iterative postorder using two stacks

```python
def postorder_iterative(root):
    if root is None:
        return []
    first, second = [root], []
    while first:
        node = first.pop()
        second.append(node)
        if node.left is not None:
            first.append(node.left)
        if node.right is not None:
            first.append(node.right)
    return [node.value for node in reversed(second)]
```

## Complexity
Let `n` be the node count, `h` the tree height, and `w` the maximum width of any level.

| Method | Time | Auxiliary space |
|---|---|---|
| Recursive DFS traversals | O(n) | O(h) call stack |
| Iterative DFS | O(n) | Usually O(h) stack |
| Level-order BFS | O(n) | O(w) queue |
| Postorder with two stacks | O(n) | O(n) worst case |

Each traversal visits every node once. A balanced tree has height O(log n), but a skewed tree can have height O(n). BFS may also need O(n) queue space in the worst case.

## Common mistakes
- Forgetting the base case for `node is None`.
- Mixing up preorder, inorder, and postorder positions.
- Assuming inorder sorts every binary tree; this only holds for a valid BST.
- Pushing left before right in iterative preorder when left-first output is wanted.
- Using `list.pop(0)` for BFS instead of `deque.popleft()`.
- Assuming recursive stack space is always O(log n); it is O(h).
- Not testing empty, single-node, skewed, and two-child trees.

## Practice
1. Write all four orders for the example tree from memory.
2. Build a tree with root 10, children 5 and 15, and children 2 and 7 under 5. Find all four orders.
3. Implement node count and tree height.
4. Return BST values in ascending order without `sorted()`.
5. Find the maximum width of a tree using BFS.
6. Compare recursive and iterative preorder.
7. Explain the time and auxiliary space for a skewed tree.

## Quick revision
- Preorder: Node → Left → Right.
- Inorder: Left → Node → Right; sorted for a valid BST.
- Postorder: Left → Right → Node.
- Level-order: level by level using a queue.
- Full traversal time: O(n).
- Recursive DFS stack: O(h); BFS queue: O(w).
