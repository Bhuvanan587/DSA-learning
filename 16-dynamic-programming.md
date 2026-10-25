# Dynamic Programming (DP)

> Dynamic Programming solves a problem by breaking it into smaller, overlapping subproblems and reusing their answers instead of recalculating them.

## 1. When should you think about DP?

DP is useful when a problem has both:

- **Overlapping subproblems:** the same smaller problem is solved repeatedly.
- **Optimal substructure:** an optimal answer can be built from answers to smaller subproblems.

Common signals in problem statements:

- Count the number of ways to do something.
- Find the minimum cost, minimum number, maximum profit, or longest sequence.
- Decide whether a target can be formed or a choice is possible.
- Each step depends on a small set of earlier states.

DP is not automatically the right choice for every problem involving recursion. First identify repeated work and a recurrence.

## 2. The core DP recipe

For a new problem, work through these steps in order.

1. **Define the state:** What does `dp[i]` or `dp[i][j]` mean? Write the meaning in plain English.
2. **Write the transition:** How can the current state be calculated from smaller states?
3. **Set base cases:** What are the smallest states whose answers are known directly?
4. **Choose an evaluation method:** Top-down memoization or bottom-up tabulation.
5. **Determine the answer location:** Is the answer `dp[n]`, `dp[n - 1]`, a maximum over states, or something else?
6. **Analyze complexity:** Count the number of states and the work done per state.
7. **Test edge cases:** Empty input, smallest valid input, repeated values, impossible targets, and boundary indices.

A useful rule:

> **DP complexity ≈ number of distinct states × work per state.**

## 3. Recursion, memoization, and tabulation

### Plain recursion

A recursive solution expresses the recurrence directly, but may solve the same subproblem many times.

For Fibonacci:

```py
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

This is easy to understand but has exponential time complexity, approximately **O(2ⁿ)**, because the recursion tree repeats work.

### Top-down DP: memoization

Memoization stores the result of each subproblem after computing it. Later calls reuse the stored answer.

```py
from functools import lru_cache

@lru_cache(maxsize=None)
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

- **Time:** O(n), because each value from 0 to n is computed once.
- **Space:** O(n) for cached results, plus the recursion stack.

Memoization is often easiest when the recurrence is naturally recursive or only a subset of possible states is reached.

### Bottom-up DP: tabulation

Tabulation computes states in an order that guarantees their dependencies are already known.

```py
def fib(n):
    if n <= 1:
        return n

    dp = [0] * (n + 1)
    dp[1] = 1

    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]
```

- **Time:** O(n).
- **Space:** O(n).

Tabulation avoids recursion overhead and makes the evaluation order explicit.

### Space-optimized tabulation

If each state depends only on the previous two states, the whole array is unnecessary.

```py
def fib(n):
    if n <= 1:
        return n

    prev2, prev1 = 0, 1

    for _ in range(2, n + 1):
        current = prev1 + prev2
        prev2, prev1 = prev1, current

    return prev1
```

- **Time:** O(n).
- **Extra space:** O(1).

Only optimize space after you understand the state dependencies and can prove older values are no longer needed.

## 4. Example: Climbing Stairs

**Problem:** You can climb either 1 or 2 steps at a time. How many distinct ways can you reach step `n`?

### State and transition

Let `dp[i]` be the number of ways to reach step `i`.

The last move must come from step `i - 1` or step `i - 2`:

```text
dp[i] = dp[i - 1] + dp[i - 2]
```

Base cases:

- `dp[0] = 1`: one way to remain at the starting position (take no steps).
- `dp[1] = 1`: one way to reach the first step.

```py
def climb_stairs(n):
    if n <= 1:
        return 1

    two_steps_back = 1
    one_step_back = 1

    for _ in range(2, n + 1):
        current = one_step_back + two_steps_back
        two_steps_back = one_step_back
        one_step_back = current

    return one_step_back
```

For `n = 4`, the values are 1, 1, 2, 3, 5, so there are **5 ways**.

