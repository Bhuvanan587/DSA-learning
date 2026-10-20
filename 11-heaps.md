# Heaps

## 1. What Is a Heap?

A **heap** is a tree-based data structure that follows a heap-order property. A binary heap is a **complete binary tree** and is commonly stored in an array, so separate node pointers are unnecessary.

Heaps are useful for priority queues, task scheduling, top-*k* problems, graph algorithms such as Dijkstra's algorithm, and heap sort.

A heap is not a binary search tree (BST): it orders each parent relative to its children, but does not fully order the left and right subtrees.

## 2. Min-Heaps and Max-Heaps

- **Min-heap:** each parent is less than or equal to its children; the smallest value is at the root.
- **Max-heap:** each parent is greater than or equal to its children; the largest value is at the root.

Min-heap example:

```text
        2
      /   \
     5     4
    / \   /
   9   7 6
```

Array representation: `[2, 5, 4, 9, 7, 6]`

Max-heap example:

```text
        90
       /  \
     40    70
    / \   /
   15 25 60
```

Array representation: `[90, 40, 70, 15, 25, 60]`

**A heap is not fully sorted.** In a min-heap, the root is the minimum, but the children are not necessarily sorted relative to each other.

## 3. Complete Binary Tree and Array Indices

A complete binary tree has every level full except possibly the last, and the last level fills from left to right.

For a zero-indexed array, the node at index `i` has:

| Relationship | Index |
|---|---:|
| Parent | `(i - 1) // 2` |
| Left child | `2 * i + 1` |
| Right child | `2 * i + 2` |

The root is at index `0` and has no parent. These formulas let the array represent the tree without explicit pointers.

## 4. Core Operations and Complexity

| Operation | Time | Why |
|---|---:|---|
| Peek at root | `O(1)` | Read index `0` |
| Insert / push | `O(log n)` | Append and sift up |
| Remove root / pop | `O(log n)` | Replace root and sift down |
| Build heap with heapify | `O(n)` | Bottom-up construction |
| Search for arbitrary value | `O(n)` | Heap order does not support binary search |
| Repeatedly pop all values | `O(n log n)` | `n` removals |
| Space | `O(n)` | Store `n` values |

A complete binary heap has height `O(log n)`; sift-up and sift-down traverse at most that height.

## 5. Inserting: Sift Up

To insert into a min-heap:

1. Append the new value at the next open array position to preserve completeness.
2. Compare it with its parent.
3. If it is smaller than its parent, swap them.
4. Repeat until the parent is no larger, or the new value reaches the root.

Example: insert `3` into `[2, 5, 4, 9, 7, 6]`.

- Append `3`: `[2, 5, 4, 9, 7, 6, 3]`.
- Its parent is `4), so swap: `[2, 5, 3, 9, 7, 6, 4]`.
- Now `3)'s parent is `2), and `2 <= 3), so stop.

Worst-case time: `O(log n)`.

## 6. Removing the Root: Sift Down

For a min-heap, popping removes the minimum:

1. Save the root value.
2. Move the last array value to the root and remove its old last position.
3. Compare the new root with its children.
4. If the heap property is violated, swap with the **smaller child**.
5. Continue until the heap property holds.
6. Return the saved minimum.

For a max-heap, choose the larger child when sifting down. Worst-case time is `O(log n)).

## 7. Python's `heapq` Module

Python's built-in `heapq` module implements a min-heap using a regular list.

```python
import heapq

numbers = [7, 2, 9, 4, 1]
heapq.heapify(numbers)          # O(n)
print(numbers[0])               # Smallest value: O(1)

heapq.heappush(numbers, 3)      # O(log n)
smallest = heapq.heappop(numbers)  # O(log n)
print(smallest)
print(numbers)
```

The internal list is heap-ordered, **not sorted**.

### Common methods

| Method | Purpose | Typical time |
|---|---|---:|
| `heapq.heapify(x)` | Turn list `x` into a min-heap in place | `O(n)` |
| `heapq.heappush(h, item)` | Insert an item | `O(log n)` |
| `heapq.heappop(h)` | Remove and return the smallest item | `O(log n)` |
| `heapq.heappushpop(h, item)` | Push an item and pop the smallest efficiently | `O(log n)` |
| `heapq.heapreplace(h, item)` | Pop the smallest, then push an item; heap must be non-empty | `O(log n)` |
| `heapq.nsmallest(k, iterable)` | Return the k smallest values in sorted order | Depends on input and k |
| `heapq.nlargest(k, iterable)` | Return the k largest values in sorted order | Depends on input and k |

`heappushpop` and `heapreplace` differ in which value is guaranteed to be returned or removed.

### Simulating a max-heap

For numbers, store negative values in a min-heap:

```python
import heapq

values = [10, 4, 18, 7]
max_heap = [-value for value in values]
heapq.heapify(max_heap)

largest = -heapq.heappop(max_heap)
print(largest)  # 18
```

The largest original value becomes the smallest negative value.

### Priority queues with tuples

Put the priority first because tuples compare lexicographically:

```python
import heapq

tasks = []
heapq.heappush(tasks, (1, "urgent task"))
heapq.heappush(tasks, (3, "later task"))
heapq.heappush(tasks, (2, "normal task"))

