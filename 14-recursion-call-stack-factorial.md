# Recursion, Call Stack & Factorial

Beginner-friendly Python DSA notes based on the **Python DSA** Notion notebook.

## 1. What Is Recursion?

**Recursion** is a technique where a function calls itself to solve a smaller version of the same problem.

A recursive function needs two essential parts:

- **Base case:** the condition that stops further recursive calls.
- **Recursive case:** the step that calls the function again with a smaller or simpler input.

Each call must make progress toward the base case. Otherwise, Python may eventually raise a `RecursionError`.

### Example: countdown

```python
def countdown(n):
    if n == 0:              # Base case
        print("Done!")
        return

    print(n)
    countdown(n - 1)        # Recursive case


countdown(3)
```

Output:

```text
3
2
1
Done!
```

The calls move toward the base case: `countdown(3)` → `countdown(2)` → `countdown(1)` → `countdown(0)`.

## 2. The Call Stack

The **call stack** keeps track of active function calls. Each call creates a stack frame containing information needed to resume the function, such as local variables and execution state.

The stack follows **LIFO: Last In, First Out**. The most recently called function finishes first.

- **Push:** a function call adds a frame.
- **Pop:** a function returns and its frame is removed.
- A recursive call creates a new frame, even though it calls the same function.

For example, while `third()` is running inside `first() → second() → third()`, the call stack is conceptually:

```text
TOP    third()   <- currently running
       second()  <- waiting for third()
BOTTOM first()   <- waiting for second()
```

When `third()` returns, execution resumes in `second()`, then in `first()`.

### Descent and return phases

```python
def show(n):
    if n == 0:
        return

    print("Entering", n)
    show(n - 1)
    print("Leaving", n)


show(3)
```

Output:

```text
Entering 3
Entering 2
Entering 1
Leaving 1
Leaving 2
Leaving 3
```

Code before the recursive call runs while calls descend. Code after the recursive call runs as they return, in reverse order.

**Call stack vs. stack data structure:** the runtime manages the call stack automatically; an explicit stack is a data structure that your code manages, often using `append()` and `pop()`.

## 3. Factorial: A Classic Example

The factorial of a non-negative integer `n`, written `n!`, is the product of the positive integers from 1 to `n`.

```text
5! = 5 × 4 × 3 × 2 × 1 = 120
4! = 4 × 3 × 2 × 1     = 24
1! = 1
0! = 1  (by definition)
```

For `n > 0`:

```text
n! = n × (n - 1)!
```

The definition `0! = 1` gives the recursion a stopping condition.

### Recursive factorial

```python
def factorial(n):
    if n < 0:
        raise ValueError("Factorial is not defined for negative integers")

    if n == 0 or n == 1:    # Base case
        return 1

    return n * factorial(n - 1)  # Recursive case


print(factorial(5))  # 120
print(factorial(0))  # 1
```

How it works:

1. Reject negative input.
2. Return 1 for the base cases 0 or 1.
3. Otherwise, multiply `n` by the result of `factorial(n - 1)`.
4. The multiplication completes after the smaller recursive call returns.

### Trace: `factorial(4)`

**Calls descend:**

```text
factorial(4) waits for 4 × factorial(3)
    factorial(3) waits for 3 × factorial(2)
        factorial(2) waits for 2 × factorial(1)
            factorial(1) returns 1
```

**Results unwind:**

| Call | Calculation | Result |
| --- | --- | ---: |
| `factorial(1)` | Base case | 1 |
| `factorial(2)` | 2 × 1 | 2 |
| `factorial(3)` | 3 × 2 | 6 |
| `factorial(4)` | 4 × 6 | 24 |

The final result is **24**.

## 4. Common Recursion Mistakes

### Missing a base case

```python
def factorial(n):
    return n * factorial(n - 1)
```

This keeps calling itself until Python raises a `RecursionError`.

### Not making progress toward the base case

```python
def bad_countdown(n):
    if n == 0:
        return
    bad_countdown(n + 1)  # Moves away from zero
```

A base case does not help if the recursive call cannot reach it.

### Forgetting to return the recursive result

```python
def wrong_factorial(n):
    if n == 0:
        return 1
    n * wrong_factorial(n - 1)  # Result is discarded
```