- **Time:** O(n).
- **Extra space:** O(1).

The recurrence resembles Fibonacci, but the meaning and base cases come from the stairs problem. Always derive them from the problem rather than blindly copying Fibonacci.

## 5. Example: Minimum Coins (Coin Change)

**Problem:** Given coin denominations and a target amount, find the minimum number of coins needed. Each coin may be used unlimited times. Return `-1` if the amount cannot be formed.

Let `dp[a]` mean the minimum coins needed to make amount `a`.

For each coin `c` that does not exceed `a`:

```text
dp[a] = min(dp[a], dp[a - c] + 1)
```

Initialize `dp[0] = 0` and all other amounts to an unreachable sentinel, such as `amount + 1`.

```py
def coin_change(coins, amount):
    dp = [amount + 1] * (amount + 1)
    dp[0] = 0

    for a in range(1, amount + 1):
        for coin in coins:
            if coin <= a:
                dp[a] = min(dp[a], dp[a - coin] + 1)

    return dp[amount] if dp[amount] != amount + 1 else -1
```

- **Time:** O(amount × number of coin denominations).
- **Space:** O(amount).

**Why it works:** To make amount `a` using coin `c` last, first make amount `a - c`, then add one coin. The minimum over all valid last coins gives the optimal answer.

## 6. Example: House Robber

**Problem:** Each house has some money, but adjacent houses cannot both be robbed. Find the maximum amount.

Let `dp[i]` be the maximum money obtainable from the first `i` houses.

For each house, choose between:

- **Skip it:** keep the best answer for the previous house.
- **Rob it:** take its money plus the best answer before the previous house.

The recurrence is:

```text
dp[i] = max(dp[i - 1], dp[i - 2] + nums[i - 1])
```

```py
def rob(nums):
    prev2 = 0
    prev1 = 0

    for money in nums:
        current = max(prev1, prev2 + money)
        prev2, prev1 = prev1, current

    return prev1
```

- **Time:** O(n).
- **Extra space:** O(1).

The state is defined using the first `i` houses, which is why the current house is `nums[i - 1]` in the array-based recurrence. This indexing distinction is a common source of bugs.

## 7. Common DP patterns

### One-dimensional DP

A state is usually indexed by one position, amount, or item count.

Examples:

- Fibonacci and Climbing Stairs
- House Robber
- Minimum Cost Climbing Stairs
- Coin Change

### Grid / two-dimensional DP

A state often describes reaching cell `(r, c)` or solving a problem for prefixes of two sequences.

For paths moving only right and down, if every cell is allowed:

```text
dp[r][c] = dp[r - 1][c] + dp[r][c - 1]
```

The first row and first column need boundary handling. If obstacles exist, blocked cells must contribute zero paths.

### Knapsack / choose-or-skip DP

For each item, decide whether to include it or exclude it.

In **0/1 knapsack**, an item can be used at most once. A common state is `dp[i][capacity]`, the best value using the first `i` items with the given capacity.

- **Skip:** `dp[i - 1][capacity]`
- **Take, if it fits:** item value + `dp[i - 1][capacity - weight]`

A one-dimensional 0/1 knapsack optimization usually iterates capacity **backwards** so an item cannot be reused during the same pass. For unlimited-use variants, forward iteration may be appropriate. Loop direction changes the problem's meaning.

### Subsequence / two-string DP

For **Longest Common Subsequence (LCS)**, `dp[i][j]` can mean the LCS length for the first `i` characters of one string and first `j` characters of another.

- If the last characters match: `1 + dp[i - 1][j - 1]`
- Otherwise: `max(dp[i - 1][j], dp[i][j - 1])`

This pattern also appears in edit distance and related sequence problems, though their transitions differ.

### Interval DP

A state represents a range, such as `dp[left][right]`. Try splitting the interval at every possible middle point and combine the subproblem results. This often appears in matrix-chain multiplication and some palindrome problems.

### State-machine DP

Track a small state for each position, such as whether you are holding a stock, not holding a stock, or are in a cooldown period. Transitions model allowed changes between states.