priority, task = heapq.heappop(tasks)
print(priority, task)  # 1 urgent task
```

If priorities can tie and task objects cannot be compared, add a unique counter as the second tuple field.

## 8. Implementing a Min-Heap from Scratch

```python
class MinHeap:
    def __init__(self):
        self.heap = []

    def __len__(self):
        return len(self.heap)

    def peek(self):
        if not self.heap:
            raise IndexError("peek from empty heap")
        return self.heap[0]

    def push(self, value):
        self.heap.append(value)
        self._sift_up(len(self.heap) - 1)

    def pop(self):
        if not self.heap:
            raise IndexError("pop from empty heap")

        smallest = self.heap[0]
        last = self.heap.pop()

        if self.heap:
            self.heap[0] = last
            self._sift_down(0)

        return smallest

    def _sift_up(self, i):
        while i > 0:
            parent = (i - 1) // 2
            if self.heap[parent] <= self.heap[i]:
                break
            self.heap[parent], self.heap[i] = (
                self.heap[i], self.heap[parent]
            )
            i = parent

    def _sift_down(self, i):
        n = len(self.heap)

        while True:
            left = 2 * i + 1
            right = 2 * i + 2
            smallest = i

            if left < n and self.heap[left] < self.heap[smallest]:
                smallest = left
            if right < n and self.heap[right] < self.heap[smallest]:
                smallest = right

            if smallest == i:
                break

            self.heap[i], self.heap[smallest] = (
                self.heap[smallest], self.heap[i]
            )
            i = smallest


heap = MinHeap()
for value in [7, 2, 9, 1, 5]:
    heap.push(value)

print(heap.peek())  # 1
print([heap.pop() for _ in range(len(heap))])  # [1, 2, 5, 7, 9]
```

This implementation supports `peek`, `push`, `pop`, and `len`. The sift methods preserve the min-heap invariant. It does not implement arbitrary deletion or priority updates.

## 9. Heap Sort

Heap sort typically works by building a max-heap, swapping the maximum root with the last unsorted element, shrinking the unsorted region, and sifting down the new root. Repeat until sorted.

- Time: `O(n log n)` in best, average, and worst cases.
- Auxiliary space: `O(1)` for the iterative in-place version.
- Stability: not stable by default.

With Python's `heapq`, repeatedly popping a min-heap also produces ascending order, but collecting the output requires extra storage.

## 10. When to Use a Heap

- **Priority queues:** process the highest-priority task next.
- **Top-k problems:** find the k largest or smallest values.
- **Scheduling:** select the next event or task.
- **Graph algorithms:** choose the next lowest-distance candidate in Dijkstra's algorithm.
- **Streaming data:** maintain a running set of the largest or smallest values.
- **Merging sorted sequences:** efficiently select the next smallest item across multiple sequences.

Use a heap when you repeatedly need the minimum or maximum, rather than keeping all values fully sorted.

## 11. Heap vs. Binary Search Tree

| Feature | Heap | Binary search tree |
|---|---|---|
| Root | Minimum or maximum | Depends on the tree and ordering |
| Ordering | Parent-child property | Left subtree values are smaller; right subtree values are larger (for a standard unique-key BST) |
| Peek min/max | `O(1)` at the appropriate root | `O(h)`, where h is tree height |
| Insert/remove | `O(log n)` for a binary heap | `O(h)`; balanced trees are `O(log n)), unbalanced can be `O(n)` |
| Search arbitrary key | `O(n)` | `O(h)` |
| Best use | Repeated priority access | Ordered search, predecessor/successor, range queries |

A heap cannot guide an arbitrary search by comparing a target with one child, unlike a BST.

## 12. Common Mistakes

- Assuming the internal heap array is sorted.
- Choosing the wrong child during sift-down: smaller for a min-heap, larger for a max-heap.
- Forgetting that array indices start at zero.
- Calling `peek` or `pop` on an empty heap without handling the case.
- Assuming arbitrary search is `O(log n)`; it is generally `O(n)`.
- Assuming `heapify` is `O(n log n)); bottom-up heap construction is `O(n)`.
- Using tuples with unorderable task objects when priorities tie.
- Assuming a heap is stable; equal-priority values are not automatically returned in insertion order.

## 13. Quick Revision

- A binary heap is a complete binary tree efficiently stored in an array.
- Min-heap root = minimum; max-heap root = maximum.
- Parent index: `(i - 1) // 2`; children: `2*i + 1` and `2*i + 2`.
- Peek: `O(1)`; push and pop: `O(log n)`; heapify: `O(n)`.
- Python's `heapq` is a min-heap.
- Heaps are great for priority queues, but not arbitrary ordered searches.

## 14. Practice Questions

1. Insert `3` into the min-heap `[2, 5, 4, 9, 7, 6]`. Show every swap.
2. Starting with `[1, 3, 2, 7, 5, 4]`, pop the minimum and show each sift-down step.
3. Why is `heapq.heapify` `O(n)` rather than `O(n log n)`?
4. Implement a max-heap using negative values and `heapq`.
5. Maintain the three largest values from a stream using a heap.
6. Explain why searching for an arbitrary value in a heap can take `O(n)`.
7. Compare heaps and BSTs for a task scheduler and for range queries.
