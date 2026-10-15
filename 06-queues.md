# Queues

A **queue** is a linear data structure that follows **FIFO (First In, First Out)**: the first item added is the first item removed.

Think of a line of people waiting. New arrivals join the rear; the person at the front leaves first.

```text
FRONT                                      REAR
  ↓                                           ↓
[10] → [20] → [30] → [40]
 remove here                              add here
```

## Core operations

| Operation | Meaning | Typical time |
|---|---|---:|
| `enqueue(value)` | Add to the rear | O(1) |
| `dequeue()` | Remove and return the front item | O(1) |
| `peek()` | Read the front without removing it | O(1) |
| `is_empty()` | Check whether the queue is empty | O(1) |
| `size()` | Return the number of items | O(1) |

A queue is an **abstract data type (ADT)**: FIFO describes its behavior, while a linked list, circular buffer, or Python's `collections.deque` can provide the implementation.

## Implementation 1: Python's `deque` (usually the practical choice)

Python's `collections.deque` supports efficient operations at both ends. Use `append()` to enqueue and `popleft()` to dequeue.

```python
from collections import deque

queue = deque()

# Enqueue
queue.append(10)
queue.append(20)
queue.append(30)

# Inspect the front without removing it
front = queue[0] if queue else None
print(front)  # 10

# Dequeue
item = queue.popleft()
print(item)   # 10

print(len(queue))  # 2
print(not queue)   # False: queue is not empty
```

**Important:** avoid using `list.pop(0)` for a queue. Removing the first list item shifts the remaining elements, making that operation **O(n)**. With a deque, appending and popping at the ends are approximately **O(1)**.

## Implementation 2: Queue with a singly linked list

To implement a queue manually, keep two references:

- `front`: the next node to leave.
- `rear`: the last node, where the next item is added.

Keeping both references makes enqueue and dequeue O(1), without traversing the list.

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None


class Queue:
    def __init__(self):
        self.front = None
        self.rear = None
        self.length = 0

    def is_empty(self):
        return self.front is None

    def size(self):
        return self.length

    def enqueue(self, value):
        new_node = Node(value)

        if self.rear is None:  # Empty queue
            self.front = self.rear = new_node
        else:
            self.rear.next = new_node
            self.rear = new_node

        self.length += 1

    def dequeue(self):
        if self.front is None:
            raise IndexError("dequeue from empty queue")

        removed = self.front
        self.front = removed.next
        self.length -= 1

        # If the last node was removed, reset both references.
        if self.front is None:
            self.rear = None

        return removed.value

    def peek(self):
        if self.front is None:
            raise IndexError("peek from empty queue")
        return self.front.value

    def __len__(self):
        return self.length

    def __repr__(self):
        values = []
        current = self.front
        while current is not None:
            values.append(repr(current.value))
            current = current.next
        return "Queue([" + ", ".join(values) + "])"


if __name__ == "__main__":
    q = Queue()
    q.enqueue(10)
    q.enqueue(20)
    q.enqueue(30)

    assert q.peek() == 10
    assert q.dequeue() == 10
    assert q.dequeue() == 20
    assert q.dequeue() == 30
    assert q.is_empty()
    assert q.size() == 0

    q.enqueue(99)  # Verify reuse after becoming empty
    assert q.peek() == 99
    assert q.dequeue() == 99
    print("All queue tests passed.")
```

### How enqueue works

1. Create a node.
2. If the queue is empty, make it both `front` and `rear`.
3. Otherwise, link the old rear to the new node, then move `rear`.
4. Increase the length.

```text
Before: front → [10] → [20] ← rear

enqueue(30)

After:  front → [10] → [20] → [30] ← rear
```

### How dequeue works

1. Raise an error if the queue is empty.
2. Save the front node's value.
3. Move `front` to `front.next` and decrease the length.
4. If the queue became empty, also set `rear = None`.
5. Return the saved value.

```text
Before: front → [10] → [20] → [30] ← rear

dequeue() returns 10

After:           front → [20] → [30] ← rear
```

## Queue invariants and edge cases

These conditions should hold after every operation:

- Empty queue: `front is None`, `rear is None`, and `length == 0`.
- Non-empty queue: `front` points to the first node, `rear` points to the last node, and `rear.next is None`.
- `length` equals the number of nodes.
- The front item is always the next one to be dequeued.

Always test:

1. Dequeue and peek on an empty queue.
2. Enqueue one item, then dequeue it (both references must become `None`).
3. Enqueue again after the queue has become empty.
4. Enqueue several items and confirm FIFO order.
5. Alternate enqueue and dequeue operations.

Common bugs include forgetting to connect `rear.next` before moving `rear`, failing to reset `rear` after removing the last item, and accessing `front.value` before checking for an empty queue.

## Complexity comparison

| Operation | Linked-list queue | `collections.deque` | Python list |
|---|---:|---:|---:|
| Enqueue at rear | O(1) | O(1) | O(1) amortized with `append` |
| Dequeue at front | O(1) | O(1) | O(n) with `pop(0)` |
| Peek at front | O(1) | O(1) | O(1), if non-empty |
| Check empty | O(1) | O(1) | O(1) |
| Get size | O(1), with a counter | O(1) | O(1) |

## Queue applications

- **Breadth-first search (BFS):** visit graph or tree nodes level by level.
- **Task scheduling:** process jobs in arrival order.
- **Print spooling:** send documents to a printer in order.
- **Buffers and producer-consumer systems:** hold items until a consumer is ready.
- **Request processing:** manage work waiting to be handled.

### Why BFS uses a queue

BFS adds newly discovered neighbors to the rear and processes the oldest discovered node at the front. This naturally explores nodes level by level.

```text
    A
   / \\
  B   C
 / \\   \\
D   E   F

BFS order: A → B → C → D → E → F
```

## Queue vs. stack

| Structure | Rule | Add | Remove |
|---|---|---|---|
| Stack | LIFO — Last In, First Out | Top | Top |
| Queue | FIFO — First In, First Out | Rear | Front |

**Memory rule:** a stack serves the newest item first; a queue serves the oldest item first.

## Quick revision

- Queue = **FIFO**.
- Enqueue at the **rear**; dequeue from the **front**.
- Keep both `front` and `rear` for an efficient linked-list queue.
- When removing the last node, reset both references.
- Prefer `collections.deque` over `list.pop(0)` for Python queues.
- Queues are central to **BFS** and ordered task processing.
- Choose a representation based on the operations that must be efficient.