## 8. How to recognize and build a state

A state must contain enough information to make future decisions correctly, but not unnecessary history.

Ask:

- What information about the past affects the remaining answer?
- If two paths reach the same state, will the remaining problem be identical?
- Can I express the answer using smaller states?
- What is the smallest state that fully describes the subproblem?

For example, in House Robber, the whole list of earlier decisions is unnecessary. The best results for the last two prefixes are enough to compute the next answer.

If the state forgets information that affects future choices, the recurrence will be incorrect. If the state stores too much irrelevant information, the solution may become unnecessarily large.

## 9. Memoization vs. tabulation

| Feature | Memoization | Tabulation |
| --- | --- | --- |
| Direction | Top-down | Bottom-up |
| Evaluation | Recursion + cache | Loops + table |
| States computed | Usually only reached states | Usually all states in the chosen order |
| Main overhead | Recursion stack | Table initialization |
| Best fit | Natural recursive structure, sparse states | Clear dependency order, avoiding recursion |

Both are DP. Neither is universally better.

## 10. Complexity analysis

Suppose there are `n` possible states and each state examines `k` choices:

- **Time:** often O(nk).
- **Space:** often O(n) for a one-dimensional table or O(nm) for an `n`-by-`m` table.
- Add recursion-stack space when using recursive memoization.
- Space optimization may reduce storage if only a few earlier states are required.

Do not confuse the number of recursive calls in an uncached solution with the number of distinct states in a DP solution.

## 11. Common mistakes

- Defining `dp[i]` without explaining what it represents.
- Guessing a recurrence instead of deriving it from the final decision or move.
- Incorrect base cases, especially for empty input and amount zero.
- Reading a table entry before it has been initialized or computed.
- Off-by-one errors when a state counts elements but an array uses zero-based indexes.
- Using the wrong loop direction for 0/1 versus unlimited-use choices.
- Returning the wrong state (for example, `dp[n - 1]` instead of `dp[n]`).
- Using an arbitrary sentinel that is not safe for the problem's constraints.
- Optimizing space before the recurrence is correct.
- Assuming every optimization problem is DP; some are better solved with greedy algorithms, graph algorithms, or other techniques.

## 12. A practical problem-solving template

Write these items in your notes before coding:

1. **State:** `dp[...]` means ...
2. **Transition:** `dp[state] = ...`
3. **Base cases:** ...
4. **Evaluation order:** ...
5. **Answer:** ...
6. **Time complexity:** ...
7. **Space complexity:** ...
8. **Edge cases:** ...

Start with a simple recursive definition if that helps reveal the recurrence. Add memoization to remove repeated work, then consider tabulation and space optimization.

## 13. Practice ladder

Try these in order. For each, define the state and recurrence before writing code.

1. **Fibonacci** — compare plain recursion, memoization, and tabulation.
2. **Climbing Stairs** — count ways using one- or two-step moves.
3. **Min Cost Climbing Stairs** — minimize total cost.
4. **House Robber** — maximize value with no adjacent selections.
5. **Coin Change** — minimum number of coins.
6. **Coin Change II** — count combinations rather than minimize coins.
7. **Unique Paths** — count paths through a grid.
8. **0/1 Knapsack** — maximize value under a capacity.
9. **Longest Common Subsequence** — compare two strings.
10. **Longest Increasing Subsequence** — optimize a sequence under increasing-order constraints.

## Quick revision

- **DP = states + transitions + base cases + an evaluation order.**
- Memoization caches recursive answers; tabulation builds answers iteratively.
- First define what each state means in plain English.
- Count distinct states and work per state to analyze time.
- Optimize space only when dependencies allow it.
- The hardest part is usually finding the right state and transition—not writing the loop.

---

## 14. Worked pattern: Grid DP

**Problem:** Count paths from the top-left to the bottom-right of a grid, moving only right or down.

Let `dp[r][c]` be the number of ways to reach cell `(r, c)`.

