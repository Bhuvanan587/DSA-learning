# Sorting Algorithms: Bubble, Selection, and Insertion Sort

These beginner-friendly algorithms sort a Python list in ascending order. Each implementation modifies the list in place and returns it.

## 1. Bubble Sort

**Idea:** Compare adjacent values and swap them when they are in the wrong order. After each pass, the largest unsorted value moves to the end.

```python
def bubble_sort(arr):
    n = len(arr)

    for i in range(n):
        for j in range(n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]

    return arr


print(bubble_sort([5, 2, 8, 1, 3]))
# [1, 2, 3, 5, 8]
```

### How the code works

- `i` counts the passes through the list.
- `j` moves through adjacent pairs.
- If the left value is larger than the right value, swap them.
- `n - i - 1` avoids checking values already sorted at the end.

**Complexity**

- Best: O(n²) in this basic version; O(n) with an early-stop optimization.
- Average and worst: O(n²)
- Extra space: O(1)
- Stable: Yes

An optimization is to track whether a swap happened during a pass. If no swap happened, the list is already sorted and the algorithm can stop.

## 2. Selection Sort

**Idea:** Find the smallest value in the unsorted section and swap it into the first unsorted position.

```python
def selection_sort(arr):
    n = len(arr)

    for i in range(n):
        min_index = i

        for j in range(i + 1, n):
            if arr[j] < arr[min_index]:
                min_index = j

        arr[i], arr[min_index] = arr[min_index], arr[i]

    return arr


print(selection_sort([5, 2, 8, 1, 3]))
# [1, 2, 3, 5, 8]
```

### How the code works

- `i` marks where the next smallest value belongs.
- `min_index` stores the index of the smallest value found so far.
- `j` scans the remaining unsorted values.
- Once the inner loop finishes, swap the minimum into position `i`.

**Complexity**

- Best, average, and worst: O(n²)
- Extra space: O(1)
- Stable: No, in this standard implementation
- Usually performs at most one swap per pass.

## 3. Insertion Sort

**Idea:** Build a sorted section from left to right. Take the next value (`key`), shift larger values one position right, and insert the key into the gap.

```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1

        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1

        arr[j + 1] = key

    return arr


print(insertion_sort([5, 2, 8, 1, 3]))
# [1, 2, 3, 5, 8]
```

### How the code works

- Start at index 1 because a one-element section is already sorted.
- `key` is the value to insert into the sorted left section.
- `j` moves left while values are larger than `key`.
- Larger values shift right; `arr[j + 1] = key` inserts the key in the correct position.

**Complexity**

- Best: O(n), when the list is already sorted
- Average and worst: O(n²)
- Extra space: O(1)
- Stable: Yes

## Quick comparison

| Algorithm | Main action | Best time | Average / worst | Extra space | Stable? |
| --- | --- | --- | --- | --- | --- |
| Bubble Sort | Swap adjacent out-of-order values | O(n²)* | O(n²) | O(1) | Yes |
| Selection Sort | Find minimum and swap it into place | O(n²) | O(n²) | O(1) | No** |
| Insertion Sort | Shift values and insert the next key | O(n) | O(n²) | O(1) | Yes |

\* Bubble Sort's best case becomes O(n) with the early-stop optimization.

\** Standard Selection Sort is not stable because a swap can change the relative order of equal values.

## Memory tricks

- **Bubble:** The largest value bubbles to the end.
- **Selection:** Select the minimum and place it next.
- **Insertion:** Insert the next value into the sorted left section.

## When to use them

- **Bubble Sort:** Mainly useful for learning; rarely chosen in practical code.
- **Selection Sort:** Simple and uses few swaps, but still makes many comparisons.
- **Insertion Sort:** Useful for small or nearly sorted lists.

For real Python programs, prefer the built-in `list.sort()` or `sorted()` unless you are implementing an algorithm for learning.

## Practice

1. Trace each algorithm on `[4, 2, 5, 1]` and write the list after each pass or insertion.
2. Which algorithm repeatedly compares adjacent elements?
3. Which algorithm tracks `min_index`?
4. Which algorithm shifts elements instead of swapping each time?
5. Why is Insertion Sort efficient on an already-sorted list?
