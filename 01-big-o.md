## Big-O Time Complexity
**Time complexity** describes how the amount of computational work grows as the input size grows.
- **X-axis:** input size, usually represented by n
- **Y-axis:** number of operations / computational work
- More operations generally means more execution time.
- Big-O describes growth/scaling, not exact runtime in seconds.
### O(1) — Constant Time
The amount of work stays roughly the same regardless of the input size.
Example:
```python
items[0]
```
Whether the list contains 10 elements or 10 million elements, accessing an element by index takes roughly the same amount of work.
**Key idea:** input size can grow, but the work remains approximately constant.
---
### O(log n) — Logarithmic Time
The algorithm repeatedly reduces the problem size, often by **half**.
Classic example: **binary search**.
For 16 elements:
```javascript
16 → 8 → 4 → 2 → 1
```
That takes about 4 reductions because:
```javascript
log₂(16) = 4
```
For 1,024 elements:
```javascript
1024 → 512 → 256 → 128 → 64 → 32 → 16 → 8 → 4 → 2 → 1
```
Only 10 reductions are needed:
```javascript
log₂(1024) = 10
```
Example:
```python
def binary_search(numbers, target):
    left = 0
    right = len(numbers) - 1

    while left <= right:
        middle = (left + right) // 2

        if numbers[middle] == target:
            return middle
        elif numbers[middle] < target:
            left = middle + 1
        else:
            right = middle - 1

    return -1
```
**Key idea:** each step eliminates a large portion of the remaining search space.
**Common example:** binary search → **O(log n)**
---
### O(n) — Linear Time
The amount of work grows directly with the input size.
Example:
```python
def print_items(items):
    for item in items:
        print(item)
```
If there are:
- 5 items → about 5 iterations
- 100 items → about 100 iterations
- 1,000 items → about 1,000 iterations
Therefore:
**Time complexity = O(n)**
A single loop processing each element once is often O(n).
Another example:
```python
def find_max(numbers):
    max_num = numbers[0]

    for num in numbers:
        if num > max_num:
            max_num = num

    return max_num
```
To guarantee that the maximum has been found, every element must be checked.
**Finding the maximum in an unsorted list → O(n)**
---
### O(n²) — Quadratic Time
The amount of work grows approximately with the **square** of the input size.
Example:
```python
def print_pairs(items):
    for x in items:
        for y in items:
            print(x, y)
```
If there are 10 items:
```javascript
10 × 10 = 100
```
So the operation runs about 100 times.
If there are 100 items:
```javascript
100 × 100 = 10,000
```
If there are 1,000 items:
```javascript
1000 × 1000 = 1,000,000
```
Therefore:
**Time complexity = O(n²)**
The key pattern is:
```javascript
n × n = n²
```
Another example:
```python
for x in items:
    ...

for y in items:
    ...
```
This is **O(n + n) = O(n)**, not O(n²), because the loops are sequential.
But:
```python
for x in items:
    for y in items:
        ...
```
is approximately **O(n × n) = O(n²)** because the loops are nested.
**Common examples:** Bubble Sort and Selection Sort → O(n²)
---
## Comparing the Growth Rates
For large input sizes, lower growth is generally more time-efficient:
**O(1) \> O(log n) \> O(n) \> O(n²)**
Here, `>` means **more time-efficient than**.
Think of the curves as:
- **O(1):** flat
- **O(log n):** rises very slowly
- **O(n):** rises linearly
- **O(n²):** curves upward rapidly
### Example comparison
<table header-row="true">
<tr>
<td>Input size n</td>
<td>O(1)</td>
<td>O(log n)</td>
<td>O(n)</td>
<td>O(n²)</td>
</tr>
<tr>
<td>10</td>
<td>\~1</td>
<td>\~3</td>
<td>10</td>
<td>100</td>
</tr>
<tr>
<td>100</td>
<td>\~1</td>
<td>\~7</td>
<td>100</td>
<td>10,000</td>
</tr>
<tr>
<td>1,000</td>
<td>\~1</td>
<td>\~10</td>
<td>1,000</td>
<td>1,000,000</td>
</tr>
<tr>
<td>10,000</td>
<td>\~1</td>
<td>\~14</td>
<td>10,000</td>
<td>100,000,000</td>
</tr>
</table>
For the logarithmic column, the values use approximately log2(n).
## Big-O Growth Graph
**Reading the graph:**
- **X-axis:** input size (n)
- **Y-axis:** operations / computational work
- **O(1):** flat — work stays roughly constant
- **O(log n):** rises very slowly
- **O(n):** rises linearly
- **O(n²):** rises rapidly as n increases
**Efficiency for large inputs:** O(1) → O(log n) → O(n) → O(n²)
## Core Mental Model
> **Input size ↑ → operations ↑ → execution time generally ↑**
The goal in DSA is often to find an algorithm whose work grows **more slowly** as n becomes large.
### Important warning
Do **not** memorize only these rules:
> "One loop = O(n)"
> "Nested loops = O(n²)"
Instead, ask:
> **How many times does the important operation execute as n grows?**
That reasoning is what lets you correctly analyze unfamiliar code.