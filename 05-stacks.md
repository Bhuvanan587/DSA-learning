# Stacks

A stack is a linear data structure that follows **LIFO (Last In, First Out)**: the last item added is the first item removed.

## Core operations

- **Push:** add an item to the top.
- **Pop:** remove and return the top item.
- **Peek:** inspect the top without removing it.
- **is_empty:** check whether the stack is empty.
- **Size:** return the number of items.

## Python list implementation

Use the end of a Python list as the top:

~~~python
stack = []
stack.append(10)  # push
stack.append(20)
top_item = stack[-1]  # peek
removed = stack.pop()  # pop
~~~

Appending to the end is O(1) amortized; popping from the end is O(1). Avoid insert(0, value) and pop(0), which require shifting elements and take O(n).

## Linked-list implementation

Keep a top reference to the head node and a height counter. The head is the best top position for a singly linked list because both push and pop can be O(1).

~~~python
new_node.next = self.top
self.top = new_node
self.height += 1
~~~

For pop, first handle an empty stack, save the current top, move top to top.next, decrement height, and return the saved value. This order preserves access to the remaining nodes.

## Complexity

| Operation | Linked-list stack | Python-list stack |
|---|---:|---:|
| Push | O(1) | O(1) amortized |
| Pop | O(1) | O(1) |
| Peek | O(1) | O(1) |
| Empty check | O(1) | O(1) |
| Size | O(1) with a counter | O(1) |

## Edge cases and uses

An empty linked-list stack has top = None and height = 0. Handle pop on an empty stack deliberately (for example, return None or raise an exception). This is called underflow. Test empty, one-item, and multi-item cases.

Stacks are useful for undo operations, function calls, recursion, balanced-parenthesis checking, expression evaluation, depth-first search, and backtracking.

**Remember:** stack = LIFO; push adds to the top; pop removes from the top; peek only looks. A stack can be implemented with different structures, but its LIFO access rule defines its behavior.