Correct form:

```python
return n * wrong_factorial(n - 1)
```

## 5. Recursive vs. Iterative Factorial

A loop can calculate factorial without recursive calls:

```python
def factorial_iterative(n):
    if n < 0:
        raise ValueError("Factorial is not defined for negative integers")

    result = 1
    for number in range(2, n + 1):
        result *= number

    return result
```

| Feature | Recursive | Iterative |
| --- | --- | --- |
| Main mechanism | Function calls itself | Loop repeats |
| Stopping condition | Base case | Loop condition |
| Time | O(n) arithmetic operations | O(n) arithmetic operations |
| Auxiliary space | O(n) call frames | O(1) extra space |
| Typical advantage | Clear for naturally recursive problems | Usually lower overhead for factorial |

These complexity figures count arithmetic operations in the usual DSA model. Python integers can grow very large, so the cost of big-integer multiplication also matters for very large inputs.

## 6. Complexity of Recursive Factorial

- **Time:** O(n), because the function makes a linear number of calls, each doing constant additional work in the usual model.
- **Auxiliary space:** O(n), because all calls remain active on the call stack until the base case returns.

A simplified recurrence is:

```text
T(n) = T(n - 1) + O(1)
T(0) = O(1)
```

The iterative version uses O(1) auxiliary space because it only needs a fixed number of variables.

## 7. Python's Recursion Limit

Python limits recursion depth to protect the interpreter. Deep recursion may raise `RecursionError`, even when a base case exists.

You can inspect the limit:

```python
import sys
print(sys.getrecursionlimit())
```

Before trying to increase it, check that:

- The base case is correct and reachable.
- Each recursive call makes progress.
- Recursion is appropriate for the input size.
- An iterative solution or an explicit stack would not be safer.

Increasing the limit too far can risk exhausting the underlying stack.

## 8. Common Recursion Patterns in DSA

- **Linear recursion:** one recursive call per function call. Examples: factorial, countdown, recursive linked-list traversal. Call depth is often O(n).
- **Tree recursion:** a call makes multiple recursive calls. Naive recursive Fibonacci is an example and repeats work, giving exponential time without memoization.
- **Divide and conquer:** split a problem into smaller subproblems. Examples: Merge Sort, Quick Sort, and recursive Binary Search.
- **Recursive data structures:** trees and linked lists are often processed recursively because each node refers to smaller substructures.

The complexity depends on the number of calls, work per call, and maximum call depth.

## 9. A Checklist for Solving Recursive Problems

1. **Define the input:** What does one call represent?
2. **Find the base case:** What is the smallest solvable input and its answer?
3. **Reduce the problem:** What smaller input can the same function solve?
4. **Trust the smaller result:** Assume the recursive call returns the correct result for that input.
5. **Combine the result:** Use it to solve the current problem.
6. **Check progress:** Does each call move toward a base case?
7. **Trace a small example:** Write calls and returns separately.
8. **Analyze complexity:** Count calls, work per call, and maximum stack depth.

For factorial: base case `factorial(0) = 1`; smaller problem `factorial(n - 1)`; combination `n * factorial(n - 1)`.

## 10. Practice

Try these before looking up solutions:

1. Write `sum_to_n(n)` recursively to return `1 + 2 + ... + n`.
2. Write a countdown that prints from `n` to 1, then prints `Done!`.
3. Write `power(base, exponent)` recursively for non-negative integer exponents.
4. Trace `factorial(3)` and record the call stack before the base case returns.
5. Explain why recursive factorial uses O(n) time and O(n) auxiliary space.
6. Convert a recursive countdown into a loop.
7. Explain why the multiplication in `factorial(n)` waits for `factorial(n - 1)` to return.

### Key takeaways

- Recursion solves a problem by calling the same function on a smaller problem.
- A correct recursive function has a base case, a recursive case, and progress toward the base case.
- The call stack follows LIFO; recursive calls descend and pending calculations finish as calls unwind.
- Recursive factorial uses O(n) time and O(n) auxiliary stack space in the standard DSA model.
- Recursion is useful for trees, divide-and-conquer algorithms, and problems naturally defined in terms of smaller problems.