For an ordinary grid:
- The first row has one way to reach each cell: move right along the row.
- The first column has one way to reach each cell: move down the column.
- Every other cell can be reached from above or from the left.

```py
def unique_paths(rows, cols):
    dp = [[1] * cols for _ in range(rows)]

    for r in range(1, rows):
        for c in range(1, cols):
            dp[r][c] = dp[r - 1][c] + dp[r][c - 1]

    return dp[rows - 1][cols - 1]
```

- **Time:** O(rows × cols)
- **Space:** O(rows × cols)

A one-row optimization is possible because each cell only needs the value from above and the updated value to its left:

```py
def unique_paths(rows, cols):
    dp = [1] * cols

    for _ in range(1, rows):
        for c in range(1, cols):
            dp[c] += dp[c - 1]

    return dp[-1]
```

- **Extra space:** O(cols)

For grids with obstacles, initialize blocked cells to zero and never add paths through a blocked cell. Check empty dimensions if the function's input contract permits them.

## 15. Worked pattern: 0/1 Knapsack

**Problem:** Each item has a weight and value. Choose items to maximize total value without exceeding capacity. Each item can be chosen at most once.

Define `dp[i][cap]` as the best value using the first `i` items with capacity `cap`.

For item `i - 1`, either:
- Skip it: `dp[i - 1][cap]`
- Take it, if it fits: `values[i - 1] + dp[i - 1][cap - weights[i - 1]]`

```py
def knapsack(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        weight = weights[i - 1]
        value = values[i - 1]

        for cap in range(capacity + 1):
            dp[i][cap] = dp[i - 1][cap]

            if weight <= cap:
                dp[i][cap] = max(
                    dp[i][cap],
                    value + dp[i - 1][cap - weight]
                )

    return dp[n][capacity]
```

- **Time:** O(n × capacity)
- **Space:** O(n × capacity)

**Space optimization:** use one array, but iterate capacity backwards. This prevents the current item from being reused in the same pass.

```py
def knapsack(weights, values, capacity):
    dp = [0] * (capacity + 1)

    for weight, value in zip(weights, values):
        for cap in range(capacity, weight - 1, -1):
            dp[cap] = max(dp[cap], value + dp[cap - weight])

    return dp[capacity]
```

- **Extra space:** O(capacity)

This algorithm is pseudo-polynomial: its complexity depends on the numeric capacity, not just the number of input items. It is practical only when capacity is reasonably small.

## 16. Worked pattern: Subset Sum

**Problem:** Determine whether any subset of positive integers adds up to a target.

Define `dp[s]` as whether a sum of `s` is possible. Initially, only sum zero is possible: `dp[0] = True`.

For each number, update reachable sums **backwards** so that the same number is not used more than once.

```py
def can_make_sum(nums, target):
    dp = [False] * (target + 1)
    dp[0] = True

    for num in nums:
        for total in range(target, num - 1, -1):
            dp[total] = dp[total] or dp[total - num]

    return dp[target]
```

- **Time:** O(n × target)
- **Space:** O(target)

This is also useful for **Equal Subset Partition**: if the total sum is odd, equal partition is impossible; otherwise, check whether a subset sums to half the total. The positive-number assumption matters for this implementation.

## 17. Worked pattern: Longest Common Subsequence (LCS)

**Problem:** Find the length of the longest sequence appearing in both strings in the same order. Characters do not need to be adjacent.

Let `dp[i][j]` be the LCS length for the first `i` characters of string `a` and first `j` characters of string `b`.

- If the last characters match, add one to the diagonal subproblem.
- Otherwise, drop one character from either prefix and take the larger result.

```py
def lcs(a, b):
    dp = [[0] * (len(b) + 1) for _ in range(len(a) + 1)]

    for i in range(1, len(a) + 1):
        for j in range(1, len(b) + 1):
            if a[i - 1] == b[j - 1]:
                dp[i][j] = 1 + dp[i - 1][j - 1]
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])

    return dp[len(a)][len(b)]
```

