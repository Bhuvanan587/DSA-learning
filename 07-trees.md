# Trees — Data Structures & Algorithms

## 1. What Is a Tree?

A **tree** is a non-linear data structure made of nodes connected by edges. It represents hierarchical relationships, such as folders, organization charts, HTML documents, and decision structures.

```text
      A
    /   \
   B     C
  / \     \
 D   E     F
```

A tree with \(n\) nodes has exactly **\(n - 1\) edges**. In a rooted tree, every node except the root has exactly one parent.

## 2. Tree Terminology

Using the example above:

- **Node:** an individual element, such as A or B.
- **Edge:** a connection between two nodes.
- **Root:** the top node; it has no parent (A).
- **Parent / child:** A is the parent of B and C; B is a child of A.
- **Siblings:** nodes with the same parent (B and C).
- **Leaf:** a node with no children (D, E, F).
- **Internal node:** a node with at least one child (A, B, C).
- **Degree:** the number of children a node has.
- **Ancestor:** a node above another node on its path to the root.
- **Descendant:** a node below another node.
- **Path:** a sequence of connected nodes, such as A → B → E.
- **Subtree:** a node and all of its descendants.
- **Forest:** a collection of separate trees.

### Depth vs. Height

- **Depth of a node:** number of edges from the root to that node. The root has depth 0.
- **Height of a node:** number of edges on the longest downward path from that node to a leaf. A leaf has height 0.
- **Height of a tree:** height of its root, equivalently the maximum node depth.

## 3. Binary Trees

A **binary tree** is a tree in which each node has at most two children, called the left child and right child. A binary tree is not necessarily sorted.

A common Python node:

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
```

Example:

```text
     10
    /  \
   5    15
  / \
 2   7
```

Each child attribute stores a reference to another node, or `None` if that child does not exist.

## 4. Common Binary Tree Shapes

### Full binary tree
Every node has either **0 or 2 children**. No node has exactly one child.

### Complete binary tree
Every level is full except possibly the last, and the last level is filled **from left to right without gaps**. Heaps use complete binary trees.

### Perfect binary tree
Every internal node has two children, and all leaves are at the same depth. Every level is completely filled.

For a perfect binary tree of height \(h\), with the root at height 0:

\[
\text{nodes} = 2^{h+1} - 1
\]

### Balanced tree
A tree whose height stays relatively small as the number of nodes grows. The exact balance condition depends on the tree type.

### Skewed (degenerate) tree
A tree where nodes mostly continue in one direction. A completely skewed binary tree behaves like a linked list.

**Memory aid:**
- Full → count of children (0 or 2).
- Complete → last level packed left to right.
- Perfect → all levels completely filled.

## 5. Tree Traversal

Traversal means visiting the nodes in a defined order. For this tree:

```text
    A
   / \
  B   C
 / \
D   E
```

### Depth-first traversals (DFS)

**Preorder — Root, Left, Right**

```python
def preorder(node):
    if node is None:
        return
    print(node.value)
    preorder(node.left)
    preorder(node.right)
```

Order: `A, B, D, E, C`

**Inorder — Left, Root, Right**

```python
def inorder(node):
    if node is None:
        return
    inorder(node.left)
    print(node.value)
    inorder(node.right)
```

Order: `D, B, E, A, C`

**Postorder — Left, Right, Root**

```python
def postorder(node):
    if node is None:
        return
    postorder(node.left)
    postorder(node.right)
    print(node.value)
```

Order: `D, E, B, C, A`

DFS can be implemented with recursion (the call stack) or an explicit stack.

### Breadth-first traversal (BFS / level order)

Visit nodes level by level. A queue is the usual tool.

```python
from collections import deque

def level_order(root):
    if root is None:
        return

    queue = deque([root])

    while queue:
        node = queue.popleft()
        print(node.value)

        if node.left is not None:
            queue.append(node.left)
        if node.right is not None:
            queue.append(node.right)
```

For the example, the order is `A, B, C, D, E`.

## 6. Recursion and Subtrees

Many tree algorithms become easier when you solve the problem for one node and then apply the same logic to its subtrees.

For example, counting nodes:

```python
def count_nodes(node):
    if node is None:
        return 0
    return 1 + count_nodes(node.left) + count_nodes(node.right)
```

The base case is `node is None`. Each recursive call processes a smaller subtree.

## 7. Complexity

Let \(n\) be the number of nodes and \(h\) the tree height.

| Operation | Time |
|---|---:|
| Visit every node (traversal) | \(O(n)\) |
| Count all nodes | \(O(n)\) |
| Recursive traversal call-stack space | \(O(h)\) |
| BFS queue space | Up to \(O(n)\) |

A balanced tree typically has height \(O(\log n)\); a skewed tree can have height \(O(n)\). Exact operation costs depend on the tree type and algorithm. A **Binary Search Tree (BST)** adds ordering rules and is covered separately.

## 8. Trees, Stacks, and Queues

- **DFS:** recursion or a stack.
- **BFS / level order:** a queue.
- **Tree nodes:** connect to children through references, much like linked structures.
- **Recursion:** often reasons about one subtree at a time.

## 9. Quick Review

- Root = node with no parent.
- Leaf = node with zero children.
- Depth = edges from root to node.
- Height = longest downward path to a leaf.
- Binary tree = at most two children per node.
- Full = every node has 0 or 2 children.
- Complete = last level filled left to right.
- Perfect = all levels filled.
- Preorder = Root, Left, Right.
- Inorder = Left, Root, Right.
- Postorder = Left, Right, Root.
- Level order = breadth-first traversal using a queue.

When debugging, draw the tree and track the current node, its children, and the subtree each recursive call is processing.
