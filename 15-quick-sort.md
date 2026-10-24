# Quick Sort

Quick Sort is a **divide-and-conquer** sorting algorithm. It chooses a **pivot**, partitions the list so values less than or equal to the pivot go to the left and larger values go to the right, then recursively sorts both sides.

## Example

Sort `[5, 2, 8, 1, 3]` using the last element as the pivot:

1. Choose pivot `3`.
2. Partition the list: values less than or equal to `3` move left, and larger values move right. The list becomes `[2, 1, 3, 5, 8]`.
3. Recursively sort the left part `[2, 1]` and right part `[5, 8]`.
4. The final result is `[1, 2, 3, 5, 8]`.

Partitioning places the pivot in its final sorted position. The two sides can then be sorted independently.

## Python Implementation

This implementation uses the last element as the pivot, following the **Lomuto partition scheme**.

```python
def quick_sort(arr):
    def partition(low, high):
        pivot = arr[high]
        i = low - 1

        for j in range(low, high):
            if arr[j] <= pivot:
                i += 1
                arr[i], arr[j] = arr[j], arr[i]

        arr[i + 1], arr[high] = arr[high], arr[i + 1]
        return i + 1

    def sort(low, high):
        if low < high:
            pivot_index = partition(low, high)
            sort(low, pivot_index - 1)
            sort(pivot_index + 1, high)

    sort(0, len(arr) - 1)
    return arr


print(quick_sort([5, 2, 8, 1, 3]))
# [1, 2, 3, 5, 8]
```

## How the Code Works

- `pivot = arr[high]`: choose the last element as the pivot.
- `i` marks the end of the section containing values less than or equal to the pivot.
- `j` scans the values before the pivot.
- When `arr[j] <= pivot`, move that value into the left section by incrementing `i` and swapping.
- After the scan, swap the pivot into the position immediately after the left section.
- Recursively sort the sections on either side of the pivot.
- `low < high` stops recursion for sections with zero or one element, since those sections are already sorted.

## Complexity

| Case | Time |
| --- | --- |
| Best | O(n log n), when partitions are reasonably balanced |
| Average | O(n log n) |
| Worst | O(n²), when partitions are repeatedly very unbalanced |

- **Extra space:** O(log n) average recursion stack; O(n) in the worst case.
- **Stable:** No, not in this implementation.
- **In-place partitioning:** The algorithm rearranges the original list without building separate left and right lists, though recursive calls still use stack space.

## Memory Trick

**Quick Sort = choose a pivot → partition → sort both sides.**

## When to Use It

Quick Sort is useful to learn because its average time complexity is O(n log n), and it can work well when pivot choices produce balanced partitions. Choosing the last element as pivot can perform badly on already-sorted or reverse-sorted input. For everyday Python code, prefer the built-in `sorted()` or `list.sort()` unless you are implementing an algorithm for learning.

## Practice

1. Trace Quick Sort on `[6, 3, 8, 2, 5]` using the last element as the pivot.
2. What index does the `partition` function return, and why?
3. Why can Quick Sort take O(n²) time in the worst case?
4. Is this Quick Sort implementation stable?