- **Time:** O(len(a) × len(b))
- **Space:** O(len(a) × len(b))

The extra row and column represent empty prefixes, whose LCS length is zero. This makes the base cases and indexing straightforward.

## 18. Worked pattern: Edit Distance

**Problem:** Find the minimum number of insertions, deletions, and replacements needed to transform one string into another.

Let `dp[i][j]` be the minimum operations needed to transform the first `i` characters of `a` into the first `j` characters of `b`.

Base cases:
- Transforming a prefix of length `i` into an empty string takes `i` deletions.
- Transforming an empty string into a prefix of length `j` takes `j` insertions.

If the current characters match, no new operation is needed. Otherwise, choose the cheapest of delete, insert, or replace.

```py
def edit_distance(a, b):
    m, n = len(a), len(b)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if a[i - 1] == b[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(
                    dp[i - 1][j],      # delete
                    dp[i][j - 1],      # insert
                    dp[i - 1][j - 1]   # replace
                )

    return dp[m][n]
```

- **Time:** O(m × n)
- **Space:** O(m × n)

## 19. Worked pattern: Longest Increasing Subsequence (LIS)

**Problem:** Find the length of the longest strictly increasing subsequence. The selected elements keep their original order, but do not need to be adjacent.

A direct O(n²) DP definition is: `dp[i]` is the length of the longest increasing subsequence ending at index `i`.

```py
def lis(nums):
    if not nums:
        return 0

    dp = [1] * len(nums)

    for i in range(len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)

    return max(dp)
```

- **Time:** O(n²)
- **Space:** O(n)

This is a useful beginner implementation because the state has a clear meaning. There is also an O(n log n) approach using binary search and a tails array, but that array is not itself necessarily the actual subsequence. Learn the O(n²) recurrence first.

## 20. Reconstructing an actual solution

Many DP questions ask for the best value or length, but others ask you to return the choices that produced it. The final DP value alone may not reveal those choices.

A common method:
1. Store enough decisions or predecessor information while filling the table.
2. Start from the final state.
3. Follow the stored decisions backwards.
4. Reverse the collected choices if they were discovered in reverse order.

For example, to reconstruct a knapsack selection, store whether each state took or skipped an item, then walk backwards through the item and capacity dimensions. Alternatively, compare neighboring DP states to infer a choice when the recurrence allows it. Be careful with ties: multiple optimal solutions may exist.

## 21. Choosing between DP and other approaches

DP is a strong candidate when the problem can be expressed as a manageable set of states and transitions. But do not force it:

- **Greedy:** can work when a locally best choice can be proved to lead to a global optimum.
- **Graph algorithms:** shortest paths and reachability may be more naturally expressed as graph search; some graph problems also use DP.
- **Backtracking:** explores choices and is useful when constraints are small; memoization can help when many branches reach the same state.
- **Divide and conquer:** splits into mostly independent subproblems rather than repeatedly solving the same states.

A recurrence alone does not prove that a DP solution is correct. Explain why its cases cover every valid solution and why the chosen minimum or maximum is valid.

## 22. More practice, grouped by pattern

### 1D and decisions
- Min Cost Climbing Stairs
- House Robber II (circular arrangement)
- Decode Ways
- Word Break

### Grid and paths
- Unique Paths with Obstacles
- Minimum Path Sum
- Triangle Minimum Path Sum

### Knapsack and subsets
- Partition Equal Subset Sum
- Target Sum
- Coin Change II (count combinations)
- Unbounded Knapsack

### Strings and subsequences
- Longest Common Subsequence
- Edit Distance
- Longest Increasing Subsequence
- Longest Palindromic Subsequence

### Advanced patterns
- Matrix Chain Multiplication (interval DP)
- Stock Buy/Sell with transaction constraints (state-machine DP)
- Longest Path in a Directed Acyclic Graph (DAG DP)

For each practice problem, write the state in plain English, derive the transition, justify the base cases, identify the answer state, and calculate time and space complexity before coding.
