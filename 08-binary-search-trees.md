# Binary Search Trees (BSTs)

A **Binary Search Tree (BST)** is a binary tree whose values follow an ordering rule. With unique values, every value in a node's left subtree is smaller than the node, and every value in its right subtree is larger.

```text
       10
      /  \
     5    15
    / \   / \
   2   7 12  20
```

The rule applies to entire subtrees, not just immediate children. A binary tree only limits each node to at most two children; a BST adds ordering.

## Node structure

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
```

The root references the top node; an empty tree has `root = None`. A missing child is `None`.

## Searching

Compare the target with the current node: equal means found, smaller means go left, larger means go right. Reaching `None` means the value is absent.

```python
def contains(self, value):
    current = self.root
    while current is not None:
        if value == current.value:
            return True
        if value < current.value:
            current = current.left
        else:
            current = current.right
    return False
```

Example: searching for 12 compares with 10 (right), then 15 (left), then finds 12.

## Inserting

Follow the search path until an empty child position is found. This implementation ignores duplicates.

```python
def insert(self, value):
    def add(node):
        if node is None:
            return Node(value)
        if value < node.value:
            node.left = add(node.left)
        elif value > node.value:
            node.right = add(node.right)
        # Equal values are ignored.
        return node

    self.root = add(self.root)
```

The assignments to `node.left`, `node.right`, and `self.root` reconnect the subtree returned by recursion.

## Minimum and maximum

The minimum is the leftmost node; the maximum is the rightmost. Return `None` if the tree is empty.

```python
def min_value(self):
    current = self.root
    if current is None:
        return None
    while current.left is not None:
        current = current.left
    return current.value


def max_value(self):
    current = self.root
    if current is None:
        return None
    while current.right is not None:
        current = current.right
    return current.value
```

## Deleting: three cases

1. **Leaf:** return `None` for that subtree.
2. **One child:** return the existing child, bypassing the deleted node.
3. **Two children:** replace the value with its in-order successor (the smallest value in the right subtree), then remove the original successor. The in-order predecessor—the largest value in the left subtree—is an alternative.

```python
def delete(self, value):
    def remove(node, value):
        if node is None:
            return None

        if value < node.value:
            node.left = remove(node.left, value)
            return node
        if value > node.value:
            node.right = remove(node.right, value)
            return node

        # Found the target: leaf or one-child cases.
        if node.left is None:
            return node.right
        if node.right is None:
            return node.left

        # Two children: find the leftmost node in the right subtree.
        successor = node.right
        while successor.left is not None:
            successor = successor.left

        node.value = successor.value
        node.right = remove(node.right, successor.value)
        return node

    self.root = remove(self.root, value)
```

The first two deletion checks also handle a leaf: if both children are absent, the first returns `None`. Reassigning child/root references is essential so the updated subtree stays connected. If the value is absent, the tree remains unchanged.

## In-order traversal

In-order means left subtree → node → right subtree. For a BST, it visits values in ascending order.

```python
def inorder(node):
    if node is None:
        return
    inorder(node.left)
    print(node.value)
    inorder(node.right)
```

For the example tree, output is `2, 5, 7, 10, 12, 15, 20`.

## Complexity: depends on height

Let `n` be the number of nodes and `h` the tree height. Search, insert, delete, minimum, and maximum take **O(h)** time.

| Operation | Balanced tree | Worst case (skewed) |
|---|---:|---:|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |
| Minimum / maximum | O(log n) | O(n) |
| In-order traversal | O(n) | O(n) |

A BST is not automatically balanced. Inserting sorted values can create a chain (10 → 20 → 30 → 40), making height proportional to `n`. Self-balancing trees such as AVL and Red-Black trees maintain logarithmic height. Recursive operations use O(h) call-stack space.

## Common mistakes

- Checking only direct children instead of the entire subtree.
- Assuming every BST operation is O(log n), even when the tree is skewed.
- Forgetting to reconnect a recursive result to `node.left`, `node.right`, or `self.root`.
- Mishandling deletion of a node with two children.
- Leaving duplicate handling undefined.

## Quick mental models

- **Search:** smaller → left; larger → right.
- **Insert:** follow the search path to an empty position.
- **Minimum / maximum:** leftmost / rightmost.
- **Successor:** right once, then left as far as possible.
- **Delete:** leaf → remove; one child → bypass; two children → replace with successor or predecessor.
- **In-order:** sorted output.
- **Runtime:** O(height), not automatically O(log n).

> A BST turns each comparison into a direction. Its efficiency depends on its height.
